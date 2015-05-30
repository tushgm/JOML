# JOML - Java-OpenGL-Math-Library
A Java-based math library for OpenGL rendering calculations

Design goals
------------

The goal of JOML is to provide easy-to-use, feature-rich and efficient linear algebra operations, which are needed by any 3D application developers.

Simple examples
---------------
The following is a collection of simple usage examples.

### Vector arithmetic
All operations in JOML are designed to modify the object on which the operation is invoked. This helps in completely eliminating any object allocations, which the client could otherwise not control and which impact the GC performance resulting in small hickups.
The client is responsible to allocate the needed working objects.
```Java
Vector3f v = new Vector3f();
Vector3f a = new Vector3f(1.0f, 0.0f, 0.0f);
// v = v + a
v.add(a);
// a = cross product of v and a
a.cross(v);
```

Matrix API
------------
Using JOML you can build matrices out of basic transformations, such as scale, translate and rotate, using a fluent-interface style. All such operations directly modify the matrix instance on which they are invoked.
The following example builds a transformation matrix which effectively first scales all axes by 0.5
and then translates x by 2.0:
```Java
Matrix4f m = new Matrix4f().translate(2.0f, 0.0f, 0.0f).scale(0.5f);
Vector4f v = new Vector4f(1.0f, 0.0f, 0.0f, 1.0f);
m.transform(v);
// v is now transformed using the transformations in m
```

Perspective projections
------------
In the same way that you can concatenate/multiply multiple simple affine transformations, you can use the methods Matrix4f.perspective(), .ortho() to specify a perspective or orthogonal projection and .lookAt() to create a orthonormal transformation that resembles a camera "looking" into a given direction. Those three methods resemble the ones known from GLU and act in the same way (i.e. they apply their transformations to an already existing transformation):
```Java
Matrix4f m = new Matrix4f()
     .perspective(45.0f, 1.0f, 0.01f, 100.0f)
     .lookAt(0.0f, 0.0f, 10.0f,
             0.0f, 0.0f, 0.0f,
             0.0f, 1.0f, 0.0f);
// the camera transformation is now in m
```
The above transformation can then be used as a "view-projection" matrix in a shader.

Matrix stack
------------
JOML also features an interface that resembles the matrix stack from legacy OpenGL.
This allows you to use all of the legacy OpenGL matrix stack operations even in modern OpenGL applications,
but without the otherwise necessary JNI calls into the graphics driver.
```Java
MatrixStack stack = new MatrixStack();
// Compose the final matrix
stack.translate(2.0f, 0.0f, 0.0f);
stack.scale(0.5f, 0.5f, 0.5f);
// Obtain the final matrix
Matrix4f result = new Matrix4f();
stack.get(result);
// Or store it into a FloatBuffer for final submission to OpenGL as uniform mat4
FloatBuffer fb = ByteBuffer.allocateDirect(4 * 16).order(ByteOrder.nativeOrder()).asFloatBuffer();
stack.get(fb);
fb.rewind();
```