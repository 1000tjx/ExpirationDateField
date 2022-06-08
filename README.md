# Usage

```vue
<ExpirationDateField
  v-model="expiryDate"
  @extractData="({month, year}) => extractMonthAndYear(month, year)"
  :minYear='22'
  />

```
