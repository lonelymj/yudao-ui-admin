<template>
  <div class="head-container">
    <el-input v-model="deptName" class="mb-20px" clearable placeholder="请输入部门名称">
      <template #prefix>
        <Icon icon="ep:search" />
      </template>
    </el-input>
  </div>
  <div class="head-container">
    <el-tree
      ref="treeRef"
      :data="deptList"
      :expand-on-click-node="false"
      :filter-node-method="filterNode"
      :props="defaultProps"
      default-expand-all
      highlight-current
      node-key="id"
      @node-click="handleNodeClick"
    />
  </div>
</template>

<script lang="ts" setup>
import { ElTree } from 'element-plus'
import * as DeptApi from '@/api/system/dept'
import { defaultProps, handleTree } from '@/utils/tree'
import { propTypes } from '@/utils/propTypes'

defineOptions({ name: 'SystemUserDeptTree' })

const props = defineProps({
  tenantId: propTypes.number.def(0)
})

const deptName = ref('')
const deptList = ref<Tree[]>([]) // 树形结构
const treeRef = ref<InstanceType<typeof ElTree>>()

/** 获得部门树 */
const getTree = async (tenantId: number) => {
  // const res = await DeptApi.getSimpleDeptList()
  const res = await DeptApi.getSimpleDeptList(tenantId)
  deptList.value = []
  deptList.value.push(...handleTree(res))
}

/** 基于名字过滤 */
const filterNode = (name: string, data: Tree) => {
  if (!name) return true
  return data.name.includes(name)
}

/** 处理部门被点击 */
const handleNodeClick = async (row: { [key: string]: any }) => {
  emits('node-click', row)
}
const emits = defineEmits(['node-click'])

/** 监听deptName */
watch(deptName, (val) => {
  treeRef.value!.filter(val)
})


watch(props, async (val) => {
  const tenantId = val.tenantId
  console.log(tenantId)
  await getTree(tenantId)
})

/** 初始化 */
onMounted(async () => {
  await getTree(props.tenantId)
})
</script>
