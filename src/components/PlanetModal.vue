<template>
  <div class="modal-overlay" @click="closeModal">
    <div class="modal-content" @click.stop>
      <div class="modal-header">
        <div class="modal-icon">🔒</div>
      </div>
      <div class="modal-body">
        <p>{{ message }}</p>
      </div>
      <div class="modal-footer">
        <button class="close-button" @click="closeModal">确定</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PlanetModal',
  props: {
    message: {
      type: String,
      required: true
    }
  },
  mounted() {
    document.addEventListener('keydown', this.handleEscape)
  },
  beforeUnmount() {
    document.removeEventListener('keydown', this.handleEscape)
  },
  methods: {
    closeModal() {
      this.$emit('close')
    },
    handleEscape(event) {
      if (event.key === 'Escape') {
        this.closeModal()
      }
    }
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  animation: fadeIn 0.3s ease-out;
}

.modal-content {
  background: linear-gradient(145deg, #1e3a5f, #2c5aa0);
  border-radius: 20px;
  padding: 30px;
  max-width: 400px;
  width: 90%;
  text-align: center;
  box-shadow: 
    0 20px 40px rgba(0, 0, 0, 0.3),
    0 0 30px rgba(32, 178, 170, 0.2);
  border: 1px solid rgba(32, 178, 170, 0.3);
  animation: modalSlideIn 0.3s ease-out;
}

.modal-header {
  margin-bottom: 20px;
}

.modal-icon {
  font-size: 48px;
  margin-bottom: 10px;
  filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.3));
}

.modal-body {
  margin-bottom: 30px;
}

.modal-body p {
  color: #ffffff;
  font-size: 16px;
  line-height: 1.6;
  margin: 0;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

.modal-footer {
  display: flex;
  justify-content: center;
}

.close-button {
  background: linear-gradient(145deg, #20b2aa, #008b8b);
  color: white;
  border: none;
  padding: 12px 30px;
  border-radius: 25px;
  font-size: 14px;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 
    0 4px 15px rgba(32, 178, 170, 0.3),
    inset 0 1px 0 rgba(255, 255, 255, 0.2);
}

.close-button:hover {
  transform: translateY(-2px);
  box-shadow: 
    0 6px 20px rgba(32, 178, 170, 0.4),
    inset 0 1px 0 rgba(255, 255, 255, 0.3);
}

.close-button:active {
  transform: translateY(0);
  box-shadow: 
    0 2px 10px rgba(32, 178, 170, 0.3),
    inset 0 1px 0 rgba(255, 255, 255, 0.2);
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes modalSlideIn {
  from {
    opacity: 0;
    transform: translateY(-30px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}
</style>