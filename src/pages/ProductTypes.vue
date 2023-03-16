<template>
  <div class="q-ma-xl">
    <q-table
      title="Product types"
      :data="types"
      :columns="cols"
    >
      <!-- <template #body="props">
        <q-tr :props="props">
          <q-td key="id" :props="props">
            {{ props.row.id }}
          </q-td>

          <q-td key="name_ru" :props="props">
            {{ props.row.name_ru }}
          </q-td>

          <q-td>
            {{ props.row.name_uz }}
          </q-td>

          <q-td key="name_uk" :props="props">
            {{ props.row.name_uk }}
          </q-td>

          <q-td key="description" style="color: black" :props="props">
            {{ props.row.description !== null ? props.row.description : 'empty'  }}
          </q-td>
         </q-tr>
      </template> -->
    </q-table>
  </div>
</template>

<script>
  export default {
    data: () => ({
      cols: [
        {
          name: "id",
          field: "id",
          label: "ID"
        },
        {
          name: "name_ru",
          field: "name_ru",
          label: "Name RU",
        },
        {
          name: "name_uz",
          field: "name_uz",
          label: "Name RU",
        },
        {
          name: "name_uk",
          field: "name_uk",
          label: "Name RU",
        },
        {
          name: 'Description',
          field: "description",
          label: "Description"
        }
      ],
      types: []
    }),
    methods: {
      async getProductTypes() {
        const res = await this.$axios.get('product/get-product-types')
        console.log(res);
        this.types = res.data
      }
    },
    beforeMount() {
      this.getProductTypes()
    }
  }
</script>

<style lang="scss" scoped>

</style>