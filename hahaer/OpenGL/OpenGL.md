![[Pasted image 20250313003844.png]]创建一个VBO时，还没有分配显存呢，只是创建了个描述显存的对象（句柄）
![[Pasted image 20250313003953.png]]![[Pasted image 20250313004519.png]]
![[Pasted image 20250313004847.png]]
![[Pasted image 20250313005717.png]]![[Pasted image 20250313010309.png]]注意：VAO是描述信息的，即存储数据格式的，不是存储具体的数据的，那个是VBO
![[Pasted image 20250313010812.png]]
![[Pasted image 20250313011055.png]]![[Pasted image 20250313011449.png]]
![[Pasted image 20250313011700.png]]
下面看下交叉存储的VAO写法：
![[Pasted image 20250313012601.png]]
![[Pasted image 20250313012638.png]]
![[Pasted image 20250313013041.png]]![[Pasted image 20250313013503.png]]
![[Pasted image 20250313013711.png]]![[Pasted image 20250318010134.png]]
![[Pasted image 20250318010311.png]]
![[Pasted image 20250322175444.png]]
glUniform3f 与glUniform3fv的区别
`glUniform3f` 和 `glUniform3fv` 都用于向着色器中的 `uniform vec3` 变量传递数据，但它们的使用方式有所不同。

---

### **1. `glUniform3f(location, v0, v1, v2)`**

- 作用：**传递单个 `vec3` 值**。
- 参数：
    - `location`：着色器中 `uniform vec3` 变量的位置。
    - `v0, v1, v2`：分别是 `vec3` 的 `x, y, z` 分量。
- 适用于：只需要更新一个 `vec3` 值时。

#### **示例**

```cpp
glUniform3f(location, 1.0f, 0.5f, 0.2f);
```

相当于在 GLSL 中：

```glsl
uniform vec3 color;
void main() {
    vec3 myColor = color; // color = vec3(1.0, 0.5, 0.2)
}
```

---

### **2. `glUniform3fv(location, count, value)`**

- 作用：**传递一个或多个 `vec3` 值**。
- 参数：
    - `location`：着色器中 `uniform vec3` 变量的位置。
    - `count`：要传递的 `vec3` 的数量（数组大小）。
    - `value`：指向存储 `vec3` 数据的 **连续 `float` 数组** 的指针（每个 `vec3` 用 3 个 `float` 表示）。
- 适用于：需要批量更新多个 `vec3`，或者直接传递数组数据时。

#### **示例**

如果 GLSL 中有一个 `vec3` 数组：

```glsl
uniform vec3 colors[2];
```

可以这样传值：

```cpp
float colorData[] = {1.0f, 0.5f, 0.2f, 0.3f, 0.6f, 0.9f};
glUniform3fv(location, 2, colorData);
```

相当于：

```glsl
colors[0] = vec3(1.0, 0.5, 0.2);
colors[1] = vec3(0.3, 0.6, 0.9);
```

---

### **区别总结**

|函数|作用|适用于|传递的数据形式|
|---|---|---|---|
|`glUniform3f`|传递单个 `vec3`|单个 `vec3`|`x, y, z` 分量|
|`glUniform3fv`|传递多个 `vec3`|数组 `vec3` 或批量更新|`float` 数组，每个 `vec3` 3 个值|

如果你只是给一个 `uniform vec3` 变量传值，用 `glUniform3f`；  
如果你要一次性更新多个 `vec3`，用 `glUniform3fv` 更高效。
![[Pasted image 20250322223939.png]]
![[Pasted image 20250322224106.png]]
![[Pasted image 20250322224415.png]]
![[Pasted image 20250322224847.png]]
![[Pasted image 20250322225106.png]]
![[Pasted image 20250323011948.png]]
![[Pasted image 20250323012212.png]]
![[Pasted image 20250323012315.png]]
![[Pasted image 20250323012335.png]]
![[Pasted image 20250323012449.png]]
![[Pasted image 20250323012558.png]]
![[Pasted image 20250323012705.png]]

![[Pasted image 20250323013332.png]]
之所以给sampler赋值，是因为需要把sampler指向不同的texture unit，而每一个texture unit已经在程序运行前bind好了不同的texture object
![[Pasted image 20250323165410.png]]![[Pasted image 20250418233927.png]]
![[Pasted image 20250418234020.png]]![[Pasted image 20250418234218.png]]
![[Pasted image 20250418234302.png]]![[Pasted image 20250418235910.png]]
![[Pasted image 20250419005325.png]]
![[Pasted image 20250419010301.png]]
![[Pasted image 20250419011309.png]]
### opengl 中vs shader，gl_position随着时间缩小，uv不变，问显示出来的三角形会怎么变
在 OpenGL 中，**顶点着色器（Vertex Shader）中 `gl_Position` 缩小**，而 **传递给片段着色器的 `uv`（纹理坐标）不变**，这会导致 **三角形在屏幕上变小，但纹理保持原样贴图**，从而造成以下视觉效果：

---

### 🎬 场景设定回顾：

- `gl_Position` 是用来决定三角形 **在屏幕上的位置和大小（NDC空间）**。
    
- `uv` 是用来决定纹理坐图如何在表面上映射。
    
- 你让 `gl_Position` 随时间缩小（比如乘上一个 `scale = sin(time)`），**表示三角形在屏幕上变小**。
    
- `uv` 不变，意味着纹理坐标没被缩放。
    

---

### 📌 结果：三角形变小，但显示的纹理范围不变！

所以你会看到：

- 三角形随着时间缩小。
    
- 但纹理仍然完整贴在这个变小的三角形上（只是变得更“压缩”了）。
    
- 最终你看到的效果是：
    
    > **三角形变小，纹理被“裁剪”显示更少的一部分**。
    

---

### 🖼️ 举个例子：

比如你原来是一个大三角形，显示了纹理的 `[0,0]` 到 `[1,1]` 全图。

现在三角形缩小为原来的 50%，但 `uv` 还是 `[0,0]` 到 `[1,1]`，那会发生：

- 顶点位置变小，三角形在屏幕上小了一圈。
    
- 但是片段着色器仍然收到 `[0,0]` ~ `[1,1]` 范围的 `uv`，意味着这小块区域仍然尝试显示整个纹理。
    
- 所以纹理变得“被压缩”地贴在小区域上。
    

---

### 🧪 如果你希望纹理不变形怎么办？

你需要同时**缩放 `gl_Position` 和 `uv`**，或者让 `uv` 根据缩放来偏移裁剪。

---

如果你愿意提供你的 shader 代码片段，我可以给你直接模拟分析它具体显示什么样子！你要看看吗？
### 所以uv坐标决定了纹理对象的哪个部分会完全贴合在glposition区域上吗
对！你理解得很对，**`uv` 坐标决定了纹理对象的哪一部分会贴合到 `gl_Position` 所定义的图形区域上**，它们之间是这样配合的：

---

### 🎯 总体理解：

- **`gl_Position`** 决定了图形在屏幕上的**位置和形状**（比如一个三角形在窗口的哪一部分）。
    
- **`uv` 坐标**（或 `texCoord`）决定了纹理图片中的哪一部分被映射到这个三角形上。
    

---

### 🖼️ 打个形象的比方：

想象一下：

- 一个大海报（纹理）上的某个区域（比如中间 10%）被裁下来（`uv` 定义）。
    
- 然后贴到了你的三角形上（`gl_Position` 定义的屏幕区域）。
    

如果你不改变 `uv`，只把三角形变小，海报上的这一块裁剪区域**仍然完整地贴在这个变小的三角形上**，于是就“压缩”地显示出来了。

---

### 📌 关键点：

|元素|作用|
|---|---|
|`gl_Position`|决定三角形在屏幕上有多大、在哪里|
|`uv`|决定纹理中哪一部分贴到这个三角形上|
|`gl_FragColor = texture(sampler, uv)`|片段着色器根据 `uv` 采样纹理贴图|

---

### ✅ 举个最常见的三角形例子：

假设你传入的三个顶点是：

```glsl
// 顶点着色器输出
v_uv = vec2(0.0, 0.0); // 左下
v_uv = vec2(1.0, 0.0); // 右下
v_uv = vec2(0.5, 1.0); // 顶部
```

那么这三个点，就把整个纹理的底部和中部“贴”到三角形上了。

如果三角形本身变小，但这三个 `uv` 还是不变，那就意味着这张纹理图会在更小的区域上显示（也许会看得模糊、压缩或局部失真）。

---

想更进一步理解的话，我可以画个图形或示意图解释它们的对应关系！你需要可视化图吗？
![[Pasted image 20250419172421.png]]

 ![[Pasted image 20250419175533.png]]
 ![[Pasted image 20250419175614.png]]
在3维情况下 ![[Pasted image 20250419175940.png]]![[Pasted image 20250419180204.png]]
### gl_position是几维的
`gl_Position` 是 **四维的向量**，类型为：

```glsl
vec4 gl_Position;
```

---

### ✅ 四个分量的含义（标准 NDC 管线中的）

```glsl
gl_Position = vec4(x, y, z, w);
```

|分量|含义|
|---|---|
|`x`, `y`, `z`|顶点的空间位置（裁剪空间坐标）|
|`w`|齐次坐标的缩放因子（用于透视除法）|

---

### 🔍 为什么是 4D？

因为 OpenGL 的图形流水线使用 **齐次坐标（Homogeneous Coordinates）**，以便统一处理透视投影、平移等仿射变换。

- 最终在经过 **裁剪空间 -> 归一化设备坐标(NDC)** 的转换时，会做一次：
    
    ```glsl
    ndc = vec3(x/w, y/w, z/w);
    ```
    
- 然后 NDC 会被映射到屏幕坐标上。
    

---

### 🎯 举个简单例子：

```glsl
// 顶点位置在正中央
gl_Position = vec4(0.0, 0.0, 0.0, 1.0);
// 最终屏幕位置：0/1, 0/1, 0/1 => 中央
```

如果你写成：

```glsl
gl_Position = vec4(0.0, 0.0, 0.0, 2.0);
// 屏幕位置变成了 0/2, 0/2, 0/2 => 还是中央，但 w = 2，影响了透视深度
```

---

所以结论是：

> ✅ `gl_Position` 是 4 维（vec4），为了支持 **透视投影** 和 **裁剪判断**，不是我们直接看到的 3D 坐标。

如果你还想看看从模型坐标到 gl_Position 的整个变换链路（MVP 变换：Model → View → Projection），我也可以总结一下~

![[Pasted image 20250419221712.png]]
![[Pasted image 20250419222033.png]]
![[Pasted image 20250419222213.png]]![[Pasted image 20250419222344.png]]![[Pasted image 20250419222427.png]]![[Pasted image 20250419233303.png]]
![[Pasted image 20250419233840.png]]
![[Pasted image 20250419233849.png]]
viewMatrix：将世界坐标系-》摄像机坐标系
![[Pasted image 20250419234915.png]]
![[Pasted image 20250419234925.png]]
![[Pasted image 20250419234948.png]]![[Pasted image 20250419235519.png]]![[Pasted image 20250419235525.png]]![[Pasted image 20250419235531.png]]![[Pasted image 20250419235537.png]]![[Pasted image 20250419235544.png]]![[Pasted image 20250419235833.png]]
![[Pasted image 20250420000606.png]]
此处，由于归一化时，z方向除以的是far-near，far为-2，near为+2，所以z坐标数值变了相反数（赵新政说的）
![[Pasted image 20250420001604.png]]
![[Pasted image 20250420001615.png]]
![[Pasted image 20250420003822.png]]![[Pasted image 20250420145354.png]]
![[Pasted image 20250420161823.png]]![[Pasted image 20250420162145.png]]
![[Pasted image 20250420163531.png]]
![[Pasted image 20250420164558.png]]
![[Pasted image 20250420164528.png]]