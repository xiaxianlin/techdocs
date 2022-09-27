# 组件分级与代码组织

## 分级

在**万物皆组件**的指导思想下，参考 `Web Component` 的规范对业务进行组件抽象，然后根据不同的作用，可以进行不同级别的划分。

### DOM 组件

最基础的组件，由 React 封装的原生组件，负责进行基础的 DOM 渲染。

### UI 组件

一般用来做基础展示和交互的组件，主要有 DOM 组件构成，在设计原则需要遵循单一职责。

### 数据组件

在 UI 组件的基础加入数据源，构成数据组件，常规的就是 Select 、 RadioGroup 之类的 UI 组件再套一个数据源。

### 业务组件

对原子业务进行封装，核心就是原子性，这个对抽象能力要求较高。

### 视图组件

视图组件的级别在另外一个维度上面，视图组件的抽象需要页面里面去剥离，把页面进行肢解，合理的切分成不同的视图组件。

### 模块组件

一个模块即一个业务，常见的模块组件是页面组件。

### 关系

每一层级的组件可以直接包含其任意深度的子层级组件。

<img src="_assets_/image-20220927155355456.png" alt="image-20220927155355456" style="zoom:50%;" />

## 代码组织

在单一职责的指导下，可以根据不同的职能划分不同的文件。

### 文件分类

##### 模型文件（Model）

模型主要对业务进行抽象，主要包含数据（状态）和行为（响应）两大类的抽象。

##### 逻辑文件（Logic）

对业务的逻辑进行抽象，主要是纯函数，尽可能的去除副作用，严格规范输入输出类型。

##### 服务文件（Service）

所有外部数据数据源必须经过服务函数处理后才能进行使用，在服务函数内做好数据防御。

##### 视图文件（View）

视图组件独立成文件，主要是因为视图里面会出现少量的业务逻辑。

##### 组件文件（Component）

非视图组件全部归类到组件里面，组件的要求有几点要求：

1.   原子性：组件只处理一个业务或功能
2.   受控性：组件必须是受控的

##### 类型文件（Type）

用来描述业务或功能需要的类型。

##### 样式文件（Style）

存放样式代码的地方，每个组件配备自己的样式文件。

##### 入口文件（Index）

即模块组件，也是对外访问的页面。

### 目录结构

##### 业务维度

```
pages
├── PageA
│    ├── components
│    ├── logics
│    ├── models
│    ├── services
│    ├── views
│    ├── index.module.scss
│    ├── index.tsx
│    ├── types.ts
------
```

这种结构是从业务维度出发去抽象，在整体上抽象出业务的各个职能。

##### 组件维度

```
pages
├── PageA
│    ├── ComponentA
│    ├── ComponentB
│    ├── EntryComponent
│    │    ├── index.tsx
│    │    ├── index.module.css
│    │    ├── service.ts
│    │    ├── logic.ts
│    │    ├── model.ts
│    │    ├── type.ts
-----------
```

这种结构完全遵循 `Web Component` 的规范，要求通过业务结合视图进行组件拆分，拆分出来的组件必须是完全独立的组件，然后根据搭积木的思维，进行业务组装。