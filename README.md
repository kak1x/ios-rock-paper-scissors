# iOS 커리어 스타터 캠프
# 🤜🏻 묵찌빠 게임

> 프로젝트 기간: 2022.12.26 ~ 2022.12.29
> 
> 팀원: 👩🏻‍💻[sehong](https://github.com/sehoong0429), 👨🏻‍💻[kaki](https://github.com/kak1x) | 리뷰어: 👨🏻‍💻[Ayaan](https://github.com/oneStar92)
> 

# 프로젝트 소개
사용자가 입력한 숫자와 컴퓨터가 가진 랜덤숫자 값으로 가위바위보 게임을 진행하고, 가위바위보 게임의 승자를 정한 뒤 묵찌빠 게임을 진행합니다.
가위바위보 게임에서 이긴 사람은 턴을 가지게 되고 묵찌빠 게임을 진행하며 묵찌빠 게임에서 이기면 다시 턴을 가져오게 되고, 본인의 턴에서 비길 시 해당 턴을 가진 사람이 승자가 됩니다.
<br/>

# 타임라인
- 22.12.26 (월): STEP1 가위바위보 게임 기본 기능 구현
- 22.12.27 (화): STEP1 PR feedback 적용, STEP2 묵찌빠게임 기본 기능 구현
- 22.12.28 (수): STEP1 feedback 사항 STEP2 적용
- 22.12.29 (목): STEP2 PR feedback 적용
<br>

# 실행 화면
### 1. 가위바위보 게임 진행

1. ```1, 2, 3```을 선택할 시 가위바위보 게임이 시작되며 비길 경우 게임을 반복하다 승패가 결정될 시 이긴 사람의 턴으로 묵찌빠 게임으로 전환됨.<br>
![](https://i.imgur.com/UyNRncA.png)

### 2. 올바르지 않은 메뉴 입력 상황 및 게임 종료 (가위바위보 게임)

  1. ```0, 1, 2, 3``` 이외의 값이 들어올 경우 잘못된 입력입니다. 다시 시도해주세요.```라는 문구가 출력되고 옳은 값이 들어오기까지 반복됨.  
  2. ```0```을 선택시, 프로그램 종료<br>
  ![](https://i.imgur.com/LQmai8W.png)


### 3. 묵찌빠 게임 진행
  1. 가위바위보 게임의 승자를 기준으로 턴이 결정됨.
  2. 묵찌빠 게임의 승패에 따라 턴이 바뀌며 묵찌빠 게임에서 비길 시 마지막 턴이었던 쪽이 게임의 승자가 됨.<br>
  ![](https://i.imgur.com/3qBYbyR.png)

  
### 4. 올바르지 않은 메뉴 입력 상황 및 게임 종료 (묵찌빠 게임)
  1. 묵찌빠 게임 도중 올바르지 않은 메뉴를 입력 시 컴퓨터의 턴으로 바뀌게 됨.
  2. 묵찌빠 게임 진행 도중 ```0```을 입력할 시 게임이 즉시 종료됨.<br>
![](https://i.imgur.com/oi5HPI5.png)
<br>

# 트러블 슈팅
### 1. 한 가지의 Enum 타입에서 여러 rawValue 처리하기
  - 가위바위보 게임과 묵찌빠 게임 모두 가위, 바위, 보의 case를 가지고 있지만 각각의 입력값에 따라 반환되는 case가 달라서 문제가 발생하였습니다.
  - 두 가지의 Enum 타입을 정의하면 쉽게 해결되는 문제였지만 이는 중복되는 코드의 느낌을 준다고 생각하여 해결 방법을 고민하였습니다.
  - 위 문제에 대한 해결 방법으로 Enum 타입의 실패 가능 생성자(init?)를 사용하게 되었습니다.

```swift
//해결 방법
enum HandShape {
    case scissors
    case rock
    case paper
    
    init?(rockScissorsPaper: Int) {
          switch rockScissorsPaper {
          case 1:
              self = .scissors
          case 2:
              self = .rock
          case 3:
              self = .paper
          default:
              return nil
          }
    }
    
    init?(mukJjiBba: Int) {
        switch mukJjiBba {
        case 1:
            self = .rock
        case 2:
            self = .scissors
        case 3:
            self = .paper
        default:
            return nil
        }
    }
}
```
- 실패 가능 생성자를 사용하면 반환 타입이 옵셔널이 되지만 옵셔널끼리의 비교가 가능하기에 따로 ```unwrapping``` 과정 없이 진행하였습니다.
<br>


### 2. 승패 판단 함수 구현 
 - 사용자가 입력한 값과 컴퓨터의 값을 비교해서 승패를 가려야하는 부분이 있었습니다. 처음에는 ```if문```을 통해 5가지의 경우의 수를 분리해 같은 값을 가질때, 서로 다른 값을 가져 승자를 고르는 경우 3가지, 승자와 비겼을 때를 제외한 패자를 고르는 경우로 나누어 보았습니다. 
 
 - 하지만 단순하게 보면 ```win```, ```lose```, ```draw```3가지 case만 존재하기 때문에 ```switch```문과 ```Compound Cases```를 활용하여 구현하는 방식으로 함수를 변경하였습니다.

- 또한 ```myChoice```, ```computerChoice```라는 변수명보다 손을 사용하는 게임에 맞는 변수명을 사용하기 위해 ```userHand```, ```computerHand```라고 수정하기도 하였습니다. 

```swift
//이전 코드
func judgeGameResult(myChoice: RPS, computerChoice: RPS) {
    if myChoice == computerChoice {
        printGameResult(gameResult: .draw)
        play()
    } else if myChoice == .scissors && computerChoice == .paper {
        printGameResult(gameResult: .win)
    } else if myChoice == .rock && computerChoice == .scissors {
        printGameResult(gameResult: .win)
    } else if myChoice == .paper && computerChoice == .rock {
        printGameResult(gameResult: .win)
    } else {
        printGameResult(gameResult: .lose)
    }
}

```
```swift
//수정한 코드 
func judgeRockScissorsPaperGameResult(comparing userHand: HandShape, and computerHand: HandShape?) {
    switch (userHand, computerHand) {
    case (.scissors, .paper), (.rock, .scissors), (.paper, .rock):
        print(GameResult.win.message)
        winner = Player.user
        startMukJjiBbaGame()
    case (.scissors, .rock), (.rock, .paper), (.paper, .scissors):
        print(GameResult.lose.message)
        winner = Player.computer
        startMukJjiBbaGame()
    case (.scissors, .scissors), (.rock, .rock), (.paper, .paper):
        print(GameResult.draw.message)
        startGame()
    default:
        print("에러 발생")
    }
}
```
<br>


# 참고 링크
* [swift 공식문서](https://www.swift.org/)
* [Initializer](https://developer.apple.com/documentation/swift/rawrepresentable/init(rawvalue:))
* [Enumarations](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html)
<br>

# 아쉬운 점
 1. 비슷한 기능을 가진 함수가 존재하는데, 이를 하나의 함수로 처리해주지 못한 부분
 2. 반복문이 아닌 재귀함수로 많은 부분을 처리해준 부분
