<template>
<!-- Resumen 2-->
  <div>
    <button
      class="btn btn-link mb-3 pl-0"
      @click="showSimulationDetails = false">
      <font-awesome-icon
        icon="chevron-left"
        class="mr-2" /> {{ $t('main.summary.view-summary-btn') }}
    </button>

    <div class="row">
     <div class="col-12 text-center text-lg-left">
        <p class="consumer-loan__simulator-details-total-credit-cost h5 text-primary">
          {{ $t('main.summary.credit-data') }}
        </p>
      </div>
      <div class="col-4">
        <div class="form-group">
          <label for="">Nombres</label>
          <input v-model="message" class="d-block form-control" placeholder="Ej.: Carlos Andres"/>
        </div>
      </div>
       <div class="col-4">
        <div class="form-group">
          <label for="">Apellido paterno</label>
          <input v-model="message" class="d-block form-control" placeholder="Ej.: Olea"/>
        </div>
      </div>
       <div class="col-4">
        <div class="form-group">
          <label for="">Apellido Materno</label>
          <input v-model="message" class="d-block form-control" placeholder="Ej.: Vera"/>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-4">
        <div class="form-group">
          <label for="">RUN</label>
          <input
            v-model="rut"
            v-format-rut
            type="text"
            placeholder="Ej.: 11.111.111-1"
            class="d-block form-control">
        </div>
      </div>
       <div class="col-4">
        <div class="form-group">
          <label for="">Ingreso Mensual</label>
          <money
            id="amount"
            v-model="money"
            v-bind="moneyOptions"
            class="d-block form-control" />
        </div>
      </div>
    </div>

    <div class="mb-5 mt-lg-0 mb-lg-4">
      <div class="col-12 text-center text-lg-left">
        <p class="consumer-loan__simulator-details-total-credit-cost h5 text-primary">
          {{ $t('main.summary.data-credit') }}
        </p>
      </div>
      <div class="bg-white px-3 pt-3 pb-2 rounded">
        <div class="d-flex justify-content-between mb-2">
          <strong>{{ $t('main.summary.credit-total-amount') }}</strong>
          <strong>{{ amount | currency($t('currency.format')) }}</strong>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <strong>{{ $t('main.simulation-summary.fees') }}</strong>
          <span>{{ payments }}</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <strong>{{ $t('main.simulation-summary.fee-value') }}</strong>
          <span>{{ monthlyAmount | currency($t('currency.format')) }}</span>
        </div>
      </div>
      <div class="bg-white px-3 pt-3 pb-2 rounded mt-3">
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.months-of-grace') }}</span>
          <span>{{ monthsGrace }}</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.interest-rate') }}</span>
          <span>{{ interestRate }}% {{ $t('main.summary.monthly') }}</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.annual-interest') }}</span>
          <span>{{ annualRate }}% {{ $t('main.summary.annual') }}</span>
        </div>
        <div
          v-if="!isLangEn"
          class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.equivalent-anual-load-abbr') }}</span>
          <span>{{ cae }}%</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.simulation-summary.to-account') }}</span>
          <span class="text-capitalize text-right ">{{ account.accountType }}<br>
            <strong>{{ account.accountNumber }}</strong>
          </span>
        </div>
      </div>
      <div class="bg-white px-3 pt-3 pb-2 rounded mt-3">
        <div
          v-if="disgrace"
          class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.disgrace-insurance') }}</span>
          <span>{{ disgraceInsurance | currency($t('currency.format')) }}</span>
        </div>
        <div
          v-if="unemployment"
          class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.unemployment-insurance') }}</span>
          <span>{{ unemploymentInsurance | currency($t('currency.format')) }}</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.stamps-tax') }}</span>
          <span>{{ stampTax | currency($t('currency.format')) }}</span>
        </div>
        <div class="d-flex justify-content-between mb-2">
          <span>{{ $t('main.summary.notarial-fees') }}</span>
          <span>{{ notaryFee | currency($t('currency.format')) }}</span>
        </div>
      </div>
      <div class="row mt-4">
        <div class="col-12 text-center text-lg-left">
          <p class="consumer-loan__simulator-details-total-credit-cost h5 text-primary">
            {{ $t('main.summary.credit-total-cost') }}
          </p>
        </div>
        <div class="col-12 text-center text-lg-right font-weight-normal">
          <p class="h5 text-primary">
            {{ totalLoanAmount | currency($t('currency.format')) }}
          </p>
        </div>
      </div>
    </div>
    <a href="https://azurian.modyo.cloud/hackaton/resultados"
      class="btn btn-primary btn-lg btn-block mt-5">
      {{ $t('main.summary.request-advance-btn') }}
    </a>
  </div>
</template>
<script>
import { currency } from '@modyo/financial-commons';
import { mapState, mapGetters } from 'vuex';
import { formatRut } from '@modyo/financial-commons';
import { Money } from 'v-money-no-negative';

export default {
  name: 'SimulationDetails',
  filters: {
    currency,
  },
  components: {
    Money,
  },
  directives: {
    formatRut
  },
  computed: {
    ...mapState([
      'monthsGrace',
      'amount',
      'interestRate',
      'annualRate',
      'monthlyAmount',
      'totalLoanAmount',
      'cae',
      'stampTax',
      'notaryFee',
      'disgraceInsurance',
      'unemploymentInsurance',
      'account',
      'payments',
      'disgrace',
      'unemployment',
    ]),
    ...mapGetters(['isLangEn']),
    activeDetailInfo: {
      get() {
        return this.$store.state.activeDetailInfo;
      },
      set(value) {
        this.$store.commit('SET_ACTIVE_DETAIL_INFO', value);
      },
    },
    showSettingsView: {
      get() {
        return this.$store.state.showSettingsView;
      },
      set(value) {
        this.$store.commit('SET_SHOW_SETTINGS_VIEW', value);
      },
    },
    showSimulationDetails: {
      get() {
        return this.$store.state.showSimulationDetails;
      },
      set(value) {
        this.$store.commit('SET_SHOW_SIMULATION_DETAILS', value);
      },
    },
    moneyOptions() {
      if (this.isLangEn) {
        return {
          prefix: '$',
          decimal: '.',
          thousands: ',',
          precision: 2,
          maxlength: this.amountMaxLength + 3,
        };
      }
      return {
        prefix: '$',
        decimal: ',',
        thousands: '.',
        precision: 0,
        maxlength: this.amountMaxLength,
      };
    },
  },
};
</script>
<style lang="scss" scoped>
@import "../../scss/variables.scss";

.consumer-loan__simulator-details-total-credit-cost {
  font-weight: bolder;
}

@media (min-width: 992px) {
  .consumer-loan__simulator-details-total-credit-cost {
    font-weight: 500;
  }
}
</style>
