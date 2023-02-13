<template>
  <div class="container">
    <div class="column inputs">
      <!--content-->
    </div>
    <div class="column uploads">
      <p>Importere visma lønn</p>
      <form action="/action_page.php">
        <input type="file" id="visma-lønn" name="VismaLønn">
        <input type="submit">
      </form>
      <p>Fordele dekningsbidrag</p>
      <button>Fordel</button>
      <p>Importere fakturagrunnlag</p>
      <form action="/action_page.php">
        <input type="file" id="fakturagrunnlag" name="Fakturagrunnlag">
        <input type="submit">
      </form>
    </div>
    <div class="column log">
      <!--content-->
    </div>
  </div>
</template>
