<template>
  <table>
    <thead>
      <tr>
        <th>level</th>
        <th>Amount</th>
        <th>Unit Price</th>
        <th>Discount</th>
        <th>Price</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="step in shownSteps" :key="step.level">
        <td>{{ step.level }}</td>
        <td>{{ format(step.amount) }}</td>
        <td>${{ format(step.price) }}</td>
        <td>${{ format(step.discount) }}</td>
        <td>${{ format(step.subTotal) }}</td>
      </tr>
      <tr v-if="shownSteps.length < steps.length">
        <td colspan="5">
          <a href="" @click.prevent="showAllSteps = !showAllSteps">View Rest</a>
        </td>
      </tr>

      <tr v-if="showAllSteps">
        <td colspan="5">
          <a href="" @click.prevent="showAllSteps = !showAllSteps">Hide</a>
        </td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <th colspan="2"></th>
        <th>Avg.</th>
        <th>Vol. Discounts</th>
        <th>Total</th>
      </tr>
      <tr>
        <th colspan="2"></th>
        <th>${{ format(totals.avgUnitPrice) }}</th>
        <th>${{ format(totals.totalDiscount) }}</th>
        <th>${{ format(totals.total) }}</th>
      </tr>
    </tfoot>
  </table>
</template>
<script>
import { ref, computed, watch } from 'vue';

export default {
  name: 'VolumePricingTable',
  props: {
    unitCount: {
      type: Number,
      default: 0,
    },
    startingPrice: {
      type: Number,
      default: 0.0,
    },
    stepUnits: {
      type: Number,
      default: 1000,
    },
    stepDiscount: {
      type: Number,
      default: 0.0,
    },
    updateTotal: {
      type: Function,
    },
  },
  setup(props, { emit }) {
    const showAllSteps = ref(false);

    const stepCount = computed(() => {
      return Math.ceil(props.unitCount / props.stepUnits);
    });

    const steps = computed(() => {
      let price = props.startingPrice;
      let units = props.unitCount;

      const emptySteps = Array.apply(null, Array(stepCount.value));
      const _steps = [];

      emptySteps.forEach((step, index) => {
        const _price = Math.round(price * 100) / 100;
        const amount = units > props.stepUnits ? props.stepUnits : units;
        const subTotal = Math.round(_price * amount * 100) / 100;
        const retail = amount * props.startingPrice;
        const discount = retail - subTotal;

        _steps.push({
          level: index + 1,
          price: _price,
          amount,
          discount,
          subTotal,
        });

        price -= price * props.stepDiscount;
        units -= props.stepUnits;
      });

      return _steps;
    });

    const shownSteps = computed(() => {
      if (showAllSteps.value) {
        return steps.value;
      }
      return steps.value.slice(0, 10);
    });

    const totals = computed(() => {
      let unitPrices = 0;
      let totalDiscount = 0.0;
      let total = 0.0;

      steps.value.forEach((step) => {
        unitPrices += step.price;
        totalDiscount += step.discount;
        total += step.subTotal;
      });

      return {
        avgUnitPrice: Math.round((unitPrices / steps.value.length) * 100) / 100,
        totalDiscount,
        total,
      };
    });

    const format = (number) => {
      return number.toLocaleString('en-US');
    };

    watch(
      () => totals.value.total,
      (value) => {
        emit('update:modelValue', value);
      }
    );

    return {
      showAllSteps,
      stepCount,
      steps,
      shownSteps,
      totals,
      format,
    };
  },
};
</script>
