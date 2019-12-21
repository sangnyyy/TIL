# Mockito

## given

## when
### doNothing()

void 리턴 값을 가진 메서드에 대해 해당 메서드가 아무것도 수행하고 싶지 않게 하고 싶을 때 사용.

~~~java
@Test
public void 예제(){
    doNothing()
        .when(fileService)
        .editFile(any(Board.class), any(), anyString());
    
    doNothing()
        .when(boardFileRepository)
        .delete(any());
}
~~~

## then