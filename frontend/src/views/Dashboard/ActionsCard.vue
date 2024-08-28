<script setup lang="ts">
import { api } from '@/api';
import { useModal } from '@/composables/useModal';
import { useToast } from '@/composables/useToast';
import { ref } from 'vue';
import useVuelidate from '@vuelidate/core';
import { required, email, minValue } from '@vuelidate/validators';

const modal = useModal<boolean>();
const toast = useToast();

const rules = {
  applicantName: { required },
  applicantEmail: { required, email },
  applicantMobilePhoneNumber: { required },
  applicantAddress: { required },
  annualIncomeBeforeTax: { required, minValue: minValue(1) },
  incomingAddress: { required },
  incomingDeposit: { required, minValue: minValue(1) },
  incomingPrice: { required, minValue: minValue(1) },
  incomingStampDuty: { required, minValue: minValue(1) },
  loanAmount: { required, minValue: minValue(1) },
  loanDuration: { required, minValue: minValue(1) },
  monthlyExpenses: { required, minValue: minValue(0) },
  outgoingAddress: { required },
  outgoingMortgage: { required, minValue: minValue(0) },
  outgoingValuation: { required, minValue: minValue(0) },
  savingsContribution: { required, minValue: minValue(0) },
};

const formData = ref({
  applicantName: '',
  applicantEmail: '',
  applicantMobilePhoneNumber: '',
  applicantAddress: '',
  annualIncomeBeforeTax: 0,
  incomingAddress: '',
  incomingDeposit: 0,
  incomingPrice: 0,
  incomingStampDuty: 0,
  loanAmount: 0,
  loanDuration: 0,
  monthlyExpenses: 0,
  outgoingAddress: '',
  outgoingMortgage: 0,
  outgoingValuation: 0,
  savingsContribution: 0,
});

const v$ = useVuelidate(rules, formData);

const resetFormData = () => {
  formData.value = {
    applicantName: '',
    applicantEmail: '',
    applicantMobilePhoneNumber: '',
    applicantAddress: '',
    annualIncomeBeforeTax: 0,
    incomingAddress: '',
    incomingDeposit: 0,
    incomingPrice: 0,
    incomingStampDuty: 0,
    loanAmount: 0,
    loanDuration: 0,
    monthlyExpenses: 0,
    outgoingAddress: '',
    outgoingMortgage: 0,
    outgoingValuation: 0,
    savingsContribution: 0,
  };
};

// const submitApplication = async () => {
//   @submit.prevent="submitApplication"
//   const response = await api.applications.post(formData.value)
//   if (response.success) toast.success('Application Saved Successfully.')
//   else {
//     toast.error('Error occurred while saving application')
//     formData.value.applicantName = '';
//     formData.value.applicantEmail = '';
//     formData.value.applicantMobilePhoneNumber = '';
//     formData.value.applicantAddress = '';
//     formData.value.annualIncomeBeforeTax = 0;
//     formData.value.incomingAddress = '';
//     formData.value.incomingDeposit = 0;
//     formData.value.incomingPrice = 0;
//     formData.value.incomingStampDuty = 0;
//     formData.value.loanAmount = 0;
//     formData.value.loanDuration = 0;
//     formData.value.monthlyExpenses = 0;
//     formData.value.outgoingAddress = '';
//     formData.value.outgoingMortgage = 0;
//     formData.value.outgoingValuation = 0;
//     formData.value.savingsContribution = 0;
//   }
//   modal.confirm(false)
// }

const submitApplication = async () => {
  v$.value.$touch();
  if (v$.value.$invalid) {
    toast.error('Please correct the validation errors');
    return;
  }
  try {
    const response = await api.applications.post(formData.value);
    if (response.success) {
      toast.success(response?.message);
      modal.confirm(false);
    } else {
      toast.error('Error occurred while saving application');
      resetFormData();
    }
  } catch (error) {
    console.error('Error submitting application:', error);
    toast.error('An unexpected error occurred.');
  }
};
</script>

<template>
  <div class="action-section">
    <BCard align-title="center" align-footer="center" align-content="center">
      <template #title>Submit loan application</template>
      <BSvgIcon name="dashboard-loan" />
      <template #footer>
        <BButton
          variant="primary"
          label="Submit application"
          icon-pos="right"
          icon="pi pi-chevron-right"
          @click="modal.showModal()"
        />
      </template>
    </BCard>

    <BModal :visible="modal.isVisible.value" :confirm="modal.confirm">
      <template #header>Submit loan application</template>

      <form @submit.prevent="submitApplication()" class="p-4">
        <!-- Need to change with v-for after change state with object -->

        <label for="applicant_name">Name</label>
        <BTextInput v-model="formData.applicantName" id="applicant_name" type="text" required />
        <label v-if="v$.applicantName.$error" class="error-message">Name is required</label>

        <label for="applicant_email">Email</label>
        <BTextInput v-model="formData.applicantEmail" id="applicant_email" type="email" required />
        <label v-if="v$.applicantEmail.$error" class="error-message">A valid email is required</label>

        <div for="applicant_mobile_phone_number">Mobile Phone Number</div>
        <BTextInput
          v-model="formData.applicantMobilePhoneNumber"
          id="applicant_mobile_phone_number"
          type="tel"
          required
        />
        <label v-if="v$.applicantMobilePhoneNumber.$error" class="error-message">Applicant mobile number is required</label>

        <label for="applicant_address">Applicant Address</label>
        <BTextInput v-model="formData.applicantAddress" id="applicant_address" required />
        <label v-if="v$.applicantAddress.$error" class="error-message">Applicant address is required</label>

        <label for="annual_income_before_tax">Annual Income Before Tax</label>
        <BNumberInput v-model="formData.annualIncomeBeforeTax" id="annual_income_before_tax" required />
        <label v-if="v$.annualIncomeBeforeTax.$error" class="error-message">Annual income before tax is required</label>

        <label for="incoming_address">Incoming Address</label>
        <BTextInput v-model="formData.incomingAddress" id="incoming_address" required />
        <label v-if="v$.incomingAddress.$error" class="error-message">Incoming address is required</label>

        <label for="incoming_deposit">Incoming deposit</label>
        <BNumberInput v-model="formData.incomingDeposit" id="incoming_deposit" required />
        <label v-if="v$.incomingDeposit.$error" class="error-message">Incoming deposit is required</label>

        <label for="incoming_price">Incoming Price</label>
        <BNumberInput v-model="formData.incomingPrice" id="incoming_price" required />
        <label v-if="v$.incomingPrice.$error" class="error-message">Incoming price is required</label>

        <label for="incoming_stamp_duty">Incoming Stamp Duty</label>
        <BNumberInput v-model="formData.incomingStampDuty" id="incoming_stamp_duty" required />
        <label v-if="v$.incomingStampDuty.$error" class="error-message">Incoming stamp duty is required</label>

        <label for="loan_amount">Loan Amount</label>
        <BNumberInput v-model="formData.loanAmount" id="loan_amount" required />
        <label v-if="v$.loanAmount.$error" class="error-message">Loan amount is required</label>

        <label for="loan_duration">Loan Duration</label>
        <BNumberInput v-model="formData.loanDuration" id="loan_duration" required />
        <label v-if="v$.loanDuration.$error" class="error-message">Loan duration is required</label>

        <label for="monthly_expenses">Monthly Expenses</label>
        <BNumberInput v-model="formData.monthlyExpenses" id="monthly_expenses" required />
        <label v-if="v$.monthlyExpenses.$error" class="error-message">Monthly expenses is required</label>

        <label for="outgoing_address">Outgoing Address</label>
        <BTextInput v-model="formData.outgoingAddress" id="outgoing_address" required />
        <label v-if="v$.outgoingAddress.$error" class="error-message">Outgoing address is required</label>

        <label for="outgoing_mortgage">Outgoing Mortgage</label>
        <BNumberInput v-model="formData.outgoingMortgage" id="outgoing_mortgage" required />
        <label v-if="v$.outgoingMortgage.$error" class="error-message">Outgoing mortgage is required</label>

        <label for="outgoing_valuation">Outgoing Valuation</label>
        <BNumberInput v-model="formData.outgoingValuation" id="outgoing_valuation" required />
        <label v-if="v$.outgoingValuation.$error" class="error-message">Outgoing valuation is required</label>

        <label for="savings_contribution">Savings Contribution</label>
        <BNumberInput v-model="formData.savingsContribution" id="savings_contribution" required />
        <label v-if="v$.savingsContribution.$error" class="error-message">Savings contribution is required</label>
      </form>

      <template #footer>
        <BButton type="submit" variant="primary" label="Submit" @click="submitApplication"></BButton>
        <BButton label="Cancel" @click="modal.confirm(false)"></BButton>
      </template>
    </BModal>
  </div>
</template>

<style lang="scss" scoped>
.action-section {
  display: flex;
  flex-flow: row wrap;
  gap: 1rem;
  align-items: stretch;
  container-type: inline-size;

  > * {
    flex: 1 1 100%;
  }

  @container (min-width: 900px) {
    > * {
      flex: 1 1 calc((100% - 2rem) / 3);
    }
  }
}

.b-card {
  height: 100%;
}

.b-icon {
  width: 5rem;
  height: 5rem;
}

.error-message {
  color: #ee4b2b;
  font-size: 11px;
  margin-bottom: 5px;
  font-weight: 500;
  float: right;
}
</style>
