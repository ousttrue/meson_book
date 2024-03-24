# error

## LINK : fatal error LNK1181: 入力ファイル 'pcre2-8.lib' を開けません。

rename

`libpcre2-8.a` => `pcre2-8.lib`

## ERROR: can't resolve libraries to shared libraries: glib-2.0, gobject-2.0

環境変数 LIB に `$prefix/lib` を追加

## glib/gstring.h

```sh
$PREFIX\include\glib-2.0\glib\gstring.h:193: syntax error, unexpected identifier in '__forceinline' at '__forceinline'
```

```c
G_ALWAYS_INLINE
static inline GString*
g_string_append_c_inline (GString *gstring,
                          gchar    c)

// 👇 remove inline

G_ALWAYS_INLINE
static GString*
g_string_append_c_inline (GString *gstring,
                          gchar    c)
```

## hlsl

```
FAILED: subprojects/gst-plugins-bad/sys/d3d12/hlsl/VSMain_coord.h
"C:\VulkanSDK\1.3.275.0\Bin\dxc.EXE" "/Fh" "subprojects/gst-plugins-bad/sys/d3d12/hlsl/VSMain_coord.h" "/E" "VSMain_coord" "/T" "vs_6_0" "/all-resources-bound" "../subprojects/gst-plugins-bad/sys/d3d12/hlsl/VSMain_coord.hlsl"
../subprojects/gst-plugins-bad/sys/d3d12/hlsl/VSMain_coord.hlsl:34:10: error: cannot initialize return object of type 'VS_OUTPUT' with an lvalue of type 'VS_INPUT'
  return input;
         ^~~~~
```

```hlsl
struct VS_INPUT
{
  float4 Position : POSITION;
  float2 Texture : TEXCOORD;
};

struct VS_OUTPUT
{
  float4 Position : SV_POSITION;
  float2 Texture : TEXCOORD;
};

VS_OUTPUT VSMain_coord (VS_INPUT input)
{
  return input;
}

// 👇


```

## meson が vulkan の version を解決できない

Windows 版の meson 。

```py title="site-packages\mesonbuild\dependencies\ui.py"
            # XXX: this is very odd, and may deserve being removed
            self.type_name = DependencyTypeName('vulkan_sdk')
            self.is_found = True
            self.compile_args.append('-I' + inc_path)
            self.link_args.append('-L' + lib_path)
            self.link_args.append('-l' + lib_name)
            self.version = '1.3.275.0' # 👈
```

## ../gsk/gpu/gskvulkandescriptors.c(51): error C4098: 'gsk_vulkan_descriptors_bind': 戻り値の型が 'void' で宣言された関数が、値を返しました。

```c
void
gsk_vulkan_descriptors_bind (GskVulkanDescriptors *self,
                             GskVulkanDescriptors *previous,
                             VkCommandBuffer       vk_command_buffer)
{
  return GSK_VULKAN_DESCRIPTORS_GET_CLASS (self)->bind (self, previous, vk_command_buffer);
}
```
