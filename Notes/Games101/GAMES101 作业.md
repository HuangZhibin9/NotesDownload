# GAMES101 作业

## 作业1

### 任务一 旋转矩阵

- **get_model_matrix(float rotation_angle):** 逐个元素地构建模型变换矩 阵并返回该矩阵。在此函数中，你只需要实现三维中绕 z 轴旋转的变换矩阵， 而不用处理平移与缩放。

![image-20230515194433534](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515194433534.png)

```c++
Eigen::Matrix4f get_model_matrix(float rotation_angle)
{
    Eigen::Matrix4f model = Eigen::Matrix4f::Identity();

    // TODO: Implement this function
    // Create the model matrix for rotating the triangle around the Z axis.
    // Then return it.

    double rotation = rotation_angle/180*MY_PI;
    Eigen::Matrix4f z_rotate_matrix;
    z_rotate_matrix <<  cos(rotation),-sin(rotation),0,0,
                        sin(rotation),cos(rotation),0,0,
                        0,0,1,0,
                        0,0,0,1;

    model = z_rotate_matrix * model;

    return model;
}
```

### 任务二 投影矩阵

- **get_projection_matrix(float eye_fov, float aspect_ratio, float zNear, float zFar):** 使用给定的参数逐个元素地构建透视投影矩阵并返回 该矩阵。

- 将透视投影变换为正交投影
  $$
  \begin{align}
  M_{persp\rightarrow ortho}&=
  \begin{bmatrix}n& 0& 0& 0\\ 0& n& 0& 0\\ 0& 0& n+f& -nf\\ 0& 0& 1& 0\end{bmatrix}
  \end{align}
  $$

  - 计算n和f

    ```c++
    float n,f;
    n = -zNear;
    f = -zFar;
    ```

  - 计算矩阵

    ```c++
    Eigen::Matrix4f M_persp2ortho;
    M_persp2ortho << n,0,0,0,
                     0,n,0,0,
                     0,0,-n-f,-n*f,
                     0,0,1,0;
    ```

    

- 正交投影矩阵
  $$
  \begin{align}
  M_{ortho}&=\begin{bmatrix}\frac{2}{r-l}& 0& 0& 0\\ 0& \frac{2}{t-b}& 0& 0\\ 0& 0& \frac{2}{n-f}& 0\\ 0& 0& 0& 1\end{bmatrix}
  \begin{bmatrix}1& 0& 0& -\frac{r+L}{2}\\ 0& 1& 0& -\frac{t+b}{2}\\ 0& 0& 1& -\frac{n+f}{2}\\ 0& 0& 0& 1\end{bmatrix}\\\\
  &=\begin{bmatrix}\frac{2}{r-l}& 0& 0& -\frac{r+l}{r-l}\\ 0& \frac{2}{t-b}& 0& -\frac{t+b}{t-b}\\ 0& 0& \frac{2}{n-f}& -\frac{n+f}{n-f}\\ 0& 0& 0& 1\end{bmatrix}
  \end{align}
  $$

  - 计算r,l,t,b

    ```c++
    float t,b,l,r;
    t = zNear*tan(eye_fov/(2*180)*MY_PI);
    b = -t;
    r = aspect_ratio*t;
    l = -r;
    ```

  - 计算矩阵

    ```c++
    Eigen::Matrix4f M_ortho;
    Eigen::Matrix4f M_translate;
    M_translate << 1,0,0,-(l+r)/2,
                   0,1,0,-(t+b)/2,
                   0,0,1,-(n+f)/2,
                   0,0,0,1;
    Eigen::Matrix4f M_scale;
    M_scale << 2/(r-l),0,0,0,
               0,2/(t-b),0,0,
               0,0,2/(n-f),0,
               0,0,0,1;
    M_ortho = M_scale * M_translate;
    ```

    

- 最后的投影矩阵

$$
\begin{align}
M_{per}&=M_{ortho}M_{persp\rightarrow ortho}
\end{align}
$$

```c++
projection = M_ortho * M_persp2ortho;
```

- 整体代码：

  ```c++
  Eigen::Matrix4f get_projection_matrix(float eye_fov, float aspect_ratio,
                                        float zNear, float zFar)
  {
      // Students will implement this function
  
      Eigen::Matrix4f projection = Eigen::Matrix4f::Identity();
  
      // TODO: Implement this function
      // Create the projection matrix for the given parameters.
      // Then return it.
  
      float n,f;
      n = zNear;
      f = zFar;
      Eigen::Matrix4f M_persp2ortho;
      M_persp2ortho << n,0,0,0,
                       0,n,0,0,
                       0,0,-n-f,-n*f,
                       0,0,1,0;
      
      float t,b,l,r;
      t = zNear*tan(eye_fov/(2*180)*MY_PI);
      b = -t;
      r = aspect_ratio*t;
      l = -r;
      Eigen::Matrix4f M_ortho;
      Eigen::Matrix4f M_translate;
      M_translate << 1,0,0,-(l+r)/2,
                     0,1,0,-(t+b)/2,
                     0,0,1,-(n+f)/2,
                     0,0,0,1;
      Eigen::Matrix4f M_scale;
      M_scale << 2/(r-l),0,0,0,
                 0,2/(t-b),0,0,
                 0,0,2/(n-f),0,
                 0,0,0,1;
      M_ortho = M_scale * M_translate;
  
      projection = M_ortho * M_persp2ortho;
      return projection;
  }
  ```

- 效果

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/%E4%BD%9C%E4%B8%9A1_%E6%95%88%E6%9E%9C1.gif" alt="作业1_效果1" style="zoom:67%;" />

### 提高任务 任意轴旋转

- 罗德里格斯旋转公式

  ![image-20230515195300289](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230515195300289.png)

  ```c++
  //全局变量 旋转轴 默认为Z轴
  Eigen::Vector4f axis(0,0,1,0);
  //模型变换
  Eigen::Matrix4f get_model_matrix(float rotation_angle)
  {
      Eigen::Matrix4f model = Eigen::Matrix4f::Identity();
  
      // TODO: Implement this function
      // Create the model matrix for rotating the triangle around the Z axis.
      // Then return it.
   
      //旋转角 弧度
      double rotation = rotation_angle/180*MY_PI;
      //I单位矩阵，N叉乘矩阵，M_rotation旋转矩阵
      Eigen::Matrix4f I,N,M_rotation;
      //旋转轴 n
      Eigen::Vector4f n;
      //旋转轴转置 nt
      Eigen::RowVector4f nt;
      
      n << axis.x(),axis.y(),axis.z(),0;
      nt << axis.x(),axis.y(),axis.z(),0;
      I = Eigen::Matrix4f::Identity();
      N << 0,-n.z(),n.y(),0,
           n.z(),0,-n.x(),0,
           -n.y(),n.x(),0,0,
           0,0,0,1;
      
      //罗德里格斯旋转公式
      M_rotation = cos(rotation)*I + (1-cos(rotation))*n*nt + sin(rotation)*N;
      //齐次坐标使用非齐次坐标公式时，需要将右下角最后一个数归一，否则比例会出错
      M_rotation(3,3) = 1;
      model = M_rotation * model;
  
      return model;
  }
  
  int main () {
      //...
      //程序开始时，输入旋转轴，默认为z轴
      std::cout << "Please input your rotate axis(default 0,0,1):" << std::endl;
      std::string input;
      std::getline(std::cin,input);
      if(!input.empty()){
          std::istringstream iss(input);
          if(!(iss >> axis.x() >> axis.y() >> axis.z())) {
              std::cout << "using default axis" << std::endl;
          }
      }
      
      //...
  }
  ```

  ![1684298355328](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/1684298355328.gif)



## 作业二

### 任务零 投影矩阵

- 将上次作业的投影矩阵复制即可

### 任务一 测试点是否在三角形内

- 方法——叉乘

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230517104813039.png" alt="image-20230517104813039" style="zoom: 67%;" />

```c++
static bool insideTriangle(float x, float y, const Vector3f* _v)
{   
    //按顺序建立三条边向量
    Vector3f ab = _v[1] - _v[0];
    Vector3f bc = _v[2] - _v[1];
    Vector3f ca = _v[0] - _v[2];
    //设置待求点
    Vector3f q(x,y,1);
    //以待求点为终点的三条向量
    Vector3f aq = q-_v[0];
    Vector3f bq = q-_v[1];
    Vector3f cq = q-_v[2];
    
    return (ab.cross(aq).dot(bc.cross(bq))>0 && 
            ab.cross(aq).dot(ca.cross(cq))>0 && 
            bc.cross(bq).dot(ca.cross(cq))>0);
}
```

### 任务二 光栅化

#### Bounding box

- 确定边界

```c++
int x_min = std::min(v[0].x(),std::min(v[1].x(),v[2].x()));
int y_min = std::min(v[0].y(),std::min(v[1].y(),v[2].y()));
int x_max = std::max(v[0].x(),std::max(v[1].x(),v[2].x()));
int y_max = std::max(v[0].y(),std::max(v[1].y(),v[2].y()));
```

#### 采样

- 遍历Bounding box，插值得到每个点的z坐标（目前没有学习到，框架提供，但是需要稍微修改）
- 将z值与深度缓冲中的做比较，z值小于深度缓冲（离摄像机更近），则修改深度缓冲且进行着色

```c++
for(int x = x_min; x <= x_max; x++) {
    for (int y = y_min; y <= y_max; y++)
    {
        if (insideTriangle(x + 0.5, y + 0.5, t.v))
        {
            // Z-Buffer算法
            // 获取当前像素的深度(框架提供)
            auto tmp = computeBarycentric2D(x + 0.5, y + 0.5, t.v);
            float alpha, beta, gamma;
            std::tie(alpha, beta, gamma) = tmp;
            float w_reciprocal = 1.0 / (alpha / v[0].w() + beta / v[1].w() + gamma / v[2].w());
            float z_interpolated = alpha * v[0].z() / v[0].w() + 
                				   beta * v[1].z() / v[1].w() + 
                				   gamma * v[2].z() / v[2].w();
            z_interpolated *= w_reciprocal;
            
            // 深度测试
            if (z_interpolated < depth_buf[get_index(x, y)])// 当前像素在前面
            {
                depth_buf[get_index(x, y)] = z_interpolated;// 更新深度缓存
                set_pixel(Eigen::Vector3f(x,y,1),t.getColor());//着色
            }
        }
	}
}
```

#### 效果

![image-20230519182042534](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230519182042534.png)

#### 问题

- 三角形前后顺序相反
- 原因：框架中，代码漏了一个符号

```c++
void rst::rasterizer::draw(pos_buf_id pos_buffer, ind_buf_id ind_buffer, 
                           col_buf_id col_buffer, Primitive type)
{
        //Viewport transformation
        for (auto & vert : v)
        {
            vert.x() = 0.5*width*(vert.x()+1.0);
            vert.y() = 0.5*height*(vert.y()+1.0);
            //将符号补上
            vert.z() = -(vert.z() * f1 + f2);
        }
}
```

​                             <img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/4VJI6T5GYILHBZ~(30QW)7B.png" alt="img" style="zoom:50%;" /> 

### 提高任务 使用MSAA抗锯齿

- MSAA的核心就是把一个像素分为 $n\times n$ 的样本

`rasterizer.hpp`

```c++
std::vector<Eigen::Vector3f> frame_buf;
std::vector<float> depth_buf;

//模仿声明小样本的深度缓冲和颜色缓冲
std::vector<Eigen::Vector3f> frame_sample_buf;
std::vector<float> depth_sample_buf;

int get_index(int x, int y);
//模仿声明样本的取址方法
int get_sample_index(int x, int y);
```

`rasterizer.cpp`

```c++
//补充构造函数
rst::rasterizer::rasterizer(int w, int h) : width(w), height(h)
{
    frame_buf.resize(w * h);
    depth_buf.resize(w * h);
    //补充样本缓冲的初始化
    frame_sample_buf.resize(w * h * 4);
    depth_sample_buf.resize(w * h * 4);

}
//补充清空函数
void rst::rasterizer::clear(rst::Buffers buff)
{
    if ((buff & rst::Buffers::Color) == rst::Buffers::Color)
    {
        std::fill(frame_buf.begin(), frame_buf.end(), Eigen::Vector3f{0, 0, 0});
        //清空样本的颜色缓冲
        std::fill(frame_sample_buf.begin(), frame_sample_buf.end(), Eigen::Vector3f{0, 0, 0});
    }
    if ((buff & rst::Buffers::Depth) == rst::Buffers::Depth)
    {
        std::fill(depth_buf.begin(), depth_buf.end(), std::numeric_limits<float>::infinity());
        //清空样本的深度缓冲
        std::fill(depth_sample_buf.begin(), depth_sample_buf.end(), std::numeric_limits<float>::infinity());
    }
}
//定义取址函数
int rst::rasterizer::get_sample_index(int x, int y)
{
    return (height*2-1-y)*width*2 + x;
}
//定义光栅化函数
void rst::rasterizer::rasterize_triangle(const Triangle& t) {
    auto v = t.toVector4();
    
    // Bounding box
    int x_min = std::min(v[0].x(),std::min(v[1].x(),v[2].x()));
    int y_min = std::min(v[0].y(),std::min(v[1].y(),v[2].y()));
    int x_max = std::max(v[0].x(),std::max(v[1].x(),v[2].x()));
    int y_max = std::max(v[0].y(),std::max(v[1].y(),v[2].y()));
	//定义一个像素中，四个小样本的step间距
    std::vector<Vector2f> sample_step {
        {0.25,0.25},
        {0.75,0.25},
        {0.25,0.75},
        {0.75,0.75}
    };
	//遍历Bounding box
    for(int x = x_min; x <= x_max; ++x) {
        for(int y = y_min; y <= y_max; ++y) {
            //记录是否通过了深度测试
            int flag_z = 0;
            //遍历一个像素中的每一个小样本
            for(int i = 0; i < 4; ++i) {
                //如果这个小样本在三角形内
                if(insideTriangle(x+sample_step[i][0], y+sample_step[i][1], t.v)) {
                    //获得当前点的深度值，由框架提供，暂未学习
                    float alpha;
                    float beta;
                    float gamma;
                    auto temp = computeBarycentric2D(x+sample_step[i][0], y+sample_step[i][1], t.v);
                    std::tie(alpha,beta,gamma) = temp;
                    float w_reciprocal = 1.0/(alpha / v[0].w() + beta / v[1].w() + gamma / v[2].w());
                    float z_interpolated = alpha * v[0].z() / v[0].w() + 
                        				   beta * v[1].z() / v[1].w() + 
                        				   gamma * v[2].z() / v[2].w();
                    z_interpolated *= w_reciprocal;
					//如果该小样本通过了深度测试
                    if(z_interpolated < depth_sample_buf[get_sample_index(x*2 + i%2,y*2 + i/2)]) {
                        //修改样本深度缓冲
                        depth_sample_buf[get_sample_index(x*2 + i%2,y*2 + i/2)] = z_interpolated;
                        //修改样本颜色缓冲，但是只取1/4的值，用于平均四个点的颜色
                        frame_sample_buf[get_sample_index(x*2 + i%2,y*2 + i/2)] = t.getColor()/4;
                        //修改标志
                        flag_z = 1;
                    }
                }
            }
            //如果该像素点有一个样本通过了深度测试
            if(flag_z) {
                //颜色为该像素四个样本的平均
                Eigen::Vector3f color = frame_sample_buf[get_sample_index(x*2,y*2)]+
                    					frame_sample_buf[get_sample_index(x*2+1,y*2)]+
                                        frame_sample_buf[get_sample_index(x*2,y*2+1)]+
                                        frame_sample_buf[get_sample_index(x*2+1,y*2+1)];
                //着色
                set_pixel(Eigen::Vector3f(x,y,1),color);
            }
            
        }
    }  
}
```

- 效果

![img](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/K3WO6A8%60X3%60CRK_B9$%7D@VCE.png)

- 对比

<img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230519185346564.png" alt="image-20230519185346564" style="zoom:90%;" /><img src="https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230519185433905.png" alt="image-20230519185433905" style="zoom:50%;" />



## 作业三

作业参考：[03 渲染管线和着色（含提高） (yuque.com)](https://www.yuque.com/gaoshanliushui-mbfny/sst4c5/hr2dgd)

### 渲染管线

#### 输入处理

- 输入处理主要集中在`main.cpp->main()`中
- 1、记录每个小三角形的顶点信息，小三角形为光栅化的单位

```c++
//main.cpp->main()

//文件目录
std::string filename = "output.png";
objl::Loader Loader;
std::string obj_path = "../models/spot/";

// 加载.obj文件
bool loadout = Loader.LoadFile("../models/spot/spot_triangulated_good.obj");
//遍历文件的每一个面
for (auto mesh : Loader.LoadedMeshes)
{
    //遍历每个面中的每三个顶点
    for (int i = 0; i < mesh.Vertices.size(); i += 3)
    {
        Triangle *t = new Triangle();
        //将每个小三角形的顶点信息记录在新建的Triangle类对象 t中
        for (int j = 0; j < 3; j++)
        {
            //记录顶点位置
            t->setVertex(j, Vector4f(mesh.Vertices[i + j].Position.X, 
                                     mesh.Vertices[i + j].Position.Y, 
                                     mesh.Vertices[i + j].Position.Z, 1.0));
            //记录顶点法线
            t->setNormal(j, Vector3f(mesh.Vertices[i + j].Normal.X, 
                                     mesh.Vertices[i + j].Normal.Y, 
                                     mesh.Vertices[i + j].Normal.Z));
            //记录顶点纹理
            t->setTexCoord(j, Vector2f(mesh.Vertices[i + j].TextureCoordinate.X, 
                                       mesh.Vertices[i + j].TextureCoordinate.Y));
        }
        //把三角形信息加入列表
        TriangleList.push_back(t);
    }
}
```

- 2、构造光栅化对象，把输入参数的光栅化shader传入光栅化调用的funtion中

```c++
//main.cpp->main()

//构造光栅化对象
rst::rasterizer r(700, 700);
//记录纹理到光栅化
auto texture_path = "hmap.jpg";
r.set_texture(Texture(obj_path + texture_path));
//定义shader的function对象
std::function<Eigen::Vector3f(fragment_shader_payload)> active_shader = phong_fragment_shader;
//输入处理 用参数设置shader的function对象或设置纹理
if (argc >= 2)
{
    command_line = true;
    filename = std::string(argv[1]);

    if (argc == 3 && std::string(argv[2]) == "texture")
    {
        std::cout << "Rasterizing using the texture shader\n";
        active_shader = texture_fragment_shader;
        texture_path = "spot_texture.png";
        r.set_texture(Texture(obj_path + texture_path));
    }
    else if (argc == 3 && std::string(argv[2]) == "normal")
    {
        std::cout << "Rasterizing using the normal shader\n";
        active_shader = normal_fragment_shader;
    }
    else if (argc == 3 && std::string(argv[2]) == "phong")
    {
        std::cout << "Rasterizing using the phong shader\n";
        active_shader = phong_fragment_shader;
    }
    else if (argc == 3 && std::string(argv[2]) == "bump")
    {
        std::cout << "Rasterizing using the bump shader\n";
        active_shader = bump_fragment_shader;
    }
    else if (argc == 3 && std::string(argv[2]) == "displacement")
    {
        std::cout << "Rasterizing using the bump shader\n";
        active_shader = displacement_fragment_shader;
    }
}
```

- 3、根据算出的MVP变换矩阵并通过`draw()`函数正式进入渲染管线流程

```c++
//main.cpp->main()

//设置顶点着色器
r.set_vertex_shader(vertex_shader);
//设置片元着色器
r.set_fragment_shader(active_shader);

int key = 0;
int frame_count = 0;

if (command_line)
{
    //清空缓冲区
    r.clear(rst::Buffers::Color | rst::Buffers::Depth);
    //设置MVP变换矩阵
    r.set_model(get_model_matrix(angle));
    r.set_view(get_view_matrix(eye_pos));
    r.set_projection(get_projection_matrix(45.0, 1, 0.1, 50));

    //应用变换矩阵 并进行光栅化、片元处理、帧缓冲
    r.draw(TriangleList);
    cv::Mat image(700, 700, CV_32FC3, r.frame_buffer().data());
    image.convertTo(image, CV_8UC3, 1.0f);
    cv::cvtColor(image, image, cv::COLOR_RGB2BGR);

    cv::imwrite(filename, image);

    return 0;
}
```

#### 顶点、三角形处理阶段

```c++
//rasterizer.cpp->draw()

void rst::rasterizer::draw(std::vector<Triangle *> &TriangleList) {
	//zFar和zNear距离的一半
    float f1 = (50 - 0.1) / 2.0;
    //zFar和zNear的中心点坐标z
    float f2 = (50 + 0.1) / 2.0;
	
    //计算MVP变换矩阵
    Eigen::Matrix4f mvp = projection * view * model;
    //遍历每个小三角形
    for (const auto& t:TriangleList)
    {
        Triangle newtri = *t;
		
        //计算viewspace_pos，其中viewspace_pos的坐标是经过MV变换，但是还未进行P投影变换
        //即默认在观察空间坐标系，而不是世界空间坐标
        
        //记录三角形顶点，MV变换后的坐标，即观察空间坐标（摄像机坐标系）
        std::array<Eigen::Vector4f, 3> mm {
                (view * model * t->v[0]),
                (view * model * t->v[1]),
                (view * model * t->v[2])
        };
		//将变换后的坐标存入viewspace_pos
        std::array<Eigen::Vector3f, 3> viewspace_pos;
        std::transform(mm.begin(), mm.end(), viewspace_pos.begin(), [](auto& v) {
            return v.template head<3>();
        });
		//得到MVP变换后的坐标
        Eigen::Vector4f v[] = {
                mvp * t->v[0],
                mvp * t->v[1],
                mvp * t->v[2]
        };
        //换算齐次坐标归一化
        for (auto& vec : v) {
            vec.x()/=vec.w();
            vec.y()/=vec.w();
            vec.z()/=vec.w();
        }

        //计算mv变换的逆转置矩阵，得到观察空间的法线，为何用逆转置下面证明
        //因为光线作用是在view_space下进行的
        Eigen::Matrix4f inv_trans = (view * model).inverse().transpose();
        Eigen::Vector4f n[] = {
                inv_trans * to_vec4(t->normal[0], 0.0f),
                inv_trans * to_vec4(t->normal[1], 0.0f),
                inv_trans * to_vec4(t->normal[2], 0.0f)
        };

        //视口变换
        for (auto & vert : v)
        {
            vert.x() = 0.5*width*(vert.x()+1.0);
            vert.y() = 0.5*height*(vert.y()+1.0);
            //为了深度测试保留Z值
            //(透视)投影变换最后一步是把正交投影变换到正则正方体
            //这一步是把正则正方体的Z值还原到正交投影时的Z值
            vert.z() = vert.z() * f1 + f2;
        }
		//记录MVP变换视口变换后的顶点坐标
        //完成顶点变换，变换到屏幕空间
        for (int i = 0; i < 3; ++i)
        {
            //screen space coordinates
            newtri.setVertex(i, v[i]);
        }
		//记录顶点的法向量
        for (int i = 0; i < 3; ++i)
        {
            //view space normal
            newtri.setNormal(i, n[i].head<3>());
        }
		//设置颜色
        newtri.setColor(0, 148,121.0,92.0);
        newtri.setColor(1, 148,121.0,92.0);
        newtri.setColor(2, 148,121.0,92.0);

        //对小三角形进行光栅化
        //传入viewspace_pos坐标，因为光线作用是在观察空间下的
        rasterize_triangle(newtri, viewspace_pos);
    }
}
```

- 法向量逆转置证明

  - 首先直接使用MV变换法向量，可能会的到错误的观察空间法向量，如下：

    ![img](http://www.cppblog.com/images/cppblog_com/summericeyl/normalmat1.gif) ![img](http://www.cppblog.com/images/cppblog_com/summericeyl/normalmat2.gif)

  - 需要使用MV变换矩阵的逆转置，下证：
    $$
    由于法线N与切线T一定垂直，即:T\cdot N=0,转换后的N'\cdot T'=0\\
    假设矩阵G是法线N到观察空间的变换矩阵，M是正常mv变换矩阵，把切线转换到观察空间\\
    则：N'\cdot T'=(GN)\cdot(MT)=0；\\
    点积用矩阵表示为:(GN)\cdot(MT)=(GN)^T(MT)=N^TG^TMT\\
    即:N^TG^TMT=0,由于N\cdot T=N^TT=0\\
    推测G^TM=0\\
    即:G^T=M^{-1}\\
    即:G=(M^{-1})^T\\
    所以法线从世界空间转换到观察空间的变换矩阵为MV矩阵的逆转置
    $$

- 顶点进行MV变换，得到观察空间中的坐标`viewspace_pos`，这个坐标用于后续光线作用。光线作用发生在观察空间
- 顶点进行MVP变换+视口变换，把三维空间的顶点坐标变换至屏幕空间
  - 在齐次变换中，w并未发生变换，它记录了原本的z值信息，用于后续插值
  - 视口变换中，(x,y)变换至屏幕空间，但是Z值还原为初始Z值，用于深度测试。
  - 透视投影变换=变换到正交投影 -> 变换到正则立方体，把Z值变换会正交投影时的Z值
- 记录观察空间的顶点法向量，因为光线作用在观察空间

![image-20230523112836340](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230523112836340.png)

![image-20230523112916631](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230523112916631.png)

- 视口变换中的Z还原证明

  ```c++
  // 视口变换
  for (auto &vert : v)
  {
      vert.x() = 0.5 * width * (vert.x() + 1.0);
      vert.y() = 0.5 * height * (vert.y() + 1.0);
      // 为了深度测试保留Z值
      //(透视)投影变换最后一步是把正交投影变换到正则正方体
      // 这一步是把正则正方体的Z值还原到正交投影时的Z值
      vert.z() = vert.z() * f1 + f2;
  }
  ```

- 视口变换矩阵：
  $$
  M_{viewport}=\begin{bmatrix}
  \frac{width}{2}&0&0&\frac{width}{2}\\
  0&\frac{height}{2}&0&\frac{height}{2}\\
  0&0&1&0\\
  0&0&0&1
  \end{bmatrix}
  $$

- 其中X,Y变换到屏幕空间，但是Z保留了，只不过这里的Z是正则立方体中的Z值，我们需要的是变换到正则立方体前的正交投影的值

- 正交投影变换矩阵：
  $$
  \begin{align}
  M_{ortho}&=\begin{bmatrix}\frac{2}{r-l}& 0& 0& 0\\ 0& \frac{2}{t-b}& 0& 0\\ 0& 0& \frac{2}{n-f}& 0\\ 0& 0& 0& 1\end{bmatrix}
  \begin{bmatrix}1& 0& 0& -\frac{r+L}{2}\\ 0& 1& 0& -\frac{t+b}{2}\\ 0& 0& 1& -\frac{n+f}{2}\\ 0& 0& 0& 1\end{bmatrix}\\\\
  &=\begin{bmatrix}\frac{2}{r-l}& 0& 0& -\frac{r+l}{r-l}\\ 0& \frac{2}{t-b}& 0& -\frac{t+b}{t-b}\\ 0& 0& \frac{2}{n-f}& -\frac{n+f}{n-f}\\ 0& 0& 0& 1\end{bmatrix}
  \end{align}
  $$

- 所以变换后的$z'=\frac{2}{n-f}z-\frac{n+f}{n-f}$

- 所以$z = \frac{n-f}{2}z'+\frac{n+f}{2}$

#### 光栅化、片元处理、帧缓冲阶段

```c++
//rasterizer.cpp

//光栅化
void rst::rasterizer::rasterize_triangle(const Triangle& t, const std::array<Eigen::Vector3f, 3>& view_pos)
{
    //最大问题所在，导致v.w()=1.0f，下面解释
    auto v = t.toVector4();
    
    //Bounding Box
    int x_min = m_min(v[0].x(),v[1].x(),v[2].x());
    int y_min = m_min(v[0].y(),v[1].y(),v[2].y());
    int x_max = m_max(v[0].x(),v[1].x(),v[2].x());
    int y_max = m_max(v[0].y(),v[1].y(),v[2].y());


    //Sampling
    for(int x = x_min; x <= x_max; ++x) {
        for(int y = y_min; y <= y_max; ++y) {
            //若点在三角形内
            if(insideTriangle(x+0.5, y+0.5, t.v)){
                //求重心坐标
                //(注意此时得到的中心坐标是有误差的，需要进行透视矫正)
                auto temp = computeBarycentric2D(x+0.5, y+0.5, t.v);
                float alpha,beta,gamma;
                std::tie(alpha,beta,gamma) = temp;
                
                //矫正得到正确的Z值
                float Z = 1.0/(alpha/v[0].w() + beta/v[1].w() + gamma/v[2].w());
                
                //对任意属性I= (alpha*I1/v[0].z + beta*I2/v[1].z + gamma*I3/v[2].z)*Z
                //代入属性I 为深度值得下式
                float zp = alpha * v[0].z() / v[0].w() + 
                    	   beta * v[1].z() / v[1].w() + 
                    	   gamma * v[2].z() / v[2].w();
                zp *= Z;
                
                //如果通过了深度测试
                if(zp < depth_buf[get_index(x,y)]) {
                    //修改深度缓冲
                    depth_buf[get_index(x,y)] = zp;
                    //插值得到颜色
                    auto interpolated_color = interpolate(alpha,beta,gamma,//重心坐标，但是这里没有矫正，有误差
                                                          t.color[0],t.color[1],t.color[2],//插值的属性
                                                          1);//这个东西下面解释，此处因为没有矫正，所以为1
                    //插值得到法线
                    auto interpolated_normal = interpolate(alpha,beta,gamma,
                                                           t.normal[0],t.normal[1],t.normal[2],
                                                           1);
                    //插值得到UV坐标
                    auto interpolated_texcoords = interpolate(alpha,beta,gamma,
                                                       		t.tex_coords[0],t.tex_coords[1],t.tex_coords[2],
                                                              1);
                    //插值得到viewspace坐标
                    auto interpolated_shadingcoords = interpolate(alpha,beta,gamma,
                                                                  view_pos[0],view_pos[1],view_pos[2],
                                                                  1);
					//传入shader参数
                    fragment_shader_payload payload( interpolated_color, 
                                                    interpolated_normal.normalized(), 
                                                    interpolated_texcoords, 
                                                    texture ? &*texture : nullptr);
                    //传入shader需要的坐标，此处传入的观察空间坐标
                    payload.view_pos = interpolated_shadingcoords;
                    //接收颜色
                    auto pixel_color = fragment_shader(payload);
                    //着色
                    set_pixel(Vector2i(x,y),pixel_color);
                }
            }
        }
    }
}
//自定义取最大最小值
int m_max(float i,float j,float k) {
    float max = i;
    max = max>j?max : j;
    max = max>k?max : k;
    return max;
}
int m_min(float i,float j,float k) {
    float min = i;
    min = min<j?min : j;
    min = min<k?min : k;
    return min;
}
//判断是否在三角形内
static bool insideTriangle(float x, float y, const Vector4f* _v){
    Vector3f v[3];
    for(int i=0;i<3;i++)
        v[i] = {_v[i].x(),_v[i].y(), 1.0};
    Vector3f f0,f1,f2;
    f0 = v[1].cross(v[0]);
    f1 = v[2].cross(v[1]);
    f2 = v[0].cross(v[2]);
    Vector3f p(x,y,1.);
    if((p.dot(f0)*f0.dot(v[2])>0) && (p.dot(f1)*f1.dot(v[0])>0) && (p.dot(f2)*f2.dot(v[1])>0))
        return true;
    return false;
}
//求重心坐标，未矫正
static std::tuple<float, float, float> computeBarycentric2D(float x, float y, const Vector4f* v){
    float c1 = (x*(v[1].y() - v[2].y()) + (v[2].x() - v[1].x())*y + v[1].x()*v[2].y() - v[2].x()*v[1].y()) / (v[0].x()*(v[1].y() - v[2].y()) + (v[2].x() - v[1].x())*v[0].y() + v[1].x()*v[2].y() - v[2].x()*v[1].y());
    float c2 = (x*(v[2].y() - v[0].y()) + (v[0].x() - v[2].x())*y + v[2].x()*v[0].y() - v[0].x()*v[2].y()) / (v[1].x()*(v[2].y() - v[0].y()) + (v[0].x() - v[2].x())*v[1].y() + v[2].x()*v[0].y() - v[0].x()*v[2].y());
    float c3 = (x*(v[0].y() - v[1].y()) + (v[1].x() - v[0].x())*y + v[0].x()*v[1].y() - v[1].x()*v[0].y()) / (v[2].x()*(v[0].y() - v[1].y()) + (v[1].x() - v[0].x())*v[2].y() + v[0].x()*v[1].y() - v[1].x()*v[0].y());
    return {c1,c2,c3};
}
//插值求属性
static Eigen::Vector3f interpolate(float alpha, float beta, float gamma, const Eigen::Vector3f& vert1, const Eigen::Vector3f& vert2, const Eigen::Vector3f& vert3, float weight)
{
    return (alpha * vert1 + beta * vert2 + gamma * vert3) / weight;
}
```

- 光栅化
  - bounding box
  - 采样，判断是否在三角形内，进行深度测试
  - 通过深度测试后，进行插值
- 片元处理阶段
  - 把得到的三角形插值新的传入shader，并调用片元着色器
- 帧缓冲
  - 把着色器得到的结果传入frame_buffer（set_pixel）



**重心坐标矫正以及插值解释**

[《GAMES101》作业框架问题详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/509902950)

#### 显示

```c++
//main.cpp->main()

cv::Mat image(700, 700, CV_32FC3, r.frame_buffer().data());
image.convertTo(image, CV_8UC3, 1.0f);
cv::cvtColor(image, image, cv::COLOR_RGB2BGR);

cv::imwrite(filename, image);
```

- Opencv自带的方法

### 着色模型

#### normal_fragment_shader

```c++
Eigen::Vector3f normal_fragment_shader(const fragment_shader_payload& payload)
{
    //取出法向量，归一化后在[-1,1]中，加1.0f在除以2，即在[0,1]中
    Eigen::Vector3f return_color = (payload.normal.head<3>().normalized() + 
                                    Eigen::Vector3f(1.0f, 1.0f, 1.0f)) / 2.f;
    Eigen::Vector3f result;
    //乘以255作为RGB值
    result << return_color.x() * 255, return_color.y() * 255, return_color.z() * 255;
    return result;
}
```

- 渲染结果

![output_normal](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_normal.png)

#### phong_fragment_shader

```c++
Eigen::Vector3f phong_fragment_shader(const fragment_shader_payload& payload)
{
    //环境光、漫反射、高光系数
    Eigen::Vector3f ka = Eigen::Vector3f(0.005, 0.005, 0.005);
    Eigen::Vector3f kd = payload.color;
    Eigen::Vector3f ks = Eigen::Vector3f(0.7937, 0.7937, 0.7937);
	//光源信息
    auto l1 = light{{20, 20, 20}, {500, 500, 500}};
    auto l2 = light{{-20, 20, 0}, {500, 500, 500}};
	//光源
    std::vector<light> lights = {l1, l2};
    //环境光强度
    Eigen::Vector3f amb_light_intensity{10, 10, 10};
    //相机位置
    Eigen::Vector3f eye_pos{0, 0, 10};
	//高光指数
    float p = 150;
	//点的颜色
    Eigen::Vector3f color = payload.color;
    //点的位置(viewspace_pos)
    Eigen::Vector3f point = payload.view_pos;
    //点的法向量
    Eigen::Vector3f normal = payload.normal;
	//光照着色结果
    Eigen::Vector3f result_color = {0, 0, 0};
	//环境光计算 ambient = ka*Ia
    Eigen::Vector3f ambient = ka.cwiseProduct(amb_light_intensity);
    //遍历每个光源
    for (auto& light : lights)
    {   //光源方向、观察方向
        Eigen::Vector3f light_dir = (light.position - point).normalized();
        Eigen::Vector3f view_dir = (eye_pos - point).normalized();
        //半程向量
        Eigen::Vector3f half_dir = (view_dir + light_dir).normalized();
        //光强
        Eigen::Vector3f I = light.intensity;
        //r平方
        float r2 = (light.position - point).dot(light.position - point);
		//计算漫反射 diffuse = kd*(I/r2)*max(0,n*l)
        Eigen::Vector3f diffuse = kd.cwiseProduct(I/r2)*std::max(0.0f,normal.dot(light_dir));
        //计算高光 specular = ks*(I/r2)*(n*h)^p
        Eigen::Vector3f specular = ks.cwiseProduct(I/r2)*std::pow(std::max(0.0f,normal.dot(half_dir)),p);
		//着色结果 = ambient+diffuse+specular
        result_color += ambient + diffuse + specular;
        // TODO: For each light source in the code, calculate what the *ambient*, *diffuse*, and *specular* 
        // components are. Then, accumulate that result on the *result_color* object.
    }
    return result_color * 255.f;
}
```

- 渲染结果：

![output_phong](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_phong.png)

#### texture_fragment_shader

```c++
Eigen::Vector3f texture_fragment_shader(const fragment_shader_payload& payload)
{
    //着色结果
    Eigen::Vector3f return_color = {0, 0, 0};
    //如果texture不为空 ，则取出该点对应的UV纹理作为texture_color
    if (payload.texture)
    {
        // TODO: Get the texture value at the texture coordinates of the current fragment
        return_color = payload.texture->getColorBiliner(payload.tex_coords.x(),payload.tex_coords.y());
    }
    Eigen::Vector3f texture_color;
    texture_color << return_color.x(), return_color.y(), return_color.z();
	
    //环境光系数
    Eigen::Vector3f ka = Eigen::Vector3f(0.005, 0.005, 0.005);
    //texture_color作为漫反射系数
    Eigen::Vector3f kd = texture_color / 255.f;
    //高光系数
    Eigen::Vector3f ks = Eigen::Vector3f(0.7937, 0.7937, 0.7937);
	//光源信息
    auto l1 = light{{20, 20, 20}, {500, 500, 500}};
    auto l2 = light{{-20, 20, 0}, {500, 500, 500}};
    std::vector<light> lights = {l1, l2};
    Eigen::Vector3f amb_light_intensity{10, 10, 10};
    //相机位置
    Eigen::Vector3f eye_pos{0, 0, 10};
	//高光指数
    float p = 150;
	//点的信息
    Eigen::Vector3f color = texture_color;
    Eigen::Vector3f point = payload.view_pos;
    Eigen::Vector3f normal = payload.normal;
	//着色结果
    Eigen::Vector3f result_color = {0, 0, 0};
	//环境光计算
    Eigen::Vector3f ambient = ka.cwiseProduct(amb_light_intensity);
    for (auto& light : lights)
    {   
        //光源方向、观察方向、半程向量、光强、r平方
        Eigen::Vector3f light_dir = (light.position - point).normalized();
        Eigen::Vector3f view_dir = (eye_pos - point).normalized();
        Eigen::Vector3f half_dir = (view_dir + light_dir).normalized();
        Eigen::Vector3f I = light.intensity;
        float r2 = (light.position - point).dot(light.position - point);
		//漫反射计算，漫反射系数为纹理颜色
        Eigen::Vector3f diffuse = kd.cwiseProduct(I/r2)*std::max(0.0f,normal.dot(light_dir));
        //高光计算
        Eigen::Vector3f specular = ks.cwiseProduct(I/r2)*std::pow(std::max(0.0f,normal.dot(half_dir)),p);

        result_color += ambient + diffuse + specular;
    }
    return result_color * 255.f;
}
```

- 渲染结果

![output_texture](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_texture.png)

#### bump_fragment_shader

```c++
Eigen::Vector3f bump_fragment_shader(const fragment_shader_payload& payload)
{
    //环境光、漫反射、高光系数
    Eigen::Vector3f ka = Eigen::Vector3f(0.005, 0.005, 0.005);
    Eigen::Vector3f kd = payload.color;
    Eigen::Vector3f ks = Eigen::Vector3f(0.7937, 0.7937, 0.7937);
	//光源信息
    auto l1 = light{{20, 20, 20}, {500, 500, 500}};
    auto l2 = light{{-20, 20, 0}, {500, 500, 500}};
    std::vector<light> lights = {l1, l2};
    Eigen::Vector3f amb_light_intensity{10, 10, 10};
    Eigen::Vector3f eye_pos{0, 0, 10};

    float p = 150;

    Eigen::Vector3f color = payload.color; 
    Eigen::Vector3f point = payload.view_pos;
    Eigen::Vector3f normal = payload.normal;

	//影响系数
    float kh = 0.2, kn = 0.1;
	//计算切线tangent，原理不清楚，之后会将
    float x = normal.x();
    float y = normal.y();
    float z = normal.z();
    Eigen::Vector3f t{x*y/std::sqrt(x*x+z*z),
                      sqrt(x*x+y*y),
                      z*y/std::sqrt(x*x+y*y)};
    //计算副切线
    Eigen::Vector3f b = normal.cross(t);
    //计算TBN矩阵用于把法线从切线空间转换至观察空间
    Eigen::Matrix3f TBN;
    TBN << t.x(), b.x(), x,
           t.y(), b.y(), y,
           t.z(), b.z(), z;
    float u = payload.tex_coords.x();
    float v = payload.tex_coords.y();
    float w = payload.texture->width;
    float h = payload.texture->height;
	//计算dU、dV 法线为(-dU,-dV,1)
    float dU = kh*kn*(payload.texture->getColor(u+1.0f/w,v).norm()-payload.texture->getColor(u,v).norm());
    float dV = kh*kn*(payload.texture->getColor(u,v+1.0f/h).norm()-payload.texture->getColor(u,v).norm());
    Eigen::Vector3f ln {-dU, -dV, 1};
    //转换至观察空间
    normal = TBN * ln;
    //用法线作为颜色值
    Eigen::Vector3f result_color = {0, 0, 0};
    result_color = normal.normalized();
    return result_color * 255.f;
}
```

- 渲染结果

![output_bumpmap](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_bumpmap.png)

#### displacement_fragment_shader

```c++
Eigen::Vector3f displacement_fragment_shader(const fragment_shader_payload& payload)
{
    
    Eigen::Vector3f ka = Eigen::Vector3f(0.005, 0.005, 0.005);
    Eigen::Vector3f kd = payload.color;
    Eigen::Vector3f ks = Eigen::Vector3f(0.7937, 0.7937, 0.7937);

    auto l1 = light{{20, 20, 20}, {500, 500, 500}};
    auto l2 = light{{-20, 20, 0}, {500, 500, 500}};

    std::vector<light> lights = {l1, l2};
    Eigen::Vector3f amb_light_intensity{10, 10, 10};
    Eigen::Vector3f eye_pos{0, 0, 10};

    float p = 150;

    Eigen::Vector3f color = payload.color; 
    Eigen::Vector3f point = payload.view_pos;
    Eigen::Vector3f normal = payload.normal;

    float kh = 0.2, kn = 0.1;
    
    float x = normal.x();
    float y = normal.y();
    float z = normal.z();
    Eigen::Vector3f t{x*y/std::sqrt(x*x+z*z),
                      sqrt(x*x+y*y),
                      z*y/std::sqrt(x*x+y*y)};
    Eigen::Vector3f b = normal.cross(t);
    Eigen::Matrix3f TBN;
    TBN << t.x(), b.x(), x,
           t.y(), b.y(), y,
           t.z(), b.z(), z;
    float u = payload.tex_coords.x();
    float v = payload.tex_coords.y();
    float w = payload.texture->width;
    float h = payload.texture->height;

    float dU = kh*kn*(payload.texture->getColor(u+1.0f/w,v).norm()-payload.texture->getColor(u,v).norm());
    float dV = kh*kn*(payload.texture->getColor(u,v+1.0f/h).norm()-payload.texture->getColor(u,v).norm());
    Eigen::Vector3f ln {-dU, -dV, 1};
    
	//与凹凸贴图不同的地方，此处直接修改了点的位置
    point += (kn*normal*payload.texture->getColor(u,v).norm());
    normal = TBN * ln;
	//其他与blinn_phong一样
    Eigen::Vector3f ambient = ka.cwiseProduct(amb_light_intensity);
    Eigen::Vector3f result_color = {0, 0, 0};
    for (auto& light : lights)
    {   
        Eigen::Vector3f light_dir = (light.position - point).normalized();
        Eigen::Vector3f view_dir = (eye_pos - point).normalized();
        Eigen::Vector3f half_dir = (view_dir + light_dir).normalized();
        Eigen::Vector3f I = light.intensity;
        float r2 = (light.position - point).dot(light.position - point);

        Eigen::Vector3f diffuse = kd.cwiseProduct(I/r2)*std::max(0.0f,normal.dot(light_dir));
        Eigen::Vector3f specular = ks.cwiseProduct(I/r2)*std::pow(std::max(0.0f,normal.dot(half_dir)),p);

        result_color += ambient + diffuse + specular;
    }
    return result_color * 255.f;
}
```

- 渲染结果

![output_displacement](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_displacement.png)

### 提高

- 在texture中使用双线性插值得到纹理颜色

```c++
//正常获取颜色的方法
Eigen::Vector3f getColor(float u, float v)
{
    u = std::fmin(1, std::fmax(u, 0));
    v = std::fmin(1, std::fmax(v, 0));

    auto u_img = u * width;
    auto v_img = (1 - v) * height;
    auto color = image_data.at<cv::Vec3b>((int)v_img, (int)u_img);
    return Eigen::Vector3f(color[0], color[1], color[2]);
}
//双线性插值
Eigen::Vector3f getColorBiliner(float u, float v)
{
    //对uv坐标限定在[0,1]中
    u = std::fmin(1, std::fmax(u, 0));
    v = std::fmin(1, std::fmax(v, 0));
	//获得实际上对应的点
    auto u_img = u * width;
    auto v_img = (1 - v) * height;
	//获取该坐标最近的四个点
    float u0 = std::fmin(width, floor(std::fmax(u_img - 0.5f, 0) + 0.5f));
    float u1 = std::fmin(width, floor(std::fmax(u_img + 0.5f, 0) + 0.5f));
    float v0 = std::fmin(height, floor(std::fmax(v_img - 0.5f, 0) + 0.5f));
    float v1 = std::fmin(height, floor(std::fmax(v_img + 0.5f, 0) + 0.5f));
    //步长
    float u_step = u1 - u0;
    float v_step = v1 - v0;
	//四个点的颜色
    auto color_00 = image_data.at<cv::Vec3b>(v0, u0);
    auto color_01 = image_data.at<cv::Vec3b>(v0, u1);
    auto color_10 = image_data.at<cv::Vec3b>(v1, u0);
    auto color_11 = image_data.at<cv::Vec3b>(v1, u1);
	//先进行两次插值
    auto color_0 = color_00 + (color_01 - color_00) * (u_img - u0) / u_step;
    auto color_1 = color_10 + (color_11 - color_10) * (u_img - u0) / u_step;
    //在进行一次插值
    auto color = color_0 + (color_1 - color_0) * (v_img - v0) / v_step;
	//返回最后颜色
    return Eigen::Vector3f(color[0], color[1], color[2]);
}
```

![image-20230520200807640](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230520200807640.png)

- 渲染结果

![output_texture_biliner](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/output_texture_biliner.png)

- 对比 左边双线性插值、右边最近邻

![image-20230524123814614](https://picgo-huangzhibin.oss-cn-hangzhou.aliyuncs.com/image/image-20230524123814614.png)