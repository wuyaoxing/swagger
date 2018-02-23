# swagger
> convert swagger 2.0 data structure

## description
之前项目做过基于 swagger 2.0 标准的api可视化编辑器。

![editor](https://github.com/wuyaoxing/swagger/blob/master/src/assets/editor.png)

大致实现思路如下：
- swagger 2.0
    - api 项目，包含swagger object的一些基本信息，例如swagger, info, host, basePath, schemas等
    - endpoints 分组，对应tags
        - endpoint 组成paths
            - get
            - put
            - post
            - delete
            - options
            - head
            - patch
            - 每种operation包含着description, parameters, responses等
    - models 分组，对应definitions
        - model 组成definitions
            - 单个model可被endpoint引用

总的来说就是把这一整块数据拆的特别细，将每个处理成最小单位。

## idea
细有细的好处，但结合业务场景他的不足之处很快就暴露出来。譬如，

例一：当endpoint引用model做$ref时，对应endpoint会有类似这样的数据结构
```
"schema": {
    "type": "object",
    "properties": {
        "data": {
            "$ref": "#/definitions/model"
        }
    }
}
```
当我们需要改变model的名字时，需要对model的引用修正。
当前处理方式：
- model引用处处理成model的id
- 数据展示时根据modelId转换为modelName，凡是涉及model的地方基本都需要转换，譬如选择model和数据导出时

这样的代码略微臃肿、冗余。

例二：对于endpoint，其结构为
- Basics
- Description
- Path Parameters
- Query String
- Request Headers
- Request Body
- Responses

我们需要对其属性进行展示、编辑，而数据库存储的数据结构需要一次或多次转换才能满足展示、编辑的数据结构，保存时仍需要转换。这个处理数据的过程相对复杂而且容易出错。

**plan**

1. 数据存储
    - 每个api项目存储为swagger 2.0 数据格式，转换拆分存储为整体存储
2. 数据转换
    ```
        class Swagger {
            constructor(info) {
                this._info = info
            }

            swaggerToEditorSchema(swaggerSchema) {
                // swagger -> editor
            }

            editorToSwaggerSchema(editorSchema) {
                // editor -> swagger
            }

            // 拆分方法
            …
        }
    ```
