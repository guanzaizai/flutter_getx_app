# Flutter 应用开发规范 (基于 GetX + GetCLI)

## 1. 整体技术规范

### 1.1 语言与框架

- **语言**: Dart 2.12+ (支持 Null Safety)
- **框架**: Flutter 3.29.0+
- **状态管理**: GetX (`GetXController`, `GetBuilder`, `GetX`, `Obx`)
- **路由管理**: GetX (`Get.to`, `Get.off`, `Get.offAll`, `Get.back`)
- **依赖注入**: GetX (`Get.put`, `Get.find`, `Get.lazyPut`)
- **网络请求**: Dio
- **数据存储**: GetStorage

### 1.2 代码风格

- 遵循 Dart 官方代码风格指南。
- 使用 `dart format` 进行代码格式化。
- 避免使用 `dynamic` 类型，尽量明确类型。
- 变量、函数、类名等命名应具有描述性，遵循 camelCase 规范。
- 常量使用 `const` 或 `final` 关键字，并使用 SCREAMING_SNAKE_CASE 命名。
- 命名规范：
  - **类**: `PascalCase`
  - **变量/函数**: `camelCase`
  - **文件**: `snake_case.dart`
  - **常量**: `SCREAMING_SNAKE_CASE`

### 1.3 错误处理

- 统一的错误处理机制，例如使用 `try-catch` 捕获异步操作异常。
- 对于用户可见的错误，提供友好的提示信息。
- 记录关键错误日志，便于问题排查。

---

## 2. 架构规范 (GetX 风格 + GetCLI)

本项目采用 GetX 的响应式编程和依赖注入特性，推荐使用以下架构模式：

### 2.1 MVC/MVVM 变种 (GetX 风格)
- **View (视图)**: 负责 UI 渲染，只包含 UI 逻辑，不包含业务逻辑。通过 `GetBuilder`, `GetX`, `Obx` 监听 Controller 的状态变化。
- **Controller (控制器)**: 包含业务逻辑、状态管理和数据处理。通过 `Get.put` 或 `Get.lazyPut` 注入，并通过 `update()` 或 `Rx` 变量更新视图。
- **Service (服务层)**: 负责数据获取、持久化等操作，例如网络请求、数据库操作。Controller 通过依赖注入使用 Service。
- **Repository (数据仓库)**: (可选) 封装多个数据源（如网络、本地缓存），为 Controller 提供统一的数据接口。
- **Binding**: 管理依赖注入，将 Controller/Service 注入模块。

### 2.2 模块化

- 每个功能模块独立，包含 View/Controller/Binding/Service/Widgets 等。
- 模块之间通过 GetX 的依赖注入通信，避免直接引用。

---

## 3. 目录规范

```
lib/
├── main.dart                               # 应用入口
├── app/                                    # 应用核心配置、绑定、中间件等
│   ├── data/                               # 数据层
│   │   ├── models/                         # 数据模型 (Dart Class)
│   │   ├── providers/                      # 数据提供者 (网络请求、本地存储等)
│   │   │   ├── api_provider.dart           # 网络请求提供者
│   │   │   └── local_storage_provider.dart # 本地存储提供者
│   │   └── repositories/                   # 数据仓库 (可选)
│   │       └── user_repository.dart        # 用户数据仓库
│   ├── modules/                            # 业务模块 (按功能划分)
│   │   ├── home/                           # 首页模块
│   │   │   ├── views/                      # 页面视图
│   │   │   │   ├── home_view.dart
│   │   │   │   └── other_page_view.dart
│   │   │   ├── controllers/                # 页面控制器
│   │   │   │   ├── home_controller.dart
│   │   │   │   └── other_page_controller.dart
│   │   │   ├── bindings/                   # 依赖绑定
│   │   │   │   ├── home_binding.dart
│   │   │   │   └── other_page_binding.dart
│   │   │   ├── services/                   # 模块内服务 (可选)
│   │   │   │   └── home_service.dart
│   │   │   ├── widgets/                    # 模块内私有组件 (可选)
│   │   │   │   ├── home_item_widget.dart
│   │   │   │   └── custom_card_widget.dart
│   │   │   └── models/                     # 模块内数据模型 (可选) 
│   │   │   │   └── home_model.dart
│   │   ├── auth/                           # 认证模块
│   │   │   ├── views/                      # 页面视图
│   │   │   ├── controllers/                # 页面控制器
│   │   │   ├── bindings/                   # 依赖绑定
│   │   │   ├── services/                   # 模块内服务 (可选)
│   │   │   ├── widgets/                    # 模块内私有组件 (可选)
│   │   │   └── models/                     # 模块内数据模型 (可选)
│   │   └── ...
│   ├── routes/                             # 路由配置
│   │   ├── app_pages.dart
│   │   └── app_routes.dart
│   ├── bindings/                           # 全局绑定
│   │   └── initial_binding.dart            # 应用启动依赖注入
│   ├── services/                           # 全局服务
│   │   ├── api_service.dart
│   │   ├── storage_service.dart
│   │   └── permission_service.dart
│   ├── config/                             # 应用配置 (常量、主题、API 地址等)
│   │   ├── app_constants.dart              # 常量配置
│   │   ├── app_theme.dart                  # 主题配置
│   │   └── api_config.dart                 # API 地址/环境配置
├── shared/                                 # 共享资源 (跨模块通用)
│   ├── widgets/                            # 通用 UI 组件 (按钮、输入框等)
│   │   └── custom_button.dart
│   ├── utils/                              # 工具类 (日期格式化、校验等)
│   │   └── date_formatter.dart
│   ├── services/                           # 通用服务 (如权限管理、通知服务)
│   │   └── permission_service.dart
│   └── extensions/                         # Dart 扩展
│       └── string_extensions.dart
└── generated/                              # 自动生成文件 (如国际化、路由代码)
```

---

## 4. 通用组件、工具类、服务查阅和参考

### 4.1 通用 UI 组件
- **查阅**: 在 `lib/shared/widgets/` 目录下查找。例如，如果你需要一个自定义按钮，可以查看 `custom_button.dart`。
- **参考**:
  - 优先复用现有组件。
  - 如果现有组件不满足需求，可以对其进行扩展或创建新组件。
  - 新组件应具有良好的封装性、可配置性，并遵循 Flutter Widget 的最佳实践。

### 4.2 工具类
- **查阅**: 在 `lib/shared/utils/` 目录下查找。例如，日期格式化工具在 `date_formatter.dart`。
- **参考**:
  - 优先使用 `shared/utils` 中已有的工具函数。
  - 如果需要新的工具函数，请在 `shared/utils` 中创建新的文件或添加到现有文件中。
  - 工具函数应是纯函数，不依赖外部状态，易于测试。

### 4.3 通用服务
- **查阅**: 在 `lib/shared/services/` 目录下查找。例如，权限管理服务在 `permission_service.dart`。
- **参考**:
  - 通用服务应通过 GetX 的依赖注入进行管理和使用。
  - 确保服务的职责单一，便于维护和测试。

---

## 5. 功能开发流程

- **页面功能开发**：用 `get create page` → 自动生成 View/Controller/Binding。
- **全局服务**：用 `get create service` → 管理 API、缓存、用户状态。
- **数据封装**：
  - `get create model` → 定义数据对象
  - `get create provider` → 封装请求
  - `get create repo` → 聚合 Provider，提供给 Controller
- **UI 组件复用**：用 `get create widget` → 标准化小组件。 
  - 组件应具有良好的封装性、可配置性，并遵循 Flutter Widget 的最佳实践。
- **依赖注入管理**：用 `get create binding` → 页面或模块依赖注入自动化。

---

## 6. 状态管理 & 依赖注入

- **状态管理**：
  - 响应式：`Rx` + `Obx`/`GetX`
  - 简单更新：`GetBuilder` + `update()`
- **依赖注入**：
  - `Get.put()` → 立即注入
  - `Get.lazyPut()` → 懒加载
- **Binding**：
  - 页面或模块依赖绑定自动注入 Controller/Service
