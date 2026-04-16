# agent_ref/ — 官方参考资料目录
# 只读层：厂商文档、语言手册、技术规范

> 本目录存放所有官方文档。Agent 执行任务时按需查阅。
> 禁止将本目录内容写入 knowledge/（官方文档 ≠ 项目经验）。

---

## 目录索引

```
agent_ref/
├── README.md                    ← 本文件（索引）
│
├── controllers/                 ← 控制器编程手册
│   ├── [品牌_型号_PacScript.md]
│   ├── [品牌_型号_KRL.md]
│   └── [品牌_型号_TP.md]
│
├── vision/                      ← 视觉系统手册
│   ├── [品牌_型号_Setup.md]
│   └── [品牌_型号_Communication.md]
│
├── motion/                      ← 运动控制手册
│   ├── [Robot_Motion.md]        ← 运动指令、速度、插补
│   └── [坐标系与标定.md]
│
├── io/                          ← I/O 系统手册
│   ├── [IO_Mapping.md]
│   └── [IO_Control_Commands.md]
│
├── communication/               ← 通讯协议手册
│   ├── [EtherNetIP_Guide.md]
│   ├── [PROFINET_Guide.md]
│   ├── [ModbusTCP_Guide.md]
│   └── [OPCUA_Guide.md]
│
├── safety/                      ← 安全规范
│   ├── [Safety_Standards.md]
│   └── [ESTOP_Design_Guide.md]
│
└── hmi/                         ← HMI / 示教器界面
    └── [TP_Panel_Guide.md]
```

---

## 文件命名规范

```
[厂商]_[型号]_[内容类型].md

示例：
  DENSO_RC8_PacScript_Motion.md
  Keyence_CV-X_Communication.md
  Siemens_S71200_EtherNetIP.md
  KUKA_KRC4_KRL_Reference.md
```

---

## 查阅规则

| 任务类型 | 优先查阅 |
|---------|---------|
| 机器人程序生成 | controllers/ + motion/ |
| PLC逻辑生成 | io/ + communication/ |
| 技术问答 | 对应设备的所有相关手册 |
| 故障诊断 | 对应设备手册的错误代码章节 |
| 系统集成 | communication/ + 各设备接口章节 |
| 安全相关 | safety/（最高优先级） |

---

## 当前已加载文件

| 文件 | 内容 | 适用任务 | 加载日期 |
|------|------|---------|---------|
| motion/Robot_Motion.md | PacScript 运动指令完整参考（43条指令） | Task1/2/4 | 初始化 |
| （后续随项目添加） | | | |

---

## 添加新手册

1. 将手册文件放入对应子目录
2. 在上方"当前已加载文件"表格中添加记录
3. 在 knowledge/domain_map/device_registry.md 中更新对应设备条目
4. 文件格式：Markdown（从PDF/官方文档转换，保留章节结构和页码注释）

### 页码注释格式
```markdown
<!-- p.42 -->
## Motion Commands

### Move
...内容...

<!-- p.45 -->
### MoveL
...内容...
```
保留页码注释，确保 [REF: 文件名 §章节 p.XX] 引用可核实。
