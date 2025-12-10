<template>
  <div class="text-center pa-4">
    <v-btn v-if="!props.inputDialog" @click="dialog = true">
      {{ props.label }}
    </v-btn>

    <v-dialog v-model="dialog" width="auto" v-bind="props.persistent ? { persistent: '' } : {}">
      <v-card
        :max-width="props.maxWidth"
        :prepend-icon="props.icon"
        :text="props.message"
        :title="props.title">
        <template v-if="props.dialogType === 'dismiss-dialog'" v-slot:actions>
          <v-btn class="ms-auto" @click="dialog = false">
            {{ props.dismissText }}
          </v-btn>
        </template>
        <template v-if="props.dialogType === 'yesno-dialog'" v-slot:actions>
          <v-btn color="primary" @click="onConfirm()">
            {{ props.confirmText }}
          </v-btn>
          <v-btn class="ms-auto" @click="dialog = false">
            {{ props.declineText }}
          </v-btn>
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
        this.msg = {
          topic:  msg.topic    || this.props.topic,
          payload: msg.payload || this.props.payload
        }
        if (this.props.inputDialog) {
          this.dialog = true
        }
      },
      onLoad(msg, base) {
        this.base = base
        this.msg = msg || {}
        this.msg.topic =   msg?.topic   || this.props.topic
        this.msg.payload = msg?.payload || this.props.payload
      },
      onConfirm() {
        this.dialog = false
        this.$socket.emit('widget-send', this.id, {
          topic:   this.msg.topic,
          payload: this.msg.payload
        })
      }
    }
  }
</script>