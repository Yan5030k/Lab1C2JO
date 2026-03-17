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
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    color: #1e293b;
}

.modulo h2 {
    margin-bottom: 24px;
    font-size: 28px;
    font-weight: 700;
    color: #0f172a;
    text-align: center;
}

.paneles {
    display: flex;
    gap: 24px;
    align-items: flex-start;
}

@media (max-width: 900px) {
    .paneles {
        flex-direction: column;
    }
}

.panel-formulario,
.panel-tabla {
    background: rgba(255, 255, 255, 0.92);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(226, 232, 240, 0.9);
    border-radius: 20px;
    box-shadow: 0 14px 40px rgba(15, 23, 42, 0.08);
}

.panel-formulario {
    flex: 1;
    padding: 24px;
}

.panel-tabla {
    flex: 2;
    padding: 22px;
    overflow-x: auto;
}

.panel-formulario h3,
.panel-tabla h3 {
    margin-bottom: 18px;
    font-size: 20px;
    color: #0f172a;
    font-weight: 700;
}

.formulario {
    display: flex;
    flex-direction: column;
    gap: 4px;
}

.campo {
    margin-bottom: 16px;
}

label {
    display: block;
    font-weight: 600;
    margin-bottom: 8px;
    font-size: 14px;
    color: #334155;
}

input,
select {
    width: 100%;
    padding: 12px 14px;
    border: 1px solid #cbd5e1;
    border-radius: 12px;
    box-sizing: border-box;
    background: #f8fafc;
    color: #0f172a;
    font-size: 14px;
    transition: all 0.25s ease;
    outline: none;
}

input:focus,
select:focus {
    border-color: #2563eb;
    background: #ffffff;
    box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.12);
}

.btn-registrar {
    width: 100%;
    background: linear-gradient(135deg, #1d4ed8, #2563eb);
    color: white;
    padding: 13px;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    font-weight: 700;
    font-size: 14px;
    letter-spacing: 0.3px;
    transition: transform 0.2s ease, box-shadow 0.25s ease, opacity 0.25s ease;
    box-shadow: 0 10px 22px rgba(37, 99, 235, 0.28);
}

.btn-registrar:hover {
    transform: translateY(-1px);
    opacity: 0.95;
}

.encabezado-tabla {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 16px;
    margin-bottom: 18px;
    flex-wrap: wrap;
}

.buscador {
    width: 260px;
    max-width: 100%;
}

table {
    width: 100%;
    border-collapse: collapse;
    overflow: hidden;
    border-radius: 14px;
    background: white;
}

th,
td {
    padding: 14px 12px;
    text-align: left;
    border-bottom: 1px solid #e2e8f0;
    vertical-align: middle;
}

th {
    background: #eff6ff;
    color: #1e3a8a;
    font-size: 14px;
    font-weight: 700;
}

tbody tr {
    transition: background 0.2s ease;
}

tbody tr:hover {
    background: #f8fafc;
}

td strong {
    color: #0f172a;
}

td small {
    color: #64748b;
    font-size: 12px;
}

.tarifa {
    color: #16a34a;
    font-weight: 700;
}

.mensaje-vacio {
    color: #64748b;
    font-style: italic;
    background: #f8fafc;
    padding: 14px;
    border-radius: 12px;
    border: 1px dashed #cbd5e1;
}

/* Badges */
.badge {
    display: inline-block;
    padding: 7px 12px;
    border-radius: 999px;
    font-size: 12px;
    font-weight: 700;
    letter-spacing: 0.2px;
}

.badge-mostrador {
    background: #fef3c7;
    color: #92400e;
}

.badge-bodega {
    background: #dbeafe;
    color: #1d4ed8;
}

.badge-cargado {
    background: #dcfce7;
    color: #166534;
}

.acciones {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
}

.btn-estado,
.btn-eliminar {
    border: none;
    padding: 8px 12px;
    border-radius: 10px;
    cursor: pointer;
    font-size: 12px;
    font-weight: 600;
    transition: transform 0.2s ease, opacity 0.2s ease;
}

.btn-estado {
    background: #334155;
    color: white;
}

.btn-estado:hover {
    transform: translateY(-1px);
    opacity: 0.92;
}

.btn-eliminar {
    background: #dc2626;
    color: white;
}

.btn-eliminar:hover {
    transform: translateY(-1px);
    opacity: 0.92;
}

.errores {
    background: #fef2f2;
    color: #991b1b;
    padding: 16px;
    border-radius: 14px;
    margin-top: 18px;
    border: 1px solid #fecaca;
}

.errores h4 {
    margin: 0 0 10px 0;
    font-size: 15px;
}

.errores ul {
    margin: 0;
    padding-left: 20px;
}
</style>