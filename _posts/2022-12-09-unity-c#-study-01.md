---
title: Unity and C# Study Notes - Computer Graphics Basics
tags: [Computer Graphics, Unity, C#]
style: border
color: primary
description: Notes about the techniques that are important or easy to forget. 
---

### Vector 

- In Unity, vector is defined as `struct`. 
    - When you want to equal sign to copy the content (allocate new memory), it is more convenient to define the the type of object as `struct`. Otherwise, `class` will allow you to copy it as reference.

- Override ToString function (the `print` function will default call the object's ToString function):

```c#
public override string ToString(){
    return string.Format("(0:F2),(1:F2),(2:F2)", x, y, z);
}
```

- In Unity, `mesh.normals` are the normals associated to the vertices: usually the shader calculates each pixel's normal by interpolating among the normals. Flat surfaces have equal normals in their vertices (like if they were surface normals), but curved surfaces have different normals associated to each vertex. **Notice** that all data in a mesh is in local space, which doesn't take the transform settings into account (rotation, scale, position). You should convert each vertex and its normal to world space in order to correctly display them

## Matrix

```c#
using System;
using System.Text;
using System.Runtime.InteropServices;

[StructLayout(LayoutKind.Sequential)]
namespace My{
struct Matrix4x4{
    [MarshalAs(UnmanagedType.ByValArray, SizeConst = 4*4)]
    private float[,] m; // = null
    public Matrix4x4(float [,] m){
        this.m = m;
    }
    public float this[int row, int col]{
        get{
            if (row < 0 || row >= 4)
				throw new IndexOutOfRangeException();
			if (col < 0 || col >= 4)
				throw new IndexOutOfRangeException();
			
            return m[row, col];
        }
        set{
            if (row < 0 || row >= 4)
				throw new IndexOutOfRangeException();
			if (col < 0 || col >= 4)
				throw new IndexOutOfRangeException();

			m[row, col] = value;
        }
    }
    public Matrix4x4 transpose{
        get{
            Matrix4x4 mRet = new Matrix4x4(new float [4,4]);
            for(int i = 0; i < 4; i++){
                for(int j = 0; j < 4; j++){
                    mRet[i, j] = this[j, i];
                }
            }
            return mRet;
        }
    }
    public static Matrix4x4 operator *(Matrix4x4 a, Matrix4x4 b){
        Matrix4x4 c = new Matrix4x4(new float [4,4]);
        for(int i = 0; i < 4; i++){
            for(int j = 0; j < 4; j++){
                c[i, j] = a[i, 0] * b[0, j] +
                          a[i, 1] * b[1, j] +
                          a[i, 2] * b[2, j] +
                          a[i, 3] * b[3, j];
            }
        }
        return c;
    }
    public override string ToString(){
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < 4; i++){
            for(int j = 0; j < 4 ;j++){
                sb.AppendFormat("{0:F2} ", m[i, j]);
            }
            sb.Append("\n");
        }
        return sb.ToString();
    }
}
} // namespace
```

- In C#, `struct` does not allow to override the default constructor for a properties since it will automatically fill the content with null. 

    > If a struct declares any field initializers, it must explicitly declare a constructor. That constructor need not be parameterless. If a struct declares a field initializer but no constructors, the compiler reports an error. Any explicitly declared constructor (with parameters, or parameterless) executes all field initializers for that struct. All fields without a field initializer or an assignment in a constructor are set to the default value. 

- To the implementer of a class, a property is one or two code blocks, representing a `get` accessor and/or a `set` accessor. 
    - The code block for the `get` accessor is executed when the property is read.
    - The code block for the `set` accessor is executed when the property is assigned a value. 
    - This is useful for **Encapsulation**, to make sure that "sensitive" data is hidden from users.

- `throw (C# Reference)` Signals the occurrence of an exception during program execution. 
    - e.g. `throw new IndexOutOfRangeException();`
    - e.g. `throw new NotImplementedException();`

- Game Engine might optimize the matrix multiplication using SIMD.

- Unity 3D and OpenGL use **column-major** matrix, Direct3D use **row-major** matrix.
    - row-vector * row-major-matrix
    - col-major-matrix * col-vector

- When deploying Direct3D API in Unity, it will automatically convert the column-major matrix to a row-major matrix, along with the transformation of the coordinates' axis directions.

- Transformation matrix: 
    - `M = M_translate * M_rotate * M_scale`
    - `M_translate.inverse = -M_translate`
    - `M_rotate.inverse = M_rotate.transpose`
    - `M_scale(x, y, z).inverse = M_scale(1/x, 1/y, 1/z)`

- The inverse of the transformation matrix indicates the inverse transformation process.

### Projection Matrix

- Direct3D transformation:
```c
p_screen = p_local * M_w * M_v * M_p * M_s
M_w: local -> world
M_v: world -> view 
M_p: view -> projection
M_s: projection -> screen
```

- OpenGL transformation:
```c
p_screen = M_s * M_p * M_v * M_w * p_local
```

 