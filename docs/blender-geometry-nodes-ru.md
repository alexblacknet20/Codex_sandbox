# Blender Geometry Nodes: исчерпывающее руководство для моделлера

> Актуальность: май 2026 года. Руководство написано для художника, который уверенно моделирует в Blender, понимает вершины, рёбра, полигоны, UV, нормали, модификаторы и материалы, но не понимает, как думать в Geometry Nodes.

## Содержание

1. [Главная идея](#главная-идея)
2. [История по годам и версиям](#история-по-годам-и-версиям)
3. [Как думать в Geometry Nodes, если вы моделлер](#как-думать-в-geometry-nodes-если-вы-моделлер)
4. [Интерфейс и базовый рабочий процесс](#интерфейс-и-базовый-рабочий-процесс)
5. [Типы данных и сокеты](#типы-данных-и-сокеты)
6. [Геометрия, компоненты и домены](#геометрия-компоненты-и-домены)
7. [Поля, атрибуты и named attributes](#поля-атрибуты-и-named-attributes)
8. [Инстансы: самый важный источник производительности](#инстансы-самый-важный-источник-производительности)
9. [Селекции, маски и логика](#селекции-маски-и-логика)
10. [Кривые, меши, точки, volumes и Grease Pencil](#кривые-меши-точки-volumes-и-grease-pencil)
11. [Зоны: Simulation, Repeat, For Each Element и Closure](#зоны-simulation-repeat-for-each-element-и-closure)
12. [Node Tools и процедурные ассеты](#node-tools-и-процедурные-ассеты)
13. [Библиотека основных паттернов](#библиотека-основных-паттернов)
14. [Практические проекты](#практические-проекты)
15. [Производительность, отладка и типичные ошибки](#производительность-отладка-и-типичные-ошибки)
16. [Совместимость версий](#совместимость-версий)
17. [План обучения на 30 дней](#план-обучения-на-30-дней)
18. [Справочник терминов](#справочник-терминов)
19. [Источники](#источники)

---

## Главная идея

**Geometry Nodes** — это визуальная система процедурного моделирования в Blender. Она работает как модификатор: на вход получает геометрию объекта, выполняет цепочку операций и отдаёт результат в сцену. В отличие от ручного моделирования, результат можно пересчитать в любой момент: поменять плотность травы, высоту здания, количество этажей, форму кабеля, распределение камней или параметры генератора — и Blender построит новую геометрию по правилам.

Для моделлера это полезно в четырёх ситуациях:

- **Много повторов:** болты, плитка, кирпичи, листья, камни, окна, панели, цепи, провода.
- **Вариативность:** один ассет превращается в десятки похожих вариантов без ручной правки каждого.
- **Неразрушаемость:** можно вернуться к параметрам, не применяя модификатор.
- **Инструменты для себя:** node group можно превратить в модификатор или инструмент, который ведёт себя как кастомная команда Blender.

Ключевой сдвиг мышления: вы не «двигаете вершины руками», а **описываете правило**, по которому вершины, точки, кривые, инстансы и атрибуты должны появляться, изменяться и удаляться.

---

## История по годам и версиям

Geometry Nodes менялись очень быстро. Старые уроки часто не совпадают с современным интерфейсом, поэтому важно понимать, из какого года туториал.

### Краткая шкала

| Год | Версии Blender | Что изменилось для Geometry Nodes | Как это влияет на обучение |
| --- | --- | --- | --- |
| 2020 | разработка проекта | Проект Geometry Nodes официально стартовал 19 октября 2020 как ответ на потребность художников в процедурном set dressing. | До релиза были дизайн-эксперименты; уроки по «particle nodes» не равны современным GN. |
| 2021 | 2.92, 2.93, 3.0 | Первый публичный релиз в 2.92; в 2.93 расширение атрибутов, spreadsheet и Cycles attributes; в 3.0 — революция Fields. | Уроки 2.92/2.93 считаются legacy; современную базу лучше начинать с 3.0+. |
| 2022 | 3.1, 3.2, 3.3, 3.4 | Быстрый рост производительности, новые mesh/curve/topology nodes, удаление большинства legacy-нод 2.93, улучшение Viewer Node. | Версии 3.x уже близки к современной логике, но интерфейс ещё беднее 4.x. |
| 2023 | 3.5, 3.6 LTS, 4.0 | В 3.6 появились Simulation Nodes; в 4.0 — Repeat Zone и Node-Based Tools. | GN перестали быть только «генератором мешей» и стали системой инструментов и симуляций. |
| 2024 | 4.1, 4.2 LTS, 4.3 | Bake node, Menu Switch, Sort Elements, Auto Smooth как GN asset, For Each Element, Grease Pencil support, gizmos. | Node groups стали удобнее оформлять как production-ассеты. |
| 2025 | 4.4, 4.5 LTS, 5.0 | 4.5 LTS добавила import nodes, Set Mesh Normal, Camera Info, Field statistics; 5.0 добавила Closures и прямую работу с volume grids. | 4.5 LTS — хороший стабильный выбор; 5.0 — большой шаг для сложных процедурных систем. |
| 2026 | 5.1 | Релизная ветка 5.1 продолжила развитие Blender 5.x; для Geometry Nodes важно сверяться с официальными release notes, потому что часть новых возможностей 5.x ещё быстро стабилизируется. | Для продакшена проверяйте add-ons и совместимость; для обучения можно использовать 4.5 LTS или 5.1+. |

### 2020: замысел

Проект появился не как «замена ручного моделирования», а как способ дать художникам предсказуемые инструменты для set dressing: рассыпать гальку, траву, объекты окружения, создавать вариации и контролировать результат без написания Python. Важно: изначальная цель была не сделать Houdini-клон, а встроить процедурность в привычную модель Blender: объект, модификатор, ассет, viewport.

### 2021: первый релиз и переход к Fields

- **Blender 2.92**: первый официальный релиз Geometry Nodes. Типичный пайплайн: `Point Distribute` → `Point Instance` → `Join Geometry`. Данные часто передавались через именованные строки-атрибуты.
- **Blender 2.93**: больше атрибутов, spreadsheet editor, больше нод, возможность рендерить атрибуты в Cycles.
- **Blender 3.0**: главный перелом — **Fields**. Вместо постоянной записи промежуточных named attributes многие операции стали вычисляться «на месте» для нужного домена. Появились современная логика `Position`, `Index`, `Normal`, `Set Position`, `Delete Geometry`, `Instance on Points`, curve nodes, text nodes, texture nodes в GN.

Если вы смотрите урок и там постоянно вводят имена атрибутов строками в синие поля, это почти наверняка материал эпохи 2.92/2.93. Он полезен исторически, но для новичка в 2026 году может только запутать.

### 2022: стабилизация современной базы

- **3.1**: крупные ускорения Fields, Realize Instances, Set Position, Bounding Box и других операций.
- **3.2**: большинство legacy-нод из 2.93 удалены; файлы старой системы перестали быть обратно совместимы с современной логикой.
- **3.3 LTS**: улучшение curves, UV nodes вроде unwrap/pack, укрепление новой системы Curves.
- **3.4**: Viewer Node начал удобнее показывать геометрию и атрибуты прямо во viewport и spreadsheet; `Transfer Attribute` был разделён на более конкретные sample-ноды (`Sample Index`, `Sample Nearest`, `Sample Nearest Surface`); добавились topology nodes.

### 2023: симуляции и инструменты

- **3.5**: реорганизация add menu, улучшения named attributes и workflow ассетов.
- **3.6 LTS**: появились **Simulation Nodes** — `Simulation Input` и `Simulation Output`. Теперь GN может хранить состояние между кадрами: рост, движение, накопление, простые физические или псевдофизические системы.
- **4.0**: появились **Repeat Zone** и **Node-Based Tools**. Node group можно использовать как оператор/инструмент в 3D View, а не только как модификатор.

### 2024: ассеты, bake, Grease Pencil, For Each

- **4.1**: Bake node, улучшенное baking для симуляций, `Menu Switch`, `Index Switch`, `Sort Elements`, object-mode node tools, панели в интерфейсе модификатора, Auto Smooth как GN asset.
- **4.2 LTS**: LTS-ветка для долгих проектов; polishing Repeat/Simulation/Bake workflows, ускорения sample nodes и boolean-related workflow.
- **4.3**: **For Each Element Zone** — зона, которая прогоняет подграф для каждого элемента; поддержка Grease Pencil в Geometry Nodes; gizmos для node groups.

### 2025: 4.5 LTS и 5.0

- **4.4**: улучшения Normal input, Object/Collection input nodes, Find in String, большие ускорения triangulate и sort workflows.
- **4.5 LTS**: стабильная LTS-версия с поддержкой до 2027 года. В GN важны `Set Mesh Normal`, import nodes (`Import PLY`, `OBJ`, `CSV`, `STL`), `Visual Geometry to Objects`, `Camera Info`, `Instance Bounds`, `Bit Math`, `Format String`, field statistics nodes.
- **5.0**: крупная новая ступень: **Closure Zone**, `Evaluate Closure`, volume **grid socket** и набор grid nodes, UV Tangent, новые built-in node group assets, изменения `id` attribute и улучшения viewer/workflow.

### 2026: 5.1 и дальше

На май 2026 актуальны Blender 5.1 и поддерживаемые LTS-ветки. Если вам нужна максимальная стабильность для клиента или студии, берите 4.5 LTS. Если вы учитесь и хотите новые возможности ветки 5.x, можно использовать 5.1, но проверяйте совместимость аддонов, чужих node groups и конкретные release notes вашей версии.

---

## Как думать в Geometry Nodes, если вы моделлер

### Ручное моделирование vs procedural graph

| Ручное моделирование | Geometry Nodes |
| --- | --- |
| Вы выбираете вершины и двигаете их. | Вы создаёте поле/правило, которое вычисляет позицию вершин. |
| Вы вручную дублируете объект. | Вы создаёте точки и инстансите объект на них. |
| Вы применяете bevel, array, mirror как отдельные модификаторы. | Вы можете собрать аналоги или управлять модификаторами через node group. |
| Результат фиксирован после apply. | Результат пересчитывается при изменении параметров. |
| Сложно сделать 1000 вариантов. | Вариативность — базовый сценарий. |

### Ментальная модель: «поток геометрии + поля»

Представьте GN-граф как кухонный конвейер:

1. **Group Input** приносит исходный объект.
2. Узлы меняют или создают данные: точки, кривые, меши, атрибуты.
3. Некоторые узлы не меняют геометрию сразу, а описывают формулу: «для каждой точки взять её позицию», «если высота больше 2 метров», «случайное число по id».
4. **Group Output** отдаёт финальный результат модификатору.

Главная ошибка новичка — ожидать, что каждый провод «несёт список вершин». Иногда провод несёт **поле**, то есть функцию, которая будет вычислена позже на конкретных вершинах, точках, рёбрах или полигонах.

### Простая аналогия с модификаторами

- `Set Position` похож на Edit Mode move, но выраженный формулой.
- `Delete Geometry` похож на выбор и удаление элементов.
- `Extrude Mesh` похож на Extrude, но с процедурной selection.
- `Instance on Points` похож на particle instancing или scatter.
- `Realize Instances` похож на «сделать дубликаты реальной геометрией».
- `Curve to Mesh` похож на bevel depth у кривой, но контролируемый процедурно.

---

## Интерфейс и базовый рабочий процесс

### Где открыть

1. Выберите объект.
2. Добавьте модификатор **Geometry Nodes**.
3. Нажмите **New**.
4. Откройте workspace **Geometry Nodes** или разделите окно на 3D Viewport + Geometry Node Editor + Spreadsheet.

### Минимальный граф

Стартовый граф обычно содержит:

```text
Group Input: Geometry  ─────>  Group Output: Geometry
```

Это означает: «ничего не меняй, просто верни исходный объект».

### Первые полезные действия

- Добавьте `Set Position` между Input и Output.
- Подключите `Position` к векторной математике, например прибавьте шум к Z.
- Добавьте `Delete Geometry` и управляйте Selection через сравнение (`Compare`) по высоте.
- Добавьте `Mesh Line` или `Grid`, чтобы создать геометрию с нуля.
- Используйте `Viewer Node`, чтобы смотреть промежуточный результат.
- Откройте **Spreadsheet**, чтобы видеть атрибуты, индексы и домены.

### Правило чистого графа

Хороший GN-граф читается слева направо:

1. **Input / параметры**
2. **Подготовка базовой геометрии**
3. **Создание точек или доменов**
4. **Расчёт масок/атрибутов**
5. **Инстансинг или деформация**
6. **Материалы/нормали/UV**
7. **Output**

Используйте Frames, Reroute, понятные имена node groups и выносите важные настройки в Group Interface.

---

## Типы данных и сокеты

### Частые типы

| Тип | Что означает | Пример |
| --- | --- | --- |
| Geometry | Набор компонентов: mesh, curve, point cloud, instances, volume, Grease Pencil. | Вход/выход модификатора. |
| Float | Число с дробью. | Высота, радиус, плотность. |
| Integer | Целое число. | Количество этажей, index, seed. |
| Boolean | True/False. | Selection, включатель опции. |
| Vector | 3 числа XYZ. | Position, Normal, Scale. |
| Rotation | Тип вращения в новых версиях. | Rotation socket, Rotate Rotation. |
| Color | RGBA/цвет. | Vertex color, материал, маска. |
| String | Текст. | Имя атрибута, форматирование строки. |
| Object / Collection / Material / Image | Ссылки на datablock Blender. | Объект для инстансинга, коллекция камней. |
| Matrix / Transform | Трансформации. | Более продвинутые pipelines. |
| Grid | Volume grid в Blender 5.0+. | SDF, density, voxel workflow. |
| Bundle / Closure | Продвинутые типы Blender 5.x. | Передача наборов данных и подграфов. |

### Сокеты: круглые, ромбовидные и поля

В современной GN-системе часть входов может принимать **single value**, а часть — **field**. Упрощённо:

- single value: одно значение для всей операции;
- field: значение может отличаться для каждой вершины, точки, грани или другого элемента.

Пример: в `Set Position` вход **Offset** может быть полем. Если подключить `Normal * 0.1`, каждая вершина сместится по своей нормали. Если подключить `(0, 0, 1)`, все элементы сместятся одинаково вверх.

---

## Геометрия, компоненты и домены

### Компоненты Geometry

Один провод Geometry может содержать несколько компонентов одновременно:

- **Mesh**: вершины, рёбра, face corners, faces.
- **Curve**: сплайны и control points.
- **Point Cloud**: точки без полигонов.
- **Instances**: ссылки на объекты/геометрию с трансформациями.
- **Volume**: объёмные данные.
- **Grease Pencil**: слои и кривые Grease Pencil в новых версиях.

`Join Geometry` может объединять компоненты разных типов в один поток. `Separate Components` позволяет разложить их обратно.

### Домены

**Domain** — уровень, на котором живёт значение:

| Домен | Для моделлера | Примеры данных |
| --- | --- | --- |
| Point / Vertex | Вершины или точки. | Position, weight, random scale. |
| Edge | Рёбра. | Sharp edge, bevel mask. |
| Face | Полигоны. | Material index, face area. |
| Face Corner | Угол полигона: важно для UV и split normals. | UV, tangents, corner normals. |
| Spline | Отдельный сплайн кривой. | Cyclic, resolution. |
| Instance | Каждый инстанс. | Instance scale, rotation, id. |
| Layer | Grease Pencil layers. | Layer attributes. |

Если значение «переезжает» между доменами, Blender интерполирует или агрегирует данные. Например, vertex color на face corner domain может отличаться на разных углах одного полигона, а значение на face domain одно для всей грани.

### Почему домены важны

Новичок часто говорит: «маска не работает». Обычно причина одна из трёх:

1. Маска вычисляется на другом домене.
2. Узел ожидает Selection на домене, отличном от вашего значения.
3. После `Realize Instances`, `Join Geometry` или конвертации данные изменили домен или были потеряны.

Практика: всегда проверяйте Spreadsheet и домен Viewer Node.

---

## Поля, атрибуты и named attributes

### Attribute

**Атрибут** — данные, прикреплённые к элементам геометрии: позиция вершины, нормаль, UV, id, вес, цвет, пользовательская маска. В ручном моделировании вы уже знаете атрибуты, просто называете их иначе: vertex group, UV map, material index, normal, crease, bevel weight.

### Field

**Поле** — вычисляемое выражение. Оно не обязано сразу храниться в геометрии. Например:

```text
Position → Separate XYZ → Z → Compare Greater Than 1.0
```

Это поле Boolean: «для каждого элемента true, если его Z больше 1». Его можно подключить в Selection у `Delete Geometry` или `Set Material`.

### Capture Attribute

`Capture Attribute` нужен, когда вы хотите сохранить результат поля на определённом этапе графа, чтобы использовать его позже, особенно после операций, которые могут поменять геометрию или домены.

Пример: до `Instance on Points` вычислить случайный размер для каждой точки, capture его, а потом использовать для scale инстанса.

### Store Named Attribute

`Store Named Attribute` записывает named attribute в геометрию. Он полезен, когда:

- атрибут должен быть доступен в материале через Attribute node;
- результат должен пережить выход из node group;
- нужно передать данные в другой модификатор или pipeline.

Не злоупотребляйте named attributes. В современной GN-логике лучше использовать fields и capture, пока нет причины записывать имя в геометрию.

### `id` и стабильный random

Для scatter-систем почти всегда нужен стабильный идентификатор. Если random зависит только от `Index`, он может «прыгать» при удалении или добавлении точек. Лучше использовать стабильный `id`, где это возможно, или создать свой id до операций, меняющих порядок.

---

## Инстансы: самый важный источник производительности

### Что такое instance

Инстанс — это ссылка на геометрию плюс transform. Если вы расставили 10 000 деревьев как инстансы, Blender не обязан хранить 10 000 полных копий меша. Он хранит одну геометрию и 10 000 трансформаций.

### Типовой scatter

```text
Group Input Geometry
→ Distribute Points on Faces
→ Instance on Points (Instance = Object Info или Collection Info)
→ Random Value для scale/rotation
→ Rotate Instances / Scale Instances
→ Join Geometry с исходной поверхностью
→ Group Output
```

### Когда нужен Realize Instances

`Realize Instances` превращает инстансы в настоящую геометрию. Используйте его только если нужно:

- деформировать каждую копию как mesh;
- записать атрибуты на вершины копий;
- сделать boolean/merge с реальной геометрией;
- экспортировать туда, где инстансы не поддерживаются.

Если вы просто рендерите scatter — не realize без необходимости.

### Collection Info и Pick Instance

Для вариативности подключайте коллекцию объектов в `Collection Info`, включайте As Instance, затем используйте `Instance on Points` с Pick Instance и Instance Index. Индекс можно задавать random integer, зависящий от id точки.

---

## Селекции, маски и логика

### Selection — это Boolean field

В GN selection — не выделение в viewport, а поле True/False. Узлы вроде `Delete Geometry`, `Set Position`, `Set Material`, `Extrude Mesh` применяют действие только там, где selection true.

### Частые маски

| Задача | Как собрать |
| --- | --- |
| Выше/ниже уровня | `Position` → `Separate XYZ` → `Compare` |
| По нормали вверх | `Normal` → Dot Product с `(0,0,1)` → Compare |
| Случайная часть | `Random Value Boolean` или Float → Compare |
| По расстоянию до объекта | `Object Info Location` + `Position` → `Vector Math Distance` → Compare |
| По UV | `Named Attribute` UV / sample UV workflows → Compare |
| По material index | `Material Index` → Compare |
| По группе | Named attribute / vertex group как float mask |

### Логические узлы

- `Boolean Math AND`: обе маски true.
- `OR`: хотя бы одна true.
- `NOT`: инверсия.
- `Compare`: числа/векторы в boolean.
- `Switch`: выбрать одно из двух значений по условию.
- `Menu Switch`: удобный enum в интерфейсе node group.
- `Index Switch`: выбор из многих входов по integer.

---

## Кривые, меши, точки, volumes и Grease Pencil

### Mesh workflow

Для моделлера mesh — самый привычный тип. Основные задачи:

- создавать примитивы (`Mesh Cube`, `Grid`, `UV Sphere`, `Cylinder`);
- менять позицию (`Set Position`);
- extrude/subdivide/triangulate/dual mesh;
- удалять части (`Delete Geometry`);
- назначать материалы (`Set Material`, `Set Material Index`);
- работать с UV, normals, sharp edges.

### Curve workflow

Кривые в GN нужны для:

- кабелей, труб, лиан, дорожек;
- профилей и sweep-моделирования (`Curve to Mesh`);
- генерации волос/травы;
- текста (`String to Curves`);
- управления распределением объектов вдоль пути.

Типовой кабель:

```text
Curve Line / Curve Spiral / исходная Curve
→ Resample Curve
→ Set Curve Radius
→ Curve Circle как профиль
→ Curve to Mesh
→ Set Material
```

### Points workflow

Точки — промежуточная структура для scatter и procedural layout. Их можно получить через:

- `Distribute Points on Faces`;
- `Mesh to Points`;
- `Curve to Points`;
- `Points` primitive;
- simulation или custom generation.

### Volumes и grids

До Blender 5.0 volumes часто воспринимались как отдельный тип данных. В 5.0 появились grid sockets и прямые grid nodes: можно строить SDF/density grids, фильтровать, семплировать и конвертировать grid to mesh. Это важно для процедурных облаков, voxel effects, organic blending и SDF boolean workflows.

### Grease Pencil

С Blender 4.3 Geometry Nodes умеет работать с Grease Pencil data как с геометрией, состоящей из слоёв и кривых. Это открывает процедурную обработку 2D/2.5D графики: изменение толщины, цвета, глубины, softness, генерация stroke-эффектов.

---

## Зоны: Simulation, Repeat, For Each Element и Closure

### Simulation Zone

Simulation Zone хранит состояние между кадрами. Простейшая логика:

```text
Simulation Input: предыдущее состояние
→ изменить состояние на текущем кадре
→ Simulation Output: сохранить для следующего кадра
```

Примеры:

- рост ветки или кристалла;
- накопление следов;
- простая физика частиц;
- счётчик/таймер;
- изменение геометрии со временем.

Важно: симуляции нужно кэшировать или bake-ить для стабильного воспроизведения.

### Repeat Zone

Repeat Zone повторяет подграф заданное количество раз. Это процедурный аналог цикла:

- построить лестницу из N ступеней;
- итеративно subdivide/offset;
- создать цепочку объектов;
- повторить деформацию несколько раз.

Если задача решается обычным field на всех элементах, не используйте Repeat: field быстрее и проще. Repeat нужен, когда каждый шаг зависит от результата предыдущего.

### For Each Element Zone

For Each Element Zone прогоняет подграф для каждого элемента: точки, полигона, instance и т.д. Это полезно, когда для каждого элемента нужно создать собственную мини-геометрию или сложный результат, который трудно выразить обычным полем.

Не используйте For Each для простых математических операций по вершинам — fields обычно значительно быстрее.

### Closure Zone

Closure в Blender 5.0+ позволяет передавать «кусок вычисления» как значение и затем выполнять его через `Evaluate Closure`. Это продвинутый инструмент для универсальных node groups, где пользователь может подставлять не просто число или геометрию, а процедуру.

Новичку Closure не нужен в первые недели. Сначала освойте fields, instances, domains, capture и node groups.

---

## Node Tools и процедурные ассеты

### Node group как модификатор

Если у node group включить использование как modifier asset, она может появляться в меню модификаторов. Это удобно для студийных инструментов: «Panel Generator», «Scatter Moss», «Cable Along Curve», «Building Floors».

### Node-Based Tools

С Blender 4.0 geometry node groups могут работать как инструменты/операторы в 3D View. Это ближе к кастомным командам Blender без Python. В tool-графах доступны специальные данные: selection edit mode, 3D cursor, face sets и т.д.

### Хороший интерфейс node group

Для художника важны не внутренние ноды, а понятные параметры:

- Density, Seed, Scale Min/Max;
- Height, Width, Floors;
- Enable Bevel, Bevel Radius;
- Material slots;
- Collection/Object input;
- меню режимов через `Menu Switch`;
- панели в modifier interface.

Подписывайте параметры человеческим языком, задавайте min/max и units, группируйте по панелям.

---

## Библиотека основных паттернов

### 1. Деформация по шуму

```text
Geometry
→ Set Position
   Position = Position + Normal * Noise(Position * Scale) * Strength
```

Использование: скалы, земля, ткань, неровные края.

### 2. Scatter по поверхности

```text
Surface Mesh
→ Distribute Points on Faces
→ Instance on Points
→ Random Value для scale
→ Align Euler/rotation к Normal
→ Join Geometry
```

Использование: трава, камни, мусор, листья.

### 3. Маска по высоте

```text
Position → Separate XYZ → Z → Compare Greater Than Threshold → Selection
```

Использование: снег сверху, мох снизу, удаление нижней части, материал по высоте.

### 4. Маска по направлению нормали

```text
Normal → Dot Product with Up Vector → Compare Greater Than 0.7
```

Использование: снег только на горизонтальных поверхностях, трава только на верхних полигонах.

### 5. Кабель по кривой

```text
Curve
→ Resample Curve
→ Set Curve Radius
→ Curve Circle
→ Curve to Mesh
```

Использование: провода, трубы, лианы, шланги.

### 6. Процедурная лестница

```text
Mesh Cube step
→ Transform Geometry по индексу/повтору
→ Join Geometry или Repeat Zone
```

Использование: лестницы, полки, жалюзи, ступенчатые конструкции.

### 7. Здание из этажей

```text
Integer Floors
→ Mesh Line по Z
→ Instance floor module on points
→ Instance roof/cap
→ Realize при необходимости
```

Использование: дома, башни, sci-fi блоки.

### 8. Вариативный asset randomizer

```text
Seed + id
→ Random Value для размера, поворота, материала, выбора instance
→ Switch/Index Switch/Instance Index
```

Использование: набор камней, деревьев, реквизита.

### 9. UV/материальный pipeline

```text
Store Named Attribute / Set Material Index
→ Shader Attribute Node или Material Index
```

Использование: маски в материале, procedural wear, color variation.

### 10. Bake-heavy workflow

```text
Тяжёлый расчёт
→ Bake Node или Simulation Bake
→ дальнейшие лёгкие операции
```

Использование: симуляции, дорогие scatter, slow boolean, cache for production.

---

## Практические проекты

### Проект 1: генератор травы на поверхности

Цель: научиться точкам, инстансам, random и маскам.

1. Создайте plane или landscape.
2. Добавьте Geometry Nodes.
3. `Distribute Points on Faces`.
4. Создайте травинку как curve или используйте объект через `Object Info`.
5. `Instance on Points`.
6. Random scale от 0.6 до 1.4.
7. Random rotation around Z.
8. Маска по нормали: не ставить траву на вертикальные склоны.
9. Маска по весу: vertex group/painted mask для ручного контроля.
10. Не используйте `Realize Instances`, пока не нужно.

Что вы изучили: point domain, normal mask, stable random, instances.

### Проект 2: процедурный кабель

Цель: понять curves.

1. Создайте Bezier Curve как путь.
2. В GN добавьте `Resample Curve`.
3. Добавьте `Set Curve Radius`.
4. Создайте `Curve Circle` как профиль.
5. `Curve to Mesh`.
6. Добавьте noise к radius для органики.
7. Добавьте material и caps.

Что вы изучили: curve domain, radius, resampling, conversion.

### Проект 3: генератор фасада

Цель: понять индексы, повторение, инстансы и интерфейс.

1. Входные параметры: Floors, Columns, Floor Height, Window Collection, Seed.
2. Создайте grid точек по этажам и колоннам.
3. Инстансите окно на каждую точку.
4. Через random или pattern пропускайте часть окон.
5. Добавьте отдельные modules: door, roof, corner pieces.
6. Вынесите параметры в modifier interface.

Что вы изучили: procedural layout, integer math, instance selection, asset UI.

### Проект 4: growth simulation

Цель: понять Simulation Zone.

1. Создайте стартовую точку/кривую.
2. В Simulation Zone храните геометрию прошлого кадра.
3. На каждом кадре добавляйте новый сегмент по направлению normal/noise.
4. Ограничьте рост маской по расстоянию или столкновению.
5. Bake simulation.

Что вы изучили: stateful GN, cache, frame-dependent evaluation.

### Проект 5: инструмент очистки mesh selection

Цель: понять Node Tools.

1. Создайте node tool для edit mode.
2. Получите текущую selection.
3. Расширьте/сожмите выбор через topology nodes.
4. Запишите selection обратно.
5. Добавьте параметры threshold/mode.

Что вы изучили: GN не только как генератор, но как инструмент моделирования.

---

## Производительность, отладка и типичные ошибки

### Правила производительности

1. **Инстансы дешевле реальной геометрии.** Не используйте `Realize Instances` без причины.
2. **Fields дешевле циклов.** Не используйте Repeat/For Each для простых per-point формул.
3. **Ограничивайте плотность.** Миллионы точек возможны, но UI и viewport имеют предел.
4. **Bake тяжёлые этапы.** Особенно simulation, import, сложные boolean, SDF/grid.
5. **Удаляйте раннее.** Если часть геометрии не нужна, удалите её до дорогих операций.
6. **Сохраняйте стабильный id.** Иначе random и scatter будут прыгать.
7. **Проверяйте domains.** Лишняя интерполяция может быть и ошибкой, и нагрузкой.
8. **Не храните всё named attributes.** Лишние атрибуты увеличивают память.
9. **Используйте bounding/culling.** `Camera Info` и distance masks помогают не генерировать невидимое.
10. **Разделяйте graph на node groups.** Маленькие группы проще оптимизировать.

### Отладка

- `Viewer Node`: смотрите промежуточную геометрию.
- Spreadsheet: проверяйте домен, index, id, named attributes.
- Временно подключайте `Random Value` к color/material, чтобы увидеть распределение.
- Используйте `Store Named Attribute` временно для диагностики в shader.
- Подписывайте Frames: «Generate Points», «Masks», «Instances», «Materials».
- Проверяйте предупреждения узлов; новые версии лучше показывают warning propagation.

### Типичные ошибки новичка

| Ошибка | Симптом | Решение |
| --- | --- | --- |
| Забыли `Join Geometry` | Исходный объект пропал, видны только инстансы. | Join исходную геометрию и результат. |
| Realize слишком рано | Сцена стала тяжёлой. | Оставляйте instances до финального этапа. |
| Random прыгает | Объекты меняют размер/место при изменении density. | Используйте стабильный id/seed. |
| Неверный домен | Selection работает странно. | Проверить Viewer/Spreadsheet, явно capture на нужном domain. |
| Старый урок 2.93 | Нет нужных нод или названия другие. | Ищите уроки 3.0+ или 4.x/5.x. |
| Масштаб объекта не применён | Плотность/размер не соответствует ожиданию. | Apply Scale или учитывайте transform. |
| Материал не видит атрибут | Attribute node возвращает 0. | Store Named Attribute на правильном domain/name; проверить spelling. |
| Слишком сложный один graph | Невозможно читать. | Разделить на node groups и frames. |

---

## Совместимость версий

### Что важно помнить

- **2.92/2.93**: legacy attribute workflow. Не лучший старт в 2026.
- **3.0**: современная база Fields. Большинство современных уроков начинается отсюда.
- **3.2**: старые legacy nodes удалены; backward compatibility с pre-fields ограничена.
- **3.6 LTS**: хороший старый LTS с Simulation Nodes.
- **4.0**: Repeat Zone и Node Tools.
- **4.1-4.3**: bake, панели, node tools, For Each, Grease Pencil.
- **4.5 LTS**: стабильная production-ветка, хорошая рекомендация для долгих проектов.
- **5.0/5.1**: новые продвинутые возможности, но проверяйте совместимость студийных пайплайнов.

### Как выбирать версию

| Ситуация | Рекомендация |
| --- | --- |
| Учусь с нуля | 4.5 LTS или 5.1. |
| Делаю коммерческий проект на месяцы | 4.5 LTS. |
| Нужны Closure/Grid nodes | 5.0+. |
| Нужна максимальная совместимость со старыми аддонами | 4.5 LTS или 4.2 LTS, зависит от аддона. |
| Повторяю старый туториал 2.93 | Лучше найти современный аналог. |

---

## План обучения на 30 дней

### Неделя 1: база

- День 1: интерфейс, Group Input/Output, Set Position.
- День 2: Position, Normal, Index, Random Value.
- День 3: Compare, Boolean Math, Delete Geometry.
- День 4: Distribute Points on Faces, Instance on Points.
- День 5: Object Info, Collection Info, Pick Instance.
- День 6: Viewer Node и Spreadsheet.
- День 7: мини-проект «трава на поверхности».

### Неделя 2: домены и атрибуты

- День 8: point/edge/face/face corner domains.
- День 9: Capture Attribute.
- День 10: Store Named Attribute и материал.
- День 11: UV и material index.
- День 12: stable id и random.
- День 13: named attributes из vertex groups.
- День 14: мини-проект «мох/снег по маскам».

### Неделя 3: кривые и генераторы

- День 15: Curve Line, Bezier Segment, Resample Curve.
- День 16: Curve to Mesh и профили.
- День 17: Curve to Points и scatter along curve.
- День 18: Mesh primitives и procedural layout.
- День 19: Index math для этажей/окон.
- День 20: node group interface.
- День 21: мини-проект «процедурный фасад».

### Неделя 4: продвинутый workflow

- День 22: Repeat Zone.
- День 23: Simulation Zone.
- День 24: Bake node/cache.
- День 25: Node Tools.
- День 26: For Each Element.
- День 27: оптимизация и instancing.
- День 28: debugging большого графа.
- День 29: packaging как asset.
- День 30: финальный проект из 3 систем: scatter + generator + material attributes.

---

## Справочник терминов

- **Geometry Nodes Modifier** — модификатор, который выполняет node group на объекте.
- **Node Tree / Graph** — сеть узлов.
- **Node Group** — переиспользуемый граф с входами и выходами.
- **Socket** — вход или выход узла.
- **Field** — выражение, вычисляемое для элементов геометрии.
- **Attribute** — данные на элементах геометрии.
- **Domain** — уровень хранения/вычисления данных: point, edge, face, corner, instance и т.д.
- **Instance** — ссылка на геометрию с transform вместо полной копии.
- **Realize Instances** — превращение инстансов в реальную геометрию.
- **Selection** — boolean field для выбора элементов.
- **Capture Attribute** — сохранение результата поля на конкретном этапе.
- **Store Named Attribute** — запись именованного атрибута в геометрию.
- **Viewer Node** — отладочный узел для просмотра промежуточных данных.
- **Spreadsheet** — таблица данных геометрии.
- **Simulation Zone** — зона со state между кадрами.
- **Repeat Zone** — зона повторения операций.
- **For Each Element Zone** — зона обработки каждого элемента отдельно.
- **Closure** — продвинутый тип для передачи вычисляемого подграфа.
- **Bake** — сохранение результата расчёта, чтобы не пересчитывать его каждый раз.
- **Node Tool** — node group, используемая как инструмент/оператор в Blender.

---

## Источники

Официальные и справочные материалы, использованные для проверки истории и терминологии:

- Blender Developer Documentation: Geometry Nodes design example — https://developer.blender.org/docs/handbook/design/examples/geometry_nodes/
- Blender 2.92 release notes — https://developer.blender.org/docs/release_notes/2.92/
- Blender 2.93 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/2.93/geometry_nodes/
- Blender 3.0 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.0/nodes_physics/
- Blender 3.1 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.1/nodes_physics/
- Blender 3.2 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.2/nodes_physics/
- Blender 3.3 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.3/nodes_physics/
- Blender 3.4 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.4/nodes_physics/
- Blender 3.5 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.5/nodes_physics/
- Blender 3.6 Nodes & Physics release notes — https://developer.blender.org/docs/release_notes/3.6/nodes_physics/
- Blender 4.0 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.0/geometry_nodes/
- Blender 4.1 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.1/nodes_physics/
- Blender 4.2 LTS Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.2/geometry_nodes/
- Blender 4.3 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.3/geometry_nodes/
- Blender 4.4 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.4/geometry_nodes/
- Blender 4.5 LTS release page — https://www.blender.org/download/releases/4-5/
- Blender 4.5 LTS Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/4.5/geometry_nodes/
- Blender 5.0 Geometry Nodes release notes — https://developer.blender.org/docs/release_notes/5.0/geometry_nodes/
- Blender 5.1 release page — https://www.blender.org/download/releases/5-1/
- Blender 5.1 release notes — https://developer.blender.org/docs/release_notes/5.1/
- Blender Manual: Geometry Nodes — https://docs.blender.org/manual/en/latest/modeling/geometry_nodes/index.html
- Blender Manual: Simulation Nodes — https://docs.blender.org/manual/en/latest/physics/simulation_nodes.html

---

## Быстрая шпаргалка

Если нужно запомнить только десять вещей:

1. Geometry Nodes — это модификатор с процедурным графом.
2. Современный GN строится вокруг Fields, а не старой строковой системы атрибутов 2.92/2.93.
3. Домен данных важнее, чем кажется.
4. Selection — это Boolean field.
5. Инстансы дешёвые; Realize Instances дорогой.
6. `id` нужен для стабильного random.
7. Viewer Node и Spreadsheet — ваши главные инструменты отладки.
8. Node groups должны иметь понятный интерфейс для художника.
9. Для продакшена лучше стабильная LTS-версия, например 4.5 LTS.
10. Учиться надо проектами: scatter, curve cable, facade generator, simulation, node tool.
