<template>
    <div>
        <form @submit.prevent="addColaborador">
            <input v-model="nuevoColaborador.nombre" placeholder="Nombre" required />
            <input v-model="nuevoColaborador.email" placeholder="Correo electrónico" type="email" required />
            <button type="submit">Agregar</button>
        </form>
        <ul>
            <li v-for="colaborador in colaboradores" :key="colaborador.id">
                {{ colaborador.id }} - {{ colaborador.nombre }} - {{ colaborador.email }}
                <button @click="deleteColaborador(colaborador.id)">Eliminar</button>
            </li>
        </ul>
    </div>
</template>

<script>
import { getFirestore, collection, onSnapshot, addDoc, doc, deleteDoc } from 'firebase/firestore'
import firebaseApp from '../../firebaseconfig'
export default {
    data() {
        return {
            nuevoColaborador: { nombre: '', email: '' },
            colaboradores: []
        }
    },
    mounted() {
        const db = getFirestore(firebaseApp);
        const colaboradoresRef = collection(db, 'colaboradores')
        onSnapshot(colaboradoresRef, (snapshot) => {
            this.colaboradores = snapshot.docs.map((doc) => ({
                id: doc.id,
                ...doc.data()
            }))
        })
    },
    methods: {
        async addColaborador() {
            const { nombre, email } = this.nuevoColaborador;
            if (nombre.trim() === '' || email.trim() === '') return
            const db = getFirestore(firebaseApp)
            const colaboradoresRef = collection(db, 'colaboradores')
            await addDoc(colaboradoresRef, { nombre, email })
            this.nuevoColaborador = { nombre: '', email: '' }
        },
        async deleteColaborador(colaboradorId) {
            const db = getFirestore(firebaseApp)
            const colaboradorRef = doc(db, 'colaboradores', colaboradorId)
            await deleteDoc(colaboradorRef)
        }
    }
}
</script>
