<template>
    <BasicLayout>
        <div class="login">
            <h2>Iniciar sesión</h2>
            <form class="ui form" @submit.prevent="login">
                <div class="field">
                    <input type="text" placeholder="Nombre de usuario" v-model="formData.identifier" :class="{error: formError.identifier}">
                </div>
                <div class="field">
                    <input type="password" placeholder="Contraseña" v-model="formData.password" :class="{error: formError.password}">
                </div>

                <button type="submit" class="ui button fluid primary" :class="{loading}">Entrar</button>
            </form>
            <router-link to="/register">Crear cuenta</router-link>
            <p v-if="messageError">{{ messageError }}</p>
        </div>
    </BasicLayout>    
</template>

<script>
import BasicLayout from '../layouts/BasicLayout.vue';
import {ref, onMounted} from 'vue';
import {loginApi} from '../api/user';
import {useRouter} from 'vue-router';
import * as Yup from 'yup';
import {setTokenApi, getTokenApi} from "../api/token";

export default {
    name: "Login",

    components: {
        BasicLayout,
    }, 

    setup() {
        //variables
        let formData = ref({});
        let formError = ref({});
        let loading = ref(false);
        let messageError = ref('');
        const router = useRouter();
        const token = getTokenApi();

        //functions

        onMounted(() => {
            if(token) router.push('/');
        });

        const schemaForm = Yup.object().shape({
            identifier: Yup.string().required(true),
            password: Yup.string().required(true),
        });

        const login = async () => {
            formError.value = {};
            loading.value = true;
            try {
                await schemaForm.validate(formData.value, {abortEarly: false});
                
                try {
                    const response = await loginApi(formData.value);
                    if(!response?.jwt)throw "El usuario o contraseña no son válidos";
                    console.log(response);
                    setTokenApi(response.jwt);
                    router.push('/');
                } catch (error) {
                    console.log(error);
                   error.value = messageError.value;
                }

            } catch (error) {
                error.inner.forEach((err) => {
                    formError.value[err.path] = err.message;
                });
            }
            loading.value = false;
        };


        return{
            //variables
            formData,
            formError,
            loading,
            messageError,

            //functions
            login,

        }
    },

    
}
</script>

<style lang="scss" scoped>
.login{
    text-align: center;

    > h2{
        margin: 50px 0 30px 0;
    }

    .ui.form{
        max-width: 300px !important;
        margin: 0 auto;
        margin-bottom: 10px;

        input.error {
            border-color: #faa;
            background-color: #ffeded;
        }
    }

}
</style>