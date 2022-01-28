<template>
   <teleport to="body">
      <div class="modal" :class="{ 'is-active': show }">
         <div class="modal-background"></div>
         <div class="modal-card">
            <header class="modal-card-head">
               <p class="modal-card-title">{{ title }}</p>
               <button
                  class="delete"
                  aria-label="close"
                  @click="cancel"
               ></button>
            </header>
            <section class="modal-card-body">
               <slot></slot>
            </section>
            <footer class="modal-card-foot">
               <button class="button is-success" @click="save">
                  Save changes
               </button>
               <button class="button" @click="cancel">Cancel</button>
            </footer>
         </div>
      </div>
   </teleport>
</template>

<script>
export default {
   props: {
      title: {
         type: String,
         required: true
      },
      show: {
         type: Boolean,
         default: false
      }
   },
   emits: ['cancel', 'save'],
   setup(_, { emit }) {
      const cancel = () => emit('cancel')
      const save = () => emit('save')

      return { cancel, save }
   }
}
</script>
