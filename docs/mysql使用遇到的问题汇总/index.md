# mysql使用遇到的问题汇总


## 1. mysql导入本地数据Error 3948解决办法 

- **错误**

  ```shell
  ERROR 3948 (42000): Loading local data is disabled; this must be enabled on both the client and server sides
  ```

- **解决**
  **开启全局本地文件设置 `set global  local_infile=on;`**
  **带上参数`--local-infile=1`连接 mysql --local-infile=1 -uroot -p**

> 原因是mysql8版本安全要求更高，[具体参考](https://www.zhihu.com/question/426972214)


