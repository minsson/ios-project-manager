TODO, DOING, DONE으로 진척 상태를 관리할 수 있는 칸반 형식의 할 일 앱입니다.
SwiftUI를 처음 학습하여 진행한 프로젝트입니다.

# 1. 화면 구현 📱

### 1-1. 메인 화면
| Light Mode | Dark Mode|
|:-----------:|:----------:|
| <img src=https://i.imgur.com/JKLG5TQ.png width=100%> | <img src=https://i.imgur.com/DjdkVAO.png width=100%> |

### 1-2. Task 생성 화면
| Light Mode | Dark Mode|
|:-----------:|:----------:|
| <img src=https://i.imgur.com/TmJ4shC.png width=100%> | <img src=https://i.imgur.com/vM1DCbd.png width=100%> |

### 1-3. Task 수정 화면
| Light Mode | Dark Mode|
|:-----------:|:----------:|
| <img src=https://i.imgur.com/Mx93een.png width=100%> | <img src=https://i.imgur.com/rvq8bdz.png width=100%> |

### 1-4. Task 진행상태 변경 및 삭제
| Light Mode |
|:-----------:|
|![](https://i.imgur.com/kFPQc8k.gif)|

| Dark Mode |
|:-----------:|
|![](https://i.imgur.com/1oRNMZV.gif)|

# 2. 폴더 및 파일 구조 🌴

```
.
└── ProjectManager/
    ├── GoogleService-Info.plist
    ├── Resources/
    │   └── Assets.xcassets
    └── Sources/
        ├── ProjectManagerApp
        └── Common/
            ├── Enum/
            │   └── Status
            ├── Extensions/
            │   └── Date+Extensions
            ├── Models/
            │   ├── Task
            │   └── TasksDataSource
            ├── Views/
            │   ├── Common/
            │   │   ├── SquareButtonView
            │   │   └── AddingTaskButtonView
            │   ├── MainView/
            │   │   └── TaskDashboardView
            │   ├── TaskList/
            │   │   ├── TaskListHeaderView
            │   │   └── TaskListView
            │   ├── TaskCell/
            │   │   ├── TaskCellBackgroundView
            │   │   └── TaskCellView
            │   └── TaskCreating+TaskEditing/
            │       ├── Protocol/
            │       │   └── TaskWritableView
            │       ├── TaskCreatingView
            │       ├── TaskEditingView
            │       └── TaskDescriptionView
            └── Preview Content/
                └── Preview Assets
```



# 3. 고민한 점 🧩

## 3-1. MV vs MVVM, Model과 View의 역할

처음에는 MVVM 아키텍처를 사용하려고 했는데, 그린 추천으로 아래와 같은 글을 읽고 SwiftUI에서 굳이 ViewModel이 필요하지 않다는 점에 대해 공감했습니다.
- [Stop using MVVM for SwiftUI](https://developer.apple.com/forums/thread/699003)
- [SwiftUI에서 MVVM 사용을 멈춰야 하는가?](https://green1229.tistory.com/267)

그래서 일단은 ViewModel 없이 Model과 View로 구성해봤습니다. 서비스 로직과 비즈니스 로직이라는 용어에 대해 생각해볼 때, 쉽게 생각해보면 뷰에는 서비스 로직을 넣으면 될 것 같고, SOT 역할을 하는 모델에는 비즈니스 로직을 넣으면 될 거라고 생각했습니다. 그런데 막상 구현을 하는 과정에서 역할 분리에서 명확하게 이해되지 않는 부분이 어느 정도 있었습니다. 특히 `뷰에서 이런 걸 해도 괜찮을까?`라고 고민되는 지점이 있었습니다.


## 3-2. 데이터 전달(Environment Object vs Observed Object)

Environment Object를 처음 공부할 때, 이를 싱글톤과 비슷하다고 설명하는 글이 많았습니다. 그래서 우선 가급적 사용하지 않아야겠다는 생각이 들었습니다. 처음 싱글톤 패턴을 배우고 사용했을 때 그게 너무 편해 남발한 경험이 있고, 그로 인해 싱글톤 패턴의 여러 단점 중 하나인 테스트가 어려워지는 문제를 겪어본 적이 있기 때문입니다.

그런데 Environment Object에 대해 공부를 하다 보니, 마땅한 단점이 보이지 않았습니다. 

아래는 단점을 찾아보려고 했을 때 들었던 의문과, 그에 대한 셀프 반박입니다.
- Singleton에서의 단점과 같이, 어디에서나 쓰일 수 있다.
    - 뷰 안에서 @EnvironmentObject 프로퍼티 래퍼를 사용해 받아야 쓸 수 있으므로, 코드에서 갑자기 보이는 Singleton과는 차이가 있다는 생각이 들었습니다.
- 하나의 객체가 메모리에 생성되어, 사용하지 않는 뷰에서도 여전히 메모리를 점유하고 있다.
    - 객체의 크기가 상식적으로 몇 킬로바이트도 되지 않을 것 같습니다. 현 시대의 디바이스 스펙으로 걱정할 이유가 없다는 생각이 들었습니다.
- 테스트하기가 어렵다.
    - 아직 테스트를 구현해보지 않았지만, 이 부분은 검색해보니 문제될 게 없어보였습니다. (이건 직접 테스트를 진행하면서 다시 한번 알아보려고 합니다.)


## 3-3. 폴더 및 파일 구조
처음 SwiftUI로 프로젝트를 진행하다보니 파일 구조를 어떻게 해야할지 고민이 되었습니다. 일단은 Feature 단위로 묶는다고 생각하고 정리해봤는데 그린은 어떻게 생각하시는지 궁금합니다.

```
.
└── ProjectManager/
    ├── GoogleService-Info.plist
    ├── Resources/
    │   └── Assets.xcassets
    └── Sources/
        ├── ProjectManagerApp
        └── Common/
            ├── Enum/
            │   └── Status
            ├── Extensions/
            │   └── Date+Extensions
            ├── Models/
            │   ├── Task
            │   └── TasksDataSource
            ├── Views/
            │   ├── Common/
            │   │   ├── SquareButtonView
            │   │   └── AddingTaskButtonView
            │   ├── MainView/
            │   │   └── TaskDashboardView
            │   ├── TaskList/
            │   │   ├── TaskListHeaderView
            │   │   └── TaskListView
            │   ├── TaskCell/
            │   │   ├── TaskCellBackgroundView
            │   │   └── TaskCellView
            │   └── TaskCreating+TaskEditing/
            │       ├── Protocol/
            │       │   └── TaskWritableView
            │       ├── TaskCreatingView
            │       ├── TaskEditingView
            │       └── TaskDescriptionView
            └── Preview Content/
                └── Preview Assets
```



## 3-4. SSOT(Single Source of Truth)
프로젝트의 `Models` 그룹을 보면 `Task`와 `TasksDataSource`가 있습니다.
`Task`는 모델 객체 그 자체고, `TasksDataSource`는 `Single Source of Truth`의 역할을 하고 있습니다.


## 3-5. Task 데이터 관리
`TasksDataSource`를 보면, 아래와 같이 각 Task가 담길 어레이를 진행상태(status) 별로 분류하고 있습니다. 

```swift
final class TasksDataSource: ObservableObject {
    @Published var todoTasks: [Task] = []
    @Published var doingTasks: [Task] = []
    @Published var doneTasks: [Task] = []
}
```

그러다보니 모든 비즈니스 로직에서 `switch-case`를 사용해야 한다는 불편함이 있었습니다. 예를 들어, 아래의 메서드처럼요.

```swift
func transfer(selectedTask: Task, to newCategory: Status) {
        deleteOriginalTask(equivalentTo: selectedTask)
        
    switch newCategory {
    case .todo:
        let newTask = newTask(copiedFrom: selectedTask, withNewStatus: .todo)
        todoTasks.append(newTask)
    case .doing:
        let newTask = newTask(copiedFrom: selectedTask, withNewStatus: .doing)
        doingTasks.append(newTask)
    case .done:
        let newTask = newTask(copiedFrom: selectedTask, withNewStatus: .done)
        doneTasks.append(newTask)
    }
}
```
>처음에는 아래와 같은 코드를 작성했습니다. (TaskDashboardViewModel의 네이밍을 TasksDataSource로 수정하기 전의 코드입니다)
>```swift
>class TaskDashboardViewModel: ObservableObject {
>    
>    @Published var tasks: [Task] = []
>    
>    var todo: [Task] {
>        tasks.filter { $0.status == .todo }
>    }
>    
>    var doing: [Task] {
>        tasks.filter { $0.status == .doing }
>    }
>    
>    var done: [Task] {
>        tasks.filter { $0.status == .doing }
>    }
>}
>```
> 그러다가 아래 9b2246cf1ca45e3004d8e4a1ce749411f9fdb765 번 커밋의 커밋 메시지와 같은 이유로 수정해 현재 코드가 되었습니다.

> 커밋 메시지 (9b2246cf1ca45e3004d8e4a1ce749411f9fdb765)
>- "TaskDashboardViewModel"
>   - tasks라는 하나의 어레이 타입 변수로 관리하던 Task들을 세 개로 분리
>   - 기존의 연산프로퍼티를 이용해 status별로 TaskListView에서 띄우는 경우, List에서 보는 indexSet과 실제 tasks 변수의 index를 연결할 수 없어 선택한 차선
>       - 이 문제는 List에서 id를 조회하는 방식으로 해결 가능하다는 것을 이후에 알게 되었음
>       - 하지만 task들을 status 별로 따로 관리하는 구조가 더 직관적이고, 매번 연산프로퍼티의 filter를 사용할 일이 없어 효율적이라고 생각해 현재 수정방안을 유지하기로 함
>   - 각 어레이 안의 데이터의 경우 임시로 넣어놓은 데이터로 추후 삭제할 예정


## 3-6. View를 만드는 방법
처음에는 모든 뷰를 구조체로 만들어줘야 한다고 생각했습니다. 그런데 그 외에도 메서드나 연산 프로퍼티로 구현하는 방법도 있었습니다.

우선 커스텀 버튼 등 다양한 곳에서 사용할 수 있는 뷰는 구조체로 구현하는 게 좋을 것 같다고 생각했습니다.

그런데 특정 뷰 내부에서만 쓰일 뷰를 구현하면서 고민이 시작되었습니다.
- Nested struct로 구현
- 인스턴스 메서드로 구현 (저는 이 방법을 선택했습니다. 구현하기에 가장 간단하게 느껴졌기 때문입니다.)
- 연산 프로퍼티로 구현


## 3-7. Task 생성 뷰와 편집 뷰
Task를 생성하고 수정하기 위한 sheet를 구현할 때 이 부분에서 고민을 많이 했습니다. 처음에는 하나의 뷰에서 분기처리를 통해 구현하려고 했는데, 바인딩 프로퍼티 래퍼가 복잡해지고, 신규 Task인지 기존 Task의 수정인지에 따른 `수정` 버튼의 유무와 기능, 이를 추적하기 위한 프로퍼티 등등... 프로퍼티도, 이니셜라이저도, 분기처리도 모두 복잡해져 득보다 실이 많다는 생각이 들었습니다.

하지만 뷰의 전체적인 모양 자체는 동일해 중복된 코드가 일부 존재하는 게 적절하지 않다고 생각했고, `TaskWritableView`라는 프로토콜과 기본 구현을 통해 중복 코드를 없애보았습니다.


## 3-8. 마감기한 초과 판정 로직
TaskCellView에서 Task를 보여줄 때, 마감기한이 지난 경우 날짜를 빨간색으로 표시해주고 있습니다.

처음에는 단순히 `dueDate < Date.now`이면 빨갛게 표시하면 될 것 같다고 생각했는데, 다시 생각해보니 이런 대소 비교는 `TimeInterval`, 즉, `Double`을 통해 이루어지니, `마감일과 오늘이 같은 날짜라도 시간 차이에 의해 빨갛게 표시되는 문제`가 생길 수 있다고 생각했습니다.

자주 구현할 것 같은 기능이라 Foundation Framework에 관련 메서드가 있을 거라고 생각했는데, 여러 방면으로 검색해봐도 찾지 못했습니다.

대신 블로그 등 다른 분들이 유사한 케이스에서 구현하신 걸 보면 날짜를 컴포넌트처럼 일, 시, 분, 초 등으로 나누어 구현하는 경우가 많았습니다.

코드를 줄이기 위해 아래와 같이 작성해봤습니다.
>```swift
>Text(task.dueDate, formatter: Date.formatter)
>    .foregroundColor(isOver(task.dueDate) ? .red : .primary)
>```
>
>```swift
>private extension TaskCellView {
>    func isOver(_ dueDate: Date) -> Bool {
>        if dueDate < Date.now && !Calendar.current.isDateInToday(dueDate) {
>            return true
>        }
>        
>        return false
>    }
>}
>```
