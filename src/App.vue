<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link>
      <router-link to="/about">About</router-link>
    </div>
    <router-view />
  </div>
</template>

<style lang="scss">
html,
body {
  box-sizing: border-box;
  font-size: 16px;
  height: 100vh;
}

*,
*:before,
*:after {
  box-sizing: inherit;
}

body,
h1,
h2,
h3,
h4,
h5,
h6,
p,
ol,
ul {
  margin: 0;
  padding: 0;
  font-weight: normal;
}

ol,
ul {
  list-style: none;
}

img {
  max-width: 100%;
  height: auto;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #eeeeee;
  background-color: #333;
  height: 100%;
}

#nav {
  padding: 30px;
  display: flex;
  //justify-content: flex-end;

  a {
    font-weight: bold;
    color: #55eca8;
    text-decoration: none;
    border-bottom: 3px transparent solid;
    transition: 0.3s border-bottom;
    margin: 0 1em;

    &.router-link-exact-active {
    }
    &:hover {
      color: #86ffc9;
      border-bottom: 3px #86ffc9 solid;
    }
  }
}
</style>
