<template>
   <div class="card column is-one-third-tablet mx-auto">
      <div class="card-content">
         <div class="content">
            <h3 class="title is-5 is-3-desktop">Hidden Message</h3>
            <p class="subtitle is-6 is-5-desktop">
               {{ decodeHiddenMessage() }}
            </p>
         </div>
      </div>
   </div>
</template>

<script>
import dummy from '@/assets/dummy.json'

export default {
   setup() {
      const items = dummy

      const decodeHiddenMessage = () => {
         return items
            .sort((a, b) => a.fib - b.fib)
            .map(item =>
               item.egassem
                  .split('')
                  .reverse()
                  .join('')
            )
            .join(' ')
      }

      return { decodeHiddenMessage }
   }
}
</script>
