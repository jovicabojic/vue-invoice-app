<template>
  <div>
    <div class="container">

      <div class="fromWrapper">
        <input type="text" @click="$event.target.select()" v-model="newItem.productName">
        <input type="number" @click="$event.target.select()" min="0" v-model.number="newItem.productPrice">
        <input type="number" @click="$event.target.select()" min="0" v-model.number="newItem.productQuantity">
        <button type="button" class="addButton" @click="addNewItem">Add</button>
      </div>

      <main class="main-content">
        <table class="mainTable">
          <thead>
          <tr>
            <th style="width: 10%"><input type="checkbox" :checked="allDone && items.length > 0" @change="selectAll()"></th>
            <th style="width: 50%">Product name</th>
            <th style="width: 10%">Price</th>
            <th style="width: 10%">Quantity</th>
            <th style="width: 10%">Sum</th>
            <th style="width: 10%"></th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(item, index) in items" :key="index">
            <td><input type="checkbox" :checked="item.checked" @change="checkItem(index)"></td>
            <td>{{ item.description }}</td>
            <td>
              <div class="cell-with-input">${{ item.price }}</div>
            </td>
            <td data-label="Quantity">{{ item.quantity }}</td>
            <td data-label="Total">${{ item.price * item.quantity }}</td>
            <td><button class="deleteButton deleteItem" type="button" v-if="item.checked" @click="removeItem(index)"></button></td>
          </tr>
          </tbody>
          <tfoot>
          <tr>
            <td><button type="button" :disabled="!allDone || items.length === 0" @click="removeAll" class="deleteButton">Delete All</button></td>
            <td></td>
            <td></td>
            <td></td>
            <td>Total</td>
            <td>${{ subTotal }}</td>
          </tr>
          </tfoot>
        </table>
      </main>
    </div>
  </div>
</template>

<script>

var STORAGE_KEY = "vue-invoice-app";
var todoStorage = {
  fetch: function () {
    var items = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    todoStorage.uid = items.length;
    return items;
  },
  save: function (items) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(items));
  }
};

// visibility filters
var filters = {
  active: function(items) {
    return items.filter(function(item) {
      return !item.checked;
    });
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
      }
    }
  },
  watch: {
    items: {
      handler: function (items) {
        todoStorage.save(items);
      },
      deep: true
    }
  },
  methods: {
    resetItem() {
      this.newItem.productName = ''
      this.newItem.productQuantity = 0
      this.newItem.productPrice = 0
    },
    addNewItem: function () {
      this.items.push(
          {
            id: this.newItem.id++,
            description: this.newItem.productName,
            quantity: this.newItem.productQuantity,
            price: this.newItem.productPrice,
            checked: false
          }
      )
      this.resetItem()
    },
    checkItem(index) {
      this.items[index].checked = !this.items[index].checked
    },
    removeItem(index) {
      this.items.splice(index, 1)
    },
    removeAll() {
      this.items = []
    },
    selectAll() {
      this.items.forEach(function (item) {
        item.checked = true;
      });
    }
  },
  computed: {
    subTotal: function () {
      return this.items.reduce(function (accumulator, item) {
        return accumulator + (item.price * item.quantity);
      }, 0);
    },
    remaining: function() {
      return filters.active(this.items).length;
    },
    allDone: {
      get: function() {
        return this.remaining === 0;
      },
      set: function(value) {
        this.items.forEach(function(item) {
          item.checked = value;
        });
      }
    }
  }
}
</script>
