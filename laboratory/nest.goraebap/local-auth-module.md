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

# Local Auth Module ✨

아이디, 패스워드 기반의 로그인시 사용할 수 있는 인증 모듈입니다.

* `username`, `password` 파라미터를 받기를 기대합니다.
* 결과값으로 `accessToken`, `refreshToken` 등 각 토큰의 만료 일자를 받기를 기대합니다.

### 모듈 등록

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
