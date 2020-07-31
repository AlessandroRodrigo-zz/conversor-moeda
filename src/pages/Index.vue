<template>
  <q-page class="flex items-stretch container">
    <div class="bg-white full-width col-grow rounded-borders cardDefault row">
      <div class="col q-pa-xl flex items-center">
        <div>
          <h2 class="text-bold text-grey-6 q-mb-none">
            Family <span class="text-primary">Coins</span>
          </h2>
          <h6 class="text-grey-6 q-mt-sm">
            Seu conversor de moedas preferido de real para outras moedas
          </h6>
        </div>
        <div class="full-width" transition="scale">
          <transition name="fade" mode="out-in">
            <q-form
              key="form"
              v-if="pageAlternator"
              @submit.prevent="convertCoins"
              class="full-width"
            >
              <div class="row q-gutter-sm">
                <q-select
                  v-model="newCoin"
                  :options="coinsList"
                  class="col"
                  :loading="loadingSelect"
                  option-label="nameConverted"
                  outlined
                  :rules="[(value) => !!value || 'Esse campo é obrigatório']"
                  label="Moeda"
                />
              </div>
              <div class="row q-mt-sm">
                <q-input
                  v-model="valueCoin"
                  class="col-12"
                  outlined
                  mask="#,##"
                  reverse-fill-mask
                  :rules="[(value) => !!value || 'Esse campo é obrigatório']"
                  label="Valor monetário"
                />
              </div>
              <div class="row q-mt-md">
                <q-btn
                  label="Converter"
                  color="primary"
                  unelevated
                  class="full-width text-light"
                  size="lg"
                  no-caps
                  :loading="loadingBtn"
                  type="submit"
                />
              </div>
            </q-form>
            <div key="converted" v-else class="row items-center">
              <q-card flat bordered class="col-lg-5 col-md-5 col-xs-12">
                <q-card-section horizontal>
                  <q-card-section class="flex">
                    <q-img
                      class="rounded-borders"
                      src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/05/Flag_of_Brazil.svg/1280px-Flag_of_Brazil.svg.png"
                      width="100%"
                      height="auto"
                    />
                  </q-card-section>

                  <q-card-section class="q-ml-auto">
                    <div class="text-h6 q-mt-sm q-mb-xs">REAL</div>
                  </q-card-section>
                </q-card-section>

                <q-separator />

                <q-card-actions class="flex justify-center q-pa-none">
                  <h6 class="q-ma-sm">{{ valueCoin | currencyFilter }}</h6>
                </q-card-actions>
              </q-card>
              <q-icon name="trending_flat" size="48px" class="q-mx-md" />
              <q-card flat bordered class="col">
                <q-card-section horizontal>
                  <q-card-section class="col-xs-4 col-lg-5 col-md-5 flex">
                    <q-img
                      class="rounded-borders"
                      src="data:image/png;base64,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"
                      width="100%"
                      height="auto"
                    />
                  </q-card-section>

                  <q-card-section class="q-ml-auto">
                    <div class="text-h6 q-mt-sm q-mb-xs">
                      {{ newCoin.name }}
                    </div>
                  </q-card-section>
                </q-card-section>

                <q-separator />

                <q-card-actions class="flex justify-center q-pa-none">
                  <h6 class="q-ma-sm ellipsis">
                    {{
                      newCoin.bid
                        | currencyFilterNewCoin(valueCoin, newCoin.code)
                    }}
                  </h6>
                </q-card-actions>
              </q-card>
              <div class="col-12 q-mt-md">
                <q-btn
                  label="Nova conversão"
                  color="primary"
                  unelevated
                  class="full-width text-light"
                  size="lg"
                  no-caps
                  @click="pageAlternator = true"
                />
              </div>
            </div>
          </transition>
        </div>
      </div>
      <div class="col flex flex-center bg-grey-2 q-pa-xl">
        <q-img
          src="https://ouch-cdn.icons8.com/preview/224/c66cef49-1b7f-4290-9099-14fd0c0e8bd5.png"
          alt="Ilustração da home"
        />
      </div>
    </div>
  </q-page>
</template>

<script>
import api from 'src/services/api';

export default {
  name: 'PageIndex',
  data() {
    return {
      coinsList: [],
      baseCoin: '',
      newCoin: '',
      valueCoin: '',
      pageAlternator: true,
      loadingBtn: false,
      loadingSelect: true,
    };
  },
  filters: {
    currencyFilter: function (value) {
      if (value && !(value === 'Não definido')) {
        const newValue = value.replace(',', '.');
        return new Intl.NumberFormat('pt-BR', {
          style: 'currency',
          currency: 'BRL',
        }).format(newValue);
      }
      return 'Não definido';
    },
    currencyFilterNewCoin: function (value, valueCoin, codeCoin) {
      if (value && !(value === 'Não definido')) {
        const newValue = value.replace(',', '.');
        const baseValue = valueCoin.replace(',', '.');
        const valueConverted = baseValue * newValue;
        return new Intl.NumberFormat('pt-BR', {
          style: 'currency',
          currency: codeCoin,
        }).format(valueConverted);
      }
      return 'Não definido';
    },
  },
  methods: {
    async getCoins() {
      try {
        this.loadingSelect = true;
        const response = await api.get('/json/all');
        const arrayConverted = Object.values(response.data);
        this.coinsList = arrayConverted.map((item) => ({
          ...item,
          nameConverted: `${item.name} (${item.code})`,
        }));
      } catch (e) {
        this.$q.notify({
          message: 'Ocorreu um problema ao carregar as moedas',
          type: 'warning',
          timeout: 1500,
        });
      } finally {
        this.loadingSelect = false;
      }
    },
    convertCoins() {
      this.loadingBtn = true;
      setTimeout(() => {
        this.pageAlternator = false;
        this.loadingBtn = false;
      }, 1000);
    },
  },
  mounted() {
    this.getCoins();
  },
};
</script>

<style lang="scss">
.fade-enter-active,
.fade-leave-active {
  transition-duration: 0.3s;
  transition-property: opacity, transform;
  transition-timing-function: ease;
}

.fade-enter,
.fade-leave-active {
  transform: translateX(-2em);
  opacity: 0;
}

.cardDefault {
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.1);
}

.container {
  padding: 80px;
  background-color: #f2f2f2;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='100' height='18' viewBox='0 0 100 18'%3E%3Cpath fill='%23c9c9c9' fill-opacity='0.4' d='M61.82 18c3.47-1.45 6.86-3.78 11.3-7.34C78 6.76 80.34 5.1 83.87 3.42 88.56 1.16 93.75 0 100 0v6.16C98.76 6.05 97.43 6 96 6c-9.59 0-14.23 2.23-23.13 9.34-1.28 1.03-2.39 1.9-3.4 2.66h-7.65zm-23.64 0H22.52c-1-.76-2.1-1.63-3.4-2.66C11.57 9.3 7.08 6.78 0 6.16V0c6.25 0 11.44 1.16 16.14 3.42 3.53 1.7 5.87 3.35 10.73 7.24 4.45 3.56 7.84 5.9 11.31 7.34zM61.82 0h7.66a39.57 39.57 0 0 1-7.34 4.58C57.44 6.84 52.25 8 46 8S34.56 6.84 29.86 4.58A39.57 39.57 0 0 1 22.52 0h15.66C41.65 1.44 45.21 2 50 2c4.8 0 8.35-.56 11.82-2z'%3E%3C/path%3E%3C/svg%3E");
}
</style>
