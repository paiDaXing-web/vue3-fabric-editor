<!--
 * @Author: 秦少卫
 * @Date: 2022-09-03 19:16:55
 * @LastEditors: 秦少卫
 * @LastEditTime: 2022-09-04 00:12:54
 * @Description: 保存文件
-->

<template>
  <el-button @click="clear" size="small">清空</el-button>
  <el-dropdown :teleported="true" style="margin-left: 10px" @command="saveWith">
    <el-button size="small" type="primary">
      保存
      <el-icon>
        <ArrowDown />
      </el-icon>
    </el-button>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item command="clipboard()">复制到剪切板</el-dropdown-item>
        <el-dropdown-item command="saveImg()">保存为图片</el-dropdown-item>
        <el-dropdown-item command="saveSvg()">保存为SVG</el-dropdown-item>
        <el-dropdown-item command="saveJson()" divided>保存为JSON</el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>


<script setup>
import { v4 as uuid } from 'uuid';
import { ArrowDown } from '@element-plus/icons-vue'
const canvas = inject("canvas")
const saveWith = (type) => {
  eval(type)
}
const saveJson = () => {
  const dataUrl = canvas.c.toJSON(['id'])
  const fileStr = `data:text/json;charset=utf-8,${encodeURIComponent(JSON.stringify(dataUrl, null, '\t'))}`;
  downFile(fileStr, 'json')
}
const saveSvg = () => {
  const dataUrl = canvas.c.toSVG()
  const fileStr = `data:image/svg+xml;charset=utf-8,${encodeURIComponent(dataUrl)}`;
  downFile(fileStr, 'svg')
}
const saveImg = () => {
  const option = { name: 'New Image', format: 'png', quality: 1, multiplier: 2 }
  const dataUrl = canvas.c.toDataURL(option)
  downFile(dataUrl, 'png')
}
const downFile = (fileStr, fileType) => {
  const anchorEl = document.createElement('a');
  anchorEl.href = fileStr;
  anchorEl.download = uuid() + '.' + fileType;
  document.body.appendChild(anchorEl); // required for firefox
  anchorEl.click();
  anchorEl.remove();
}
const clipboard = () => {
  const jsonStr = canvas.c.toJSON(['id'])
  _mixinClipboard(JSON.stringify(jsonStr, null, '\t'))
}
const clear = () => {
  canvas.c.clear();
  canvas.c.setBackgroundColor('#ffffff', canvas.c.renderAll.bind(canvas.c))
}


</script>

<style scoped lang="less">
.el-dropdown {
  vertical-align: initial;
}
</style>
