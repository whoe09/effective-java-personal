* 플라이웨이트 패턴: 동일하거나 유사한 객체들 사이에 많은 데이터를 공유하여 최적화 도출하는 경량패턴
> - 사용시기
    >   - 객체의 수가 많아 저장 비용이 높아질때
>   - 생성된 객체가 오래도록 메모리에 상주하며 사용되는 횟수가 많을때
>   - 공통적인 인스턴스를 많이 생성하는 로직이 포함된 경우
> - 장점
    >   - 사용되는 메모리 이점
>   - 프로그램 속도 개선
> - 단점
    >   - 코드 복잡도 증가
>
> intrinsic한 객체 : 의존 가능성이 없다 = 값 고정 = 공유 될 수 있는  
> extrinsic한 객체 : 의존 가능성이 높다 = 값 유동 = 공유 할 수 없는

마인크래프트 예시
```java
public class Main {
    class Memory {
        public static long size = 0; //메모리 사용량 

        public static void checkMemory() {
            System.out.println("Total used memory : "+Memory.size+"MB");
        }
    }

    class Tree {
        // 1회 tree 생성시 사용되는 메모리
        long objSize = 100; //100MB;

        String type;
        Object mesh;
        Object texture;

        double position_x;
        double position_y;

        public Tree(String type, Object mesh, Object texture, double position_x, double position_y) {
            this.type = type;
            this.mesh = mesh;
            this.texture = texture;
            this.position_x = position_x;
            this.position_y = position_y;

            // 나무 객체를 생성하였으니 메모리 사용 크기 증가
            Memory.size +=  this.objSize;
        }
    }

    class Terrain {
        static final int CANVAS_SIZE = 10000;

        public void render(String type, Object mesh, Object texture, doule position_x, double position_y) {
            // 나무를 지형에 생성 
            Tree tree = new Tree(
                    type, // 나무 종류
                    mesh, // mesh
                    texture, // texture
                    Math.random() * CANVAS_SIZE, // position_x
                    Math.random() * CANVAS_SIZE // position_y
            );

            System.out.println("x:" + tree.position_x + " y:" + tree.position_y + " 위치에 " + type + " 나무 생성 완료");
        }
    }
    
    public static void main(String[] args) {
        Terrain terrain = new Terrain();

        for (int i = 0; i < 5; i++) {
            terrain.render(
                    "Oak",
                    new Object(),
                    new Object(),
                    Math.random()*Terrain.CANVAS_SIZE,
                    Math.random()*Terrain.CANVAS_SIZE
            );
        }
        // Acacia, jungle 등 3종의 나무 생성
        
        Memory.checkMemory();
    }
}
```
문제점 : 나무가 생성될때 마다 인스턴스가 올라가서 메모리르 잡아먹는다.  
여기서 동일한 나무의 경우 position값을 제외하곤 동일한 value 이지만 다소 어지러운 코드이다.  
***
<div align="center">
    <img src="./fly_weight_factory.png">
</div>

1. intrinsic / extrinsic 객체 쪼개기  
```java
// ConcreteFlyweight - 불변성을 가져야한다 = 동일한 값으로 공유되어야함
final class TreeModal {
    long objSize = 90;
    
    String type;
    Object mesh;
    Object texture;
    
    public TreeModal(String type, Object mesh, Object texture) {
        this.type = type;
        this.mesh = mesh;
        this.texture = texture;

        // 나무 객체를 생성하여 메모리에 적재했으니 메모리 사용 크기 증가
        Memory.size += this.objSize;
    }
}

// UnsahredConcreteFlyweight
class tree {
    // 죄표값과 나무 모델 참조 객체 크기를 합친 사이즈
    long objSize = 10; // 10MB

    // 위치 변수
    double position_x;
    double position_y;

    // 나무 모델
    TreeModel model;

    public Tree(TreeModel model, double position_x, double position_y) {
        this.model = model;
        this.position_x = position_x;
        this.position_y = position_y;

        // 나무 객체를 생성하였으니 메모리 사용 크기 증가
        Memory.size +=  this.objSize;
    }
}
```