## 简介

使用的工具：

- **Commitizen**：是一个命令行提示工具，它主要用于帮助我们更快地写出规范的 commit message
- **Commitlint**：用于校验填写的 commit message 是否符合设定的规范
- **Husky**：是一个 git hook 工具，用于在提交过程中的某个特定时刻触发 commitlint

## Commitizen

1. 全局安装 Commitizen。

```shell
npm install -g commitizen
```

2. 在项目中安装 Commitizen 的适配器 `cz-conventional-changelog`。

```shell
# npm
commitizen init cz-conventional-changelog --save-dev --save-exact

# yarn
commitizen init cz-conventional-changelog --yarn --dev --exact

# pnpm
commitizen init cz-conventional-changelog --pnpm --save-dev --save-exact
```

适配器的作用是按照某个指定的规范帮助我们生成 commit message，这里预设的是 [Angular团队规范](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fangular%2Fangular.js%2Fblob%2Fmaster%2FDEVELOPERS.md%23commits "https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#commits")。你也可以选择其他的适配器来切换不同的规范，甚至自定义一个适配器。

至此，第一步全局安装 Commitizen 完成。以后每次提交都使用 `cz` 替代 `git commit -m` 。

## Husky

1. 在项目中安装 Husky。

```shell
npm install --save-dev husky
```

2. 初始化 Husky。

```shell
npx husky init
```

## Commitlint

1. 在项目中安装 Commitlint。

```shell
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

2. 配置 commitlint 以使用 Angular 团队规范。

```shell
echo "export default { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
```

3. 配置 Husky Hook 以在每次提交时用 Commitlint 进行检查

```shell
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

到这里，一个完整的校验流程已经搭建完成了。
