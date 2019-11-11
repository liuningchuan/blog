> 使用[Hexo](https://hexo.io/)搭建个人网站 只需要简单的配置即可建站 一条命令即部署到Github Pages.
> 
> 支持Markdown文件 基于Node.js快速渲染 可以自定义主题

# [示例](https://liuningchuan.github.io/)
![example](./source/images/example.png)

### 初始化
```bash
git clone https://github.com/liuningchuan/blog.git
cd blog
npm install
```

### 运行
Run hexo in local environment

```shell
hexo server
hexo server --debug
```

Clean cache

```
hexo clean
```

Generate the static file

```bash
hexo generate
```

### 部署
Config the deploy
```yml
deploy:
  type: git
  repository: git@github.com:liuningchuan/liuningchuan.github.io.git
  branch: master
```

Deploy

```
hexo deploy
```

