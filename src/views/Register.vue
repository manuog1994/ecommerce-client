<template>
    <BasicLayout>
        <div class="register">
            <h2>Registro de usuario</h2>

            <form class="ui form" @submit.prevent="register">
                <div class="field">
                    <input type="text" placeholder="Nombre de usuario" v-model="formData.username" :class="{ error: formError.username }">
                </div>
                <div class="field">
                    <input type="email" placeholder="Correo electrónico" v-model="formData.email" :class="{ error: formError.email }"> 
                </div>
                <div class="field">
                    <input type="password" placeholder="Contraseña" v-model="formData.password" :class="{ error: formError.password }">
                </div>

                <button type="submit" class="ui button fluid primary" :class="{loading}">Crear usuario</button>
            </form>

            <router-link to="/login">Iniciar sesión</router-link>
        </div>
    </BasicLayout>    
</template>

<script>
//packages
import {ref, onMounted} from 'vue';
import {useRouter} from "vue-router";
import * as Yup from 'yup';
import {registerApi} from '../api/user';
import {getTokenApi} from '../api/token';

//components
import BasicLayout from '../layouts/BasicLayout.vue';

export default {
    name: "Register",

    components: {
        BasicLayout,
    },

    setup() {
        //variables
        let formData = ref({});
        let formError = ref({});
        let loading = ref(false);
        const router = useRouter();
        const token = getTokenApi();

        onMounted(() => {
            if(token) router.push('/');
        });
        
        //functions
        const register = async () => {
            formError.value = {};
            loading.value = true;

            try {
                await schemaForm.validate(formData.value, {abortEarly: false});

                try {
                    const response = await registerApi(formData.value);
                    router.push("/login");
                } catch (error) {
                   console.log(error); 
                }
            } catch (err) {
                err.inner.forEach((error) => {
                    formError.value[error.path] = error.message;
                });
            }
            loading.value = false;
        };

        const schemaForm = Yup.object().shape({
            username: Yup.string().required(true),
            email: Yup.string().email(true).required(true),
            password: Yup.string().required(true),
        })

        return{
            //variables
            formData,
            formError,
            loading,

            //functions
            register,
        }
    }
}
</script>

<style lang="scss" scoped>
.register{
    text-align: center;
    > h2{
        margin: 50px 0 30px 0;
    }

    .ui.form{
        max-width: 300px !important;
        margin: 0 auto;
        margin-bottom: 10px;

        input.error{
            border-color: #faa;
            background-color: #ffeded;
        }
    }
}
</style>