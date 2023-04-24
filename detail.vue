<template>
  <div>
    <template v-if="!isScanpackingShow">
      <div
        v-if="orderDetail">
        <b-container
          class="has-top-bar px-0">
          <div class="header-navbar">
            <div class="standard-navbar justify-content-between">
              <div class="d-flex align-items-center" style="z-index: 1">
                <nuxt-link event="disabled" to="">
                  <i class="icon-arrow-left" @click="backToOrderList($route.params.status)" />
                </nuxt-link>
                <div class="nav-title fw3">
                  Detail {{ $route.params.status !== '1105' ? 'Pesanan -' : '' }} {{ $route.params.status | toStatusTabName }}
                </div>
              </div>
              <div v-if="+(orderDetail.sapaStatus !== 1105 && orderDetail.sapaStatus !== 1104) && orderDetail.returRefundID === 0 || orderDetail.returRefundStatus === 1306 && orderDetail.deliveryTypeId !== '2'" class="position-relative" style="z-index: 1">
                <nuxt-link :to="`/retur/request/`+ orderDetail.shipmentNumber+`/`+orderDetail.deliveryMethod">
                  <img src="~/assets/images/ic_retur_detail.png" style="height:30px;">
                </nuxt-link>
              </div>
            </div>
            <img src="~/assets/images/bg_ hal sub menu.png" class="hero-img" style="z-index: 0; background-color: #ffe592">
            <div style="background-color: #ffe592;" />
          </div>
          <!--        <pull-to :top-load-method="onRefresh" :top-config="TOP_DEFAULT_CONFIG">-->
          <b-row
            class="subheader mx-0"
            :class="`${
              orderDetail.deliveryTypeId === '1'
                ? 'dc'
                : (orderDetail.deliveryTypeId === '2' ? 'marketplace-product' : (orderDetail.timer > ($route.params.status === '1101' || $route.params.status === '1102' ? 5 : 20) ? 'urgent' : ''))
            }`">
            <b-col cols="12">
              <template v-if="$route.params.status === '1106' || $route.params.status === '1107'">
                <b-card no-body class="transaction-cancel mb-3">
                  <b-col cols="12">
                    <b-row align-v="center">
                      <b-col cols="7" class="pr-0 address-section fw7">
                        Pesanan Dibatalkan
                        <p class="mb-4 fw2">
                          {{ orderDetail.reasonForCancel !== '' ? orderDetail.reasonForCancel : 'Batal saja' }}
                        </p>
                        <p class="mb-0 fw-2 address-detail">
                          {{ orderDetail.canceledDate }}, {{ orderDetail.canceledTime }} WIB
                        </p>
                      </b-col>
                      <b-col cols="5 pr-2">
                        <div class="d-flex justify-content-end">
                          <img
                            v-if="!isLoading"
                            src="~/assets/images/ic_batal_reason.svg"
                            class="img-fluid"
                            style="width: 50px; height: 50px; object-fit: contain;">
                        </div>
                      </b-col>
                    </b-row>
                  </b-col>
                </b-card>
              </template>
              <template v-else>
                <b-card v-if="$route.params.status === '1104' || $route.params.status === '1105'" no-body class="transaction-info mb-3">
                  <b-col cols="12">
                    <b-row align-v="center">
                      <b-col cols="5" class="pr-0">
                        <i class="icon-officer" />
                        <span>{{ orderDetail.deliveryMethod === '0' ? 'Karyawan Toko' : 'Pengirim' }}</span>
                      </b-col>
                      <b-col
                        cols="7"
                        class="pl-0 text-right text-default">
                        {{ orderDetail.kurir | toPascalCase }} ({{ orderDetail.nik }})
                      </b-col>
                    </b-row>
                    <b-row align-v="center">
                      <b-col cols="6">
                        <i class="icon-user-o" />
                        <span>Penerima</span>
                      </b-col>
                      <b-col
                        cols="6 text-right text-default">
                        {{ orderDetail.receiverName | toPascalCase }} ({{ orderDetail.receiverOther || orderDetail.receiverRelation | toPascalCase }})
                      </b-col>
                    </b-row>
                    <b-row align-v="center">
                      <b-col cols="5" class="pr-0">
                        <i class="icon-calendar" />
                        <span>Tanggal diterima</span>
                      </b-col>
                      <b-col
                        cols="7"
                        class="pl-0 text-right text-default">
                        {{ orderDetail.receivedDate | reviewDateFormat }} WIB
                      </b-col>
                    </b-row>
                    <p v-if="orderDetail.deliveryMethod === '1'" class="mb-0 mt-2 text-center">
                      <a
                        :href="'https://maps.google.com/maps?q=' + orderDetail.receiverLat + ',' + orderDetail.receiverLong"
                        target="_blank"
                        style="text-decoration: none">
                        <b-button variant="link text-primary p-0 fw6" size="sm">
                          <i class="icon-pin text-primary" />
                          Lokasi Penerima
                          <span class="flip-h">
                            <b-icon-chevron-right class="text-primary" />
                          </span>
                        </b-button>
                      </a>
                    </p>
                  </b-col>
                </b-card>
                <b-row v-if="orderDetail.returRefundID != 0 && orderDetail.returRefundStatus !== 1306" class="pt-0 text-center mx-0">
                  <b-col cols="12 pb-3">
                    <b-alert
                      show
                      class="py-1 px-2 mb-0"
                      variant="warning dash">
                      <i :class="[orderDetail.returRefundID != 0 && orderDetail.returRefundStatus !== 1306 ? 'icon-warning' : 'icon-thumb-up']" /> Pesanan ini telah diproses pengajuan Retur, Silahkan cek status dimenu Retur Refund.
                    </b-alert>
                  </b-col>
                </b-row>
              </template>
              <div v-if="orderDetail.deliveryTypeId === '1'">
                <b-row>
                  <b-col class="font-italic fw6 text-center pb-2" style="font-size: 12px">
                    Barang dari DC
                  </b-col>
                </b-row>
              </div>
              <div v-else-if="orderDetail.deliveryTypeId === '2'">
                <b-row>
                  <b-col class="font-italic fw6 text-center pb-2" style="font-size: 12px">
                    Barang dari Marketplace
                  </b-col>
                </b-row>
              </div>
              <b-card
                v-if="!isLoading"
                no-body
                :class="{
                  lunas: orderDetail.paymentId !== 2 && orderDetail.paymentId !== 3,
                }">
                <b-row align-v="center">
                  <b-col cols="7" class="pr-0">
                    <div class="d-flex align-items-center">
                      <i
                        :class="`${
                          orderDetail.paymentId !== 2 && orderDetail.paymentId !== 3
                            ? 'icon-money'
                            : 'icon-hand-money'
                        }`" />
                      <p class="mb-0">
                        Pembayaran {{ orderDetail.paymentId | toPaymentName }}
                      </p>
                    </div>
                  </b-col>
                  <b-col cols="5" class="text-right">
                    {{ orderDetail.price | toRp }}
                  </b-col>
                </b-row>
              </b-card>
              <fragment v-if="!isLoading">
                <b-row v-if="orderDetail.deliveryTypeId === '0'" class="fw6">
                  <b-col cols="7">
                    {{ orderDetail.orderDate | reviewDateFormat }} WIB
                  </b-col>
                  <b-col cols="5" class="text-right font-italic">
                    <img
                      v-if="$route.params.status === '1104' || $route.params.status === '1105' || $route.params.status === '1106' || $route.params.status === '1107'"
                      src="~/assets/images/ic_finis.png">
                    <img v-else src="~/assets/images/ic_timer.png">
                    <span class="text-default">{{ orderDetail.timer }}</span> Menit
                  </b-col>
                </b-row>
                <b-row v-else>
                  <b-col cols="6" style="font-size: 12px; color: #616161">
                    {{ orderDetail.orderDate | reviewDateFormat }} WIB
                  </b-col>
                  <b-col cols="6" class="text-right text-default pl-0 fw6" style="font-size: 12px">
                    <fragment v-if="+orderDetail.timer < 0">
                      <span>Pengiriman : </span>
                      <span class="text-default fw7">{{
                        orderDetail.estimatedDeliveryDate
                      }}</span>
                    </fragment>
                    <fragment v-else>
                      <img src="~/assets/images/ic_timer.png" alt="timer">
                      <span class="text-default">{{ orderDetail.timer }}</span>
                      <span class="font-italic">Menit</span>
                    </fragment>
                  </b-col>
                </b-row>
              </fragment>
              <skeleton-box v-if="isLoading" height="111px" width="100%" class="mb-3" />
            </b-col>
          </b-row>
          <b-row class="pt-3 bg-white mx-0">
            <b-col cols="12">
              <div class="d-flex justify-content-between mb-3">
                <div>
                  <div
                    class="label fw7"
                    :class="[
                      { alfagift: orderDetail.appId === 379 },
                      { gomart: orderDetail.appId === 396 },
                      { grabmart: orderDetail.appId === 415 },
                      {
                        alfamind:
                          orderDetail.appId === 91 ||
                          orderDetail.appId === 92 ||
                          orderDetail.appId === 330 ||
                          orderDetail.appId === 335,
                      },
                    ]">
                    {{ orderDetail.app | formatAppName }}
                  </div>
                  <p class="mt-2 mb-0 order-no">
                    <img src="~/assets/images/ic_order.png" class="mr-2">
                    {{ orderDetail.shipmentNumber }}
                  </p>
                </div>
                <div v-if="$route.params.status !== '1106' && $route.params.status !== '1107'">
                  <small class="data-status">Status Data</small>
                  <p
                    class="mb-0 data-status"
                    :class="`${orderDetail.isSendStore !== 'F' ? 'sent' : 'pending'}`">
                    <b-icon-check-circle-fill />
                    <i class="icon-info" />
                    <span>{{ orderDetail.isSendStore | toSendStoreStatus }}</span>
                  </p>
                </div>
              </div>
            </b-col>
          </b-row>
          <b-row v-if="orderDetail.gomartPinStatus === 0 || orderDetail.gomartPinStatus === 1 || orderDetail.gomartPinStatus === 2" class="pt-0 bg-white text-center mx-0">
            <b-col cols="12 py-3">
              <b-alert
                v-if="orderDetail.gomartPinStatus === 0"
                show
                class="py-1 px-2 mb-0"
                variant="warning dash">
                <i class="icon-warning" />{{ orderDetail.gomartPinMessage }}
              </b-alert>
              <b-alert
                v-else-if="orderDetail.gomartPinStatus === 1"
                show
                class="py-1 px-2 mb-0"
                variant="success- dash">
                <i class="icon-thumb-up" />{{ orderDetail.gomartPinMessage }}
              </b-alert>
              <b-alert
                v-else-if="orderDetail.gomartPinStatus === 2"
                show
                class="py-1 px-2 mb-0"
                variant="warning dash">
                <i class="icon-warning" />WASPADA ! Pastikan total order pada struk senilai <b>{{ orderDetail.gomartPinMessage | toRp }}</b>. Jika berbeda maka batalkan order
              </b-alert>
            </b-col>
          </b-row>
          <b-row
            v-if="$route.params.status !== '1106' || $route.params.status !== '1107' || $route.params.status !== '1105' || orderDetail.showQr === true"
            class="bg-white mx-0"
            :class="[orderDetail.gomartPinStatus === 0 || orderDetail.gomartPinStatus === 1 || $route.params.status === '1106' || $route.params.status === '1107' || $route.params.status === '1105' || orderDetail.showQr === false || getRoleID === KeyRolePICView ? 'pt-1' : 'pt-4 pb-4']"
            align-h="center">
            <b-col v-if="$route.params.status !== '1105' && $route.params.status !== '1106' && $route.params.status !== '1107' && orderDetail.showQr === true && getRoleID !== KeyRolePICView" cols="10">
              <b-card v-if="$route.params.status !== '1106' || $route.params.status !== '1107' || $route.params.status !== '1105'" no-body class="barcode-scan">
                <b-row align-h="center" class="mx-0">
                  <b-col cols="5">
                    <img
                      slot="img"
                      class="img-fluid"
                      src="~/assets/images/bg_scan_barcode.png"
                      alt="qr">
                    <!-- <b-carousel
                      v-if="!isLoading"
                      id="carousel-1"
                      v-model="slideIndex"
                      :interval="0"
                      controls
                      no-wrap
                      indicators>
                      <b-carousel-slide v-for="(qr, index) in qrCode" :key="index">
                        <img
                          slot="img"
                          class="img-fluid"
                          :src="qr.Url"
                          alt="qr"
                          @click="$bvModal.show('modalQr')">
                      </b-carousel-slide>
                    </b-carousel> -->
                    <skeleton-box v-if="isLoading" height="111px" width="100%" class="mb-1" />
                  </b-col>
                  <!-- <b-col cols="12" class="text-center">
                    <p class="mb-0 fs-1em" :class="[ isLoading === true ? 'pt-0' : 'pt-3' ]">
                      {{ slideIndex + 1 }} / {{ qrCode.length }}
                    </p>
                  </b-col> -->
                  <b-col cols="12" class="text-center">
                    <p>
                      Scan QRcode ini di POS untuk kirim data order. Klik untuk
                      memperbesar.
                    </p>
                  </b-col>
                  <b-col cols="5" class="text-center">
                    <b-button
                      v-if="orderDetail.isPosAndroid === 'T'"
                      variant="default shadow-sm"
                      class="text-md"
                      block
                      :disabled="orderDetail.showQr === false"
                      @click="showModalConfirmShowQr()">
                      SHOW QR
                    </b-button>
                    <b-button
                      v-else
                      variant="default shadow-sm"
                      class="text-md"
                      block
                      :disabled="orderDetail.showQr === false"
                      @click="handleGenerateQrCode">
                      SHOW QR
                    </b-button>
                  </b-col>
                </b-row>
              </b-card>
            </b-col>
          </b-row>
          <div style="height: 10px;" />
          <b-row class="address-section pb-3 bg-white mx-0" :class="[$route.params.status === '1104' && orderDetail.app === 'GOMART' ? '' : 'pt-4']">
            <b-col cols="12">
              <p class="cust-name fw7 mb-2">
                {{ orderDetail.intendedReceiver | toPascalCase }}
              </p>
              <div class="d-flex">
                <div class="mr-3" style="width: 24px;">
                  <img
                    v-if="orderDetail.deliveryMethod === '1'"
                    src="~/assets/images/ic_kirim alamat.png"
                    class="img-fluid">
                  <img
                    v-else
                    src="~/assets/images/ic_Ambil toko.png"
                    class="img-fluid">
                </div>
                <div class="w-100">
                  <fragment v-if="orderDetail.deliveryMethod === '1'">
                    <div class="d-flex align-items-center justify-content-between">
                      <div>
                        <p class="mb-1">
                          Dikirim ke Alamat
                        </p>
                        <template v-if="$route.params.status === '1106' || $route.params.status === '1107'">
                          <p class="mb-0 address-detail">
                            Waktu : {{ orderDetail.orderDate | reviewDateFormat }} WIB
                          </p>
                          <p class="mb-0 address-detail">
                            Telepon : {{ orderDetail.deliveryPhone }}
                          </p>
                        </template>
                        <template v-else>
                          <p class="mb-0 address-detail">
                            {{ orderDetail.deliveryAddress | toPascalCase }}
                          </p>
                        </template>
                      </div>
                      <div v-if="$route.params.status !== '1106' && $route.params.status !== '1107'">
                        <i
                          class="icon-copy"
                          @click="copyToClipboard(orderDetail.deliveryAddress)" />
                      </div>
                    </div>
                  </fragment>
                  <fragment v-else>
                    <p class="mb-1">
                      Ambil di Toko
                    </p>
                    <p class="mb-0 address-detail">
                      Waktu : {{ orderDetail.estimatedDeliveryDate }},
                      {{ orderDetail.deliverySlotDesc }}.
                    </p>
                    <p class="mb-0 address-detail">
                      Telepon : {{ orderDetail.deliveryPhone }}
                    </p>
                  </fragment>
                  <div v-if="$route.params.status !== '1106' && $route.params.status !== '1107'">
                    <div class="d-flex justify-content-between" style="column-gap: 5px;">
                      <div v-if="orderDetail.deliveryMethod === '1'">
                        <a
                          :href="orderDetail.deliveryDirection"
                          target="_blank"
                          style="text-decoration: none">
                          <b-button variant="white shadow-sm" :disabled="getRoleID === KeyRolePICView" class="fs-1em" block>
                            <i class="icon-sign-right text-primary" /> Arahkan
                          </b-button>
                        </a>
                      </div>
                      <div :offset="orderDetail.deliveryMethod === '0' ? '1' : ''">
                        <a
                          :href="'tel:' + orderDetail.deliveryPhone"
                          style="text-decoration: none">
                          <b-button variant="white shadow-sm" :disabled="getRoleID === KeyRolePICView" class="fs-1em" block>
                            <i class="icon-phone text-primary" />
                            Call
                          </b-button>
                        </a>
                      </div>
                      <div>
                        <b-button
                          variant="white shadow-sm"
                          class="fs-1em"
                          :disabled="getRoleID === KeyRolePICView"
                          block
                          s
                          @click="openModalChat">
                          <i class="demo-icon icon-whatsapp text-success" />
                          WhatsApp
                        </b-button>
                      </div>
                    </div>
                    <div>
                      <fragment v-if="orderDetail.showChat && orderDetail.appId === 379">
                        <fragment v-if="orderDetail.isChatEnabled">
                          <div
                            :class="[
                              'mt-3 chat-container',
                              { disableChatContainer: disableChatContainer }
                            ]">
                            <ButtonChat
                              :btn-text="'Chat customer di sini'"
                              :btn-mode="'detail'"
                              :unread-chat-count="getUnreadChat"
                              :room-id="orderDetail.roomId"
                              :shipment-no="orderDetail.shipmentNumber"
                              :order-no="orderDetail.orderNumber"
                              @updateChatCount="updateCount_"
                              @emitCheckVersion="checkVersion" />
                          </div>
                          <div
                            v-if="orderDetail.sapaStatus == 1104"
                            :class="[
                              'mt-2 chat-container information-box',
                              { disableChatContainer: disableChatContainer }
                            ]">
                            <div>{{ chatButtonI[orderDetail.sapaStatus] }}</div>
                          </div>
                        </fragment>
                        <fragment v-else>
                          <div
                            :class="[
                              'mt-3 chat-container',
                              { disableChatContainer: disableChatContainer }
                            ]">
                            <ButtonChat
                              :btn-text="'Chat berakhir'"
                              :btn-mode="'detail'"
                              :is-chat-ended="true"
                              @emitCheckVersion="checkVersion" />
                          </div>
                          <div
                            :class="[
                              'mt-2 chat-container information-box',
                              { disableChatContainer: disableChatContainer }
                            ]">
                            <div>{{ chatButtonI[0] }}</div>
                          </div>
                        </fragment>
                      </fragment>
                    </div>
                  </div>
                </div>
              </div>
            </b-col>
            <fragment v-if="orderDetail.sapaStatus === 1105 && orderDetail.appId == 379 && orderDetail.showChat">
              <fragment v-if="orderDetail.isChatEnabled">
                <b-col
                  cols="auto"
                  :class="[
                    'pr-1 offset-1 mt-3 chat-container',
                    { disableChatContainer: disableChatContainer }
                  ]">
                  <ButtonChat
                    :btn-text="'Chat customer di sini'"
                    :btn-mode="'detail'"
                    :unread-chat-count="getUnreadChat"
                    :room-id="orderDetail.roomId"
                    :shipment-no="orderDetail.shipmentNumber"
                    :order-no="orderDetail.orderNumber"
                    @updateChatCount="updateCount_"
                    @emitCheckVersion="checkVersion" />
                </b-col>
                <b-col
                  cols="auto"
                  :class="[
                    'pr-1 offset-1 mt-2 chat-container information-box',
                    { disableChatContainer: disableChatContainer }
                  ]">
                  <div>{{ chatButtonI[orderDetail.sapaStatus] }}</div>
                </b-col>
              </fragment>
              <fragment v-else>
                <b-col
                  cols="auto"
                  :class="[
                    'pr-1 offset-1 mt-3 chat-container',
                    { disableChatContainer: disableChatContainer }
                  ]">
                  <ButtonChat
                    :btn-text="'Chat berakhir'"
                    :btn-mode="'detail'"
                    :is-chat-ended="true"
                    @emitCheckVersion="checkVersion" />
                </b-col>
                <b-col
                  cols="auto"
                  :class="[
                    'pr-1 offset-1 mt-2 chat-container information-box',
                    { disableChatContainer: disableChatContainer }
                  ]">
                  <div>{{ chatButtonI[0] }}</div>
                </b-col>
              </fragment>
            </fragment>
          </b-row>
          <div style="height: 10px;" />
          <b-row class="pt-3 bg-white mx-0">
            <b-col :cols="$route.params.status === '1102' ? '8' : '10' " class="fw7">
              Daftar Barang
            </b-col>
            <b-col
              :cols="$route.params.status === '1102' ? '2 pr-0' : '2'"
              class="fw7 text-center">
              Qty
            </b-col>
          </b-row>
          <b-row class="bg-white pt-3 mx-0">
            <b-col cols="12">
              <b-form-checkbox-group
                id="checkboxProduct"
                v-model="selected"
                :disabled="getRoleID === KeyRolePICView"
                stacked>
                <b-row
                  v-for="(product, index) in orderProducts"
                  :key="index"
                  align-v="center"
                  class="mb-3">
                  <b-col
                    cols="2">
                    <b-card no-body class="p-1">
                      <LazyImage
                        :src="require('~/assets/images/placeholder_fail_to_load.png')"
                        :lazy-src="product.image"
                        :alt="product.productName"
                        :lazy-class="'img-fluid'" />
                    </b-card>
                  </b-col>
                  <b-col
                    :cols="$route.params.status === '1102' ? '6' : '8'"
                    class="pl-0">
                    <p class="mb-0 product-name">
                      {{ product.productName }}
                    </p>
                    <p v-if="product.isStockBackup === 1 && orderDetail.storeTier === 4 && ($route.params.status === '1101' || $route.params.status === '1102' || $route.params.status === '1103')" class="mb-0 label label-stock-backup">
                      Stock backup
                    </p>
                    <p v-else-if="product.isStockBackup === 0 && orderDetail.storeTier === 4 && ($route.params.status === '1101' || $route.params.status === '1102' || $route.params.status === '1103')" class="mb-0 label label-stock-tersedia">
                      Stock: {{ product.stockOrigin }}
                    </p>
                  </b-col>
                  <b-col
                    cols="2"
                    class="text-center"
                    :class="[{ 'pr-0': $route.params.status === '1102' }, { 'text-primary': product.qty !== product.qtyOriginal } ]">
                    {{ product.qty }}
                  </b-col>
                  <b-col v-if="$route.params.status === '1102'" :cols="(orderDetail.appId === 396 || orderDetail.appId === 415) ? '1 text-right' : '2 text-right'" class="pl-0">
                    <b-form-checkbox name="ck-filter-alfagift" :value="product.productID" />
                  </b-col>
                  <b-col v-if="$route.params.status === '1102' && (orderDetail.appId === 396 || orderDetail.appId === 415)" cols="1" class="pl-0">
                    <i v-if="product.sku !== 'A7503470001001' && product.sku !== 'A7542550001311'" class=" icon-pencil" :class="{ 'text-primary': product.qty !== product.qtyOriginal }" @click="openModalEditQty(product)" />
                  </b-col>
                </b-row>
              </b-form-checkbox-group>
            </b-col>
          </b-row>
          <b-row class="bg-white mx-0">
            <b-col cols="12">
              <hr class="my-0">
            </b-col>
          </b-row>
          <b-row class="pt-3 bg-white mx-0">
            <b-col :cols="$route.params.status === '1102' ? '8' : '10' " class="fw7">
              Total Qty :
            </b-col>
            <b-col
              :cols="$route.params.status === '1102' ? '2 pr-0' : '2'"
              class="fw7 text-center">
              {{ totalQuantity }}
            </b-col>
          </b-row>
          <!-- Tampilkan setelah MAT terbuat -->
          <div v-if="orderDetail.sapaStatus === 1102 && orderDetail.appId !== 396 && orderDetail.appId !== 415 && orderDetail.matNo !== null && orderDetail.matStatus !== 0">
            <div class="pt-3 bg-white">
              <div class="mat-trigger d-flex align-items-center justify-content-between p-3">
                <div v-if="orderDetail.matStatus === 0" />
                <div v-else-if="orderDetail.matStatus === 1321" class="d-flex align-items-center">
                  <b-icon-clock-fill class="text-lg mr-2" />
                  <span class="fw6 text-md">Pengajuan Mat</span>
                </div>
                <div v-else-if="orderDetail.matStatus === 1322" class="d-flex align-items-center">
                  <b-icon-clock-fill class="text-lg mr-2" />
                  <span class="fw6 text-md">Dalam proses MAT</span>
                </div>
                <div v-else-if="orderDetail.matStatus === 1323" class="d-flex align-items-center">
                  <b-icon-clock-fill class="text-lg mr-2" />
                  <span class="fw6 text-md">Mat Selesai/Batal</span>
                </div>
                <nuxt-link :to="'/mat'">
                  <b-button variant="link p-0 text-secondary text-decoration-none text-md fw7">
                    {{ orderDetail.matStatus === 1323 ? 'Selesai' : 'Lihat MAT' }}
                    <b-icon-arrow-right class="ml-1 text-secondary" />
                  </b-button>
                </nuxt-link>
              </div>
            </div>
          </div>

          <template v-if="$route.params.status !== '1104' && $route.params.status !== '1105' && $route.params.status !== '1106' && $route.params.status !== '1107'">
            <b-row v-if="$route.params.status === '1102' && (orderDetail.appId !== 396 && orderDetail.appId !== 415)" class="bg-white pb-3 pt-4 mx-0">
              <b-col cols="12">
                <div class="d-flex align-items-center">
                  <div class="mr-2" style="flex: 0 0 86px;">
                    <b-button
                      v-b-modal.create-mat
                      variant="outline-primary"
                      block
                      :disabled="
                        orderDetail.matStatus === 1322 || orderDetail.matStatus === 1323 ">
                      <b-icon-plus /> MAT
                    </b-button>
                  </div>
                  <div class="w-100">
                    <!-- <nuxt-link :to="'/order'"> -->
                    <b-button
                      variant="default shadow-sm"
                      block
                      :disabled="
                        orderDetail.matStatus === 1322">
                      Proses
                    </b-button>
                    <!-- </nuxt-link> -->
                  </div>
                  <!-- <div class="w-100">
                    <b-button
                      variant="default shadow-sm"
                      block
                      :disabled="
                        $route.params.status === '1102' &&
                          selected.length !== orderProducts.length || orderDetail.returRefundID !== 0 && orderDetail.returRefundStatus !== 1306
                      "
                      @click="handleScanPacking($route.params.status)">
                      {{ $route.params.status | toButtonOrderDetailName }}
                    </b-button>
                  </div> -->
                </div>
              </b-col>
            </b-row>
            <b-row v-if="$route.params.status === '1102' && (orderDetail.appId === 396 || orderDetail.appId === 415)" class="bg-white pb-3 pt-4 mx-0">
              <b-col cols="4">
                <b-button
                  v-if="orderDetail.appId === 396 || orderDetail.appId === 415"
                  variant="white shadow-sm text-secondary border-btn-white"
                  block
                  :disabled="orderDetail.returRefundID !== 0 && orderDetail.returRefundStatus !== 1306"
                  @click="doOpenModalConfirmCancel()">
                  Batal
                </b-button>
              </b-col>
              <b-col cols="8" class="pl-0">
                <b-button
                  variant="default shadow-sm"
                  block
                  :disabled="
                    $route.params.status === '1102' &&
                      selected.length !== orderProducts.length || totalQuantity === 0 || orderDetail.returRefundID !== 0 && orderDetail.returRefundStatus !== 1306
                  "
                  @click="handleScanPacking($route.params.status)">
                  {{ $route.params.status | toButtonOrderDetailName }}
                </b-button>
              </b-col>
            </b-row>
            <b-row v-if="$route.params.status !== '1102'" cols="12" class="bg-white pb-3 pt-4 mx-0">
              <b-col v-if="orderDetail.qrAndroid && orderDetail.billNo === '' && orderDetail.isPosAndroid === 'T'" cols="12">
                <b-button
                  variant="default shadow-sm"
                  block
                  :disabled="$route.params.status === '1103' && (orderDetail.gomartPinStatus !== 1 && orderDetail.gomartPinStatus !== 2) && (orderDetail.appId == 396 || orderDetail.appId == 415) || getRoleID === KeyRolePICView || orderDetail.returRefundID !== 0 && orderDetail.returRefundStatus !== 1306"
                  @click="openFlutterScanner()">
                  Scan QR Receipt
                </b-button>
              </b-col>
              <b-col v-if="orderDetail.qrAndroid && orderDetail.billNo !== '' || orderDetail.isPosAndroid !== 'T' || !orderDetail.qrAndroid" :cols="'12'">
                <b-button
                  variant="default shadow-sm"
                  block
                  :disabled="$route.params.status === '1103' && (orderDetail.gomartPinStatus !== 1 && orderDetail.gomartPinStatus !== 2) && (orderDetail.appId == 396 || orderDetail.appId == 415) || getRoleID === KeyRolePICView || orderDetail.returRefundID !== 0 && orderDetail.returRefundStatus !== 1306"
                  @click="handleOrderDetail($route.params.status)">
                  {{ $route.params.status | toButtonOrderDetailName }}
                </b-button>
              </b-col>
            </b-row>
          </template>
          <b-toast
            id="toastCopy"
            no-close-button
            :auto-hide-delay="1000"
            no-hover-pause
            :toast-class="''"
            :body-class="'text-center'"
            :toaster="'b-toaster-top-center'">
            {{ toastText }}
          </b-toast>
        <!--        </pull-to>-->
        </b-container>
        <ModalQrCode
          :shipment-no="orderDetail.shipmentNumber"
          :qr-code="qrCodeData"
          :slide-index="slideIndex" />
        <ModalConfirmDc
          v-if="$route.params.status === '1101' && orderDetail.deliveryTypeId === '1'"
          :shipment-no="orderDetail.shipmentNumber" />
        <BarcodeScanner ref="barcodeScanner" @succesScan="doUpdateReceiptQrPosAndroid" />
      </div>
    </template>
    <template v-else>
      <ModalConfirmPrintBill
        v-if="$route.params.status === '1102'"
        :shipment-no="orderDetail.shipmentNumber"
        :application="orderDetail.appId" />
      <ScanPacking @handleProses="handleOrderDetail" @hideScanpacking="hideScanPacking" />
    </template>
    <ModalError />
    <ModalChat :order-detail="orderDetail" />
    <ModalScanPackinInfo :scan-packing-info="scanPackingInfo" />
    <ModalEditQty :product="productQty" @do-set-checkbox-product="doSetCheckboxProduct" />
    <ModalConfirmCancel @do-post-goamrt-cancel="doPostOrderGomartCancel" />
    <ModalConfirmShowQr @do-generate-qr="handleGenerateQrCode" />
    <ModalMATOos
      v-if="showModal"
      :order-detail="orderDetail"
      :mat-no="matNo"
      :shipment-number="shipmentNumber"
      @close="showModal = false" />
    <ModalConfirmMATOos ref="confirmModal" />
  </div>
</template>

<script>
import { computed, ref, watch } from '@vue/composition-api'
import {
  BIconCheckCircleFill,
  BIconChevronRight,
  BIconPlus,
  BToast,
  BIconClockFill,
  BIconArrowRight
} from 'bootstrap-vue'
// import PullTo from 'vue-pull-to'
import SkeletonBox from '~/components/_base/SkeletonBox.vue'
import ModalQrCode from '~/components/order/detail/ModalQrCode'
import ModalEditQty from '~/components/order/detail/ModalEditQty.vue'
import ModalChat from '~/components/order/detail/ModalChat.vue'
import ModalConfirmDc from '~/components/order/detail/ModalConfirmDc'
import ModalConfirmPrintBill from '~/components/order/detail/ModalConfirmPrintBill'
import ModalError from '~/components/_base/ModalError'
import ScanPacking from '~/components/order/detail/ScanPacking.vue'
import ModalScanPackinInfo from '~/components/order/detail/ModalScanPackinInfo.vue'
import BarcodeScanner from '~/components/order/detail/BarcodeScanner.vue'
import ModalConfirmCancel from '~/components/order/detail/ModalConfirmCancel.vue'
import ModalMATOos from '~/components/order/detail/ModalMATOos.vue'
import { ACT_ORDER_DETAIL } from '~/constants/action-types'
import { GTR_LOADING, GTR_ORDER_DETAIL, GTR_MEMBER } from '~/constants/getter-types'
import { MTT_ORDER_DETAIL } from '~/constants/mutation-types'
import { postOrderPacking, generateQrCode, postOrderGomartCancel, updateReceiptPosAndroid } from '~/api/order-detail'
import { handleError, toLocalTimeZone } from '~/utils/custom-helpers'
import { APIS } from '~/constants/constant'
import ButtonChat from '~/components/in-app-chat/ButtonChat.vue'
import useNative from '~/composable/native-composable'
import ModalConfirmShowQr from '~/components/order/detail/ModalConfirmShowQr.vue'
import ModalConfirmMATOos from '~/components/order/detail/ModalConfirmMATOos.vue'

export default {
  name: 'OrderDetail',
  components: {
    SkeletonBox,
    BIconCheckCircleFill,
    BIconPlus,
    BToast,
    ModalQrCode,
    ModalConfirmDc,
    ModalConfirmPrintBill,
    BIconChevronRight,
    ModalError,
    ScanPacking,
    ModalScanPackinInfo,
    ModalChat,
    BarcodeScanner,
    ModalEditQty,
    ModalConfirmCancel,
    ModalConfirmShowQr,
    ModalMATOos,
    ButtonChat,
    BIconClockFill,
    BIconArrowRight,
    ModalConfirmMATOos
    // 'pull-to': PullTo
  },
  data () {
    return {
      orderDetail: {},
      showModal: false,
      matNo: null,
      shipmentNumber: null // add this new property
    }
  },
  emits: ['hideScanpacking', 'handleProses'],
  setup (_, context) {
    const { getVersiApps } = useNative(context)
    const doGetOrderDetail = () => {
      const params = {
        route: context.root.$route.params
      }
      context.root.$store.dispatch(
        ACT_ORDER_DETAIL.base + '/' + ACT_ORDER_DETAIL.doGetOrderDetail,
        params
      ).then((response) => {
        if (response && response.data && response.data.message === 'sql: no rows in result set') {
          context.root.$router.push('/')
        }
      }).catch((error) => {
        console.log('Error:', error)
      })
    }
    const getRoleID = computed(() => context.root.$store.getters[GTR_MEMBER.base + '/' + GTR_MEMBER.getRoleID])
    const KeyRolePICView = APIS.ROLE_ID.KeyRolePICView

    const onRefresh = () => {
      return new Promise((resolve, reject) => {
        const reload = window.location.reload()
        setTimeout(() => {
          resolve(reload)
        }, 1000)
      })
    }

    const TOP_DEFAULT_CONFIG = {
      pullText: 'plung', // The text is displayed when you pull down
      triggerText: 'Mohon tunggu...', // The text that appears when the trigger distance is pulled down
      loadingText: 'Mohon tunggu...', // The text in the load
      doneText: 'selamat', // Load the finished text
      failText: 'Gagal', // Load failed text
      loadedStayTime: 400, // Time to stay after loading ms
      stayDistance: 60, // Trigger the distance after the refresh
      triggerDistance: 10
    }

    const isLoading = computed(() => context.root.$store.getters[GTR_LOADING.base + '/' + GTR_LOADING.getLoading])
    const orderDetail = computed(() => {
      return context.root.$store.getters[
        GTR_ORDER_DETAIL.base + '/' + GTR_ORDER_DETAIL.getOrderDetail
      ]
    })

    const orderProducts = computed(() => {
      return context.root.$store.getters[
        GTR_ORDER_DETAIL.base + '/' + GTR_ORDER_DETAIL.getOrderProducts
      ]
    })

    const productScanned = computed(() => {
      return context.root.$store.getters[
        GTR_ORDER_DETAIL.base + '/' + GTR_ORDER_DETAIL.getOrderProductScan
      ]
    })

    const totalQuantity = computed(() => {
      let total = 0
      for (let i = 0; i < orderProducts.value.length; i++) {
        total += orderProducts.value[i].qty
      }
      return total
    })

    const qrCode = computed(() => {
      return context.root.$store.getters[
        GTR_ORDER_DETAIL.base + '/' + GTR_ORDER_DETAIL.getQrCode
      ]
    })

    const selected = ref([])

    const slideIndex = ref(0)

    const makeToast = (text) => {
      toastText.value = 'Direction tidak tersedia'
      context.root.$bvToast.show('toastCopy')
    }

    const toastText = ref('')
    const copyToClipboard = (value) => {
      const tempInput = document.createElement('input')
      tempInput.value = value
      document.body.appendChild(tempInput)
      tempInput.select()
      document.execCommand('copy')
      document.body.removeChild(tempInput)
      toastText.value = 'Text berhasil disalin'
      context.root.$bvToast.show('toastCopy')
    }

    const isModalQROpen = ref(false)
    const openModalQRNew = () => {
      isModalQROpen.value = true
    }

    const secondsWithLeadingZeros = (dt) => {
      return /\((.*)\)/.exec(dt.toString())[1]
    }

    const handleScanPacking = () => {
      for (let i = 0; i < orderProducts.value.length; i++) {
        const product = productScanned.value.find(x => x.productID === orderProducts.value[i].productID)
        if (!product && orderProducts.value[i].qty === 0) {
          const product = {
            barcodeID: orderProducts.value[i].barcodeID,
            from: orderProducts.value[i].from,
            image: orderProducts.value[i].image,
            inputQty: orderProducts.value[i].qty,
            onInputQty: orderProducts.value[i].qty,
            productID: orderProducts.value[i].productID,
            productName: orderProducts.value[i].productName,
            qty: orderProducts.value[i].qty,
            qtyOriginal: orderProducts.value[i].qtyOriginal,
            tbtdID: orderProducts.value[i].tbtdID,
            tbtoID: orderProducts.value[i].tbtoID,
            tbtopID: orderProducts.value[i].tbtopID
          }
          context.root.$store.dispatch(
            ACT_ORDER_DETAIL.base + '/' + ACT_ORDER_DETAIL.setOrderProductScan,
            product
          )
        }
      }
      isScanpackingShow.value = true
    }

    const qrCodeData = ref([])
    const handleGenerateQrCode = async () => {
      try {
        const response = await generateQrCode(orderDetail.value.shipmentNumber, orderDetail.value.orderNumber)
        if (
          response &&
          orderDetail.value.showQr === true &&
          response.data &&
          Object.keys(response.data.payload).length !== 0 &&
          response.data.payload.constructor === Object
        ) {
          context.root.$store.commit(MTT_ORDER_DETAIL.base + '/' + MTT_ORDER_DETAIL.setQrAndroid, response.data.payload.flagQrAndroid)
          qrCodeData.value = [...response.data.payload.Head, ...response.data.payload.Body]
          context.root.$bvModal.show('modalQr')
        }
      } catch (error) {
        handleError(context.root.$store.dispatch, error, 'close-modal')
      }
    }

    const handleOrderDetail = (status) => {
      if (status === '1101') {
        if (orderDetail.value.deliveryTypeId === '1') {
          context.root.$bvModal.show('modalConfirmDc')
        } else {
          postOrderPacking(orderDetail.value.shipmentNumber, toLocalTimeZone(secondsWithLeadingZeros(new Date()))).then(() => {
            context.root.$router.push('/list-packing')
          }).catch((error) => {
            handleError(context.root.$store.dispatch, error, 'go-to-order')
          })
        }
      } else if (
        status === '1102' &&
        selected.value.length === orderProducts.value.length
      ) {
        context.root.$bvModal.show('modalConfirmPrintBill')
      } else if (status === '1103') {
        context.root.$router.push('/order/confirm/1103/' + context.root.$route.params.shipmentNo)
      }
    }

    doGetOrderDetail()

    // scan packing here
    const isScanpackingShow = ref(false)

    const hideScanPacking = () => {
      isScanpackingShow.value = false
    }

    const scanPackingInfo = ref({})
    const showScanPackingInfo = (title, text, type) => {
      scanPackingInfo.value = {
        show: true,
        title,
        text,
        type
      }
    }

    const openModalChat = () => {
      context.root.$bvModal.show('modalChat')
    }

    const backToOrderList = (status) => {
      if (status === '1101') {
        // context.root.$router.push('/order')
        context.root.$router.push({ name: 'Pesanan' })
      } else if (status === '1102') {
        if (productScanned.value.length > 0) {
          showScanPackingInfo('Kembali', 'Data produk yg sudah discan berpotensi akan hilang. anda yakin ingin kembali ?', 'confirm-back')
        } else {
          context.root.$router.push('/list-packing')
        }
      } else if (status === '1103') {
        context.root.$router.push('/list-proses')
      } else if (status === '1104') {
        context.root.$router.push('/list-done')
      } else if (status === '1105' || status === '1107') {
        context.root.$router.push('/history')
      } else if (status === '1106') {
        context.root.$router.push('/list-cancel')
      } else {
        context.root.$router.push('/')
      }
      window?.flutter_inappwebview?.callHandler('goBack')
    }

    const barcodeScanner = ref(null)

    const updateReceiptQrPosAndroid = async (result) => {
      console.log('QR ID = ' + JSON.stringify(result))
      barcodeScanner.value.openCloseScanner(false)
      const data = JSON.parse(result)
      // alert(data.no)
      if (data.no !== orderDetail.value.shipmentNumber) {
        const err = {
          response: {
            data: {
              code: 400,
              message: 'Nomor order tidak sesuai'
            }
          }
        }
        handleError(context.root.$store.dispatch, err, 'close-modal')
        return
      }
      try {
        const response = await updateReceiptPosAndroid(data)
        if (response.data.code === 200) {
          context.root.$store.commit(MTT_ORDER_DETAIL.base + '/' + MTT_ORDER_DETAIL.setBillno, data.billNo)
          showScanPackingInfo('Info', 'Update receipit berhasil', 'ok')
        }
      } catch (error) {
        handleError(context.root.$store.dispatch, error, 'close-modal')
      }
    }

    const doUpdateReceiptQrPosAndroidV2 = async (result) => {
      console.log('QR ID = ' + JSON.stringify(result))
      barcodeScanner.value.openCloseScanner(false)
      const data = JSON.parse(result)
      // alert('Shipment No :: ' + data.no)
      if (data.no !== orderDetail.value.shipmentNumber) {
        const err = {
          response: {
            data: {
              code: 400,
              message: 'Nomor order tidak sesuai'
            }
          }
        }
        handleError(context.root.$store.dispatch, err, 'close-modal')
        return
      }
      try {
        const response = await updateReceiptPosAndroid(data)
        if (response.data.code === 200) {
          context.root.$store.commit(MTT_ORDER_DETAIL.base + '/' + MTT_ORDER_DETAIL.setBillno, data.billNo)
          showScanPackingInfo('Info', 'Update receipit berhasil', 'ok')
        }
      } catch (error) {
        handleError(context.root.$store.dispatch, error, 'close-modal')
      }
    }

    let timer
    const doUpdateReceiptQrPosAndroid = (result) => {
      clearTimeout(timer)
      timer = setTimeout(() => {
        updateReceiptQrPosAndroid(result)
      }, 1000)
    }

    const openFlutterScanner = async () => {
      const appVersion = await getVersiApps()
      if (appVersion >= 10) {
        // @param (nativeFlutterFunction, scanMode, callBack)
        window.flutter_inappwebview.callHandler('openFlutterBarcodeScanner', 'qr', 'window.$nuxt.$children[1].$children[0].$children[0].$children[0].doUpdateReceiptQrPosAndroidV2(\'[result]\');')
        // window.flutter_inappwebview.callHandler('openBarcodeScanner', 'qr', 'window.$nuxt.$children[1].$children[0].$children[0].$children[0].doUpdateReceiptQrPosAndroidV2(\'[result]\');')
      } else {
        barcodeScanner.value.openCloseScanner(true)
      }
    }

    const productQty = ref({})
    const openModalEditQty = (product) => {
      if (orderDetail.value.appId === 396 || orderDetail.value.appId === 415) {
        productQty.value = product
        context.root.$bvModal.show('modalEditQty')
      }
    }
    const doSetCheckboxProduct = (productID) => {
      const indexItem = selected.value.findIndex(val => val === productID)
      if (indexItem === -1) {
        selected.value.push(productID)
      }
    }
    const doOpenModalConfirmCancel = () => {
      if (orderDetail.value.appId === 396 || orderDetail.value.appId === 415) {
        context.root.$bvModal.show('modalConfirmCancel')
      }
      // if (orderDetail.value.appId === 415) {
      //   showScanPackingInfo('', 'Untuk melakukan pembatalan hubungi CS Grab (021) 8064-8787', 'cancel-order')
      //   // context.root.$router.push('/list-cancel')
      // }
    }

    const showModalConfirmShowQr = () => {
      context.root.$bvModal.show('modalConfirmShowQr')
    }

    const doPostOrderGomartCancel = async (data) => {
      try {
        data.shipmentNumber = orderDetail.value.shipmentNumber
        console.log(JSON.stringify(data))
        const response = await postOrderGomartCancel(data)
        console.log(response.data)
        if (response.data.code === 200) {
          // showScanPackingInfo('', 'Order berhasil dibatalkan', 'cancel-order')
          context.root.$router.push('/list-cancel')
        }
      } catch (error) {
        handleError(context.root.$store.dispatch, error, 'close-modal')
      }
    }

    const getUnreadChat = ref(orderDetail.unreadChatCount)

    const chatButtonI = {
      0: 'Fitur chat tidak bisa dilakukan',
      1104: 'Customer masih bisa menghubungi 1 jam setelah pesanan dikonfirmasi',
      1105: 'Customer masih bisa menghubungi 1 jam setelah pesanan batal'
    }

    const chatButtonIStatusArr = [1104, 1106]

    const updateCount_ = (val) => {
      getUnreadChat.value = val
    }

    watch(orderDetail, (x) => {
      getUnreadChat.value = x.unreadChatCount
    })

    const disableChatContainer = ref(false)
    const checkVersion = (bool) => {
      disableChatContainer.value = bool
    }

    return {
      orderDetail,
      orderProducts,
      totalQuantity,
      copyToClipboard,
      handleOrderDetail,
      qrCode,
      selected,
      slideIndex,
      isLoading,
      toastText,
      makeToast,
      onRefresh,
      TOP_DEFAULT_CONFIG,
      isScanpackingShow,
      handleScanPacking,
      hideScanPacking,
      scanPackingInfo,
      showScanPackingInfo,
      backToOrderList,
      openModalChat,
      KeyRolePICView,
      getRoleID,
      openModalQRNew,
      handleGenerateQrCode,
      qrCodeData,
      barcodeScanner,
      openModalEditQty,
      productQty,
      doSetCheckboxProduct,
      doOpenModalConfirmCancel,
      doPostOrderGomartCancel,
      doUpdateReceiptQrPosAndroid,
      doUpdateReceiptQrPosAndroidV2,
      openFlutterScanner,
      getUnreadChat,
      chatButtonI,
      chatButtonIStatusArr,
      updateCount_,
      disableChatContainer,
      checkVersion,
      showModalConfirmShowQr
    }
  }
}
</script>

<style scoped lang="scss">
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
    color: #f54b2c;
  }
  &.alfamind {
    color: #f35b17;
  }
  &.gomart {
    color: #d71716;
  }
}
.subheader {
  background-color: #fff8e7;
  margin-top: -15px;
  padding-top: 25px;
  padding-bottom: 10px;
  color: #a2a29e;
  &.urgent {
    background-color: #ffe592;
    border: solid 1px #d1a466;
    color: #5f5d56;
  }
  &.dc {
    background-color: #e3f9ff;
    color: #20a5c9;
  }
  &.marketplace-product {
    background-color: #c1ffbd;
    color: #21bb5c;
  }
  .card {
    background-color: #ff7f01;
    border: 0;
    color: #ffffff;
    font-size: 16px;
    font-weight: 600;
    padding: 4px 15px;
    margin-bottom: 12px;
    &.lunas {
      background-color: #1cb869;
    }
    i {
      font-size: 26px;
    }
  }
  img {
    height: 18px;
    margin-top: -3px;
    margin-right: 5px;
  }
  .transaction-info {
    padding: 11px 0;
    background-color: #d4f7ff;
    border: solid 1px #22a6ca;
    font-size: 13px;
    font-weight: 400;
    color: #96948d;
    i {
      font-size: 15px;
      color: #22a6ca;
    }
    svg {
      font-size: 15px !important;
    }
  }
  .transaction-cancel {
    padding: 11px 0;
    background-color: #ffffff;
    border: solid 1px #a1a1a1;
    font-size: 13px;
    font-weight: 400;
    color: #000000;
    i {
      font-size: 15px;
      color: #22a6ca;
    }
    svg {
      font-size: 15px !important;
    }
  }
}
.data-status {
  font-size: 12px;
  color: #d5d5d5;
  svg {
    font-size: 10px;
    top: -1px;
    position: relative;
    left: 3px;
    margin-right: 6px;
  }
  &.pending {
    color: #bc7a09;
    svg {
      display: none;
    }
  }
  &.sent {
    color: #20b86a;
    i {
      display: none;
    }
  }
}
.order-no {
  font-size: 16px;
  img {
    height: 20px;
  }
}
.barcode-scan {
  border: 0;
  background-color: #f6f6f6;
  padding-top: 20px;
  padding-bottom: 20px;
  border-radius: 20px;
  p {
    font-size: 11px;
    color: #8c8c8c;
    line-height: 16px;
    margin-top: 15px;
  }
}
.address-section {
  .cust-name {
    font-size: 16px;
  }
  .address-detail {
    color: #6d6d6d;
  }
  .icon-copy {
    font-size: 20px;
    color: #f23007;
  }
}
.product-name {
  color: #6d6d6d;
}

.label-stock-backup {
  font-size: 12px;
  color: #025da6;
  font-style: italic;
}
.label-stock-tersedia {
  color: #6d6d6d;
  font-size: 12px;
  font-style: italic;
}

.fs-1em {
  font-size: 1em !important;
}
#toastCopy {
  background-color: #2a2a2abb;
  border-radius: 8px;
  .toast-body {
    // font-size: 12px;
    color: white;
    padding: 0.5rem;
  }
}
.b-toaster {
  top: 50% !important;
  margin-top: -18px !important;
}
.carousel-control-prev {
  left: -40px;
}
.carousel-control-next {
  right: -40px;
}
.carousel-control-prev-icon {
  background-image: url("data:image/svg+xml;charset=utf8,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='%666666aa' viewBox='0 0 8 8'%3E%3Cpath d='M5.25 0l-4 4 4 4 1.5-1.5-2.5-2.5 2.5-2.5-1.5-1.5z'/%3E%3C/svg%3E") !important;
}

.carousel-control-next-icon {
  background-image: url("data:image/svg+xml;charset=utf8,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='%666666aa' viewBox='0 0 8 8'%3E%3Cpath d='M2.75 0l-1.5 1.5 2.5 2.5-2.5 2.5 1.5 1.5 4-4-4-4z'/%3E%3C/svg%3E") !important;
}
.carousel-indicators {
  bottom: -40px;
  & li {
    background-color: #666666aa;
  }
}
.chat-container {
  height: 50px;
}
.information-box {
  color: #999999;
  font-size: 13px;
  height: 33px;
}
.disableChatContainer {
  display: none!important;
}
.current-action-sheet {
  padding-bottom: 138px !important;
}
</style>
