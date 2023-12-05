<template>
  <div id="app" class="container mt-5">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <div class="card">
          <div class="card-body">
            <h1 class="mb-4">Verificación de Contribuyente y Licencia</h1>
            <form @submit.prevent="verificarCedula" class="mb-4">
              <div class="mb-3">
                <label for="cedula" class="form-label">Ingrese Cédula:</label>
                <div class="input-group">
                  <input type="text" v-model="cedula" class="form-control" required />
                  <button type="submit" class="btn btn-primary">Verificar</button>
                </div>
              </div>
            </form>
            <div v-if="resultadoSRI" class="alert alert-success">{{ resultadoSRI }}</div>
            <div v-if="resultadoLicencia" class="alert alert-success">{{ resultadoLicencia }}</div>
            <div v-if="errorSRI" class="alert alert-danger">{{ errorSRI }}</div>
            <div v-if="errorLicencia" class="alert alert-danger">{{ errorLicencia }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cedula: '',
      resultadoSRI: null,
      resultadoLicencia: null,
      errorSRI: null,
      errorLicencia: null,
    };
  },
  methods: {
    async verificarCedula() {
      try {
        this.resetResults();
        const responseSRI = await this.$axios.post('http://localhost:3001/verificarSRI', { numeroRuc: this.cedula });
        this.resultadoSRI = responseSRI.data;
        const responseLicencia = await this.$axios.get(`http://localhost:3002/verificarLicencia/${this.cedula}`);
        this.resultadoLicencia = responseLicencia.data;
      } catch (error) {
        console.error(error);
        if (error.response) {
          // Respuesta con estado diferente de 2xx
          this.errorSRI = `Error en la verificación del SRI: ${error.response.data.details}`;
        } else if (error.request) {
          // La solicitud se hizo pero no se recibió respuesta
          this.errorSRI = 'Error en la verificación del SRI: No se recibió respuesta del servidor';
        } else {
          // Otros errores
          this.errorSRI = 'Error en la verificación del SRI: Ocurrió un error inesperado';
        }
        this.errorLicencia = 'Error en la verificación de la Licencia';
      }
    },
    resetResults() {
      this.resultadoSRI = null;
      this.resultadoLicencia = null;
      this.errorSRI = null;
      this.errorLicencia = null;
    },
  },
};
</script>

<style>
#app {
  text-align: center;
}

.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.form-label {
  font-weight: bold;
}

.input-group {
  display: flex;
}

.btn-primary {
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
}

.alert {
  margin-top: 20px;
}
</style>
