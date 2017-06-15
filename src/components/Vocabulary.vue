<template>
  <ul>
    <li v-for="(item, index) in list">
      <input :id="'item_' + index" type="checkbox" v-model="item.checked">
      <label :for="'item_' + index">{{item.text}}</label>
    </li>
  </ul>
</template>

<script>
	export default {
		name: 'Vocabulary',
		props: ['list']
	}
</script>

<style scoped>
ul {
  margin: 10px 50px;
  list-style: none;
}

li {
	padding: 10px;
  text-align: left;
}

li input {
	float: left;
	margin-top: 10px;
}

li label {
	display: block;
	height: 30px;
	line-height: 30px;
	background: #edf;
}

li label:hover {
	cursor: pointer;
	background: #cbe;
}
</style>