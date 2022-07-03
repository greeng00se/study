## 외부 API를 사용하는 클래스 단위 테스트 하기

- `HttpService`를 사용하여 외부 API를 호출하는 클래스의 경우 어떻게 테스트해야 할까?

### 1. HttpService 사용

```tsx
@Injectable()
export class GooseService {
  constructor(private readonly httpService: HttpService) {}

  findAll(): Observable<AxiosResponse<Goose[]>> {
    return this.httpService.get('http://localhost:3000/geese');
  }
}
```

- nest의 HttpModule은 Axios를 한 번 추상화하여 Http 통신을 편하게 할 수 있게 도와준다.
- axios의 경우 반환 값인 AxiosResponse를 Promise로 감싼 데이터를 반환한다.
- HttpService의 경우 Axios의 반환값을 스트림 객체인 Observable로 한 번 더 감싸서 반환한다.
    - 내부적으로 통신 결과를 `makeObservable()` 메서드를 이용하여 Observable 객체 생성 후 반환한다.

### 2. 테스트 대상

```tsx
@Injectable()
export class BookSearchService {
  private readonly URL: string = 'https://openapi.naver.com/v1/search/book.json';
  private readonly API_KEY = {
    'X-Naver-Client-Id': process.env.BOOKAPI_ID,
    'X-Naver-Client-Secret': process.env.BOOKAPI_PW
  };
  
  constructor (
      private readonly httpService: HttpService,
  ) {}
    
  public async call()(bookQuery: string): Promise<any> {
    const params = { query: bookQuery };
    const config = { params, headers: this.API_KEY };
    const apiResult = await firstValueFrom(this.httpService.get(this.URL, config));
    return apiResult.data;
  }
}
```

- 네이버 검색 API를 이용해서 책에 대한 정보를 불러오는 클래스다.
- `firstValueFrom()` 메서드를 사용한다면 Observable 객체가 올 때 즉시 구독하여 `Promise<AxiosResponse<any, any>>` 타입으로 값을 받는다.
    - rxjs 8부터 Observable 객체를 Promise 타입으로 변환할 때 `.toPromise()` 메서드는 deprecated 되고 `firstValueFrom()`과 `lastValueFrom()` 메서드가 사용된다.
- 그 중 실제 api가 반환하는 데이터 부분만 반환한다.
- 메서드에서 any를 반환하는 것을 최대한 지양하고 싶었지만 반환값에 대한 변환이 필요했고, 변환을 담당하는 클래스를 따로 두었다.

### 3. 문제

- nest의 HttpService를 이용해 외부 API를 호출하는 클래스를 단위테스트를 하는 도중 문제가 발생하였다.
- jest가 지원하는 함수로 Mock 객체 생성하기엔 불편함이 있어 ts-mockito를 사용하고 있었다.
- HttpService를 ts-mockito를 사용하여 Mock 객체를 생성하기엔 다음과 같은 문제가 있었다.
    - `HttpService.get()` 메서드를 호출할 때 인자로 전달하는 값들이 너무 많다.
    - `thenReturn()`을 이용하여 Observable 객체를 반환하도록 해도 제대로 동작하지 않음

### 4. 해결

```tsx
let sut: BookSearchService;
let mockHttpService: HttpService;

beforeEach(async () => {
  const module: TestingModule = await Test.createTestingModule({
    providers: [
      BookSearchService,
      {
        provide: HttpService,
        useValue: {
          get: jest.fn(),
        },
      },
    ],
  }).compile();

  sut = module.get<BookSearchService>(BookSearchService);
  mockHttpService = module.get<HttpService>(HttpService);
});
```

- 테스트를 위한 모듈을 생성한다. 호출하려는 `get()` 메서드를 `jest.fn()`으로 모킹한다.
- 그리고 모킹한 HttpService를 테스트 대상 클래스에게 DI하도록 설정한다.

```tsx
it('call() 메서드는 실제 API의 호출 결과만 반환한다.', () => {
  // given
  const apiResult = 실제 책 API가 반환하는 json 형식의 값
  const axiosResponse: AxiosResponse<any, any> = {
    data: apiResult,
    status: 200,
    statusText: 'OK',
    headers: {},
    config: {},
  };
  
  jest.spyOn(mockHttpService, 'get').mockReturnValueOnce(
    of(axiosResponse)
  );
  
  // when
  const expected = sut.call('Real My SQL 8.0');

  // then
  expect(expected).toBe(apiResult);
});
```

- axios의 Http메서드는 AxiosResponse 값을 반환한다.
    - data, status, statusTest, headers, config는 필수로 있어야 하는 값이기 때문에 수동으로 더미객체를 생성해준다.
    - data에는 실제 도서 검색 API 반환 결과와 같은 형식의 값을 넣어주었다.
- `jest.spyOn()` 메서드는 보통 호출을 추적하는데 사용되지만 기존 메서드를 재구현할 수 있다.
- `mockReturnValueOnce()` 메서드를 이용해 mockHttpservice가 get() 메서드를 호출하면 Observable 객체를 반환하도록 설정한다.
- Observable 객체는 `rxjs.of()` 메서드를 사용하여 객체를 생성해준다.
- 테스트 대상의 `call()` 메서드가 실제 책 API가 반환하는 값만 반환하는 것을 확인할 수 있다.

## 정리

- 단순한 값을 반환하는 메서드를 모킹하거나 `Promise<T>` 타입을 반환하는 메서드를 모킹 하는 경우 ts-mockito가 편한 것 같다.
    - `thenReturn()`, `thenResolve()` 메서드 사용
- 스트림 객체인 Observable을 반환하는 HttpService 같은 경우 기존 jest를 통해 테스트하는 것이 좋은 방법인 것 같다.

## 참고 자료

- [HttpService, nest.js](https://docs.nestjs.com/techniques/http-module)
- [toPromise deprecations, rx.js](https://rxjs.dev/deprecations/to-promise)
- [firstValueFrom, rxjs](https://rxjs.dev/api/index/function/firstValueFrom)