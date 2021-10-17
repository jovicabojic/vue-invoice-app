<template>
  <div>
    <div class="container">

      <div class="fromWrapper">
        <input type="text" @click="$event.target.select()" v-model="newItem.productName">
        <input type="number" @click="$event.target.select()" v-model.number="newItem.productPrice">
        <input type="number" @click="$event.target.select()" v-model.number="newItem.productQuantity">
        <button type="button" @click="addNewItem" @keyup.enter="addNewItem">Add</button>
      </div>

      <div class="page-container">
        <main class="main-content">
          <div id="invoice-app">
            <table class="responsive-table">
              <thead>
              <tr>
                <th><input type="checkbox" @change="selectAll()"></th>
                <th>Product name</th>
                <th>Price</th>
                <th>Quantity</th>
                <th>Sum</th>
                <th></th>
              </tr>
              </thead>
              <tr v-for="(item, index) in items" :key="index">
                <td><input type="checkbox" :checked="item.checked" @change="checkItem(index)"></td>
                <td><input type="text" v-model="item.description"/></td>
                <td>
                  <div class="cell-with-input"><input type="number" min="0" v-model.number="item.price"/></div>
                </td>
                <td data-label="Quantity"><input type="number" min="0" v-model.number="item.quantity"/></td>
                <td data-label="Total">{{ item.price * item.quantity }}</td>
                <td class="text-right">
                  <button class="is-danger" @click="deleteItem(index)">Delete item</button>
                </td>
              </tr>
            </table>
<!--            <button @click="addNewItem">Add item</button>-->
            <table>
              <tr>
                <td colspan="5">Subtotal</td>
                <td>{{ subTotal }}</td>
              </tr>
            </table>

            <button @click="printInvoice">Print Invoice</button>
            <button @click="deleteAllItems">Delete All</button>
          </div>

        </main>
      </div>
    </div>
  </div>
</template>

<script>

var STORAGE_KEY = "vue-invoice-app";
var todoStorage = {
  fetch: function() {
    var items = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    todoStorage.uid = items.length;
    return items;
  },
  save: function(items) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(items));
  }
};

export default {
  name: 'InvoiceCalculator',
  data() {
    return {
      items: todoStorage.fetch(),
      newItem: {
        id: '',
        productName: '',
        productPrice: 0,
        productQuantity: 0,
        checked: false
      },
    }
  },
  watch: {
    items: {
      handler: function(items) {
        todoStorage.save(items);
      },
      deep: true
    }
  },
  methods: {
    addNewItem: function() {
      this.items.push(
          {
            id: this.newItem.id++,
            description: this.newItem.productName,
            quantity: this.newItem.productQuantity,
            price: this.newItem.productPrice,
            checked: false
          }
      )
      this.newItem.productName = ''
      this.newItem.productQuantity = ''
      this.newItem.productPrice = ''
    },
    checkItem(index) {
      this.items[index].checked = !this.items[index].checked
    },
    deleteItem(index) {
      this.items.splice(index, 1)
    },
    deleteAllItems() {
      this.items = []
    },
    printInvoice() {
      window.print();
    },
    selectAll() {
      this.items.forEach(function(item) {
        item.checked = true;
      });
    }
  },
  computed: {
    subTotal: function() {
      return this.items.reduce(function (accumulator, item) {
        return accumulator + (item.price * item.quantity);
      }, 0);
    },
  }
}
</script>
