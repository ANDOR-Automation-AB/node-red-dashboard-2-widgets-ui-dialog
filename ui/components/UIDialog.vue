<template>
  <div class="text-center pa-4">
    <v-btn @click="dialog = true">
      Open Dialog
    </v-btn>

    <v-dialog v-model="dialog" width="auto">
      <v-card
        max-width="400"
        prepend-icon="mdi-update"
        text="Your application will relaunch automatically after the update is complete."
        title="Update in progress">
        <template v-slot:actions>
          <v-btn class="ms-auto" text="Ok" @click="dialog = false"></v-btn>
        </template>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
  export default {
    name: "UIDialog",
    inject: ['$dataTracker', '$socket'],
    props: {
      id:    { type: String, required: true },
      props: { type: Object, default: () => ({}) },
      state: { type: Object, default: () => ({ enabled: false, visible: false }) }
    },
    data() {
      return {
        base: null,
        msg: null,
        dialog: false
      }
    },
    created() {
      this.$dataTracker(this.id, this.onInput, this.onLoad)
    },
    methods: {
      onInput(msg) {
        this.msg = msg
      },
      onLoad(msg, base) {
        this.msg = msg
        this.base = base
      }
    }
  }
</script>