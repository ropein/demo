<template>
  <div class="index-container">
    <el-button @click="isEdit = true">编辑</el-button>
    <el-button @click="addLine">添加</el-button>
    <el-button @click="onSave">保存</el-button>
    <el-form ref="formRef" :model="form" :inline="true" label-width="108px">
      <el-table :data="form.tagList">
        <el-table-column key="tag_name" prop="tag_name" label="Key">
          <template slot-scope="scope">
            <span v-if="!isEdit">{{ scope.row.tag_name }}</span>
            <el-form-item
              v-else
              :prop="'tagList.' + scope.row.frontKey + '.tag_name'"
              :error="
                computedFieldError(scope.row.frontKey, scope.row.tag_name)
              "
            >
              <el-input
                v-model="scope.row.tag_name"
                placeholder="请输入key"
              ></el-input>
            </el-form-item>
          </template>
        </el-table-column>
        <el-table-column key="tag_value" prop="tag_value" label="Value">
          <template #default="{ row }">
            <span v-if="!isEdit">{{ row.tag_value }}</span>
            <el-form-item
              v-else
              :rules="form.rules.tag_name"
              :prop="'tagList.' + row.frontKey + '.tag_value'"
            >
              <el-input
                v-model="row.tag_value"
                placeholder="请输入value"
              ></el-input>
            </el-form-item>
          </template>
        </el-table-column>
        <el-table-column key="operation" prop="operation" label="操作">
          <template #default="{ row }">
            <el-button @click="delLine(row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-form>
  </div>
</template>
<script>
  export default {
    name: 'RepeatTable',
    data() {
      return {
        isEdit: false,
        form: {
          tagList: [],
          rules: {
            tag_name: [{ required: true, message: '请输入', trigger: 'blur' }],
          },
        },
        tableOptions: [
          {
            label: 'Key',
            prop: 'tag_name',
          },
          {
            label: 'Value',
            prop: 'tag_value',
          },
        ],
      }
    },
    mounted() {
      this.form.tagList = [
        {
          tag_name: 'name',
          tag_value: 'value',
          frontKey: 0,
        },
      ]
    },
    methods: {
      async onSave() {
        if (document.querySelectorAll('.el-form-item__error').length) {
          console.log('校验不通过')
        } else {
          console.log('校验通过')
        }
      },
      computedFieldError(fieldFrontKey, value) {
        if (value === '') {
          return '请输入'
        } else {
          let timeGroup = this.form.tagList.reduce(function (arr, tagObject) {
            let flag = arr.find((item) => {
              return item.name === tagObject.tag_name
            })
            if (!flag) {
              let obj = {
                name: tagObject.tag_name,
                frontKey: tagObject.frontKey,
                time: 1,
              }
              arr.push(obj)
            } else {
              flag.time++
            }
            return arr
          }, [])
          let repeatArr = timeGroup
            .filter((item) => item.name && item.time > 1)
            .map((item) => item.name)
          if (repeatArr.includes(value)) {
            return '存在重复的值：' + value
          }
        }
      },
      addLine() {
        this.form.tagList.push({
          tag_name: null,
          tag_value: '',
          frontKey:
            this.form.tagList.length === 0
              ? 1
              : Math.max(...this.form.tagList.map((item) => item.frontKey)) + 1,
        })
      },
      delLine(row) {
        this.form.tagList = this.form.tagList.filter((item) => {
          return item.frontKey !== row.frontKey
        })
      },
    },
  }
</script>
