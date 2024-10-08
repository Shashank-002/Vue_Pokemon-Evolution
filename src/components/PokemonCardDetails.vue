<template>
  <div class="card">
    <div class="card-title">
      <slot name="title"></slot>
    </div>
    <hr>
    <div class="card-content">
      <slot name="content"></slot>
    </div>
    <hr>
    <div class="card-footer">
      <slot name="footer"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PokemonCardDetails',
};
</script>

<style scoped>
.card {
  background-color: #fff;
  border: 2px solid gray;
  border-radius: 12px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  padding: 20px 0px;
  display: grid;
  grid-gap: 20px;
  justify-content: center;
  grid-template-columns: repeat(3, 1fr);
  margin: 20px;
  width: 25%;
  text-align: center;
  display: inline-block;
}

.card-title {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 15px;
  color: #333;
}

.card-content {
  font-size: 1rem;
  margin-bottom: 15px;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}

.card-footer {
  font-size: 1.5rem;
  font-weight: 600;
  color: #666;
  margin-top: 10px;
  display: flex;
  justify-content: center;
  gap: 10px;
}

.card-footer div {
  padding: 5px 10px;
  border-radius: 20px;
  background-color: #f2f2f2;
}

@media (max-width:1024px) {
  .card-title {
    font-size: 1rem;
  }

  .card-footer {
    font-size: 1.2rem;
  }
}

@media (max-width:475px) {
  .card {
    justify-content: center;
    grid-template-columns: repeat(1, 1fr);
    width: 90%;
  }
}

@media (min-width:1600px)
{
  .card{
    width: auto;
  }
}
</style>