<template>
  <div class="q-pa-xl q-ma-xl">
    <q-table title="Treats" :data="products" :columns="headers" row-key="name">
      <template #top>
        <q-btn flat outline dence color="primary" @click="show_dialog = true"
          >Add row</q-btn
        >
        <q-dialog v-model="show_dialog" persistent>
          <q-card>
            <q-card-section> Add new item </q-card-section>
            <q-card-section>
              <div class="row">
                <q-input
                  v-model="product.name_uz"
                  label="Name"
                  class="q-mx-sm"
                ></q-input>
                <q-input
                  v-model="product.cost"
                  type="number"
                  label="Cost"
                  class="q-mx-sm"
                ></q-input>
                <q-input
                  v-model="product.address"
                  label="Address"
                  class="q-mx-sm"
                ></q-input>
              </div>
            </q-card-section>

            <q-card-actions align="between" class="q-mt-sm">
              <q-btn
                flat
                label="cancel"
                color="gray"
                @click="cancel"
              ></q-btn>
              <q-btn flat label="Ok" color="green" @click="save"></q-btn>
            </q-card-actions>
          </q-card>
        </q-dialog>
      </template>
      <template v-slot:body="props">
        <q-tr :props="props">
          <q-td key="name_uz" :props="props">
            {{ props.row.name_uz }}
          </q-td>

          <q-td key="cost" :props="props">
            <q-badge color="purple" class="q-pa-sm">
              {{ props.row.cost }} UZS
            </q-badge>
          </q-td>

          <q-td key="address" :props="props">
            {{ props.row.address }}
          </q-td>

          <q-td key="actions" :props="props">
            <q-btn
              flat
              round
              icon="fa-solid fa-pencil"
              @click="showItem(props.row)"
            ></q-btn>
            <q-btn flat round icon="fa-solid fa-trash" @click="deleteItem(props.row)"></q-btn>
          </q-td>
        </q-tr>
      </template>
    </q-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      show_dialog: false,
      product: {},
      headers: [
        {
          name: "name_uz",
          required: true,
          label: "Name uz",
          align: "left",
          field: "name_uz",
        },
        { name: "cost", align: "left", label: "Cost", field: "cost" },
        {
          name: "address",
          label: "Address",
          field: "address",
        },
        {
          name: "actions",
          label: "Actions",
          field: "actions",
          align: "center",
        },
      ],
      products: [
        {
          name_uz: "Frozen Yogurt",
          cost: 2000,
          address: "AddRESs",
        },
      ],
    };
  },
  computed: {
    product_form() {
      return {
        name_uz: this.product.name_uz,
        cost: Number(this.product.cost),
        address: this.product.address,
        product_type_id: 0,
        created_date: new Date().toISOString()
      }
    }
  },
  methods: {
    async getProducts() {
      const res = await this.$axios.get("/product");
      console.log(res, "res");
      this.products = res.data;
    },
    cancel() {
      this.product = {}
      this.show_dialog = false
    },

    async save() {
      if (!this.product.id) {
        const res = await this.$axios.post('/product', this.product_form)
        console.log(res);
        this.product = {}
        this.show_dialog = false
      } else {
        const res = await this.$axios.put('/product', {id: this.product.id, ...this.product_form})
        console.log(res);
        this.product = {}
        this.show_dialog = false
      }
      this.getProducts()
    },
    
    showItem(item) {
      console.log(item, "item");
      this.product = item
      this.show_dialog = true
    },
    async deleteItem(item) {
      console.log(item);
      await this.$axios.delete(`/product/${item.id}`)
      this.getProducts()
    }
  },
  beforeMount() {
    this.getProducts();
  },
};
</script>
