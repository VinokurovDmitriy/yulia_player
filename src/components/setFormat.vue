<template>
  <h4>Выберете формат</h4>
  <div class="formatBlock">
    <div>
      <h5>Горизонтальное расположение</h5>
      <div class="horizontalView">
        <div class="itemView firstView">1</div>
        <div class="itemView secondView">2</div>
      </div>
    </div>
    <div>
      <div>
        <h5>Вертикальное расположение</h5>
        <div class="verticalView">
          <div class="itemView firstView">1</div>
          <div class="itemView secondView">2</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "setFormat"
}
</script>

<style scoped>
.itemView{
  background: #eee;
  border: 1px solid #ccc;
}
.verticalView{
  width: 200px;
  /*display: flex;*/
  /*flex-direction: column;*/
}
.horizontalView{
  display: flex;
}
.formatBlock {
  display: flex;
}
</style>