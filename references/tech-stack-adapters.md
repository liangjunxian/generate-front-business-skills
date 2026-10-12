# Tech Stack Adapters — 技术栈指纹与适配库

> 双用途:
> 1. **给 generate-front-business-skills 自己用** —— 主 SKILL.md §2.2 的指纹矩阵的展开版,扫描时按本表逐项命中。
> 2. **给被生成的 skill 引用** —— 任何一个被生成的 skill,若需"按技术栈分支",
>    应把相关章节复制/链接到自己的 `references/tech-stack.md`。
>
> **铁律**:每种技术栈都按统一字段写,保证生成器读到任意一种都能产出可复用的 skill。

---

## 目录约定总表

| 主栈 | 入口文件 | 业务页 | 复用组件 | 业务逻辑层 | 网络/数据 | 工具 | 平台分支 |
|---|---|---|---|---|---|---|---|
| **uni-app** | `App.vue` + `main.{js,ts}` | `pages/<模块>/<页>.vue` | `components/<组件>.vue` | `common/`、`store/`、`composables/` | `api/`、`utils/request.js` | `utils/` | `manifest.json` + `pages.json` 路由 + `uni.scss` 全局样式 |
| **Vue** | `src/main.{js,ts}` + `src/App.vue` | `src/views/<模块>/<页>.vue` | `src/components/<组件>.vue` | `src/store/`、`src/composables/` | `src/api/`、`src/services/` | `src/utils/` | `vue.config.js` / `vite.config.ts` |
| **React** | `src/main.{js,ts,tsx}` + `src/App.{tsx,jsx}` | `src/pages/<模块>/<页>.{tsx,jsx}` | `src/components/<组件>.{tsx,jsx}` | `src/store/`、`src/hooks/` | `src/api/`、`src/services/` | `src/utils/`、`src/lib/` | `vite.config.ts` / `next.config.js` |
| **React Native** | `index.js` + `App.{tsx,jsx}` | `src/screens/<屏>.tsx` | `src/components/<组件>.tsx` | `src/services/`、`src/hooks/` | `src/api/` | `src/utils/` | `metro.config.js` + `android/` + `ios/` |
| **Android 原生** | `app/src/main/java/<pkg>/MainActivity.kt` | `app/src/main/java/<pkg>/ui/<模块>/<页>Activity.kt` | `app/src/main/res/layout/*.xml` | `app/src/main/java/<pkg>/{domain,data,repository}/` | `app/src/main/java/<pkg>/data/api/` | `app/src/main/java/<pkg>/util/` | `app/build.gradle` flavors + `AndroidManifest.xml` |
| **iOS 原生** | `<App>.swift`(SceneDelegate) | `Sources/<模块>/<屏>.swift` | `Sources/Common/<组件>.swift` | `Sources/Domain/`、`Sources/Data/` | `Sources/Networking/` | `Sources/Utils/` | `*.xcodeproj` targets + `Podfile` |
| **Flutter** | `lib/main.dart` | `lib/screens/<屏>.dart` | `lib/widgets/<组件>.dart` | `lib/{models,services,providers}/` | `lib/api/` | `lib/utils/` | `pubspec.yaml` |
| **原生微信小程序** | `app.{js,ts}` + `app.json` | `pages/<页>/{页.js,页.wxml,页.wxss,页.json}` | `components/<组件>.{js,wxml,wxss,json}` | `utils/`、`services/` | `utils/request.js` | `utils/` | `project.config.json` + `sitemap.json` |
| **原生鸿蒙** | `entry/src/main/ets/entryability/EntryAbility.ets` | `entry/src/main/ets/pages/<页>.ets` | `entry/src/main/ets/components/<组件>.ets` | `entry/src/main/ets/{model,viewmodel}/` | `entry/src/main/ets/common/` | `entry/src/main/ets/utils/` | `build-profile.json5` + `oh-package.json5` |
| **Taro** | `app.config.ts` 或 `app.config.js` + `src/app.{tsx,jsx,vue}` | `src/pages/<模块>/<页>.{tsx,vue}` | `src/components/<组件>.{tsx,vue}` | `src/store/` | `src/api/`、`src/services/` | `src/utils/`、`src/constants/` | `project.config.json`(微信)+ `babel.config.js` + `taros` 配置 |
| **Electron** | `electron/main.{js,ts}` 或 `src/main/index.{js,ts}` + `electron-builder.yml` 或 `forge.config.*` | `src/renderer/pages/<页>.{tsx,vue}` | `src/renderer/components/<组件>.{tsx,vue}` | `src/main/services/`、`src/main/ipc/` | `src/main/api/` | `src/shared/`、`src/utils/` | `electron-builder` + `forge` + 平台 targets(win/mac/linux) |
| **Tauri** | `src-tauri/src/main.rs` + `src-tauri/tauri.conf.json` + `src/main.{tsx,vue}`(前端) | `src/pages/<页>.{tsx,vue}`(前端) | `src/components/<组件>.{tsx,vue}`(前端) | `src-tauri/src/{commands,domain}/`(Rust) | `src-tauri/src/api/`(Rust) + 前端 `src/api/` | `src/utils/`、`src-tauri/src/utils/` | 前端 Vite + Rust Cargo + `tauri.conf.json` + capabilities |

---

## 1. uni-app

### 1.1 识别指纹(全部命中即为主栈)

```
✓ manifest.json                  ← uni-app 工程根标识
✓ pages.json                      ← 路由表
✓ App.vue
✓ main.js 或 main.ts
```

辅证:`uni.scss` 全局样式、`uni_modules/`(插件)、`unpackage/`(HBuilder 编译产物)。

### 1.2 关键配置

- **目标平台**:`manifest.json` 的 `appid` / `mp-weixin` / `h5` / `app-plus` 块
- **路由**:`pages.json` 的 `pages` 数组
- **tabBar**:`pages.json` 的 `tabBar` 块
- **分包**:`manifest.json` 的 `mp-weixin.subPackages`

### 1.3 目录约定(默认,HBuilder 创建的标准结构)

```
project/
├── pages/                ← 页面,每个子目录一个完整页(.vue + .json)
│   └── index/index.vue
├── components/           ← 自定义组件
├── static/               ← 静态资源(不进编译)
├── common/               ← 公共业务(图片、字典、混入 mixin.js)
├── store/                ← Vuex / Pinia 状态
├── api/                  ← 接口请求(按模块拆文件)
├── utils/
│   ├── request.js        ← uni.request 封装
│   └── ...
├── uni_modules/          ← uni-ui / 第三方插件
├── App.vue
├── main.js
├── manifest.json
├── pages.json
└── uni.scss
```

### 1.4 命名约定

- 页面目录:小写 + 中划线(`user-center/`)
- 组件文件:大驼峰(`UserCard.vue`)
- API 文件:按模块(`api/user.js`、`api/order.js`)
- 状态模块:大驼峰(`store/modules/user.js`)

### 1.5 代码落点决策树

```
可复用业务逻辑(state / filter / submit 组装 / 字典)?
├─ 是 → store/(Vuex/Pinia) 或 common/ 业务模块
└─ 否(纯 UI/布局) → components/ 或 pages/ 内

是否被 ≥2 个页面使用?
├─ 是 → 抽 components/ 或 utils/
└─ 否 → 写在当前页面

跨页面共享数据?
└─ 是 → store/(必走 Vuex/Pinia,不要塞 globalData)
```

### 1.6 TDD 路径与命令

- **测试目录**:`uni-app` 自身不带测试框架,通常引入 `jest` / `vitest` + `@dcloudio/uni-app` 适配
- **本地测试根**:`local-tests/`,镜像 `pages/`、`components/`、`store/`、`api/` 结构
- **典型命令**:
  ```
  npm run test:unit            # 跑单元测试
  npm run dev:h5               # H5
  npm run dev:mp-weixin        # 微信小程序
  ```

### 1.7 Lint 命令

- `npx eslint pages/ components/ store/ api/ utils/ --ext .js,.ts,.vue`
- 或项目内 `npm run lint`

### 1.8 平台分支(此为跨端栈关键)

每个工作流动作都要问:**这次跑的是哪个平台?**

| 平台 | 构建命令 | 产物 | 调试 |
|---|---|---|---|
| H5 | `npm run dev:h5` | `dist/build/h5/` | 浏览器 |
| 微信小程序 | `npm run dev:mp-weixin` | `dist/build/mp-weixin/` | 微信开发者工具打开 |
| 支付宝小程序 | `npm run dev:mp-alipay` | `dist/build/mp-alipay/` | 支付宝 IDE |
| Android App | `npm run build:app-plus` + HBuilder 云打包 | apk | 真机 |
| iOS App | `npm run build:app-plus` + HBuilder 云打包 | ipa | TestFlight |

### 1.9 常见反模式

- ❌ 把接口请求写在页面 `.vue` 的 `methods` 里,不复用
- ❌ 用 `globalData` 替代 `store/`(全局状态必须走 Vuex/Pinia)
- ❌ 在 H5 上能跑就以为小程序也能跑(注意 `uni.*` API 兼容性)
- ❌ 直接 `setStorage` 存业务关键数据,不封装

---

## 2. Vue 2 / Vue 3

### 2.1 识别指纹

```
✓ package.json 依赖 vue(2.x 或 3.x)
✓ src/main.js 或 src/main.ts
✓ src/App.vue
✓ vite.config.ts / vue.config.js / nuxt.config.ts(其一)
```

辅证:`@vue/cli-service` 或 `@vue/compiler-sfc` 在 `devDependencies`。

### 2.2 目录约定

```
src/
├── api/                  ← 接口
├── assets/               ← 图片、字体
├── components/           ← 公共组件
│   └── common/           ← 纯展示组件
│   └── business/         ← 业务组件
├── composables/          ← Vue3 组合式函数(类似 hooks)
├── directives/           ← 自定义指令
├── layouts/              ← 布局
├── router/               ← 路由
├── store/                ← Pinia 或 Vuex
├── styles/               ← 全局样式
├── utils/                ← 纯工具
├── views/                ← 页面级组件
│   └── <模块>/<页>.vue
├── App.vue
└── main.{js,ts}
```

### 2.3 命名约定

- 页面:`views/<模块>/<页名>.vue`(`UserProfile.vue`)
- 组件:`components/business/<模块>/<组件>.vue`
- composable:`composables/use<X>.ts`(`useTable.ts`)
- store 模块:`store/modules/<模块>.ts`

### 2.4 代码落点决策树

```
可复用业务逻辑?
├─ 是 → composables/use<X>.ts(组合式)或 store/<模块>.ts
└─ 否 → 写当前页面

被 ≥2 处共用?
├─ 是 → 抽 composables/ 或 utils/
└─ 否 → 内联

纯函数、无副作用、无状态?
└─ 是 → utils/<name>.ts + TDD
```

### 2.5 TDD 路径与命令

- 测试框架:Vitest / Jest
- 本地测试根:`local-tests/`,镜像 `src/` 结构
- 命令:
  ```
  npm run test:unit          # 跑 vitest/jest
  npm run test:watch         # 监听
  npm run typecheck          # vue-tsc / tsc --noEmit
  ```

### 2.6 Lint 命令

- `npm run lint`(通常是 `eslint . --ext .vue,.js,.ts`)
- `npm run lint:style`(stylelint)

### 2.7 平台分支

通常**单一平台**(H5)。若同时支持 SSR:

- CSR:`npm run dev` + `npm run build`
- SSR(Nuxt):`npm run dev`(开发) + `npm run build`(产物分 client / server)

### 2.8 常见反模式

- ❌ 在 `.vue` 的 `setup` 里写可复用逻辑(不抽 composable)
- ❌ 一个 `utils.ts` 几千行不分类
- ❌ Pinia store 里直接调接口(应放 `api/`,store 只管理状态)

---

## 3. React

### 3.1 识别指纹

```
✓ package.json 依赖 react(17+ / 18+ / 19)
✓ src/main.{js,ts,tsx} 或 src/index.{js,ts,tsx}
✓ src/App.{tsx,jsx}
✓ vite.config.ts / next.config.js / craco.config.js(其一)
```

辅证:`react-dom`、`react-router-dom`、`@reduxjs/toolkit` 或 `zustand`。

### 3.2 目录约定

```
src/
├── api/                  ← 接口
├── assets/               ← 静态
├── components/           ← 公共组件
│   └── common/           ← 纯展示
│   └── business/         ← 业务组件
├── contexts/             ← React Context
├── hooks/                ← 自定义 hooks(use<X>.ts)
├── layouts/              ← 布局
├── pages/ 或 views/      ← 页面
│   └── <模块>/<页>.tsx
├── router/               ← 路由配置
├── services/             ← 业务服务层(可注入)
├── store/                ← Redux/Zustand/Jotai
├── utils/                ← 纯工具
├── App.{tsx,jsx}
└── main.{ts,tsx}
```

### 3.3 命名约定

- 页面:`pages/<模块>/<页>.tsx`
- 组件:`components/business/<组件>.tsx`
- hook:`hooks/use<X>.ts`(`useTable.ts`、`useUserAuth.ts`)
- service:`services/<领域>Service.ts`(`userService.ts`)

### 3.4 代码落点决策树

```
可复用业务逻辑?
├─ 是 → hooks/use<X>.ts(纯 hook) 或 services/<领域>.ts(含副作用)
└─ 否 → 当前组件

被 ≥2 处共用?
├─ 是 → 抽 hooks/ 或 utils/
└─ 否 → 内联

跨组件状态?
├─ 局部 → useState / useReducer
├─ 跨树 → Context
└─ 复杂全局 → Redux/Zustand store
```

### 3.5 TDD 路径与命令

- 测试框架:Vitest / Jest + @testing-library/react
- 本地测试根:`local-tests/`,镜像 `src/`
- 命令:
  ```
  npm run test              # CI 模式
  npm run test:watch        # 监听
  npm run typecheck         # tsc --noEmit
  ```

### 3.6 Lint 命令

- `npm run lint`(eslint + react/recommended 规则集)
- `npm run format`(prettier)

### 3.7 常见反模式

- ❌ 业务逻辑写在 JSX 里(.tsx 文件臃肿到几百行)
- ❌ `useEffect` 滥用(同步状态用 `useMemo` / `useState` 派生)
- ❌ 一个 `index.ts` 导出几百个东西

---

## 4. React Native

### 4.1 识别指纹

```
✓ package.json 依赖 react-native
✓ metro.config.js
✓ ios/(Xcode 工程)
✓ android/(Gradle 工程)
✓ index.js
```

### 4.2 目录约定

```
src/
├── screens/              ← 屏幕级页面
├── components/           ← 复用组件
├── navigation/           ← React Navigation 配置
├── services/             ← 业务服务
├── hooks/                ← 自定义 hooks
├── api/                  ← 网络
├── utils/
├── store/                ← Redux/Zustand
└── App.tsx
```

### 4.3 代码落点决策树

- 与 React 一致,但**所有平台分支**(Android / iOS)必须显式处理:
  ```
  Platform.OS === 'ios' ? <IosView/> : <AndroidView/>
  ```
- 原生模块调用 → `android/app/src/main/java/<pkg>/` + `ios/<App>/`

### 4.4 平台分支

- Android:Gradle 构建,`./gradlew assembleRelease`
- iOS:Xcode 构建,`xcodebuild -workspace ios/<App>.xcworkspace`

### 4.5 常见反模式

- ❌ 忽略 `Platform.select` 导致样式不一致
- ❌ 直接在 JS 线程做重 CPU 计算(应放 native module)

---

## 5. Android 原生(Java / Kotlin)

### 5.1 识别指纹

```
✓ build.gradle 或 build.gradle.kts
✓ settings.gradle 或 settings.gradle.kts
✓ app/src/main/AndroidManifest.xml
✓ app/src/main/java/**/MainActivity.kt(或 .java)
✓ gradle/wrapper/gradle-wrapper.properties
```

辅证:`buildToolsVersion`、`kotlinVersion`(若 Kotlin)。

### 5.2 目录约定(标准 MVP / MVVM)

```
app/src/main/
├── AndroidManifest.xml
├── java/<package>/
│   ├── ui/
│   │   ├── <模块>/
│   │   │   ├── <页>Activity.kt
│   │   │   └── <页>Fragment.kt
│   │   └── adapter/
│   ├── data/
│   │   ├── api/                ← Retrofit Service
│   │   ├── repository/         ← 数据仓库
│   │   └── model/              ← 数据类
│   ├── domain/                 ← 业务用例(可选)
│   ├── di/                     ← Hilt/Dagger 模块
│   ├── util/                   ← 工具
│   └── base/                   ← BaseActivity/BaseFragment
└── res/
    ├── layout/                 ← XML 布局
    ├── values/                 ← strings/colors/styles
    └── drawable/
```

### 5.3 命名约定

- Activity:`<页名>Activity`(`UserProfileActivity.kt`)
- Fragment:`<页名>Fragment`(`OrderListFragment.kt`)
- Adapter:`<列表名>Adapter`(`OrderListAdapter.kt`)
- 数据类:`<实体>`(`UserResponse.kt`、`OrderItem.kt`)
- 布局 XML:`activity_<页>` / `fragment_<页>` / `item_<列表项>`

### 5.4 代码落点决策树

```
可复用业务逻辑?
├─ 是 → domain/(UseCase) 或 repository/
└─ 否 → 当前 Activity/Fragment

数据来源单一但 UI 多?
├─ 是 → ViewModel + LiveData/StateFlow
└─ 否 → 直接在 Activity 调 repository(不推荐)

网络层?
└─ Retrofit Service 接口 + OkHttp 拦截器 + Repository 封装
```

### 5.5 TDD 路径与命令

- 测试框架:JUnit4/5 + MockK / Mockito + Robolectric(单元) / Espresso(UI)
- 本地测试根:`app/src/test/`(单元)+ `app/src/androidTest/`(UI)
- 命令:
  ```
  ./gradlew test                # 单元测试
  ./gradlew connectedAndroidTest # UI 测试
  ./gradlew assembleDebug       # 构建
  ```

### 5.6 Lint 命令

- `./gradlew lint` 或 `./gradlew lintDebug`
- `ktlint`(若项目配置)

### 5.7 平台分支

- **构建变体**(buildTypes / flavors):
  ```
  ./gradlew assembleDebug       # debug
  ./gradlew assembleRelease     # release
  ./gradlew assembleStagingDebug # 自定义 flavor
  ```
- **ABI 分支**:`armeabi-v7a` / `arm64-v8a` / `x86` —— 影响包体积

### 5.8 常见反模式

- ❌ Activity 里直接 new Thread(应换 Coroutine / RxJava)
- ❌ Adapter 不复用 ViewHolder / 不用 ListAdapter + DiffUtil
- ❌ 网络请求在主线程(应 IO 调度)
- ❌ 把布局逻辑写在 Activity 里几百行(应抽 XML + ViewModel)

---

## 6. iOS 原生(Swift / Objective-C)

### 6.1 识别指纹

```
✓ *.xcodeproj 或 *.xcworkspace
✓ Podfile 或 Package.swift
✓ <App>App.swift / AppDelegate.swift
```

### 6.2 目录约定(SwiftPM + Tuist 或纯 Xcode)

```
<App>/
├── Sources/
│   ├── App/                  ← AppDelegate、SceneDelegate
│   ├── Features/<模块>/
│   │   ├── <屏>.swift
│   │   └── <屏>ViewModel.swift
│   ├── Common/               ← 复用组件
│   ├── Domain/               ← Models / UseCases
│   ├── Networking/           ← API Client
│   └── Utils/
├── Resources/
└── Tests/
    ├── UnitTests/
    └── UITests/
```

### 6.3 代码落点决策树

- 与 Android 类似,但 Swift 特性更强:优先 protocol-oriented,组合优于继承
- 视图:**SwiftUI**(新项目) / **UIKit**(老项目) —— 看 `Sources/App/<App>App.swift` 是否含 `@main`

### 6.4 TDD 路径与命令

- XCTest / Quick + Nimble
- 命令:
  ```
  xcodebuild test -workspace <App>.xcworkspace -scheme <App>
  swift test                   # SPM
  ```

### 6.5 Lint 命令

- SwiftLint:`swiftlint`
- SwiftFormat:`swiftformat Sources/`

### 6.6 常见反模式

- ❌ 强制解包 `!` 滥用
- ❌ 巨大 `ViewController`
- ❌ 闭包循环引用(忘 `[weak self]`)

---

## 7. Flutter(Dart)

### 7.1 识别指纹

```
✓ pubspec.yaml
✓ lib/main.dart
✓ android/ + ios/(存在即跨端)
```

### 7.2 目录约定

```
lib/
├── main.dart
├── models/                ← 数据类
├── services/              ← 业务服务
├── providers/             ← Provider / Riverpod
├── screens/               ← 页面
│   └── <模块>/<页>.dart
├── widgets/               ← 复用组件
├── api/                   ← Dio/Http 封装
└── utils/
test/
└── mirror lib/
```

### 7.3 代码落点决策树

```
可复用逻辑?
├─ 纯 Dart 函数 → utils/<name>.dart + TDD
├─ 含状态 → providers/<name>.dart(Provider/Riverpod/Bloc)
└─ UI 渲染 → widgets/ 或 screens/

跨平台差异?
├─ 是 → Platform.isIOS / Platform.isAndroid 分支
└─ 否 → 公共代码
```

### 7.4 TDD 路径与命令

- `test/<name>_test.dart`
- `flutter test` / `flutter test --watch`

### 7.5 Lint 命令

- `flutter analyze`(内置)
- `dart fix --apply`

---

## 8. 原生微信小程序

### 8.1 识别指纹

```
✓ app.json
✓ app.{js,ts}
✓ project.config.json
✓ sitemap.json(可选)
```

### 8.2 目录约定

```
project/
├── pages/
│   └── <页>/
│       ├── <页>.js
│       ├── <页>.wxml
│       ├── <页>.wxss
│       └── <页>.json
├── components/
├── utils/
├── images/
├── app.js
├── app.json
├── app.wxss
├── project.config.json
└── sitemap.json
```

### 8.3 命名约定

- 页面:`pages/<页>/<页>.{js,wxml,wxss,json}`(文件名一致)
- 组件:`components/<组件>.{js,wxml,wxss,json}`
- 工具:`utils/request.js`、`utils/auth.js`

### 8.4 代码落点决策树

```
业务逻辑?
├─ 可复用 → utils/<模块>.js
└─ 页面级 → pages/<页>/<页>.js

跨页面状态?
└─ app.js 的 globalData(慎用)或 mobx / EventBus

网络请求?
└─ utils/request.js 封装 wx.request
```

### 8.5 TDD 路径与命令

- `miniprogram-automator`(E2E)+ `jest`(单元)
- 命令:开发者工具 → 构建 npm + 自动化测试

### 8.6 Lint 命令

- `eslint . --ext .js,.ts,.wxml`
- 微信开发者工具自带代码质量检查

### 8.7 常见反模式

- ❌ `setData` 滥用(每次 setData 触发渲染)
- ❌ 一个 page.js 几百行不拆
- ❌ 业务逻辑写在 wxml(应 wx:if + 数据驱动)

---

## 9. 原生鸿蒙(ArkTS / ArkUI)

### 9.1 识别指纹

```
✓ AppScope/app.json5
✓ entry/src/main/ets/entryability/EntryAbility.ets
✓ entry/oh-package.json5
✓ build-profile.json5
```

### 9.2 目录约定

```
entry/src/main/ets/
├── entryability/
│   └── EntryAbility.ets
├── pages/
│   └── Index.ets
├── components/
├── model/
├── viewmodel/
├── common/
└── utils/
```

### 9.3 代码落点决策树

- 与 Vue/React 类似:可复用逻辑 → `viewmodel/` 或 `utils/`,UI → `pages/` 或 `components/`
- **跨设备分支**:`phone` / `tablet` / `2in1` / `tv` —— 通过资源限定符(`resources/base/profile/`、`resources/phone/`)分支

### 9.4 TDD 路径与命令

- 单元测试:`ohpm test`(具体命令视 hvigor 配置)
- 命令:`hvigorw assembleHap` / `hvigorw test`

### 9.5 Lint 命令

- hvigor 自带 lint

### 9.6 常见反模式

- ❌ `@State` 滥用(应用 `@Provide` / `@Consume` 跨组件共享)
- ❌ UI 与业务不分层(应 ViewModel 隔离)

---

## 10. 跨端栈的平台分支策略(给被生成的 skill 用)

被生成的 skill 处理跨端栈(uni-app / RN / Flutter / 鸿蒙)时,**每个工作流动作都要标平台**。

### 推荐结构

```markdown
## 工作流

### 阶段 X:跑测试
- **H5 / 通用**:`npm run test:unit`
- **微信小程序**:开发者工具 → 构建 npm → 运行自动化测试
- **Android App**:`./gradlew test`(原生侧)+ Metro bundle 重载(JS 侧)
- **iOS App**:Xcode Test + Metro bundle 重载
```

### 三种分支粒度(对应主 SKILL.md §3.1+ Q6.5)

1. **统一一套 + if 分支**:流程紧凑,差异点塞条件分支 —— 适合动作差异小
2. **每平台独立小节**:流程长但清晰 —— 适合动作差异大
3. **仅差异点单独说明**(推荐):公共流程写一次,差异点列子表 —— 平衡清晰与紧凑

---

## 11. 通用代码落点决策树(跨栈)

> 所有栈都适用的元规则。

```
可复用业务逻辑?(含修 bug)
├─ 是 → 业务层(store / service / repository / composable / hook)
│        ⚠ 禁止只在视图层打补丁
└─ 否 → 纯 UI / 布局 → 视图层(components / pages / screens)

被 ≥2 处共用?
├─ 是 → 抽 utils/(或对应栈的工具层)
└─ 否 → 内联在当前文件

含状态但可注入依赖?
├─ 是 → hook / composable / viewmodel + TDD
└─ 否 → 纯函数 utils + TDD

跨包改动?
└─ 必先列出改动点 + 影响面 + 风险 → 用户确认
```

---

## 12. TDD 缺失时的安装建议(按主栈)

> 配合主 SKILL.md §2.4 使用。扫描判定"未具备 TDD 环境"时,
> 生成器必须**告知** + 给出本节对应命令 + 询问用户(选项 A/B/C)。
> **不得**在用户未确认前擅自安装。

### 12.1 检测依据速查

```
已具备 TDD 环境(任一命中即视为具备):
├─ package.json 的 devDependencies 含:vitest / jest / mocha / jasmine / @vue/test-utils / @testing-library/*
├─ 工程内存在:tests/、__tests__/、*.test.*、*.spec.*、test/ 目录(Flutter)
├─ Android:app/src/test/ 目录存在,或 build.gradle 含 junit/mockito/mockk/robolectric/espresso
├─ iOS:工程内含 *Tests.swift / *Spec.swift / Package.swift 有 test target
├─ Flutter:pubspec.yaml 含 test:/flutter_test:
├─ 鸿蒙:entry/src/test/ 目录存在
└─ 小程序:含 miniprogram-automator 依赖
```

### 12.2 安装命令(可直接复制)

| 主栈 | 推荐安装命令 | 配套配置 |
|---|---|---|
| **uni-app** | `npm i -D vitest @dcloudio/uni-app @vue/test-utils jsdom` | `vitest.config.ts` 配置 `environment: 'jsdom'` + `@dcloudio/uni-app/preset` |
| **Vue 2** | `npm i -D jest @vue/test-utils vue-jest@5 jest-serializer-vue` | jest 配置 `transform` 用 `vue-jest` |
| **Vue 3** | `npm i -D vitest @vue/test-utils jsdom` | `vitest.config.ts` + `environment: 'jsdom'` |
| **React** | `npm i -D vitest @testing-library/react jsdom @testing-library/jest-dom` | `vitest.config.ts` + `environment: 'jsdom'` + `setupFiles: ['./test/setup.ts']` |
| **React Native** | `npm i -D jest @testing-library/react-native react-test-renderer` | `jest.config.js` + `preset: 'react-native'` |
| **Android(Java)** | 在 `app/build.gradle` 添加:<br>`testImplementation 'junit:junit:4.13.2'`<br>`testImplementation 'org.mockito:mockito-core:5.7.0'` | 无需额外配置,IDE 会识别 |
| **Android(Kotlin)** | 在 `app/build.gradle` 添加:<br>`testImplementation 'junit:junit:4.13.2'`<br>`testImplementation 'io.mockk:mockk:1.13.8'`<br>`testImplementation 'org.jetbrains.kotlinx:kotlinx-coroutines-test:1.7.3'` | 无需额外配置 |
| **iOS(Swift)** | Xcode → File → New → Target → Unit Testing Bundle;或 SPM:<br>`Package.swift` 加 `.testTarget(name: "AppTests", dependencies: ["App"])` | 生成 `<App>Tests.swift` |
| **iOS(Swift + Quick)** | `Podfile` 加 `target '<App>' do; pod 'Quick'; pod 'Nimble'; end`,然后 `pod install` | 写 `class <Name>Spec: QuickSpec` |
| **Flutter** | `flutter pub add --dev test flutter_test mockito`(`test` 通常随 `flutter create` 已带) | 无需额外配置 |
| **原生微信小程序** | `npm i -D miniprogram-automator jest`<br>`npm i -D @types/miniprogram-automator`(可选) | jest.config.js + automator 启微信开发者工具 |
| **原生鸿蒙** | `entry/oh-package.json5` 加:`"@ohos/hypium": "1.0.0"`、`"@ohos/hvigor-ohos-plugin": "^5.0.0"`(测试增强) | hvigorw test 跑测 |

### 12.3 三种处理方式对应的 SKILL.md 标注

用户在 §2.4.2 选择不同处理方式时,生成的 SKILL.md 必须有对应的明确标注:

#### (A) 现在安装并继续

→ 不需要在 SKILL.md 里加额外标注,5 步闸门第 3 步按正常 "TDD 红绿" 走。

#### (B) 跳过 TDD,降级为"最小自检"

→ 必须在 SKILL.md 里加:

```markdown
## TDD 环境状态

> 当前工程**未启用 TDD**。本 skill 第 3 步闸门降级为"最小自检(可选)":
> - 不写正式测试
> - 但仍跑一次"输入 → 输出"的手工/脚本核对,留运行结果
> - 用户后续补装测试框架后,可恢复严格 TDD
```

#### (C) 延后处理

→ 必须在 SKILL.md 里加:

```markdown
## TDD 环境状态

> ⚠ **本 skill 第 3 步闸门暂不可用**。
> 工程当前未具备 TDD 环境;需先安装测试框架(命令:`<按主栈的具体命令>`),
> 安装完成后再使用本 skill 的 TDD 红绿步骤。
> 在此之前,第 3 步请手动跳过或仅做"最小自检"。
```

### 12.4 不建议的 fallback

- ❌ 用 console.log / debugger 当"测试"(不可验证、不可重跑)
- ❌ 用生产代码里的 if (window.__TEST__) 分支绕过逻辑
- ❌ 把"测试"外包给第三方 QA(这不是开发自检的替代)

---

## 13. Taro(类 uni-app 跨端框架)

### 13.1 识别指纹

```
✓ package.json 依赖 @tarojs/taro + @tarojs/cli
✓ project.config.json(微信)+ babel.config.js + tsconfig.json
✓ src/app.{tsx,jsx,vue} 或 src/app.config.{ts,js}
```

辅证:`@tarojs/runtime`、`@tarojs/webpack-runner`、`@tarojs/plugin-framework-*`。

### 13.2 关键配置

- **目标平台**:通过 Taro CLI 命令选择,常见组合:`--type weapp` / `h5` / `rn` / `swan` / `alipay` / `tt` / `qq` / `jd`
- **框架选择**:Taro 3 支持 React / Vue2 / Vue3;看 `package.json` 是否含 `react` 或 `vue`
- **路由**:`app.config.ts` 的 `pages` 数组
- **tabBar**:`app.config.ts` 的 `tabBar`

### 13.3 目录约定

```
project/
├── config/               ← Taro 构建配置
│   ├── index.ts
│   ├── dev.ts
│   └── prod.ts
├── src/
│   ├── pages/<模块>/<页>.{tsx,vue}
│   ├── components/<组件>.{tsx,vue}
│   ├── store/<模块>.ts
│   ├── api/<模块>.ts
│   ├── utils/
│   ├── assets/
│   ├── constants/
│   └── app.{tsx,vue}      ← 入口
├── app.config.ts         ← 路由/tabBar
├── babel.config.js
├── project.config.json   ← 微信小程序原生配置
└── tsconfig.json
```

### 13.4 命名约定

- 页面:`pages/<页>.tsx`,文件名小驼峰
- 组件:`components/<组件>.tsx`,大驼峰
- store 模块:大驼峰,`store/modules/user.ts`

### 13.5 代码落点决策树

与 uni-app 类似,但**核心 API 是 React Hooks**(`useState`、`useEffect`、`useRequest`)或 **Vue 组合式 API**,而不是 `uni.*`。

```
可复用业务逻辑?
├─ 是 → hooks/use<X>.ts(React 系)或 composables/use<X>.ts(Vue 系)
├─ 状态管理 → store/modules/<模块>.ts(Redux / Mobx / Zustand / Pinia)
└─ 否(纯 UI)→ components/

跨页面共享?
└─ 是 → store/(必须,不要塞 globalData)
```

### 13.6 TDD 路径与命令

- **测试框架**:Vitest / Jest + `@testing-library/react`(React 系)或 `@vue/test-utils`(Vue 系)
- **本地测试根**:`local-tests/`,镜像 `src/`
- **命令**:
  ```
  npm run test:unit             # 跑单元测试
  npm run build:weapp           # 构建微信小程序
  npm run build:h5              # 构建 H5
  ```

### 13.7 Lint 命令

- `npm run lint`(通常是 `@tarojs/eslint-config` 或自配 eslint)
- 配置入口:`.eslintrc.js` 或 `eslint.config.js`

### 13.8 平台分支

| 平台 | 构建命令 | 产物 | 调试 |
|---|---|---|---|
| 微信小程序 | `npm run build:weapp` | `dist/weapp/` | 微信开发者工具 |
| H5 | `npm run build:h5` | `dist/h5/` | 浏览器 |
| RN | `npm run build:rn -- --type rn` | `dist/rn/` | Metro bundler |
| 支付宝小程序 | `npm run build:alipay` | `dist/alipay/` | 支付宝 IDE |
| 抖音小程序 | `npm run build:tt` | `dist/tt/` | 抖音开发者工具 |

### 13.9 常见反模式

- ❌ 直接用 `wx.*` API(应换 `Taro.*`)
- ❌ 把 Taro 当 RN 用(忽略平台差异)
- ❌ 配置 `app.config.ts` 的 `pages` 写错路径(文件名/大小写敏感)

---

## 14. Electron(桌面应用)

### 14.1 识别指纹

```
✓ package.json 依赖 electron + electron-builder(或 @electron-forge/cli)
✓ electron/main.{js,ts} 或 src/main/index.{js,ts}
✓ electron-builder.yml / forge.config.*(其一)
```

辅证:`electron/preload.{js,ts}`、`electron/renderer/`、build/icon.*。

### 14.2 关键配置

- **入口**:`package.json` 的 `"main"` 字段(指向 `electron/main.js` 等)
- **打包**:electron-builder 看 `electron-builder.yml`;Forge 看 `forge.config.*`
- **安全**:`contextIsolation: true` + `nodeIntegration: false`(必须)+ preload 脚本
- **主进程 ↔ 渲染进程**:IPC(`ipcMain` / `ipcRenderer`)+ preload 暴露 API

### 14.3 目录约定(主流:主进程 + 渲染进程分离)

```
project/
├── electron/ 或 src/main/   ← 主进程
│   ├── main.{js,ts}         ← 应用入口
│   ├── preload.{js,ts}      ← 预加载(暴露 IPC API)
│   └── ipc/                 ← IPC 处理器
├── src/renderer/ 或 src/    ← 渲染进程(前端)
│   ├── pages/<页>.{tsx,vue}
│   ├── components/<组件>.{tsx,vue}
│   ├── store/
│   └── api/
├── electron-builder.yml
└── package.json
```

### 14.4 命名约定

- 主进程文件:`main.js` / `preload.js`,单实例
- IPC handler:`ipc/<模块>Handler.ts`
- 渲染进程页面:与 React/Vue 一致

### 14.5 代码落点决策树

```
业务逻辑在哪跑?
├─ 必须 Node API(文件、网络、进程、操作系统)→ 主进程 (electron/main/)
├─ 必须 UI 渲染 → 渲染进程 (src/renderer/)
├─ 共享(纯函数) → src/shared/ 或 utils/
└─ 跨进程数据 → IPC + 序列化

主进程能访问 Node API;渲染进程开启 nodeIntegration:false 后不能
```

### 14.6 TDD 路径与命令

- **测试框架**:Vitest / Jest + Playwright / Spectron(E2E)
- **命令**:
  ```
  npm run test              # 单元
  npm run test:e2e          # E2E(Playwright + Electron)
  npm run start             # 开发启动
  npm run build             # 打包(electron-builder)
  ```

### 14.7 Lint 命令

- `npm run lint`(eslint)
- 注意:**主进程与渲染进程可分别配 eslint**(`overrides` 字段)

### 14.8 平台分支

| 平台 | 打包命令 | 产物 | 调试 |
|---|---|---|---|
| Windows | `npm run build:win` | `dist/<App>-Setup.exe` + NSIS | 真机 |
| macOS | `npm run build:mac` | `dist/<App>-<ver>.dmg` | 真机(需 codesign) |
| Linux | `npm run build:linux` | `dist/<App>-<ver>.AppImage` / `.deb` | 真机 |

### 14.9 常见反模式

- ❌ `nodeIntegration: true`(安全漏洞,必须 false + contextIsolation)
- ❌ 主进程直接 require 渲染进程的模块(进程隔离)
- ❌ IPC 数据未校验类型(应用 zod / io-ts)
- ❌ `webPreferences` 误开 `webSecurity: false`(仅 dev 临时调试用)

---

## 15. Tauri(Rust + Web 跨平台桌面)

### 15.1 识别指纹

```
✓ src-tauri/Cargo.toml + src-tauri/tauri.conf.json
✓ src-tauri/src/main.rs(应用入口)
✓ src-tauri/build.rs
✓ 前端 package.json + 前端框架指纹
```

辅证:`src-tauri/icons/`、`src-tauri/capabilities/`、`@tauri-apps/cli`。

### 15.2 关键配置

- **前端框架**:看 `package.json` 依赖(Vite + React/Vue/Svelte/Solid 等均可)
- **后端**:Rust + Tauri commands(`#[tauri::command]`)+ state
- **权限**:`src-tauri/capabilities/default.json` 定义可访问的 API
- **窗口**:`tauri.conf.json` 的 `app.windows`

### 15.3 目录约定

```
project/
├── src/                       ← 前端代码(任意 Web 框架)
│   ├── pages/<页>.{tsx,vue}
│   ├── components/<组件>.{tsx,vue}
│   ├── hooks/
│   └── api/
├── src-tauri/
│   ├── src/
│   │   ├── main.rs            ← Tauri 入口
│   │   ├── commands/          ← #[tauri::command] 函数
│   │   ├── domain/            ← 业务逻辑(Rust)
│   │   ├── api/               ← HTTP 客户端
│   │   └── utils/
│   ├── capabilities/
│   │   └── default.json       ← 权限声明
│   ├── icons/
│   ├── Cargo.toml
│   ├── tauri.conf.json
│   └── build.rs
└── package.json
```

### 15.4 命名约定

- Tauri command:`commands/<模块>.rs`,函数 `#[tauri::command] pub fn <name>(...)`
- 前端:与前端框架一致(React / Vue / ...)

### 15.5 代码落点决策树

```
业务逻辑在哪跑?
├─ 重 CPU / 文件系统 / 系统调用 / 高性能 → Rust(src-tauri/src/domain/)
├─ UI / 表单 / 路由 → 前端(src/)
├─ 跨语言调用 → Tauri command + invoke('command_name', { args })
└─ 共享类型 → 前端 src/types/ 与 Rust src-tauri/src/types/(用 specta / ts-rs 自动生成)
```

### 15.6 TDD 路径与命令

- **前端**:Vitest / Jest(同 React/Vue)
- **Rust**:`cargo test`(单元)+ `cargo test --test integration`(集成)
- **E2E**:`tauri-driver` 或 WebDriver

### 15.7 Lint 命令

- 前端:eslint / prettier(同前端框架)
- Rust:`cargo clippy`(推荐)+ `cargo fmt`

### 15.8 平台分支

| 平台 | 打包命令 | 产物 |
|---|---|---|
| Windows | `npm run tauri build` | `src-tauri/target/release/<App>.exe` + MSI |
| macOS | `npm run tauri build` | `src-tauri/target/release/bundle/macos/<App>.app` + DMG |
| Linux | `npm run tauri build` | `.deb` / `.AppImage` |
| Android / iOS(Tauri v2) | `npm run tauri android build` / `tauri ios build` | apk / ipa |

### 15.9 常见反模式

- ❌ 前端直接调 Rust 函数(必须走 `invoke('command_name')`)
- ❌ capabilities 未声明就调用(运行时无权限报错)
- ❌ 大块数据序列化 JSON 用 `serde_json`(应考虑 binary 序列化如 bincode / rkyv)
- ❌ 跨语言类型不对齐(应统一 type / 用 specta 自动生成)

---

## 16. Lint 缺失时的安装建议(按主栈)

> 配合主 SKILL.md §2.5 使用。

### 16.1 检测依据速查

```
已具备 Lint 环境(任一命中即视为具备):
├─ package.json devDependencies 含:eslint / stylelint / prettier / @vue/eslint-config / @tarojs/eslint-config
├─ 工程根有:.eslintrc.* / eslint.config.* / .prettierrc.* / .stylelintrc.*
├─ Android:app/build.gradle 含 lint 块 + lint.xml 存在
├─ iOS:SwiftLint 可用 (which swiftlint) 或 .swiftlint.yml 存在
├─ Flutter:analysis_options.yaml 含 linter 块 或 pubspec.yaml 含 flutter_lints
├─ 鸿蒙:hvigorw 可用(默认具备)
└─ Tauri:cargo clippy + 前端 eslint(各自检测)
```

### 16.2 安装命令(可直接复制)

| 主栈 | 推荐安装命令 | 配套配置 |
|---|---|---|
| **uni-app / Vue / React / Taro / RN / Electron(前端)** | `npm i -D eslint @eslint/js eslint-plugin-vue globals`(Vue)<br>或 `npm i -D eslint @eslint/js eslint-plugin-react-hooks eslint-plugin-react-refresh globals`(React) | 根目录 `eslint.config.js`,见 Vue/React 官方 starter |
| **Prettier**(跨栈统一格式化) | `npm i -D prettier eslint-config-prettier eslint-plugin-prettier` | `.prettierrc` |
| **Stylelint**(CSS / SCSS / Less) | `npm i -D stylelint stylelint-config-standard` | `.stylelintrc.json` |
| **Android** | 无需额外安装;`app/build.gradle` 已有 lint 块;可启用:`android { lint { abortOnError = true } }` | 默认具备 |
| **iOS** | `brew install swiftlint` + 项目根 `.swiftlint.yml` | SwiftLint 默认规则 |
| **Flutter** | `dart pub global activate flutter_lints`(或 `flutter pub add --dev flutter_lints`) | `analysis_options.yaml` |
| **Tauri(Rust 部分)** | `rustup component add clippy rustfmt`(默认已带) | 无需额外配置 |
| **原生鸿蒙** | hvigor 自带 | 无需额外配置 |
| **原生微信小程序** | `npm i -D eslint @babel/eslint-parser eslint-plugin-wxml` | `.eslintrc.js` |

### 16.3 三种处理方式对应的 SKILL.md 标注

#### (B) 跳过 Lint 配置,第 5 步仅做 Markdown 结构校验

→ 生成的 SKILL.md 加:

```markdown
## Lint 环境状态

> 当前工程**未配置 Lint**。本 skill 第 5 步闸门降级为"Markdown 结构校验":
> - 不跑工程 Lint(eslint / stylelint / ktlint 等)
> - 仅校验 SKILL.md / references/*.md 的 frontmatter、标题层级、关键词覆盖
> - 用户后续补装 Lint 工具后,可恢复完整 Lint
```

#### (C) 延后处理

→ 生成的 SKILL.md 加:

```markdown
## Lint 环境状态

> ⚠ **本 skill 第 5 步闸门暂不可用完整 Lint**。
> 工程当前未配置 Lint 环境;需先安装 Lint 工具(命令:`<按主栈的具体命令>`),
> 安装完成后再使用本 skill 的 Lint 步骤。
> 在此之前,第 5 步仅做 Markdown 结构校验。
```

---

## 17. 类型检查缺失时的安装建议(按主栈)

> 配合主 SKILL.md §2.6 使用。
> **Java / Kotlin / Swift / Dart / ArkTS 工程类型检查默认具备,无需询问,无需安装。**
> 仅 TypeScript 系工程可能缺 typescript / tsconfig,需要询问。

### 17.1 检测依据速查

```
已具备类型检查环境(按主栈):
├─ TypeScript 系(uni-app / Vue / React / Taro / RN / Electron / Tauri 前端):
│   package.json devDependencies 含 typescript + tsconfig.json 存在
├─ Android Kotlin:find app/src -name "*.kt" 命中
├─ Android Java:javac 默认具备
├─ iOS:Xcode + Swift 编译器默认具备
├─ Flutter:pubspec.yaml 含 flutter: 或 main.dart 存在
├─ 鸿蒙:entry/Ability 存在 + ArkTS 编译器(hvigor)
└─ 桌面原生(无 JS):默认具备
```

### 17.2 安装命令(仅 TypeScript 系需要)

| 场景 | 命令 |
|---|---|
| 全新 TS 工程 | `npm i -D typescript @types/node` + `npx tsc --init` |
| Vue3 + TS | `npm i -D typescript vue-tsc @vue/tsconfig` + 调整 `tsconfig.json` 的 `vue-tsc` |
| React + TS | `npm i -D typescript @types/react @types/react-dom` + `npx tsc --init` |
| Taro + TS | Taro 默认带 TS 模板;若缺:`npm i -D typescript @tarojs/taro @types/webpack-env` + `tsconfig.json` |
| Electron + TS | `npm i -D typescript @types/node` + `tsconfig.json` |
| Tauri + TS(前端) | Tauri create 默认生成 TS;若缺:`npm i -D typescript` |

### 17.3 三种处理方式对应的 SKILL.md 标注

#### (B) 跳过类型检查

→ 生成的 SKILL.md 加:

```markdown
## TypeCheck 环境状态

> 当前 TypeScript 工程**未启用类型检查**。本 skill 第 X 步(类型检查步骤)中:
> - 不跑 `tsc --noEmit`
> - 仅做语法层面的 sanity check
> - 用户后续补装 typescript 后,可恢复严格类型检查
```

#### (C) 延后处理

→ 生成的 SKILL.md 加:

```markdown
## TypeCheck 环境状态

> ⚠ **本 skill 的类型检查步骤暂不可用**。
> 工程当前未配置 TypeScript;需先安装(命令:`npm i -D typescript`),
> 安装完成后初始化 `npx tsc --init`,再使用本 skill 的类型检查步骤。
```
