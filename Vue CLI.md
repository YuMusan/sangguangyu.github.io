https://vue-next-template-explorer.netlify.app/#%7B%22src%22%3A%22%3Cdiv%3EHello%20World!%3C%2Fdiv%3E%22%2C%22options%22%3A%7B%22mode%22%3A%22module%22%2C%22prefixIdentifiers%22%3Afalse%2C%22optimizeImports%22%3Afalse%2C%22hoistStatic%22%3Afalse%2C%22cacheHandlers%22%3Afalse%2C%22scopeId%22%3Anull%2C%22inline%22%3Afalse%2C%22ssrCssVars%22%3A%22%7B%20color%20%7D%22%2C%22bindingMetadata%22%3A%7B%22TestComponent%22%3A%22setup-const%22%2C%22setupRef%22%3A%22setup-ref%22%2C%22setupConst%22%3A%22setup-const%22%2C%22setupLet%22%3A%22setup-let%22%2C%22setupMaybeRef%22%3A%22setup-maybe-ref%22%2C%22setupProp%22%3A%22props%22%2C%22vMySetupDir%22%3A%22setup-const%22%7D%7D%7D

https://composition-api.vuejs.org/zh/api.html#setup

https://portswigger.net/web-security/all-materials

```js
/**
 *
 * @param {object} matchedParam - 匹配的对象
 * @param {object} incomingParam - 传入的对象
 * @param {Boolean} match - 是否匹配
 * 数据处理：1、去掉空字段，2、补全参数
 */
export function dataProcessing(matchedParam, incomingParam, match) {
  if (match) {
    for (const key in matchedParam) {
      if (matchedParam.hasOwnProperty(key)) {
        const element = incomingParam[key]
        if (element) Object.assign(matchedParam, { [key]: element })
      }
    }
    return matchedParam
  } else {
    for (const key in incomingParam) {
      if (incomingParam.hasOwnProperty(key)) {
        const element = incomingParam[key]
        if (typeof (element) === 'object') {
          if (element.length === 0) delete incomingParam[key]
        } else {
          if (!element) delete incomingParam[key]
        }
      }
    }
    return incomingParam
  }
}
```


```js
// 单选
    handleOneSelect(selection, row) {
      // 当前点击元素是否勾选
      const isIn = selection.find((val) => {
        return val.id === row.id
      })
      // 勾选后添加至已选集合
      if (isIn) {
        for (const item of selection) {
          const itemIsIn = this.curSelect.find((val) => {
            return val.id === item.id
          })
          if (!itemIsIn) this.curSelect.push(item)
        }
      } else {
        // 勾选后去勾选
        this.curSelect.splice(
          this.curSelect.findIndex((val) => {
            return val.id === row.id
          }),
          1
        )
      }
    },
    // 全选
    handleAllSelect(selection) {
      if (selection.length > 0) {
        // 全选合并
        for (const item of this.curPageTableData) {
          const itemIsIn = this.curSelect.find((val) => {
            return val.id === item.id
          })
          if (!itemIsIn) this.curSelect.push(item)
        }
      } else {
        // 全选后取消全选
        for (const item of this.curPageTableData) {
          const index = this.curSelect.findIndex((val) => {
            return val.id === item.id
          })
          this.curSelect.splice(index, 1)
        }
      }
    },
    // 分页处理
    handleCurrentChange(val) {
      this.curPage = val
      // pagedata数据同步
      this.$nextTick(() => {
        // 显示勾选状态
        for (const item of this.curSelect) {
          const selectedRow = this.curPageTableData.find((val) => {
            return val.id === item.id
          })
          if (selectedRow) this.$refs.table.toggleRowSelection(selectedRow, true)
        }
      })
    },
```

组件化，工程化

工程化，路由，状态管理，多语言，axios请求封装api集中处理，协同开发，UI引入快速开发

组件化 项目分成大大小小组件，组件使用配置化

