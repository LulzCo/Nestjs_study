# Controllers

##### 컨트롤러

: 사용자로부터 받은 요청을 처리하여 응답하는 역할

<img src="./Controllers_img.png" alt="Controller_img" />



##### 데코레이터

: 스프링의 어노테이션과 비슷한 역할



##### CLI를 이용한 CRUD 컨트롤러 생성법

```
$ nest g reource name
```



##### 라우팅

: "@Controller()" 데코레이터를 이용하여 라우팅

ex)

```typescript
// localhost:3000/cats
@Controller('cats')
export class CatsController {
	// Get 요청 함수
  @Get()
	findAll() {
		return 'hello nest world!!'';
	}
 
  // localhost:3000/cats/temp
  @Get('temp')
  findAll2() {
    	return 'hello nest world!!';
  }
}
```



##### CLI를 이용한 컨트롤러 생성법

```
$ nest g controller cats
```



##### 요청 객체

: 클라이언트에게 요청하는 객체

```typescript
@Controller('cats')
export class CatsController {
	@Get()
	findAll() (@Req() request: Request): string {
		return 'hello nest world!!';
	}
}
```

express의 이점을 활용한 것 => express를 사용할 때, req, res를 사용하는 것

이를 위해서는 ''@types/express'를 설치해야함



##### nest에서 express 활용

| nestjs                  | express                      |
| ----------------------- | ---------------------------- |
| @Request(), @Req()      | req                          |
| @Response(), @Res()     | res                          |
| @Next()                 | next                         |
| @Session()              | req.session                  |
| @Param(key?: string)    | req.params/req.params[key]   |
| @Body(key?: string)     | req.body/req.body[key]       |
| @Query(key?: string)    | req.query/req.query[key]     |
| @Headers(name?: string) | req.headers/req.headers[key] |
| @Ip()                   | req.ip                       |
| @HostParam()            | req.hosts                    |



##### 리소스

```typescript
@Controller('cats')
export class CatsController {
	@Post()
	create(): string {
		return 'hello nest post request';
	}
	
	@Get()
	findAll(): string {
		return 'hello nest get request';
	}
}
```



##### 라우트 와일드카드

```typescript
// '/abcd' '/abscd', 'abaaaaaacd', ...
@Get('ab*cd')
findAll() {
	return 'this route uess a wildcard';
}
```



##### 상태 코드

```typescript
@Post()
@HttpCode(204)
create() {
	return 'this http code is 204';
}
```

'@nestjs/common'을 설치해야함



##### 헤더

```typescript
@Post()
@Header('Cache-Control', 'none')
create() {
	return 'this action adds a new cat';
}
```

'@nestjs/common'을 설치해야함



##### 리다이렉트

```typescript
@Get()
@Redirect('https:/nestjs.com', 301)
```

해당 요청을 받았을 때, 해당 파라미터 값(링크)로 요청

응답이 돌아오는 경우 아래 형식에 맞는 객체 생성

```json
{
	"url": string,
	"statusCode": number
}
```

ex)

```typescript
@Get('docs')
@Redirect('https://docs,nestjs.com', 302)
getDocs(@Query('version') version) {
	if (version && version === '5') {
		return {
			"url": 'https://docs.nestjs.com/v5/'
		};
	}
}
```



##### 라우트 파라미터

```typescript
// "/id"
@Get(':id')
findOne(@Param() params): string {
	console.log(params.id);
	return 'params.id: #${params.id}';
}
```

'@nestks/common' 설치해야함

프로젝트에서 사용 예시 ex)

```typescript
@Get(':id')
findOne(@Param('id') id: string): string {
	return 'this id is #${id}';
}
```



##### 서브 도메인 라우팅

특정 호스트의 요청을 받는 컨트롤러 라우팅

```typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
	@Get()
	index(): string {
		return 'admin page';
	}
}
```

fastify는 중첩 라우터에 대한 지원이 없기 때문에 하위 도메인 라우팅을 사용할 때 express 어댑터를 대신 사용해야 함

hosts의 옵션은 토큰을 사용하여 호스트 이름의 해당 위치에서 동적 프로그래밍 가능

-> ex)

```typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
	@Get()
	getInfo(@HostParam('account') account: string) {
		return account;
	}
}
```



##### 범위

**nest**는 **nodejs**이다!!!!!!

**nodejs는 단일 스레드 프로그래밍 언어로 설계되었다는 것을 명싱해야 한다!!!!!!!!!!!.**



##### 비동기성

js는 비동기 성질을 가진다

따라서, "**async/await**"를 지원한다

ex)

```typescript
@Get()
async findAll(): Promis<any[]> {
	return [];
}
```

nest 라우트는 RxJS의 관찰 스트림을 반환할 수 있도록 한다.

ex)

```typescript
@Get()
findAll(): Observable<any[]> {
	return of([]);
}
```



##### DTO

: 데이터 변환 객체, 데이터가 다른 기능으로 이동 및 변환될 때 사용

ex)

create-cat.dto.ts

```typescript
export class CreateCatDto {
	name: string;
	age: number;
	breed: string;
}
```

cats.controller.ts

```typescript
@Post()
async create(@Body() createCatDto: CreateCatDto) {
	return createCatDto;
}
```



##### 리소트 샘플

cats.controller.ts

```typescript
@Controller('cata')
export class CatsController {
	@Post()
	create(@Body() createCatDto: CreateCatDto) {
		return 'This action adds a new cat';
	}
	
	@Get()
	findAll(@Query() query: ListAllEntities) {
		return 'This action returns all cats (limit: ${query.limit} items)';
	}
	
	@Put(':id')
	update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
		return 'This action updates a #${id} cat';
	}
	
	@Delete(':id')
	remove(@Param('id') id: string) {
		return 'This action removes a #${id} cata';
	}
}
```

app.module.ts

```typescript
@Module({
	controllers: [CatsController],
})
export class AppModule {}
```



##### 요청에 대한 또 다른 응답 방식

return이 아닌 Response를 활용한 응답

```typescript
@Controller('cats')
export class CatsController {
	@Post()
	create(@Res() res: Response) {
		res.status(HttpStatus.CREATED).send();
	}
	
	@Get()
	findAll(@Res() res: Response) {
		res.status(HttpStatus.OK).json([]);
	}
}
```

return과 Response를 화용한 응답

```typescript
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
	res.status(HttpStatus.OK);
	return [];
}
```





















