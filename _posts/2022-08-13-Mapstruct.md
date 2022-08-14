---
layout: post
title: MapStruct
subtitle: MapStruct란
author: 97kim
categories: dev
banner:
  video:
  loop:
  volume:
  start_at:
  image: /assets/images/mapstruct/mapstruct_bg.png
  opacity: 0.618
  background: "#000"
  height: "100vh"
  min_height: "38vh"
  heading_style: "font-size: 4.25em; font-weight: bold; text-decoration: underline"
  subheading_style: "color: gold"
tags: MapStruct
sidebar: []
---

MapStruct는 자바에서 서로 다른 객체 간 매핑에 대한 코드를 간편하게 자동으로 생성해주는 라이브러리다. Annotaion Processor를 사용해 컴파일 시 구현체를 생성한다.

객체 간 매핑에 대한 라이브러리로 ModelMapper도 있지만 MapStruct는 컴파일 시 매핑 코드를 생성하기 때문에 아래와 같은 장점이 있다.

1. Reflection을 사용하지 않아 속도가 빠르다.
   - Reflection: 구체적인 클래스 타입을 알지 못해도 해당 클래스의 정보(메서드, 타입, 변수 등)에 접근할 수 있게 해주는 것
2. 컴파일 시점에 매핑 코드를 생성하기 때문에 에러가 발생하면 확인이 가능하다.
3. 자동으로 생성된 구현체 코드를 직접 확인할 수 있다.

이 글에서는 DTO를 Entity로, Entity를 DTO로 변환하는 예제를 통해 설명하려고 한다.

### 개발 환경
- Kotlin / Spring Boot
- Gradle

### build.gradle.kts 설정
```
...

plugins {
    kotlin("jvm") version "1.6.21"
    kotlin("kapt") version "1.6.21"
}

...

dependencies {
    implementation("org.mapstruct:mapstruct:1.5.2.Final")
    kapt("org.mapstruct:mapstruct-processor:1.5.2.Final")
}

...
```

`build.gradle`을 Groovy DSL 대신 Kotlin DSL을 사용했기 때문에 `.kts`라는 확장자가 추가로 붙는다. 그리고 Kotlin DSL을 사용하면 Annotation Processor 대신 kapt라는 플러그인을 사용해야 한다.

만약 Groovy DSL을 사용한다면 위 코드의 `kapt()` 대신 `annotationProcessor()`를 넣으면 된다.

### Person(Entity)
```kotlin
@Entity
class Person(

    @Id
    @Column(name = "person_id")
    var id: Long? = null,

    @Column(name = "name")
    var name: String? = null,

    @Column(name = "age")
    var age: Int? = null,

    @Column(name = "phone_number")
    var phoneNumber: String? = null,

    @Column(name = "birth")
    var birth: LocalDate? = null
)
```

### PersonDTO(DTO)
```kotlin
data class PersonDTO(

    var id: Long? = null,

    var name: String? = null,

    var age: Int? = null,

    var phoneNumber: String? = null,

    var birth: LocalDate? = null
)
```

기본적으로 Person이라는 Entity와 DTO를 생성했다.

라이브러리를 사용하지 않고 Entity와 DTO의 변환을 해야 한다면 아래와 같이 코드를 짤 수 있다.

```kotlin
fun toPersonDTO(entity: Person): PersonDTO {
        return PersonDTO(
            id = entity.id,
            name = entity.name,
            age = entity.age,
            phoneNumber = entity.phoneNumber,
            birth = entity.birth
        )
    }

fun toPerson(dto: PersonDTO): Person {
        return Person(
            id = dto.id,
            name = dto.name,
            age = dto.age,
            phoneNumber = dto.phoneNumber,
            birth = dto.birth
        )
    }
```

위 코드를 보면 간단해서 하나씩 만들어도 상관 없을 것 같다는 생각이 들 수 있지만 필드가 늘어나면 늘어날수록 개발자의 피로가 증가할 것이다.

MapStruct를 사용하면 아래와 같이 사용할 수 있다.

```kotlin
@Component
@Mapper(componentModel = "spring")
interface PersonMapper {

    fun toPerson(dto: PersonDTO): Person

    fun toPersonDTO(entity: Person): PersonDTO

    fun toPersons(dtoList: MutableList<PersonDTO>): MutableList<Person>

    fun toPersonDTOs(entityList: MutableList<Person>): MutableList<PersonDTO>
}
```

우선 Mapper 파일은 class가 아니라 interface다.
해당 인터페이스의 구현체는 컴파일 시 생성된다.

- (Kotlin/Spring, kapt 기준) `/build/generated/source/kapt/main/...` 경로에 구현체가 생성이 된다.

![mapstruct1](/assets/images/mapstruct/mapstruct1.png)

```java
@Generated(
    value = "org.mapstruct.ap.MappingProcessor",
    date = "2022-08-13T21:53:06+0900",
    comments = "version: 1.5.2.Final, compiler: IncrementalProcessingEnvironment from kotlin-annotation-processing-gradle-1.6.21.jar, environment: Java 17.0.2 (Oracle Corporation)"
)
@Component
public class PersonMapperImpl implements PersonMapper {

    @Override
    public Person toPerson(PersonDTO dto) {
        if ( dto == null ) {
            return null;
        }

        Person person = new Person();

        person.setId( dto.getId() );
        person.setName( dto.getName() );
        person.setAge( dto.getAge() );
        person.setPhoneNumber( dto.getPhoneNumber() );
        person.setBirth( dto.getBirth() );

        return person;
    }

    @Override
    public PersonDTO toPersonDTO(Person entity) {
        if ( entity == null ) {
            return null;
        }

        PersonDTO personDTO = new PersonDTO();

        personDTO.setId( entity.getId() );
        personDTO.setName( entity.getName() );
        personDTO.setAge( entity.getAge() );
        personDTO.setPhoneNumber( entity.getPhoneNumber() );
        personDTO.setBirth( entity.getBirth() );

        return personDTO;
    }

    @Override
    public List<Person> toPersons(List<PersonDTO> dtoList) {
        if ( dtoList == null ) {
            return null;
        }

        List<Person> list = new ArrayList<Person>( dtoList.size() );
        for ( PersonDTO personDTO : dtoList ) {
            list.add( toPerson( personDTO ) );
        }

        return list;
    }

    @Override
    public List<PersonDTO> toPersonDTOs(List<Person> entityList) {
        if ( entityList == null ) {
            return null;
        }

        List<PersonDTO> list = new ArrayList<PersonDTO>( entityList.size() );
        for ( Person person : entityList ) {
            list.add( toPersonDTO( person ) );
        }

        return list;
    }
}
```

구현체가 잘 생성됐다. Kotlin으로 인터페이스를 만들었지만 구현체는 Java로 생성이 된다. 생성이 잘 됐으면 아래 코드처럼 활용할 수 있다.

```kotlin
val person = Person(
            id = 1L,
            name =  "name",
            age =  20,
            phoneNumber = "01012341234",
            birth = LocalDate.of(2003,1,1)
)
val personDTO = personMapper.toPersonDTO(person)
```

테스트 코드로 확인해보자.

```kotlin
@Test
fun test() {

    // given
    val person = Person(
        id = 1L,
        name =  "name",
        age =  20,
        phoneNumber = "01012341234",
        birth = LocalDate.of(2003,1,1)
    )

    // when
    val personDTO = personMapper.toPersonDTO(person)

    // then
    assertThat(person.id).isEqualTo(personDTO.id)
    assertThat(person.name).isEqualTo(personDTO.name)
    assertThat(person.age).isEqualTo(personDTO.age)
    assertThat(person.phoneNumber).isEqualTo(personDTO.phoneNumber)
    assertThat(person.birth).isEqualTo(personDTO.birth)
}
```

![mapstruct2](/assets/images/mapstruct/mapstruct2.png)

위 방법은 MapStruct의 기본적인 기능이다.
매핑시켜야할 Entity와 DTO가 많다면 Mapper를 만들고 함수도 계속 따로 만들어야 될까?

이럴 때는 공통으로 사용할 인터페이스를 만들어 상속받으면 된다.

```kotlin
/**
 * @param D is Dto type
 * @param E is Entity type
 */
interface EntityMapper<D, E> {

    fun toEntity(dto: D): E

    fun toDto(entity: E): D

    fun toEntity(dtoList: MutableList<D>): MutableList<E>

    fun toDto(entityList: MutableList<E>): MutableList<D>
}
```

EntityMapper 인터페이스를 만들고 Generic을 사용해 DTO와 Entity 타입을 지정한다.

```kotlin
@Component
@Mapper(componentModel = "spring")
interface PersonMapper: EntityMapper<PersonDTO, Person>
```

```kotlin
@Component
@Mapper(componentModel = "spring")
interface CarMapper: EntityMapper<CarDTO, Car>
```

이렇게 사용하면 함수를 계속 안 만들어도 되고 굉장히 간결해졌다.

컴파일을 해서 구현체도 확인해보자.

```kotlin
@Component
public class PersonMapperImpl implements PersonMapper {

    @Override
    public Person toEntity(PersonDTO dto) {
        if ( dto == null ) {
            return null;
        }

        Person person = new Person();

        person.setId( dto.getId() );
        person.setName( dto.getName() );
        person.setAge( dto.getAge() );
        person.setPhoneNumber( dto.getPhoneNumber() );
        person.setBirth( dto.getBirth() );

        return person;
    }

    @Override
    public PersonDTO toDto(Person entity) {
        if ( entity == null ) {
            return null;
        }

        PersonDTO personDTO = new PersonDTO();

        personDTO.setId( entity.getId() );
        personDTO.setName( entity.getName() );
        personDTO.setAge( entity.getAge() );
        personDTO.setPhoneNumber( entity.getPhoneNumber() );
        personDTO.setBirth( entity.getBirth() );

        return personDTO;
    }

    @Override
    public List<Person> toEntity(List<PersonDTO> dtoList) {
        if ( dtoList == null ) {
            return null;
        }

        List<Person> list = new ArrayList<Person>( dtoList.size() );
        for ( PersonDTO personDTO : dtoList ) {
            list.add( toEntity( personDTO ) );
        }

        return list;
    }

    @Override
    public List<PersonDTO> toDto(List<Person> entityList) {
        if ( entityList == null ) {
            return null;
        }

        List<PersonDTO> list = new ArrayList<PersonDTO>( entityList.size() );
        for ( Person person : entityList ) {
            list.add( toDto( person ) );
        }

        return list;
    }
}
```

```kotlin
@Component
public class CarMapperImpl implements CarMapper {

    @Override
    public Car toEntity(CarDTO dto) {
        if ( dto == null ) {
            return null;
        }

        Car car = new Car();

        car.setId( dto.getId() );
        car.setName( dto.getName() );
        car.setColor( dto.getColor() );

        return car;
    }

    @Override
    public CarDTO toDto(Car entity) {
        if ( entity == null ) {
            return null;
        }

        CarDTO carDTO = new CarDTO();

        carDTO.setId( entity.getId() );
        carDTO.setName( entity.getName() );
        carDTO.setColor( entity.getColor() );

        return carDTO;
    }

    @Override
    public List<Car> toEntity(List<CarDTO> dtoList) {
        if ( dtoList == null ) {
            return null;
        }

        List<Car> list = new ArrayList<Car>( dtoList.size() );
        for ( CarDTO carDTO : dtoList ) {
            list.add( toEntity( carDTO ) );
        }

        return list;
    }

    @Override
    public List<CarDTO> toDto(List<Car> entityList) {
        if ( entityList == null ) {
            return null;
        }

        List<CarDTO> list = new ArrayList<CarDTO>( entityList.size() );
        for ( Car car : entityList ) {
            list.add( toDto( car ) );
        }

        return list;
    }
}
```

아주 잘 생성이 됐다!

---

위에서 설명한 예제들은 Entity와 DTO의 필드명과 타입이 다 같았다. 예외적인 상황에서는 어떻게 할까?

### 서로 필드명이 다를 때
Person Entity의 phoneNumber 필드가 Person DTO에서는 call 필드로 받고 싶다. 그러면 아래처럼 `source`와 `target`을 `@Maaping` 어노테이션에 명시해주면 된다.
```kotlin
@Mapping(source = "call", target = "phoneNumber")
fun toPerson(dto: PersonDTO): Person

@Mapping(source = "phoneNumber", target = "call")
fun toPersonDTO(entity: Person): PersonDTO
```
A를 B로 변환시켜야 한다고 했을 때 A의 필드는 `source`가 되고 B의 필드는 `target`이 된다.

```kotlin
@Override
public Person toPerson(PersonDTO dto) {
    if ( dto == null ) {
        return null;
    }

    Person person = new Person();

    person.setPhoneNumber( dto.getCall() );
    person.setId( dto.getId() );
    person.setName( dto.getName() );
    person.setAge( dto.getAge() );
    person.setBirth( dto.getBirth() );

    return person;
}

@Override
public PersonDTO toPersonDTO(Person entity) {
    if ( entity == null ) {
        return null;
    }

    PersonDTO personDTO = new PersonDTO();

    personDTO.setCall( entity.getPhoneNumber() );
    personDTO.setId( entity.getId() );
    personDTO.setName( entity.getName() );
    personDTO.setAge( entity.getAge() );
    personDTO.setBirth( entity.getBirth() );

    return personDTO;
}
```

구현체 코드를 보면 잘 매핑이 되는 것을 확인할 수 있다.

```kotlin
// then
assertThat(person.phoneNumber).isEqualTo(personDTO.call)
```

아까 테스트 코드에서 phoneNumber 부분만 수정해서 테스트 해보면 성공이 뜬다.

### 매핑하려는 필드끼리 타입이 다를 때
`Person`은 `job` 필드의 타입이 `String(json)`이고, `PersonDTO`는 `job` 필드의 타입이 `JobDTO`일 때 어떻게 해야 할까?

### JobDTO
```kotlin
data class JobDTO(

    var type: String? = null,

    var location: String? = null,

    var year: Int? = null
)
```

### Person에 추가 된 job 필드
```kotlin
@Entity
class Person (

    @Id
    @Column(name = "person_id")
    var id: Long? = null,

    @Column(name = "name")
    var name: String? = null,

    @Column(name = "age")
    var age: Int? = null,

    @Column(name = "phone_number")
    var phoneNumber: String? = null,

    @Column(name = "birth")
    var birth: LocalDate? = null,

    @Column(name = "job")
    var job: String? = null
)
```

### PersonDTO에 추가 된 job 필드
```kotlin
data class PersonDTO(

    var id: Long? = null,

    var name: String? = null,

    var age: Int? = null,

    var phoneNumber: String? = null,

    var birth: LocalDate? = null,

    var job: JobDTO? = null
)
```

`PersonMapper`를 수정해야 할 부분이 있다.
MapStruct가 자동으로 생성해주는 구현체는 따로 명시하지 않는 이상 같은 필드명, 같은 필드 타입으로 매핑한다. 구현체의 setter 부분에 우리가 커스텀한 함수를 사용해 타입을 변환해야 한다.

```kotlin
@Component
@Mapper(componentModel = "spring")
interface PersonMapper: EntityMapper<PersonDTO, Person> {

    @Mapping(source = "job", target = "job", qualifiedByName = ["jobDtoToJson"])
    override fun toEntity(dto: PersonDTO): Person

    @Mapping(source = "job", target = "job", qualifiedByName = ["jsonToJobDto"])
    override fun toDto(entity: Person): PersonDTO

    @Mapping(source = "job", target = "job", qualifiedByName = ["jobDtoToJson"])
    override fun toEntity(dtoList: MutableList<PersonDTO>): MutableList<Person>

    @Mapping(source = "job", target = "job", qualifiedByName = ["jsonToJobDto"])
    override fun toDto(entityList: MutableList<Person>): MutableList<PersonDTO>

    companion object {
        @Named("jsonToJobDto")
        @JvmStatic
        fun jsonToJobDto(json: String?): JobDTO {
            return json?.let { ObjectMapper().readValue(it, JobDTO::class.java) } ?: JobDTO()
        }

        @Named("jobDtoToJson")
        @JvmStatic
        fun jobDtoToJson(jobDTO: JobDTO?): String {
            return jobDTO?.let { ObjectMapper().writeValueAsString(it) } ?: ""
        }
    }
}
```
`EntityMapper<DTO, Entity>` 인터페이스를 상속 받았지만 이는 기본적인 `toDto()`, `toEntity()` 함수를 제공하므로 `job` 필드처럼 예외 케이스가 있는 경우에는 `toDto()`, `toEntity()` 함수를 `override` 해서 오버라이딩 한 함수에 대해 구현체를 만들게 한다.
추가로 `@Named` 어노테이션과 `@Mapping` 어노테이션의 `qualifiedByName` 옵션을 사용한다.

`@Mapping`의 `source`와 `target`에 명시한 서로 매핑시킬 필드는 `@Named`로 지정한 함수를 통해 매핑시킨다. 이때 `@Named`에 쓰인 이름을 `@Mapping`의 `qualifiedByName` 옵션에 명시하면 된다.

그리고 `@Named`로 지정한 함수는 `static`으로 선언해야 한다. 그렇지 않으면 MapStruct가 해당 함수도 구현체에 구현하려고 해서 컴파일 에러가 발생할 수도 있다. 컴파일 에러가 발생하지 않는다고 해도, 정상적으로 구현체가 생성되지 않을 것이다.

아래 코드는 자동 생성 된 구현체다.

```java
@Component
public class PersonMapperImpl implements PersonMapper {

    @Override
    public Person toEntity(PersonDTO dto) {
        if ( dto == null ) {
            return null;
        }

        Person person = new Person();

        person.setJob( PersonMapper.jobDtoToJson( dto.getJob() ) );
        person.setId( dto.getId() );
        person.setName( dto.getName() );
        person.setAge( dto.getAge() );
        person.setPhoneNumber( dto.getPhoneNumber() );
        person.setBirth( dto.getBirth() );

        return person;
    }

    @Override
    public PersonDTO toDto(Person entity) {
        if ( entity == null ) {
            return null;
        }

        PersonDTO personDTO = new PersonDTO();

        personDTO.setJob( PersonMapper.jsonToJobDto( entity.getJob() ) );
        personDTO.setId( entity.getId() );
        personDTO.setName( entity.getName() );
        personDTO.setAge( entity.getAge() );
        personDTO.setPhoneNumber( entity.getPhoneNumber() );
        personDTO.setBirth( entity.getBirth() );

        return personDTO;
    }

    @Override
    public List<Person> toEntity(List<PersonDTO> dtoList) {
        if ( dtoList == null ) {
            return null;
        }

        List<Person> list = new ArrayList<Person>( dtoList.size() );
        for ( PersonDTO personDTO : dtoList ) {
            list.add( toEntity( personDTO ) );
        }

        return list;
    }

    @Override
    public List<PersonDTO> toDto(List<Person> entityList) {
        if ( entityList == null ) {
            return null;
        }

        List<PersonDTO> list = new ArrayList<PersonDTO>( entityList.size() );
        for ( Person person : entityList ) {
            list.add( toDto( person ) );
        }

        return list;
    }
}
```

구현체 코드에서

```java
person.setJob( PersonMapper.jobDtoToJson( dto.getJob() ) );
```

```java
personDTO.setJob( PersonMapper.jsonToJobDto( entity.getJob() ) );
```

이 부분들을 보면 우리가 커스텀한 함수를 사용해 매핑하는 것을 확인할 수 있다.

테스트까지 해보면

```kotlin
@Test
fun test2() {

    // given
    val person = Person(
        job = "{\"type\":\"developer\",\"location\":\"Seoul\",\"year\":1}"
    )
    val jobDto = JobDTO(type = "developer", location = "Seoul", year = 1)

    // when
    val personDTO = personMapper.toDto(person)

    // then
    assertThat(personDTO.job).isEqualTo(jobDto)
}
```

```kotlin
@Test
fun test3() {

    // given
    val personDto = PersonDTO(
        job = JobDTO(type = "developer", location = "Seoul", year = 1)
    )
    val jobJson = "{\"type\":\"developer\",\"location\":\"Seoul\",\"year\":1}"

    // when
    val person = personMapper.toEntity(personDto)

    // then
    assertThat(person.job).isEqualTo(jobJson)
}
```

![mapstruct3](/assets/images/mapstruct/mapstruct3.png)

성공!

번외로 Kotlin에서는 `static`을 사용하려면 Java와 조금 다르게 해야 돼서 헷갈리는 분들을 위해 Java 코드도 준비했다.

```java
@Component
@Mapper(componentModel = "spring")
public interface PersonMapperJava extends EntityMapper<PersonDTO, Person> {

    @Override
    @Mapping(source = "job", target = "job", qualifiedByName = "jobDtoToJson")
    Person toEntity(PersonDTO dto);

    @Override
    @Mapping(source = "job", target = "job", qualifiedByName = "jsonToJobDto")
    PersonDTO toDto(Person entity);

    @Override
    @Mapping(source = "job", target = "job", qualifiedByName = "jobDtoToJson")
    List<Person> toEntity(List<PersonDTO> dtoList);

    @Override
    @Mapping(source = "job", target = "job", qualifiedByName = "jsonToJobDto")
    List<PersonDTO> toDto(List<Person> entityList);

    @Named("jsonToJobDto")
    static JobDTO jsonToJobDto(String json) throws JsonProcessingException {
        return json == null ? new JobDTO() : new ObjectMapper().readValue(json, JobDTO.class);
    }

    @Named("jobDtoToJson")
    static String jobDtoToJson(JobDTO jobDTO) throws JsonProcessingException {
        return jobDTO == null ? "" : new ObjectMapper().writeValueAsString(jobDTO);
    }
}
```

---

MapStruct의 모든 기능들을 다룬 것은 아니다. 하지만 이 정도만 알아도 우리가 개발하는 데 있어서 충분히 이점을 가져갈 수 있을 것이라고 생각한다. 앞으로 MapStruct를 잘 활용해서 편하게 개발해보자!