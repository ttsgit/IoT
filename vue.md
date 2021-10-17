# Vuetity 配置安装

## node 安装
```
sudo dnf install nodejs 
```
检查是否安装成功
```
node -v
npm -v
```

## 安装Vue CLI 

### 安装
```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```
检查是否安装成功
```
vue --version
```

### 升级
```
npm update -g @vue/cli
# OR
yarn global upgrade --latest @vue/cli
```
## 利用Vue CLI安装vuetify
新建项目
```
$ vue create my-app
// navigate to new project directory
$ cd my-app
```
新增插件
```
$ vue add vuetify
```

## 运行项目
```
cd my-app
npm run serve
```


## 其他
### Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
