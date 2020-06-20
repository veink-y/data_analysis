## 行列转换：

```sql
SELECT s_id,max(case c_id when '01' then s_score else 0 END) AS c_id_01,
max(case c_id when '02' then s_score else 0 END) AS c_id_02,
max(case c_id when '03' then s_score else 0 END) AS c_id_03,
AVG(s_score)
FROM Score
GROUP BY s_id
ORDER BY AVG(s_score) DESC
```



## SQL通配符



| 通配符                         | 描述                       |
| :----------------------------- | :------------------------- |
| %                              | 替代 0 个或多个字符        |
| _                              | 替代一个字符               |
| [*charlist*]                   | 字符列中的任何单一字符     |
| [^*charlist*] 或 [!*charlist*] | 不在字符列中的任何单一字符 |

![SQL通配符实例](https://i.loli.net/2019/07/03/5d1c6c17ecc9682749.png)

