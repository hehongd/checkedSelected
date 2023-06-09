<template>
  <div class="app-container">
    <div v-for="item in getData" :key="item.code" class="groups"
      :class="item.code.length > 4 ? 'areaBorder' : item.code.length > 2 ? 'cityBorder' : 'provinceBorder'">
      <el-checkbox v-model="item.checked" @change="handleChange(item)" :indeterminate="item.indeterminate">{{ item.name }}</el-checkbox>
      <div v-if="item.children" class="group">
        <adArea :data="item.children" v-bind="$props" v-on="$listeners"/>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'adArea',
  props: {
    data: {
      type: Array,
      default: () => { return [] }
    }
  },
  data() {
    return {
      
    };
  },
  computed: {
    getData() {
      return this.data
    }
  },
  mounted() {
    
  },
  methods: {
    handleChange(item) {
      this.$emit('handleChange', item)
    }
  }
}
</script>

<style scoped lang="less">
.groups {
  display: flex;
  align-items: center;
}

.app-container {
  display: flex;
  flex-wrap: wrap;
  .el-checkbox {
    padding: 10px;
  }
}

.group {
  flex: 1;
}

.provinceBorder {
  border: 1px solid;
  border-bottom: 0;
  border-top: 0;
  width: 100%;
  .el-checkbox {
    width: 150px;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    border-top: 1px solid;
  }
}

.cityBorder {
  border-left: 1px solid;
  border-bottom: 0;
  border-top: 0;
  width: 100%;
  .el-checkbox {
    width: 150px;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    white-space: pre-wrap;
  }
  .group {
    border-left: 1px solid;
    border-top: 1px solid;
    padding: 10px;
  }
}

.areaBorder {
  border-bottom: 0;

  .el-checkbox {
    width: auto;
    border: 0;
  }
}
</style>
