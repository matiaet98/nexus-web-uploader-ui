<template>
    <div>
        <b-form>
            <b-form-group id="archivo" label="Archivo:" label-for="archivoInput">
                <b-form-file id="archivoInput" v-model="archivo" :state="stateArchivo" placeholder="seleccionar el entregable..."></b-form-file>
            </b-form-group>
            <b-form-group id="usuario" label="Usuario:" label-for="usuarioInput">
                <b-form-input id="usuarioInput" type="text" @change="validateUsuarioSUA()" :state="stateUsuarioSUA" v-model="usuarioSUA" placeholder="Nombre de usuario en SUA. ej: u44009"></b-form-input>
                <div class="invalid-feedback">El nombre de usuario es invalido</div>
            </b-form-group>
            <b-form-group id="password" label="Password:" label-for="passwordInput">
                <b-form-input id="passwordInput" type="password" @change="validatePasswordSUA()" :state="statePasswordSUA" v-model="passwordSUA"  placeholder="password SUA"></b-form-input>
                <div class="invalid-feedback">Password invalida</div>
            </b-form-group>
            <b-form-group id="repositorio" label="Repositorio:" label-for="repositorioInput">
                <b-form-select id="repositorioInput" v-model="repositorio" :options="repositorios" class="mb-3" /></b-form-group>
            <b-form-group id="carpetas" label="Carpetas:" label-for="carpetasInput">
                <b-form-input id="carpetasInput" type="text" v-model="carpetas" placeholder="Carpetas contenedoras dentro del repositorio, ej: /efisco/backend/1.5.9/"></b-form-input>
            </b-form-group>
            <b-form-group id="uploadSection">
                <b-button variant="outline-secondary" @click="artifactUpload()">Subir</b-button>
            </b-form-group>
        </b-form>
        <b-modal ref="uploadModal" id="uploadModal" title="Subiendo archivo">
            <p class="my-4">{{modalMessage}}</p>
        </b-modal>
    </div>
</template>

<style lang="postcss">
    #uploadSection {
        text-align: center
    }
</style>

<script lang="ts">
import {
    Component,
    Vue,
} from 'vue-property-decorator';
import {
    FormFile,
} from 'bootstrap-vue';
import axios from 'axios';

@Component
export default class Formulario extends Vue {
    public usuarioSUA: string = '';
    public passwordSUA: string = '';
    public archivo: any = '';
    public repositorios: string[] = [];
    public url: string = (process.env.VUE_APP_API_URL || '') + (process.env.VUE_APP_API_PREFIX);
    public repositorio: string = '';
    public carpetas: string = '';
    public stateUsuarioSUA: any = null;
    public statePasswordSUA: any = null;
    public stateArchivo: any = null;
    public modalMessage: string = '';

    public async beforeMount() {
        await this.fetchRepositorios();
        this.repositorio = this.repositorios[0];
    }

    public async fetchRepositorios() {
        const response = await axios.get(this.url + 'repositorios');
        if (response.status === 200) {
            this.repositorios = response.data;
        }
    }
    public async validateUsuarioSUA() {
        if (!this.usuarioSUA) {
            this.stateUsuarioSUA = false;
            return false;
        } else {
            this.stateUsuarioSUA = null;
            return true;
        }
    }
    public async validatePasswordSUA() {
        if (!this.passwordSUA) {
            this.statePasswordSUA = false;
            return false;
        } else {
            this.statePasswordSUA = null;
            return true;
        }
    }
    public async validateArchivo() {
        if (!this.archivo) {
            this.stateArchivo = false;
            return false;
        } else {
            this.stateArchivo = null;
            return true;
        }
    }

    public async artifactUpload() {
        if (! await this.validateUsuarioSUA()) { return; }
        if (! await this.validatePasswordSUA()) { return; }
        if (! await this.validateArchivo()) { return; }
        const formData = new FormData();
        formData.append('archivo', this.archivo);
        formData.append('nombreArchivo', Buffer.from(this.archivo.name).toString('base64'));
        formData.append('usuarioSUA', Buffer.from(this.usuarioSUA).toString('base64'));
        formData.append('passwordSUA', Buffer.from(this.passwordSUA).toString('base64'));
        formData.append('repositorio', Buffer.from(this.repositorio).toString('base64'));
        formData.append('carpetas', Buffer.from(this.carpetas).toString('base64'));
        axios.post( this.url + 'artefactos',
            formData,
            {
            headers: {
                'Content-Type': 'multipart/form-data',
            },
          },
        ).then((res) => {
            this.modalMessage = res.data.message;
            this.$refs.uploadModal.show();
        })
        .catch((err) => {
            this.modalMessage = 'Error: No se puede subir';
            this.$refs.uploadModal.show();
        });
    }
}
</script>