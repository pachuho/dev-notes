## Given-When-Then

테스트 코드 패턴 중 보편적으로 사용되는 패턴으로 하위 단계로 구분하여 작성하는 방식이다.
- given: 테스트 실행을 준비하는 단계 
- when: 테스트를 진행하는 단계
- then: 테스트 결과를 검증하는 단계

```kotlin
@DisplayName("새로운 멤버를 저장한다.")
@Test
fun saveMemberTest() {
    // given : 멤버를 저장하기 위한 준비 과정
    val name = "juho"
    val age = 30

    val member = Member(name, age)
    // when : 실제로 멤버를 저장
    val savedId = memberService.save(member)

    // then : 멤버가 잘 추가되었는지 확인
    val savedMember = memberService.findById(savedId).get()
    assertThat(savedMember.name()).isEqualTo(name)
    assertThat(savedMember.age()).isEqualTo(age)
}
```