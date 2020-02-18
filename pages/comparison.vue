<template>
  <div class="body">
    <h3>入力</h3>
    <div class="field">
      <div class="control">
        <textarea class="textarea is-primary" v-model="inputData">
        </textarea>
      </div>
      <div class="buttons">
        <button class="button is-success" @click="doSave">保存</button>
      </div>
    </div>
    <div class="database-data" v-show="isShow.database">
      <h3>データベースの値</h3>
      <table class="table is-bordered is-striped is-narrow is-hoverable is-fullwidth">
        <thead>
          <tr>
            <th
              v-for="(headerData, headerIndex) in databaseHeader"
              :key="headerIndex"
            >
              {{headerData}}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(lineData, lineIndex) in databaseValues"
            :key="lineIndex"
          >
            <template
              v-for="(value, index) in lineData"
            >
              <td
                :key="index"
              >
                {{value}}
              </td>
            </template>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="comparison-list" v-show="isShow.comparison">
      <h3>比較結果の差異</h3>
      <table
        class="table is-fullwidth"
        v-for="(comparisonData, count) in comparisonList"
        :key="count"
      >
        <thead>
          <tr>
            <th>項目名</th>
            <th>変更前</th>
            <th>変更後</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(value, column) in comparisonData"
            :key="column"
          >
            <td>{{value.column}}</td>
            <td>{{value.before}}</td>
            <td>{{value.after}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isShow: {
        database: false,
        comparison: false,
      },
      error: '',
      inputData: '',
      databaseHeader: [],
      databaseValues: [],
      comparisonList: [],
    }
  },
  methods: {
    doSave () {
      const splitLine = this.inputData.split(/\r\n|\n/)
      for (var i = 0; i < splitLine.length; i++){
        if (splitLine[i]) {
          // 先頭文字が#を項目名
          if (splitLine[i].slice(0, 1) == '#') {
            this.databaseHeader = Object.assign(
              [],
              this.databaseHeader,
              splitLine[i].split(',')
            )
          } else {
            this.databaseValues.push(splitLine[i].split(','))
          }
        }
      }
      this.isShow.database = true
      // 配列の0は項目名の前提
      if (this.databaseValues.length > 1) {
        const key = this.comparisonList.length
        console.log('key')
        console.log(key)
        this.databaseValues[key].forEach((value, index) => {
          if (value != this.databaseValues[key + 1][index]) {
            this.comparisonList[key] = {
              index: {
                'column': this.databaseHeader[index],
                'before': value,
                'after': this.databaseValues[key + 1][index],
              }
            }
          }
        })
        this.isShow.comparison = true
      }
    },
  }
}
</script>

<style>
.body {
  margin:5%;
}
.field {
  width: 100%;
}
.database-data {
  margin: 2rem 0;
  width: 100%;
  overflow-x: auto;
}
h3 {
  font-size: 1.5rem;
  border-left: solid 3px rgb(0, 223, 0);
  padding-left: 0.5rem;
  padding-bottom: 0.2rem;
  margin-bottom: 0.5rem;
}
</style>
