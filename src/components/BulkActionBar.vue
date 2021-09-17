<template>
  <div>
    <input
      type="checkbox"
      :checked="allEmailsSelected"
      :class="[someEmailsSelected ? 'partial-check' : '']"
      @click="bulkSelect"
    />
  </div>
</template>

<script>
import { toRefs, computed } from 'vue'
import useEmailSelection from '@/composables/use-email-selection.js'
export default {
  setup(props) {
    const emailSelection = useEmailSelection()
    const numberSelected = computed(() => emailSelection.emails.size)
    const { emails } = toRefs(props)
    const numberEmails = emails.value.length
    const allEmailsSelected = computed(
      () => numberSelected.value === numberEmails
    )
    const someEmailsSelected = computed(
      () => numberSelected.value > 0 && numberSelected.value < numberEmails
    )
    const bulkSelect = () => {
      if (allEmailsSelected.value) {
        emailSelection.clear()
      } else {
        emailSelection.addMultiple(emails.value)
      }
    }

    return {
      emailSelection,
      bulkSelect,
      allEmailsSelected,
      someEmailsSelected,
    }
  },
  props: {
    emails: {
      type: Array,
      required: true,
    },
  },
}
</script>

<style lang="scss" scoped></style>
