# 🌌 Solar System Simulation 🌍☀️  

## 🚀 Overview  
This project is a **3D graphical simulation of the Solar System**, created using **OpenGL**, **GLSL shaders**, and **GLM** for matrix transformations. It aims to accurately represent celestial bodies with proper scaling, lighting, and texture mapping.

The project demonstrates:
-  **3D object rendering** using sphere-based geometry  
-  **Lighting models (ambient, diffuse, specular)**
-  **Shadow mapping for realistic effects**
-  **Texture mapping using OpenGL**
-  **Real-time camera controls for navigation**

### 🪐 Celestial Bodies Included:
- **☀ Sun**
- **🌍 Earth**
- **🌕 Moon**
- **🪐 Mercury, Venus, Mars**  

Each body is **scaled, positioned, and colored** to closely match real-world proportions.
![Image](https://github.com/user-attachments/assets/da89b20f-9cf3-4d6c-b0b6-b1424ec04289)
![Image](https://github.com/user-attachments/assets/22b91959-044e-46f1-a9dc-bd92ea1ef633)

---

## 🖥️ Technologies Used
- **C++**
- **OpenGL**
- **GLSL (Shaders)**
- **GLM (OpenGL Mathematics)**
- **SOIL (Texture Loading)**
- **GLFW & FreeGLUT (Window & Input Management)**

---

## Object Rendering
For drawing the celestial bodies, I used the same sphere, applying different scalings to modify the size and translations to adjust the position.
The coordinates of the vertices that make up the sphere were calculated using the following formula:
### Sphere Vertex Calculation Formula:
```cpp
float u = U_MIN + parr * step_u;
float v = V_MIN + merid * step_v;
float x_vf = radius * cosf(u) * cosf(v);
float y_vf = radius * cosf(u) * sinf(v);
float z_vf = radius * sinf(u);
```

An example of drawing the planet Earth starting from the base sphere:
```cpp
// PAMANT
matrTrans = glm::mat4(1.0f) * glm::translate(glm::mat4(1.0f),
glm::vec3(10.0f, -600.0f, 0.0f));
matrScale = glm::scale(glm::mat4(1.0f), glm::vec3(0.7f, 0.7f, 0.7f));
glUniform3f(objectColorLoc, 0.0f, 0.0f, 0.63f);
glUniform3f(lightColorLoc, 1.0f, 1.0f, 1.0f);
glUniform3f(lightPosLoc, 0.f, 1000.f, 0.f);
glUniform3f(viewPosLoc, Obsx, Obsy, Obsz);
myMatrix = matrTrans * matrScale;
DrawPlanet(myMatrix);
DrawShadow(myMatrix);
```

### Lightning Ussage
The lighting of the planets is implemented in the shaders for the variable codCol = 0, using ambient, diffuse, and specular light as follows:

```cpp
if (codCol==0)
{
// Ambient
float ambientStrength = 0.1f;
vec3 ambient_light = ambientStrength * lightColor;
vec3 ambient_term= ambient_light * objectColor;
// Diffuse
vec3 norm = normalize(Normal);
vec3 lightDir = normalize(inLightPos - FragPos);
float diff = max(dot(norm, lightDir), 0.0);
vec3 diffuse_light = lightColor;
vec3 diffuse_term = diff * diffuse_light * objectColor;
// Specular
float specularStrength = 0.8f;
float shininess = 100.0f;
vec3 viewDir = normalize(inViewPos - FragPos);
vec3 reflectDir = normalize(reflect(-lightDir, norm));
float spec = pow(max(dot(viewDir, reflectDir), 0.0), shininess);
vec3 specular_light = specularStrength * lightColor;
vec3 specular_term = spec * specular_light * objectColor;
// Culoarea finala
vec3 emission=vec3(0.0, 0.0, 0.0);
vec3 result = emission + (ambient_term + diffuse_term + specular_term);
out_Color = vec4(result, 1.0f);
}
```
The lighting of the Sun is implemented using codCol = 2, applying only ambient light, as it approximately overlaps with the light source for the other celestial bodies.
Additionally, texture mapping is also applied when codCol = 2.
### Shadow implementation
Shadows were applied using a light source close to the Sun and utilizing the shadow matrix.
The shadows are rendered in the shaders using codCol = 1 and the function drawShadow().
![Image](https://github.com/user-attachments/assets/f1134096-1bcd-4660-9ad3-ed1509d77968)

### Object Texturing
Since the Sun is represented as a light source, it is only illuminated using ambient light.
To maintain a realistic effect, we chose to apply texture mapping using a GL_TEXTURE_2D texture.

The texture application and calculation of texture coordinates are implemented in the shaders for codCol = 2.
```cpp
void LoadTexture(void)
{
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_REPEAT);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
int width, height;
unsigned char* image = SOIL_load_image("sun.jpg", &width, &height, 0, SOIL_LOAD_RG
B);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, i
mage);
glGenerateMipmap(GL_TEXTURE_2D);
SOIL_free_image_data(image);
glBindTexture(GL_TEXTURE_2D, 0);
}
```
### How to Use the Simulation
Once the project is running, users may want to **interact** with it.  

## 🎮 User Controls:
| Key         | Action                  |
|------------|--------------------------|
| `l`        | Rotate camera left        |
| `r`        | Rotate camera right       |
| `+`        | Zoom in                   |
| `-`        | Zoom out                  |
| ⬅️ `Arrow Left`  | Move left               |
| ➡️ `Arrow Right` | Move right              |
| ⬆️ `Arrow Up`  | Move forward             |
| ⬇️ `Arrow Down` | Move backward           |
| ⏹ `ESC`      | Exit the program          |


