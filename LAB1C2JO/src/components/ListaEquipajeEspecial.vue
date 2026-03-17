<script>
export default {
    data() {
        return {
            // --- VARIABLES REACTIVAS ---
            pasajeroNombre: "",
            numeroVuelo: "",
            tipoEquipaje: "",
            pesoEquipaje: null,
            filtroVuelo: "", // Variable para el buscador
            
            // Lista simulando base de datos
            listaEquipajes: [
                { id: 1, pasajero: "Ana Pérez", vuelo: "AV123", tipo: "Guitarra", peso: 5.5, tarifa: 15, estado: "En Mostrador" },
                { id: 2, pasajero: "Juan López", vuelo: "TA456", tipo: "Tabla de Surf", peso: 22.0, tarifa: 65, estado: "En Bodega" }
            ],
            errores: []
        }
    },
    computed: {
        // --- PROPIEDAD COMPUTADA: Buscador/Filtro ---
        // Retorna la lista filtrada según lo que se escriba en la barra de búsqueda
        equipajesFiltrados() {
            if (!this.filtroVuelo.trim()) {
                return this.listaEquipajes;
            }
            return this.listaEquipajes.filter(e => 
                e.vuelo.toUpperCase().includes(this.filtroVuelo.toUpperCase())
            );
        }
    },
    methods: {
        // --- LÓGICA DE NEGOCIO: Calculadora de Tarifas ---
        calcularTarifa(tipo, peso) {
            let tarifa = 0;
            if (tipo === "Mascota en bodega") tarifa = 50;
            else if (tipo === "Guitarra") tarifa = 15;
            else if (tipo === "Tabla de Surf") tarifa = 30 + (peso > 15 ? (peso - 15) * 5 : 0); // $5 por kg extra arriba de 15kg
            else if (tipo === "Caja Frágil") tarifa = 20 + (peso > 10 ? (peso - 10) * 2 : 0);  // $2 por kg extra arriba de 10kg
            return tarifa;
        },

        registrarEquipaje() {
            this.errores = [];

            // 1. Validaciones básicas
            if (!this.pasajeroNombre.trim()) this.errores.push("El nombre es obligatorio.");
            if (!this.numeroVuelo.trim()) this.errores.push("El número de vuelo es obligatorio.");
            if (!this.tipoEquipaje) this.errores.push("Seleccione un tipo de equipaje.");
            if (!this.pesoEquipaje || this.pesoEquipaje <= 0) this.errores.push("Peso inválido (debe ser mayor a 0).");

            // 2. Validación avanzada: Límite de capacidad (Máximo 3 mascotas por vuelo)
            if (this.tipoEquipaje === "Mascota en bodega") {
                const mascotasEnVuelo = this.listaEquipajes.filter(
                    e => e.vuelo.toUpperCase() === this.numeroVuelo.toUpperCase() && e.tipo === "Mascota en bodega"
                ).length;

                if (mascotasEnVuelo >= 3) {
                    this.errores.push(`¡Alerta! Capacidad máxima de mascotas (3) alcanzada para el vuelo ${this.numeroVuelo.toUpperCase()}.`);
                }
            }

            // 3. Registro exitoso
            if (this.errores.length === 0) {
                const nuevaTarifa = this.calcularTarifa(this.tipoEquipaje, this.pesoEquipaje);

                const nuevoRegistro = {
                    id: Date.now(),
                    pasajero: this.pasajeroNombre,
                    vuelo: this.numeroVuelo.toUpperCase(),
                    tipo: this.tipoEquipaje,
                    peso: parseFloat(this.pesoEquipaje),
                    tarifa: nuevaTarifa,
                    estado: "En Mostrador" // Estado inicial por defecto
                };

                this.listaEquipajes.push(nuevoRegistro);
                this.limpiarFormulario();
            }
        },
        limpiarFormulario() {
            this.pasajeroNombre = "";
            this.numeroVuelo = "";
            this.tipoEquipaje = "";
            this.pesoEquipaje = null;
        },
        // --- MANIPULACIÓN DE ESTADO ---
        cambiarEstado(equipaje) {
            if (equipaje.estado === "En Mostrador") equipaje.estado = "En Bodega";
            else if (equipaje.estado === "En Bodega") equipaje.estado = "Cargado en Avión";
            else equipaje.estado = "En Mostrador"; // Ciclo de reinicio
        },
        eliminarRegistro(id) {
            this.listaEquipajes = this.listaEquipajes.filter(e => e.id !== id);
        },
        // Método para clases CSS dinámicas
        obtenerClaseEstado(estado) {
            if (estado === 'En Mostrador') return 'badge-mostrador';
            if (estado === 'En Bodega') return 'badge-bodega';
            return 'badge-cargado';
        }
    }
}
</script>

<template>
    <div class="modulo">
        <h2>Registro y Control de Equipaje OOG (Especial)</h2>
        
        <div class="paneles">
            <div class="panel-formulario">
                <h3>Nuevo Registro</h3>
                <form @submit.prevent="registrarEquipaje" class="formulario">
                    <div class="campo">
                        <label>Pasajero:</label>
                        <input type="text" v-model="pasajeroNombre" placeholder="Nombre completo">
                    </div>
                    <div class="campo">
                        <label>Vuelo:</label>
                        <input type="text" v-model="numeroVuelo" placeholder="Ej. AV123">
                    </div>
                    <div class="campo">
                        <label>Equipaje:</label>
                        <select v-model="tipoEquipaje">
                            <option value="" disabled>Seleccione...</option>
                            <option value="Guitarra">Guitarra (Tarifa Fija)</option>
                            <option value="Tabla de Surf">Tabla de Surf (+15kg recargo)</option>
                            <option value="Caja Frágil">Caja Frágil (+10kg recargo)</option>
                            <option value="Mascota en bodega">Mascota en bodega (Máx 3 por vuelo)</option>
                        </select>
                    </div>
                    <div class="campo">
                        <label>Peso (kg):</label>
                        <input type="number" step="0.1" v-model="pesoEquipaje" placeholder="0.0">
                    </div>
                    <button type="submit" class="btn-registrar">Procesar y Calcular Tarifa</button>
                </form>

                <div v-if="errores.length > 0" class="errores">
                    <h4>⚠️ Atención:</h4>
                    <ul>
                        <li v-for="(error, index) in errores" :key="index">{{ error }}</li>
                    </ul>
                </div>
            </div>

            <div class="panel-tabla">
                <div class="encabezado-tabla">
                    <h3>Panel de Control de Bodega</h3>
                    <input type="text" v-model="filtroVuelo" placeholder="Filtrar por N° de Vuelo..." class="buscador">
                </div>

                <p v-if="equipajesFiltrados.length === 0" class="mensaje-vacio">No hay equipaje registrado para este criterio.</p>
                
                <table v-else>
                    <thead>
                        <tr>
                            <th>Vuelo</th>
                            <th>Pasajero</th>
                            <th>Tipo / Peso</th>
                            <th>Tarifa</th>
                            <th>Estado Actual</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="equipaje in equipajesFiltrados" :key="equipaje.id">
                            <td><strong>{{ equipaje.vuelo }}</strong></td>
                            <td>{{ equipaje.pasajero }}</td>
                            <td>{{ equipaje.tipo }} <br><small>{{ equipaje.peso }} kg</small></td>
                            <td class="tarifa">${{ equipaje.tarifa.toFixed(2) }}</td>
                            <td>
                                <span class="badge" :class="obtenerClaseEstado(equipaje.estado)">
                                    {{ equipaje.estado }}
                                </span>
                            </td>
                            <td class="acciones">
                                <button @click="cambiarEstado(equipaje)" class="btn-estado">Actualizar</button>
                                <button @click="eliminarRegistro(equipaje.id)" class="btn-eliminar">✖</button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<style scoped>
    .modulo {
        font-family: Arial, sans-serif;
        color: #333;
    }
    .paneles {
        display: flex;
        gap: 20px;
        align-items: flex-start;
    }
    @media (max-width: 800px) {
        .paneles { flex-direction: column; }
    }
    .panel-formulario {
        flex: 1;
        background: #f9f9f9;
        padding: 20px;
        border-radius: 8px;
        border: 1px solid #ddd;
    }
    .panel-tabla {
        flex: 2;
    }
    .campo { margin-bottom: 15px; }
    label { display: block; font-weight: bold; margin-bottom: 5px; font-size: 14px;}
    input, select { width: 100%; padding: 8px; border: 1px solid #ccc; border-radius: 4px; box-sizing: border-box;}
    .btn-registrar { width: 100%; background: #0056b3; color: white; padding: 10px; border: none; border-radius: 4px; cursor: pointer; font-weight: bold;}
    .btn-registrar:hover { background: #004494; }
    
    .encabezado-tabla { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px;}
    .buscador { width: 250px; }
    
    table { width: 100%; border-collapse: collapse; background: white;}
    th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; }
    th { background-color: #f1f1f1; }
    .tarifa { color: #28a745; font-weight: bold; }
    .mensaje-vacio { color: #777; font-style: italic; }

    /* ESTILOS DINÁMICOS DE ESTADO */
    .badge { padding: 5px 10px; border-radius: 12px; font-size: 12px; font-weight: bold; color: white;}
    .badge-mostrador { background-color: #ffc107; color: #333; }
    .badge-bodega { background-color: #17a2b8; }
    .badge-cargado { background-color: #28a745; }

    .acciones { display: flex; gap: 5px; }
    .btn-estado { background: #6c757d; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; font-size: 12px;}
    .btn-eliminar { background: #dc3545; color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer;}
    
    .errores { background: #f8d7da; color: #721c24; padding: 15px; border-radius: 4px; margin-top: 15px; }
    .errores h4 { margin: 0 0 10px 0; }
    .errores ul { margin: 0; padding-left: 20px; }
</style>