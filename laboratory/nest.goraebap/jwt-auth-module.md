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

# Jwt Auth Module ✨

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

### 생성 예제

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

### 검증 예제

검증 기능은 제공 예정입니다. 😅
