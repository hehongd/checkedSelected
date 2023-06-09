<template>
  <div class="tables">
    <div class="border"></div>
    <adArea :data="list" @handleChange="handleChange" ref="main" class="main" />
    <div class="border"></div>
  </div>
</template>
<script>
import dataJS from '@/utils/data'
import adArea from '@/components/area.vue'
export default {
  name: 'test',
  components: {
    adArea
  },
  data() {
    return {
      list: [],
      mainRef: null
    }
  },
  created() {
    const data = JSON.parse(JSON.stringify(dataJS)).splice(0, 5)
    this.list = this.initData(data)
  },
  methods: {
    initData(data) {
      data.forEach(province => {
        province.checked ? province.checked = true : province.checked = false
        if (!province.children || !province.children.length) return
        province.indeterminate ? province.indeterminate = true : province.indeterminate = false
        province.childrenCheck ? province.childrenCheck : province.childrenCheck = []
        this.initData(province.children)
      })
      return data
    },
    handleChange(treedata) {
      const obj = this.deepData(treedata)
      this.list.forEach((item, index) => {
        if (item.code == obj.code) {
          this.list.splice(index, 1, obj)
        }
      })
    },
    deepData(treedata) {
      let province = []
      if (treedata.code.length == 2) {
        province = treedata
        province.children.forEach((city, cityx) => {
          city.checked = province.checked
          city.checked ? province.childrenCheck.push(city.name) : province.childrenCheck = []
          city.indeterminate = false
          province.childrenCheck.push(city.name)
          city.children.forEach((area, areax) => {
            area.checked = city.checked
            area.checked ? city.childrenCheck.push(city.name) : city.childrenCheck.splice(cityx, 1)
          })
        })
        return province
      } else {
        this.list.forEach(item => {
          if (item.code == treedata.code.substring(0, 2)) {
            province = JSON.parse(JSON.stringify(item))
          }
        })
        if (treedata.code.length == 4) {
          province.children.forEach((city, cityx) => {
            if (city.code === treedata.code) {
              province.children.splice(cityx, 1, treedata)
            }
          })
          province.childrenCheck = []
          province.children.forEach((city, cityx) => {
            city.checked ? province.childrenCheck.push(city.name) : province.childrenCheck.splice(cityx, 1)
            city.children.forEach((area, areax) => {
              area.checked = city.checked
              area.checked ? city.childrenCheck.push(city.name) : city.childrenCheck.splice(cityx, 1)
            })
            city.indeterminate = city.childrenCheck.length > 0 && city.childrenCheck.length < city.children.length
          })
          province.checked = province.childrenCheck.length == province.children.length
          province.indeterminate = province.childrenCheck.length > 0 && province.childrenCheck.length < province.children.length
          return province
        } else {
          province.children.forEach((city, cityx) => {
            city.children.forEach((area, areax) => {
              if (area.code === treedata.code) {
                city.children.splice(areax, 1, treedata)
              }
            })
          })
          province.childrenCheck = []
          province.children.forEach((city, cityx) => {
            city.childrenCheck = []
            city.children.forEach((area, areax) => {
              area.checked ? city.childrenCheck.push(area.name) : city.childrenCheck.splice(areax, 1)
            })
            city.checked = city.childrenCheck.length == city.children.length
            city.indeterminate = city.childrenCheck.length > 0 && city.childrenCheck.length < city.children.length
            city.checked ? province.childrenCheck.push(city.name) : province.childrenCheck.splice(cityx, 1,)
          })
          province.checked = province.childrenCheck.length == province.children.length
          province.indeterminate = province.childrenCheck.length > 0 && province.childrenCheck.length < province.children.length || !province.childrenCheck.length

          return province
        }
      }
    },
    scrollMain() {
      let scrollTop = this.mainRef.scrollTop
      let scrollHeight = this.mainRef.scrollHeight
      let clientHeight = this.mainRef.clientHeight
      if (scrollHeight - (scrollTop + clientHeight) < 80) {
        const data = JSON.parse(JSON.stringify(dataJS)).splice(this.list.length, 5)
        if (!data.length) return
        this.list = this.list.concat(data)
      }
    }
  },
  mounted() {
    this.mainRef = document.querySelector('.main')
    this.mainRef.addEventListener('scroll', this.scrollMain)
  },
  beforeDestroy() {
    this.mainRef.removeEventListener('scroll', this.scrollMain)
  }
};
</script>

<style scoped lang="less">
@main-bg-transparent: transparent;
@main-bg-color: rgb(118, 117, 115);
.tables {
  padding: 0 80px;
}

.border {
  width: calc(100% - 30px);
  height: 1px;
  background: #333;
  margin-left: 10px;
}

.main {
  margin: auto;
  height: 800px;
  overflow: scroll;
  overflow-x: hidden;
  padding: 0 10px;
  position: relative;
}

.main:hover {
  &::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background-color: @main-bg-color;
  }
}

.main::-webkit-scrollbar {
  width: 10px;
}

.main::-webkit-scrollbar-track {
  background-color: rgb(253, 252, 249);
  background-color: @main-bg-transparent;
}

.main::-webkit-scrollbar-thumb {
  border-radius: 5px;
  background-color: @main-bg-transparent;
}
</style>