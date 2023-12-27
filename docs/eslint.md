# Eslint 让自己的代码更规范

ESLint 是一款 JavaScript 代码检查工具，可以帮助我们在编写代码时发现潜在的问题和错误，提高代码质量和可读性。下面是如何给自己的 JS 项目增加 ESLint 的步骤：

## 指南

```sh
npm install eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin --save-dev
```

在项目根目录下创建 `.eslintrc.js` 文件，并添加以下内容：

```js
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: [
    'plugin:@typescript-eslint/recommended'
  ],
  rules: {
    // 可以添加需要的规则
  }
};
```

在 `package.json` 中添加以下脚本：
```json
"scripts": {
  "lint": "eslint src --ext .ts",
  "lint:fix": "eslint src --ext .ts --fix"
}
```

运行 `npm run lint` 即可进行 ESLint 检查。

## 添加 Prettier 美化代码

```sh
npm install prettier --save-dev
```

```sh
npm install eslint-plugin-prettier --save-dev
npm install eslint-config-prettier@latest --save-dev
```

更新 `.eslintrc.js`

```js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier',
    'plugin:prettier/recommended'
  ],
  plugins: ['@typescript-eslint', 'prettier'],
  rules: {
    // 其他规则
  }
};
```

在项目根目录下添加 `.prettierrc` 文件，定义 Prettier 的配置，例如：

```json
{
  "singleQuote": true
}
```


## 忽略规则

如果检查出的问题有一些规则不需要可以主动忽略，修改 `.eslintrc.js`：

```js
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: [
    'plugin:@typescript-eslint/recommended'
  ],
  rules: {
    '@typescript-eslint/no-unused-vars': 'off'
  }
};
```
将 `@typescript-eslint/no-unused-vars` 这个规则的值设置为 'off' 即可关闭该规则。