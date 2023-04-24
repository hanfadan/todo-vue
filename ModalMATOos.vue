<template lang="">
  <b-modal id="create-mat" ref="createMAT" modal-class="fullscreen bottom" hide-header hide-footer>
    <div class="modal-header align-items-center justify-content-start">
      <b-button variant="close-modal" @click="$bvModal.hide('create-mat')">
        <b-icon-x />
      </b-button>
      <div class="title">
        Ajukan MAT
      </div>
    </div>
    <div class="modal-body current-action-sheet">
      <b-alert
        :show="dismissCountDown"
        dismissible
        variant="basic-toast centered"
        @dismissed="dismissCountDown=0"
        @dismiss-count-down="countDownChanged">
        Tidak boleh lebih dari qty diminta
      </b-alert>
      <div class="px-3 pt-4">
        <p>Masukkan Qty yang dibutuhkan untuk MAT. Biarkan 0 jika produk tidak membutuhkan MAT</p>
      </div>
      <div class="mat-item">
        <div v-for="(product, index) in orderProducts" :key="index" class="list-item-mat" :class="{ filled: product.attrQtyOos > 0 }">
          <div class="d-flex mb-2 pb-1">
            <div class="img-product mr-2">
              <img :src="product.image" class="img-fluid">
            </div>
            <div>
              <p class="text-md mb-0">
                {{ product.productID }}
              </p>
              <span class="text-muted">{{ product.productName }}</span>
            </div>
          </div>
          <div class="d-flex align-items-center justify-content-between">
            <b-alert show variant="warning-mat text-md rounded-md px-2 py-1 mb-0">
              Qty Pesanan:
              <span class="fw6">{{ product.qty }}</span>
            </b-alert>
            <div class="qty-adjustment">
              <b-button variant="link" @click="decreaseQuantity(index)">
                <b-icon-dash />
              </b-button>
              <div>{{ product.attrQtyOos }}</div>
              <b-button
                variant="link"
                @click="increaseQuantity(index)">
                <b-icon-plus :style="product.attrQtyOos >= product.qty ? 'color: gray;' : ''" />
              </b-button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="action-sheet">
      <div class="p-3">
        <div class="d-flex justify-content-between mb-3">
          <p class="text-lg fw6 mb-0">
            Ringkasan MAT
          </p>
          <div>
            <div class="d-flex align-items-center justify-content-end mb-1">
              <span class="text-md mr-3">Total Produk:</span>
              <span class="text-lg fw6">{{ totalProduct }}</span>
            </div>
            <div class="d-flex align-items-center justify-content-end">
              <span class="text-md mr-3">Total Qty:</span>
              <span class="text-lg fw6">{{ totalQtyOos }}</span>
            </div>
          </div>
        </div>
        <b-button
          variant="default"
          block
          href="/mat-store"
          @click="onNextClick">
          Selanjutnya
        </b-button>
      </div>
    </div>
  </b-modal>
</template>
<script>
import { computed, ref } from '@vue/composition-api'
import {
  BIconPlus,
  BIconDash,
  BIconX
} from 'bootstrap-vue'
import { ACT_ORDER_DETAIL } from '~/constants/action-types'

export default {
  name: 'ModalMATOos',
  components: {
    BIconPlus,
    BIconDash,
    BIconX
  },
  props: {
    orderProducts: {
      type: Array,
      required: true,
      default: () => []
    }
  },
  methods: {
  },
  setup (props, context) {
    const store = context.root.$store
    const dismissCountDown = ref(0)

    const totalQtyOos = computed(() => {
      let total = 0
      for (let i = 0; i < props.orderProducts.length; i++) {
        let currentQty = props.orderProducts[i].attrQtyOos
        const maxQty = props.orderProducts[i].qty
        if (currentQty > maxQty) {
          currentQty = maxQty
          props.orderProducts[i].qty = currentQty
        }
        total += currentQty
      }
      return total
    })

    const totalProduct = computed(() => {
      let total = 0
      for (let i = 0; i < props.orderProducts.length; i++) {
        if (props.orderProducts[i].attrQtyOos > 0) {
          total += 1
        }
      }
      return total
    })

    const totalQuantity = computed(() => {
      let total = 0
      for (let i = 0; i < props.orderProducts.length; i++) {
        total += props.orderProducts[i].qty
      }
      return total
    })

    const increaseQuantity = (index) => {
      const product = props.orderProducts[index]
      if (product.attrQtyOos < product.qty) {
        store.commit(
          ACT_ORDER_DETAIL.base + '/' + ACT_ORDER_DETAIL.increaseQuantity,
          index
        )
      } else {
        dismissCountDown.value = 2 // Show the alert for 5 seconds
      }
    }

    const countDownChanged = (dismissCountDownValue) => {
      dismissCountDown.value = dismissCountDownValue
    }

    const decreaseQuantity = (index) => {
      store.commit(ACT_ORDER_DETAIL.base + '/' + ACT_ORDER_DETAIL.decreaseQuantity, index)
    }

    const showConfirmMATModal = () => {
      props.showConfirmMATModal()
    }

    const saveDataToLocalStorage = () => {
      localStorage.setItem('orderProducts', JSON.stringify(props.orderProducts))
    }

    const onNextClick = () => {
      context.emit('show-confirm-modal')
      saveDataToLocalStorage()
    }

    return {
      dismissCountDown,
      totalQtyOos,
      totalProduct,
      totalQuantity,
      increaseQuantity,
      decreaseQuantity,
      countDownChanged,
      showConfirmMATModal,
      saveDataToLocalStorage,
      onNextClick
    }
  }
}
</script>
<style scoped lang="scss">
.mat-item {
  .list-item-mat {
    padding: 12px 16px 14px 16px;
    border-bottom: solid 1px #D8D8D8;
    &.filled {
      background-color: #FFF8E7;
    }
    .img-product {
      width: 32px;
      height: 32px;
      background-color: #F1F1F1;
      border-radius: 2px;
      overflow: hidden;
      padding: 4px;
    }
    &:last-child {
      border-bottom: none;
    }
  }
}
.mat-trigger {
  background-color: #FFF6CC;
  svg {
    color: #8B760B;
  }
  span {
    color: #554600;
  }
}
</style>
