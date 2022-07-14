## TypeORM find 사용하여 조회시 외래키가 안보이는 문제

- TypeORM에서 연관관계는 `@JoinColum()` 데코레이터를 이용해 설정한다.
- 이 경우 `Repository`에서 `find()` 메서드를 호출하여 조회를 하는 경우 외래키가 안보이는 문제가 발생한다.

```tsx
@Entity()
export class Review extends BaseTimeEntity {
  @Column()
  title: string;

  @ManyToOne(type => User)
  @JoinColumn({ name : 'user_id' })
  user: User;
}
```

### 해결

- `@JoinColumn`으로 생성한 칼럼명과 똑같은 이름으로 필드를 생성한 다음 `{nullable: true}` 옵션을 주어 사용하면 된다.
- 추가로 `@JoinColumn`으로 기본적으로 생성되는 필드명은 xxxId 형태로 생성된다.

```tsx
@Entity()
export class Review extends BaseTimeEntity {
  @Column()
  title: string;

  @ManyToOne(type => User)
  @JoinColumn({ name : 'user_id' })
  user: User;

  @Column({ nullable: true })
  user_id: number
}
```

## 참고 자료

- [Relations FAQ, TypeORM](https://typeorm.io/relations-faq#how-to-use-relation-id-without-joining-relation)