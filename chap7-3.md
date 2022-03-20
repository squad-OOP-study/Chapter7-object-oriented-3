# chap7 디자인패턴3

2022.3.20

> 목차 
- 파사드 패턴
- 추상 팩토리 패턴
- 컴포지트(composite) 패턴
- Null 패턴

<br><br>

# 파사드 패턴

> 공통으로 여러 객체를 접근 하는 경우 문제점
  - <img src="https://user-images.githubusercontent.com/55780251/159144766-13c87630-9e67-43a4-915a-6f402ab2a617.jpg" width="60%">  
  - 코드 중복
    - 클라이언트 객체가 완전히 똑같지 않고 약간씩 달라질 수 있는데 이후 변경시 미세한 차이점 누락 가능성 높아진다.
  - 직접 의존
    - 필요한 것은 3개의 Dao가 제공하는 데이터를 통합한 하나의 데이터. 그러나 지금은 개별 dao 객체에 의존 

> 파사트 패턴
  - 코드 중복과 직접적인 의존 해결
  - 파사드를 통해 간접적으로 서브 시스템에 접근
  - <img src="https://user-images.githubusercontent.com/55780251/159144767-a067d067-965d-43dd-a91e-e5f9335d6b62.jpg" width="60%">
  - 클라이언트 코드가 간졀 

> 특징
- 다수의 클라이언트에 공통된 기능은 파사드를 통해서 쉽게 서브 시스템을 사용
- 보다 세밀한 제어가 필요한 경우에는 서브 시스템에 직접 접근하는 방식을 선택할 수 있다.


<br><br>

# 추상 팩토리 패턴  

> 추상 팩토리 패턴이란
- 클래스로부터 객체 생성 책임 분리 

> 추상 팩토리 적용 가능 예시 
- 문제점 : 레벨별로 보스,적기,장애물 생성 규칙이 Stage 클래스에 포함되어 있다.
- 그럼으로써 레벨의 보스 종류가 바뀔 때나 
- 적기 생성규칙이 달라질 때 
- Stage 클래스를 수정해야 한다.

    ```java
    public class StageBefore {

        private void createEnemies() {
            for (int i = 0; i < ENEMY_COUNT; i++) {
                if (stageLevel == 1) {
                    enmies[i] = new DashSmallFilight(1, 1); //공격력, 수비력
                } else if (stageLevel == 2) {
                    enmies[i] = new MissileSmallFilight(1, 1); //공격력, 수비력
                }
            }

            if (stageLevle == 1) {
                boss = new StrongAttackBoss(1, 10);
            } else if (stageLevel == 1) {
                boss = new StrongAttackBoss(5, 20);
            }
        }

        private void createObstacle() {
            for (int i = 0; i < OBSTACLE_COUNT; i++) {
                if (stageLevel == 1) {
                    obstacles[i] = new RockObstacle();
                } else if (stageLevel == 2) {
                    obstacles[i] = new BombObstacle(); //공격력, 수비력
                }
            }
        }
    }
    ```

> 추상 팩토리 패턴 적용 후 
1. Stage 클래스에서 객체 생성 책임 분리 
    ```java
    public class EnemyFactory {
        //1. getFactory() 메서즈 
        public static EnemyFacotry getFactory(int level) {
            if(level ==1 )
                //새로운 구현체를 전달함으로써 새로운 보스,적,장애물을 완전히 다른 타입으로 변경 가능.
                return EasyEnemyFactory();
            else
                return HardEnemyFactory();
        }
        
        //2. DI를 사용하면 getFactory()도 없앨 수 있다.
        //이러면 Factory클래스를 추상 클래스에서 인터페이스로 전환할 수 있다.
        private EnemyFacotry enemyFactory;
        private int level;

        public EnemyFactory(int level, EnemyFacotry enemyFactory) {
            this.level = level;
            this.enemyFactory = enemyFactory;
        }

        //객체 생성을 위한 팩토리 메서드
        public abstract Boss createBoss();
        public abstract SmallFligth createSmallFlight();
        public abstract Obstacle createObstacle();
    }
    ```

2. 콘크리트 팩토리 클래스 구현 
    ```java
    public class EasyStageEnemyFactory extends EnemyFactory {

        @Override
        public Boss createBoss() {
            return new StrongAttackBoss();
        }

        @Override
        public SmallFligth createSmallFlight() {
            return new DashSmallFlight();
        }

        @Override
        public Obstacle createObstacle() {
            return new RockObstacle();
        }
    }
    ```
3. Stage 클래스는 객체 생성이 필요할 경우 추상 팩토리를 이용해서 객체 생성 
    - 콘크리트 제품 클래스를 사용하지 않는다.
    - 각 콘크리트 제품 클래스를 사용해서 객체를 생성하는 코드는 EnemyFactory의 하위 타입 클래스에서 담당한다.
    - 이제부터 보스, 적기, 장애물을 클라이언트 코드에 영향없이 변경할 수 있다. 새로운 EnemyFactory구현체 만들고, getFactory()에서 해당 클래스의 객체 전달하도록 하면된다.
    ```java
    //팩토리 사용하도록 바뀐 Stage 클래스
    public class Stage {

        private EnemyFactory enemyFactory;

        public Stage(EnemyFactory enemyFactory) {
		this.enemyFactory = EnemyFactory.getFactory(level);
	}

        private void createEnemies() {
            for (int i = 0; i < ENEMY_COUNT; i++) 
                enemies[i] = enemyFactory.createSmallFligth();

            boss = enemyFactory.createBoss();
        }

        private void createObstracle() {
            for (int i = 0; i < OBSTACLE_COUNT; i++)
                obstacles[i] = enemyFactory.createObstacle();
        }
    }
    ```



