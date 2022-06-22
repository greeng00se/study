## TypeORM 0.3 EntityRepository Deperecated

- TypeORM 0.3.0 부터 `EntityRepository`가 deprecated 되었다.
- nest에서 기존의 `EntityRepository` 을 사용하는 것과 사용방법이 유사한 해결방법을 찾게 되었다.
    - 직접 데코레이터와 모듈을 생성하여 기존의 패턴을 유지하는 방법이다.
- 해결 방법은 다음과 같다.

### @CustomRepository decorator 생성

```tsx
// configs/typeorm-ex/typeorm-ex.decorator.ts
import { SetMetadata } from "@nestjs/common";

export const TYPEORM_EX_CUSTOM_REPOSITORY = "TYPEORM_EX_CUSTOM_REPOSITORY";

export function CustomRepository(entity: Function): ClassDecorator {
  return SetMetadata(TYPEORM_EX_CUSTOM_REPOSITORY, entity);
}
```

- `@CustomRepository` 데코레이터를 생성해준다.
- `SetMetadata()` 메서드를 이용하여 전달받은 Entity를 `TYPEORM_EX_CUSTOM_REPOSITORY` 메타데이터에 지정해준다.

### TypeOrmExModule 생성

```tsx
import { DynamicModule, Provider } from "@nestjs/common";
import { getDataSourceToken } from "@nestjs/typeorm";
import { DataSource } from "typeorm";
import { TYPEORM_EX_CUSTOM_REPOSITORY } from "./typeorm-ex.decorator";

export class TypeOrmExModule {
  public static forCustomRepository<T extends new (...args: any[]) => any>(repositories: T[]): DynamicModule {
    const providers: Provider[] = [];

    for (const repository of repositories) {
      const entity = Reflect.getMetadata(TYPEORM_EX_CUSTOM_REPOSITORY, repository);

      if (!entity) {
        continue;
      }

      providers.push({
        inject: [getDataSourceToken()],
        provide: repository,
        useFactory: (dataSource: DataSource): typeof repository => {
          const baseRepository = dataSource.getRepository<any>(entity);
          return new repository(baseRepository.target, baseRepository.manager, baseRepository.queryRunner);
        },
      });
    }

    return {
      exports: providers,
      module: TypeOrmExModule,
      providers,
    };
  }
}
```

- `Reflect.getMetadata()` 메서드로 메타데이터 키값인 `TYPEORM_EX_CUSTOM_REPOSITORY`에 해당되는 엔티티를 가져온다.
- 메타데이터 키값에 해당하는 엔티티가 존재하는 경우 Factory를 이용하여 provider를 동적으로 생성하여 providers에 추가한다.

### 데코레이터 적용

```tsx
@CustomRepository(Book)
export class BookRepository extends Repository<Book> {
  ...
}
```

- `@CustomRepository` 데코레이터를 기존에 사용하던 `@EntityRepository` 데코레이터 적용하는 부분에 대체하여 사용한다.

### 모듈 설정

```tsx
@Module({
  imports: [
    TypeOrmExModule.forCustomRepository([BookRepository]),
  ],
  ...
})
```

- 전에 생성한 모듈을 추가 해준다.

### Service에서의 사용

```tsx
@Injectable()
export class BoardsService {
  constructor(
      private boardRepository: BoardRepository,
  ) {}

  ...
}
```

- 기존에 `@EntityRepository`를 사용한 경우 `@InjectRepository` 데코레이터를 사용했지만 해당 부분을 유지하면 오류가 발생한다.
- 따라서 `@CustomRepository`를 생성하여 사용하는 경우 `@InjectRepository`를 제거하고 사용하자

## 참고 자료

- [CustomRepository, AbolfazlR](https://stackoverflow.com/questions/71557301/how-to-workraound-this-typeorm-error-entityrepository-is-deprecated-use-repo)