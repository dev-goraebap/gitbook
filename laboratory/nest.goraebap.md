---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🍰 nest.goraebap

nestjs를 사용하면서 자주 사용되는 모듈과 기능들을 npm package 로 관리하는 목적으로 만들었습니다.

### 설치

```bash
npm i nest.goraebap
```

### 로컬 인증

아이디, 패스워드 기반의 로그인시 사용할 수 있는 인증 모듈입니다.

* `username`, `password` 파라미터를 받기를 기대합니다.
* 결과값으로 `accessToken`, `refreshToken` 등 각 토큰의 만료 일자를 받기를 기대합니다.

#### 모듈 등록

먼저 main 이 되는 모듈에 `LocalAuthModule` 을 등록해줍니다.

```typescript
import { Module } from '@nestjs/common';
import { ConfigModule } from '@nestjs/config';

import { AuthStrategy, JwtConfig } from 'src/domain';
import { JwtAuthModule, LocalAuthModule } from 'src/libs/auth-manager';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV}`,
    }),
    JwtAuthModule.register({
      useClass: JwtConfig,
    }),
    // ✨ 로컬인증 모듈 등록
    LocalAuthModule.register({
      credential: CustomCredentialModel,
      strategy: AuthStrategy,
    })
  ]
})
export class AppModule { }
```

다른 모듈들은 이번 시나리오에서 사용되기 때문에 등록한 모듈들입니다. 같이 사용될 필요는 없습니다.

모듈을 등록할 때 <mark style="background-color:purple;">credential</mark> 과 <mark style="background-color:orange;">strategy</mark> 속성을 명시하도록 되어있습니다.&#x20;

#### Credential

CredentialModel 클래스를 확장하는 클래스 타입을 선언해야합니다. 따로 선언하지 않으면 CredentialModel 클래스를 기본으로 사용합니다.

```typescript
import { IsNotEmpty } from "class-validator";

export class CredentialModel {
    @IsNotEmpty({ message: '아이디를 입력해주세요.' })
    readonly username: string;

    @IsNotEmpty({ message: '비밀번호를 입력해주세요.' })
    readonly password: string;
}
```

기본 클래스는 class-validator 라이브러리를 사용하며 username, password 속성을 입력받기를 기대합니다. 추가적인 검증로직을 필요로 하면 위 클래스를 확장하여 등록해주시면 됩니다.

#### Strategy Class

자격증명 모델의 유효성 검증을 완료하면 <mark style="background-color:blue;">LocalAuthStrategy</mark> 인터페이스를 구현하는 인증 전략 클래스를 호출합니다.&#x20;

```typescript
import { TokenResults } from "../jwt";

import { CredentialModel } from "./credential.model";

export interface LocalAuthStrategy {
    verify(credential: CredentialModel): Promise<TokenResults>;
}
```

클래스는 개발자가 요구사항에 따라 직접 작성하게 됩니다. 위 인터페이스를 구현한 예제 클래스는 다음과 같습니다.

```typescript
import { Inject, Injectable } from "@nestjs/common";
import { CredentialModel, JWT_HELPER_SERVICE, JwtHelperService, LocalAuthStrategy, Roles, TokenResults } from "src/libs/auth-manager";

@Injectable()
export class AuthStrategy implements LocalAuthStrategy {

    constructor(
        @Inject(JWT_HELPER_SERVICE)
        private readonly jwtHelper: JwtHelperService
    ) {}

    async verify(credential: CredentialModel): Promise<TokenResults> {
    
        // 적절한 비즈니스 로직 처리

        // 토큰 발급
        return await this.jwtHelper.sign({
            aud: credential.username,
            role: Roles.USER
        });
    }
}
```

자격증명 모델과, 인증 전략 클래스를 상황에 맞게 작성하고 모듈을 등록하였다면 인증을 수행할 컨트롤러에서 다음과 같이 구성할 수 있습니다.

```typescript
@Controller('auth')
export class AuthController {

    @Post('local')
    @UseGuards(LocalAuthGuard)
    localLogin(@TokenResultParam() tokenResults: TokenResults) {
        return tokenResults;
    }
}
```

위와 같이 인증이 필요한 API 요청 메서드에 `LocalAuthGuard`를 적절히 붙여주면 됩니다.

### JWT 발급/인증

Jwt 토큰을 생성하고 검증하는 모듈입니다. 생성, 검증 모두 `JwtAuthModule`을 root 모듈(main, app 등)에 등록하여 사용합니다. 모듈은 글로벌이기 때문에 root 모듈에 등록하면 다른 모듈에서도 사용할 수 있습니다.

```typescript
@Module({
  imports: [
    // JwtConfig 팩토리 클래스에서 configService 를 사용하고 있기 때문에 
    // 등록을 보여준 예제
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV}`,
    }),
    // ✨ JwtAuthModule을 등록합니다.
    // register 함수를 통해 팩토리 클래스 타입을 선언합니다.
    JwtAuthModule.register({
      useClass: JwtConfig,
    })
  ]
})
export class AppModule { }
```

`JwtConfig` 클래스는 임의로 만든 팩토리 클래스이며 `JwtHelperOptionFactory` 인터페이스를 구현하고 있습니다.

```typescript
import { Injectable } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";

import { JwtHelperOptionFactory, TokenGenerateOptions } from "src/libs/auth-manager";

@Injectable()
export class JwtConfig implements JwtHelperOptionFactory {

    constructor(
        private readonly configService: ConfigService
    ) {}

    async create(): Promise<TokenGenerateOptions> {
        const actSecret = this.configService.get('ACCESS_TOKEN_SECRET');
        const actSecretExpiresIn = this.configService.get('ACCESS_TOKEN_EXPIRES_IN');
        const refSecret = this.configService.get('REFRESH_TOKEN_SECRET');
        const refSecretExpiresIn = this.configService.get('REFRESH_TOKEN_EXPIRES_IN');

        return {
            accessToken: {
                secret: actSecret,
                expiresIn: actSecretExpiresIn
            },
            refreshToken: {
                secret: refSecret,
                expiresIn: refSecretExpiresIn
            }
        }
    }
}
```

객체리터럴 방식으로 편하게 등록하는 것은 제공하지 않습니다. 팩토리 클래스를 직접 작성하여 등록하는 것이 유일한 방식 입니다.

#### 생성 예제

위와 같이 모듈 설정을 마무리 했다면, 토큰을 발급해야하는 클래스에서 다음과 같이 사용할 수 있습니다.

```typescript
import { Inject, Injectable } from "@nestjs/common";
import { CredentialModel, JWT_HELPER_SERVICE, JwtHelperService, LocalAuthStrategy, Roles, TokenResults } from "src/libs/auth-manager";

@Injectable()
export class AuthStrategy implements LocalAuthStrategy {

    constructor(
        @Inject(JWT_HELPER_SERVICE)
        private readonly jwtHelper: JwtHelperService
    ) {}

    async verify(credential: CredentialModel): Promise<TokenResults> {

        // 비즈니스 로직 수행

        // 토큰 발급 ✨
        return await this.jwtHelper.sign({
            aud: credential.username, 
            role: Roles.USER
        });
    }
}
```

생성된 토큰 정보는 다음과 같습니다.

```bash
{
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0ZXN0ZXIiLCJ0eXBlIjoiQUNDRVNTIiwicm9sZSI6IlVTRVIiLCJpYXQiOjE3MTQ1MzgyNjAsImV4cCI6MTcxNDU0MDA2MH0.PP3FYSZRICHre8NeWKQJXFHIPmexCZ57gAALU7KZdnU",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJ0ZXN0ZXIiLCJ0eXBlIjoiUkVGUkVTSCIsInJvbGUiOiJVU0VSIiwiaWF0IjoxNzE0NTM4MjYwLCJleHAiOjE3MTUxNDMwNjB9.6Li8d8ESp4CessqEqQnKHQFv-JWeaXwJJFW-dr6h4PY",
    "accessTokenExpiresAt": "2024-05-01T05:07:40.000Z",
    "refreshTokenExpiresAt": "2024-05-08T04:37:40.000Z"
}
```

#### 검증 예제

검증 기능은 제공 예정입니다. 😅



