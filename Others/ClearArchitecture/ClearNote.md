# Clear Architecture Note

- Presentation : Chịu trách nghiệm việc hiển thị thông tin cho người dùng
  - Gồm: 
    - UI (Activities & Fragments)
    - Presenters/ViewModels (gọi User case, mapper)
- Domain : Thể hiện các khái niệm về business logic của ứng dụng
  - Gồm:
    - Use case
    - Entities (Model)
    - Repository Interfaces
- Application (có thể Chung Domain layer) : Định nghĩa các công việc mà phần mềm được cho là thực hiện bằng cách phối hợp luồng dữ liệu từ và đến các domain model
  - Usecase gọi đến Data Layer
- Data : Chứa các domain model
  - Gồm:
    - Reponsitory Implementations
    - Data Source
    - Mapper (Network, Room to Model)

![](clear_architecture_contain.png)

## Data Flow

![](clear_architecture_data_flow.png)

1. UI gọi phương thức từ Presenter/ViewModel.
2. Presenter/ViewModel thực hiện Use case.
3. Use case thu thập dữ liệu từ User và gởi Repositories.
4. Each Repository trả về dữ liệu từ Data Source (Cached or Remote).
5. Data trả về UI để hiển thị

## Dependency Rule

![](clear_architecture_dependency.png)

## Reference

- <https://proandroiddev.com/clean-architecture-data-flow-dependency-rule-615ffdd79e29>
