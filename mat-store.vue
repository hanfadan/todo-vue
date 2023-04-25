<template>
  <div>
    <b-container class="has-top-bar px-0 bg-white" style="margin-bottom: 125px;">
      <div class="header-navbar">
        <div class="standard-navbar justify-content-between">
          <div class="d-flex align-items-center" style="z-index: 1">
            <nuxt-link to="">
              <i class="icon-arrow-left" />
            </nuxt-link>
            <div class="nav-title fw3">
              Pilih Toko
            </div>
          </div>
        </div>
        <img src="~/assets/images/bg_ hal sub menu.png" class="hero-img">
      </div>
      <div class="py-3 px-3 d-flex align-items-center justify-content-between border-bottom">
        <div>
          <div class="text-muted">
            No. Shipment
          </div>
          <div>{{ shipmentNumber }}</div>
        </div>
        <div class="label alfamind">
          {{ appId }}
          {{ app }}
        </div>
      </div>
      <div class="section-divider" />
      <div class="p-3">
        <p class="text-lg fw6">
          Pilih toko untuk booking barang
        </p>
        <div class="mb-3">
          Menampilkan:
          <span class="fw6">3 Toko</span>
        </div>
        <b-card no-body class="store-option selected">
          <div class="store-info border-bottom">
            <div class="d-flex align-items-center justify-content-between mb-2">
              <b-badge variant="solid success text-md">
                Toko terlengkap!
              </b-badge>
              <b-button variant="link text-md fw7 p-0 text-decoration-none">
                <div class="d-flex align-items-center">
                  Terpilih <b-icon-check-circle-fill class="text-xlg ml-2" />
                </div>
              </b-button>
            </div>
            <div v-b-modal.store-info-modal class="d-flex align-items-center justify-content-between pt-2">
              <div>
                <div class="d-flex align-items-center mb-2">
                  <img src="~/assets/images/ic_store(outline).png" class="img-24">
                  <span class="fw6 ml-2">AC72 - Raya Serpong 10</span>
                </div>
                <table class="store-contact">
                  <tbody>
                    <tr>
                      <td><b-icon-telephone-fill class="text-grey mr-2" /></td>
                      <td><span class="text-md text-muted">08123456789</span></td>
                    </tr>
                    <tr>
                      <td><i class="icon-map-pin text-grey mr-2" /></td>
                      <td><span class="text-md text-muted">Serpong <b-icon-dot /> 3 Km</span></td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <b-icon-chevron-right class="text-lg text-muted" />
            </div>
          </div>
          <div class="section-divider" />
          <div class="store-product">
            <div v-for="product in storedOrderProducts" :key="product.id" class="items-product">
              <div class="d-flex mb-2">
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
                <div class="d-flex align-items-center text-md fw6 pt-1">
                  <span class="text-secondary">Tersedia</span>
                  <span class="ml-1">10</span>
                  <b-icon-dot class="text-muted mx-1 text-xlg" />
                  <span class="text-secondary">Qty Butuh</span>
                  <span class="ml-1">{{ product.attrQtyOos }}</span>
                </div>
                <div>
                  <b-alert variant="warning-mat mb-0 py-1" show>
                    Booking: <span class="fw7">3</span>
                  </b-alert>
                </div>
              </div>
            </div>
          </div>
        </b-card>
        <b-card no-body class="store-option">
          <div class="store-info border-bottom">
            <div class="d-flex align-items-center justify-content-between mb-2">
              <b-badge variant="solid error text-md">
                Toko ini kurang 1 Qty
              </b-badge>
              <b-button variant="link text-md fw7 p-0 text-decoration-none">
                <div class="d-flex align-items-center">
                  Pilih Toko <b-icon-plus-circle class="text-xlg ml-2" />
                </div>
              </b-button>
            </div>
            <div v-b-modal.store-info-modal class="d-flex align-items-center justify-content-between pt-2">
              <div>
                <div class="d-flex align-items-center mb-2">
                  <img src="~/assets/images/ic_store(outline).png" class="img-24">
                  <span class="fw6 ml-2">K551 - Raya Serpong</span>
                </div>
                <table class="store-contact">
                  <tbody>
                    <tr>
                      <td><b-icon-telephone-fill class="text-grey mr-2" /></td>
                      <td><span class="text-md text-muted">08123456789</span></td>
                    </tr>
                    <tr>
                      <td><i class="icon-map-pin text-grey mr-2" /></td>
                      <td><span class="text-md text-muted">Babakan <b-icon-dot /> 2 Km</span></td>
                    </tr>
                  </tbody>
                </table>
              </div>
              <b-icon-chevron-right class="text-lg text-muted" />
            </div>
          </div>
          <div class="section-divider" />
          <div class="store-product">
            <div v-for="i in 3" :key="i" class="items-product">
              <div class="d-flex mb-2">
                <div class="img-product mr-2">
                  <img src="https://cfstg.alfacart.com/product/1530232/1530232_A7556320002066_20211208161158965_small.jpg" class="img-fluid">
                </div>
                <div>
                  <p class="text-md mb-0">
                    2034991
                  </p>
                  <span class="text-muted">Jeruk Shantang Madu</span>
                </div>
              </div>
              <div class="d-flex align-items-center text-md fw6 pt-1">
                <span class="text-secondary">Tersedia</span>
                <span class="ml-1">10</span>
                <b-icon-dot class="text-muted mx-1 text-xlg" />
                <span class="text-secondary">Qty Butuh</span>
                <span class="ml-1">3</span>
              </div>
            </div>
            <div class="items-product unavailable">
              <div class="d-flex mb-2">
                <div class="img-product mr-2">
                  <img src="https://cfstg.alfacart.com/product/1530232/1530232_A7556320002066_20211208161158965_small.jpg" class="img-fluid">
                </div>
                <div>
                  <p class="text-md mb-0">
                    2034991
                  </p>
                  <span class="text-muted">Jeruk Shantang Madu</span>
                </div>
              </div>
              <div class="d-flex align-items-center text-md fw6 pt-1">
                <span class="text-primary">Tidak tersedia</span>
                <b-icon-dot class="text-muted mx-1 text-xlg" />
                <span class="text-secondary">Qty Butuh</span>
                <span class="ml-1">3</span>
              </div>
            </div>
          </div>
        </b-card>
      </div>
    </b-container>
    <div class="action-sheet shadow-lg">
      <b-alert v-b-modal.list-mat variant="warning-mat text-regular fw6 rounded-md d-flex align-items-center justify-content-between" show>
        Daftar barang MAT (Kurang 6 Qty)
        <b-icon-chevron-down />
      </b-alert>
      <b-alert v-b-modal.list-mat variant="info-mat text-regular fw6 rounded-md d-flex align-items-center justify-content-between" show>
        Daftar barang MAT
        <b-icon-chevron-down />
      </b-alert>
      <b-button v-b-modal.process-confirm-modal variant="default" block>
        Pilih
      </b-button>
    </div>
    <b-modal id="store-info-modal" dialog-class="bottom-sheets default-face" hide-header hide-footer>
      <div class="modal-header align-items-center">
        <div class="title text-mlg fw6">
          Informasi Toko
        </div>
        <b-button variant="close-modal p-0 text-mlg text-primary" @click="$bvModal.hide('store-info-modal')">
          <b-icon-x />
        </b-button>
      </div>
      <div class="modal-body">
        <div class="p-3 pb-4">
          <p class="text-lg fw6 mb-2">
            K551 <b-icon-dot /> Raya Serpong
          </p>
          <div class="d-flex">
            <img src="~/assets/images/pin_route.png" class="img-24 mr-3">
            <div>
              <p class="mb-2">
                2 Km
              </p>
              <div class="d-flex align-items-center justify-content-between mb-3">
                <p class="mb-0 text-muted">
                  Jl. Perintis Kemerdekaan I No.1, RT.007/RW.003,  Babakan, Kec. Tangerang, Kota Tangerang, Banten 15118
                </p>
                <b-button variant="link p-0 ml-3">
                  <i class="icon-copy text-mlg text-primary" />
                </b-button>
              </div>
              <b-row>
                <b-col class="pr-1">
                  <b-button variant="white border shadow-sm text-md fw7 text-secondary" block>
                    <i class="icon-phone text-regular text-primary" /> 08123456789
                  </b-button>
                </b-col>
                <b-col class="pl-1">
                  <b-button variant="white border shadow-sm text-md fw7 text-secondary" block>
                    <i class="icon-sign-right text-regular text-primary" /> Arahkan Saya
                  </b-button>
                </b-col>
              </b-row>
            </div>
          </div>
        </div>
      </div>
    </b-modal>
    <b-modal id="list-mat" dialog-class="bottom-sheets default-face" hide-header hide-footer>
      <div class="modal-header align-items-center">
        <div class="title text-mlg fw6">
          Daftar Barang MAT
        </div>
        <b-button variant="close-modal p-0 text-mlg text-primary" @click="$bvModal.hide('list-mat')">
          <b-icon-x />
        </b-button>
      </div>
      <div class="modal-body">
        <div class="product-items pt-3">
          <div v-for="i in 3" :key="i" class="items">
            <div class="d-flex mb-2">
              <div class="img-product mr-2">
                <img src="https://cfstg.alfacart.com/product/1530232/1530232_A7556320002066_20211208161158965_small.jpg" class="img-fluid">
              </div>
              <div>
                <p class="text-md mb-0">
                  2034991
                </p>
                <span class="text-muted">Jeruk Shantang Madu</span>
              </div>
            </div>
            <div class="d-flex align-items-center justify-content-between">
              <div class="d-flex pt-1">
                <b-alert variant="link text-md py-1 px-3 mb-0 mr-2 border" show>
                  <span class="text-secondary">Qty Butuh:</span> 3
                </b-alert>
                <b-alert variant="warning-mat py-1 px-3 mb-0" show>
                  Booking: <span class="fw7">0</span>
                </b-alert>
              </div>
              <span class="text-primary text-md fw7">Kurang 3</span>
            </div>
          </div>
          <div class="items">
            <div class="d-flex mb-2">
              <div class="img-product mr-2">
                <img src="https://cfstg.alfacart.com/product/1530232/1530232_A7556320002066_20211208161158965_small.jpg" class="img-fluid">
              </div>
              <div>
                <p class="text-md mb-0">
                  2034991
                </p>
                <span class="text-muted">Jeruk Shantang Madu</span>
              </div>
            </div>
            <div class="d-flex align-items-center justify-content-between">
              <div class="d-flex pt-1">
                <b-alert variant="link text-md py-1 px-3 mb-0 mr-2 border" show>
                  <span class="text-secondary">Qty Butuh:</span> 1
                </b-alert>
                <b-alert variant="warning-mat py-1 px-3 mb-0" show>
                  Booking: <span class="fw7">1</span>
                </b-alert>
              </div>
              <span class="text-success text-md fw7">Terpenuhi</span>
            </div>
          </div>
        </div>
        <div class="px-3 pb-4 pt-3 shadow sticky-bottom">
          <b-button variant="outline-primary fw6" block @click="$bvModal.hide('list-mat')">
            Tutup
          </b-button>
        </div>
      </div>
    </b-modal>
    <b-modal id="process-confirm-modal" dialog-class="bottom-sheets" hide-header hide-footer>
      <div class="modal-body">
        <div class="px-3 mt-5">
          <b-row align-h="center">
            <b-col cols="7">
              <img src="~/assets/images/booking.png" class="img-fluid">
            </b-col>
          </b-row>
        </div>
        <div class="px-4 pb-4 pt-2 text-center">
          <p class="mb-2 text-lg fw7">
            Proses booking?
          </p>
          <p class="text-grey mb-0">
            Sebelum melanjutkan proses booking, cek lagi barang yang kamu booking sudah benar
          </p>
        </div>
        <div class="px-3 pb-3 pt-0">
          <b-button variant="outline-primary shadow" block @click="$bvModal.hide('process-confirm-modal')">
            Cek lagi
          </b-button>
          <b-button variant="default shadow" block>
            Lanjutkan
          </b-button>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
import { BIconPlusCircle, BIconTelephoneFill, BIconChevronRight, BIconChevronDown, BIconDot, BIconCheckCircleFill, BIconX } from 'bootstrap-vue'
import { checkStock } from '~/api/mat'

export default {
  name: 'MatStore',
  components: { BIconPlusCircle, BIconTelephoneFill, BIconChevronRight, BIconChevronDown, BIconDot, BIconCheckCircleFill, BIconX },
  props: {
  },
  data () {
    return {
      shipmentNumber: '',
      matNo: '',
      appId: null,
      app: ''
    }
  },
  created () {
    this.shipmentNumber = localStorage.getItem('shipmentNumber')
    this.matNo = localStorage.getItem('matNo')
  },
  setup (_, context) {
    const storedOrderProducts = JSON.parse(localStorage.getItem('orderProducts')) || []
    const matNo = localStorage.getItem('matNo')
    const shipmentNumber = localStorage.getItem('shipmentNumber')

    // Map the stored order products to include an `items` property
    // containing the product items for each product
    const productsWithItems = storedOrderProducts.map((product) => {
      const items = []
      for (let i = 0; i < product.attrQtyOos; i++) {
        items.push({
          id: i + 1,
          productName: product.productName,
          image: product.image,
          productID: product.productID
        })
      }
      return {
        ...product,
        items
      }
    })
    const itemsOos = storedOrderProducts.map(product => ({
      id: product.productID,
      orderProductId: product.tbtopID,
      plu: product.plu,
      qtyOos: product.attrQtyOos
    }))
    const data = {
      shipmentNo: shipmentNumber,
      matNo,
      itemsOos
    }
    // send request to API
    checkStock(data)
      .then((response) => {
        console.log('Response:', response.data)
        this.appId = response.data.payload.appId
        this.app = response.data.payload.app
      })
      .catch((error) => {
        console.log('Error:', error)
      })

    return {
      storedOrderProducts: productsWithItems,
      itemsOos
    }
  }

}
</script>

<style lang="scss" scoped>
.hero-img {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}
.label {
  border: dashed 1px #dddddd;
  border-radius: 8px 0 8px 0;
  display: unset;
  padding: 0px 14px;
  &.alfagift {
    color: #F23005;
  }
  &.alfamind {
    color: #f35b17;
  }
  &.gomart {
    color: #d71716;
  }
}
.store-option {
  border-radius: 12px;
  border-color: #D8D8D8;
  overflow: hidden;
  margin-bottom: 16px;
  &.selected {
    border-color: #025da6;
  }
  .store-info {
    padding: 12px;
    div {
      position: relative;
    }
    &::before {
      content: '';
      width: 100%;
      height: 72px;
      background-image: url("~/assets/images/bg-store-mat.png");
      background-size: cover;
      background-repeat: no-repeat;
      background-position-x: left;
      position: absolute;
      top: 0;
      left: 0;
    }
    .store-contact {
      tbody {
        tr {
          td {
            padding-bottom: 8px;
            &:first-child {
              padding-left: 10px;
              text-align: center;
            }
            &:last-child {
              padding-left: 8px;
            }
          }
          &:last-child {
            td {
              padding-bottom: 0;
            }
          }
        }
      }
    }
  }
  .store-product {
    .items-product {
      padding: 16px 12px 12px 12px;
      border-bottom: solid 1px #D8D8D8;
      .img-product {
        width: 32px;
        height: 32px;
        background-color: #F1F1F1;
        border-radius: 2px;
        overflow: hidden;
        padding: 4px;
        img {
          max-height: 100%;
        }
      }
      &:last-child {
        border-bottom: 0;
      }
      &.unavailable {
        background-color: #F9F9F9;
        .img-product {
          img {
            filter: grayscale(100%);
          }
        }
      }
    }
  }
}
.action-sheet {
  padding: 16px;
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  background-color: #FFFFFF;
  z-index: 9;
}
.default-face {
  .modal-content {
    .modal-body {
      .modal-header {
        border: 0;
        background-color: #F1F1F1;
        padding-bottom: 32px;
      }
      .modal-body {
        border-radius: 16px 16px 0 0;
        background-color: #FFFFFF;
        margin-top: -16px;
      }
    }
  }
}
.product-items {
  margin-bottom: 78px;
  .items {
    padding: 16px 12px;
    border-bottom: solid 1px #D8D8D8;
    &:last-child {
      border-bottom: none;
    }
    .img-product {
      width: 32px;
      height: 32px;
      background-color: #F1F1F1;
      border-radius: 2px;
      overflow: hidden;
      padding: 4px;
    }
  }
}
#list-mat {
  .modal-dialog {
    .modal-content {
      .modal-body {
        .modal-header {
          position: sticky;
          top: 0;
        }
        .modal-body {
          height: auto;
          max-height: 80vh;
          overflow-y: scroll;
          &::-webkit-scrollbar {
            width: 0;
          }
        }
        .sticky-bottom {
          position: fixed;
          left: 0;
          bottom: 0;
          z-index: 9;
          width: 100%;
          background-color: #ffffff;
        }
      }
    }
  }
}
</style>
