---
Шейдеры на шейдерграфе, с функциями которые по-умолчанию присутствуют в UnrealEngine
---

## Rotate Texture [HDRP]
---
Шейдер который можно вращать на объекте.
Такой шейдер полезен если нужно немного видоизменить текстуру, например плитку.
Чтобы не перерисовывать в фотошопе, можно воспользоваться им.

В шейдер включены только три карты. Albedo, Normal, Masks.
Минимум возможностей. 
Вращение
Тайлинг
Угол вращения


## Built-in shader
Точно такой же шейдер только для albedo и со стандартным конвеером рендеринга

```hlsl
Shader "Custom/UV rotation"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _Angle ("Angle", Range(-5.0,  5.0)) = 0.0
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
       
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"
 
            struct v2f {
                float4 pos : SV_POSITION;
                float2 uv : TEXCOORD0;
            };
 
            float _Angle;
 
            v2f vert(appdata_base v)
            {
                v2f o;
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
 
                // Pivot
                float2 pivot = float2(0.5, 0.5);
                // Rotation Matrix
                float cosAngle = cos(_Angle);
                float sinAngle = sin(_Angle);
                float2x2 rot = float2x2(cosAngle, -sinAngle, sinAngle, cosAngle);
 
                // Rotation consedering pivot
                float2 uv = v.texcoord.xy - pivot;
                o.uv = mul(rot, uv);
                o.uv += pivot;
 
                return o;
            }
 
            sampler2D _MainTex;
 
            fixed4 frag(v2f i) : SV_Target
            {
                // Texel sampling
                return tex2D(_MainTex, i.uv);
            }
 
            ENDCG
        }
    }
}
```




![This is exanple](/RotateTexture/RotateTexture.gif)
---

