# Orphan (고아객체)

부모 엔티티 컬렉션과 자식 엔티티간 참조가 끊어져서 고아가 된 객체

## 예제

```java
@Entity
@Getter
public class Team{
    
    ...
    
    @OneToMany(mappedBy = "team", 
                cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Member> members = new ArrayList<Member>();
    
    ...
}
```

```java
team.getMembers().clear();  // member 엔티티들을 삭제한다.
```
고아 객체들이 어디에서도 참조되지 않을때만 사용할 것. 다른 곳에서 참조되고 있다면 문제가 생길 수 있다.