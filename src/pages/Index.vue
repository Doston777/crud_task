<template>
  <div class="q-pa-xl">
    <q-table title="Treats" :data="productsV" :columns="headers" row-key="name">
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
                  outlined
                  v-model="product.name_uz"
                  label="Name"
                  class="q-px-sm col-6"
                  :rules="[
                    (val) =>
                      val.length >= 3 || 'Please use minimum 3 characters',
                  ]"
                ></q-input>
                <q-input
                  outlined
                  v-model="product.cost"
                  type="number"
                  label="Cost"
                  class="q-px-sm col-6"
                  :rules="[
                    val => val !== null || 'Please, input cost'
                  ]"
                ></q-input>
                <q-input
                  outlined
                  :rules="[val => val.length >=3 || 'Please use minimum 3 characters']"
                  v-model="product.address"
                  label="Address"
                  class="q-px-sm col-6"
                ></q-input>

                <q-select
                  outlined
                  label="Product type id"
                  class="q-px-sm col-6"
                  :rules="[myRule]"
                  :options="types"
                  v-model="product.product_type_id"
                  option-label="name_uz"
                  option-value="id"
                  emit-value
                  map-options
                />
              </div>
            </q-card-section>

            <q-card-actions align="between" class="q-mt-sm">
              <q-btn flat label="cancel" color="gray" @click="cancel"></q-btn>
              <q-btn flat label="Ok" color="green" :disable="okBtnDisable" @click="save"></q-btn>
            </q-card-actions>
          </q-card>
        </q-dialog>
      </template>
      <template v-slot:body="props">
        <q-tr
          :class="oddEven(props.row.id) ? 'bg-white' : 'bg-secondary'"
          :props="props"
        >
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

          <q-td key="product_type_id" :props="props">
            {{ returnName(props.row.product_type_id) }}
          </q-td>
          <q-td key="actions" :props="props">
            <q-btn
              flat
              round
              icon="fa-solid fa-pencil"
              @click="showItem(props.row)"
            ></q-btn>
            <q-btn
              flat
              round
              icon="fa-solid fa-trash"
              @click="deleteItem(props.row)"
            ></q-btn>
          </q-td>
        </q-tr>
      </template>
    </q-table>
    <div>
      {{ productsSum }} {{ okBtnDisable }}
    </div>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex';
export default {
  data() {
    return {
      show_dialog: false,
      product: {
        product_type_id: null,
        cost: null,
        name_uz: null,
        address: null
      },
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
          name: "product_type_id",
          label: "Product type id",
          field: "product_type_id",
        },
        {
          name: "actions",
          label: "Actions",
          field: "actions",
          align: "right",
        },
      ],
      products: [],
      types: [],
    };
  },
  computed: {
    ...mapState(['productsV']),
    product_form() {
      return {
        name_uz: this.product.name_uz,
        cost: Number(this.product.cost),
        address: this.product.address,
        product_type_id: this.product.product_type_id,
        created_date: new Date().toISOString(),
      };
    },
    productsSum() {
      const productCosts = this.products.map((a) => a.cost);
      return productCosts.reduce((a, b) => a + b, 0);
    },
    okBtnDisable() {
      return (this.product.name_uz === null) || (this.product.address == null) || (this.product.cost === null) || (this.product.product_type_id === null)
    }
  },
  methods: {
    ...mapActions(['getProductsV']),
    myRule(val) {
      if (val === null) {
        return 'You must make a selection!'        
      }
    },
    returnName(id) {
      return this.types.find((i) => i.id === id)
        ? this.types.find((i) => i.id === id).name_uz
        : "-";
      // console.log(name, 'name')
      // return name.name_uz
    },
    oddEven(i) {
      let a = false;
      if (i % 2 == 0) {
        a = true;
      } else {
        a = false;
      }
      return a;
    },
    async getProducts() {
      const res = await this.$axios.get("/product");
      console.log(res, "res");
      this.products = res.data;
    },
    cancel() {
      this.product = {};
      this.getProducts();
      this.show_dialog = false;
    },

    async save() {
      if (!this.product.id) {
        const res = await this.$axios.post("/product", this.product_form);
        console.log(res);
        this.product = {};
        this.show_dialog = false;
      } else {
        const res = await this.$axios.put("/product", {
          id: this.product.id,
          ...this.product_form,
        });
        console.log(res);
        this.product = {};
        this.show_dialog = false;
      }
      this.getProducts();
    },

    showItem(item) {
      console.log(item, "item");
      this.product = item;
      this.show_dialog = true;
    },
    async deleteItem(item) {
      console.log(item);
      await this.$axios.delete(`/product/${item.id}`);
      this.getProducts();
    },
    async getProductTypes() {
      const res = await this.$axios.get("product/get-product-types");
      console.log(res);
      this.types = res.data;
      console.log(this.types);
      this.returnName(1);
    },
  },
  beforeMount() {
    this.getProductsV();
    // this.getProducts();
    this.getProductTypes();
  },
};
</script>

<style scoped>
.bg-white {
  background: #fff;
}

.bg-secondary {
  background: #444;
  color: white;
}
</style>
