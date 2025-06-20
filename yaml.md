`pip install pyyaml`

`#` 作为缩进

缩进：两个空格

成员表示：

* `-`：表示列表，或者[ ]
* `:`：表示字典成员

完全兼容`json`

空值：`null  # json写法`

加载`yaml`：

```py
import yaml
def load_yaml(path):
f = open(path, encoding='utf-8')
s = f.read()
data = yaml.safe_load(s)
return data
```





