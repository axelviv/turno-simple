# **Metodología de Desarrollo Utilizada: 

Enfoque Híbrido Guiado por Dominio con Arquitectura Limpia Simplificada**

## **Metodologías combinadas**

- Domain‑Driven Design (DDD)
- Clean Architecture (versión simplificada)
- Use‑Case Driven Development
- Hexagonal Architecture (Ports & Adapters)
- Prácticas ágiles de descomposición en fases y tareas


# **Fases del desarrollo**

### **FASE 1 — Dominio**

1.1 Definir entidades del dominio  
1.2 Definir relaciones entre entidades del dominio  
1.3 Definir invariantes del dominio  

### **FASE 2 — Casos de uso**

2.1 Identificación de Casos de Uso  
2.2 Descripción de Casos de Uso  
2.3 Reglas y validaciones por Caso de Uso  

### **FASE 3 — Lógica de negocio avanzada**

3.1 Definición de reglas de fechas y rangos válidos  
3.2 Definición de reglas de horarios y disponibilidad  
3.3 Definición de reglas de cupos y límites  
3.4 Definición de estrategias de asignación automática  
3.5 Definición de políticas especiales del negocio  

### **FASE 4 — Infraestructura**

4.1 Configuración de acceso a datos (ORM / repositorios)  
4.2 Configuración de la base de datos  
4.3 Estrategia de migraciones y actualización de esquema  
4.4 Estrategia de inicialización de datos  
4.5 Estrategia de backups y recuperación  
4.6 Configuración de rendimiento y modos de escritura  

### **FASE 5 — API**

5.1 Diseño de controladores / endpoints  
5.2 Definición de modelos de transporte (DTOs)  
5.3 Validaciones de entrada y reglas de contrato  
5.4 Manejo de errores y respuestas estándar  
5.5 Registro de logs y trazabilidad  

### **FASE 6 — Pruebas**

6.1 Pruebas de creación y modificación de entidades clave  
6.2 Pruebas de reglas de negocio e invariantes  
6.3 Pruebas de disponibilidad y restricciones de uso  
6.4 Pruebas de estados y transiciones  
6.5 Pruebas de límites, bordes y escenarios extremos  

### **FASE 7 — Integración**

7.1 Integración con la interfaz de usuario (frontend)  
7.2 Pruebas de flujo completo end-to-end  
7.3 Ajustes finales y correcciones de integración  

-----------------------------------------------------------------------------------------------------------

### **FASE 1 — Dominio**

1.1 **Definir entidades del dominio**

Las entidades principales del sistema son:

- Paciente
- Médico
- Turno
- Cupos *(entidad global que define los límites y parámetros de cupos del sistema)*


1.2 **Definir relaciones entre entidades del dominio**

- Paciente 1 → N Turnos
- Médico 1 → N Turnos
- Turno N → 1 Paciente
- Turno N → 1 Médico
- Cupos → afecta a Médico *(relación conceptual)*


1.3 **Definir invariantes del dominio**

- Invariantes de Paciente
  - Un Paciente solo puede tener un turno en progreso
  - El número de documento del Paciente debe ser único

- Invariantes de Médico
  - El médico solo podrá tener 60 turnos mensuales
  - Cada médico tendrá un ID único

- Invariantes de Turno
  - Un turno solo puede crearse para un médico que esté disponible
  - Un turno solo puede crearse en fechas y horarios disponibles
  - Un turno solo puede crearse para el futuro
  - Un turno pasado no puede ser eliminado de la base de datos si fue confirmado
  - Un turno no puede volver a un estado anterior una vez confirmado
  - Un turno debe tener siempre un médico asignado
  - Un turno debe tener siempre un Paciente asignado
  - Un turno no puede existir sin una fecha y un horario válidos
  - Un turno no puede cambiar su Paciente una vez confirmado
  - Un turno no puede cambiar su médico una vez confirmado
  - Un turno debe abarcar exactamente 10 días hábiles consecutivos a partir de la fecha seleccionada
  - Los turnos de un mismo médico no pueden superponerse en días ni horarios

- Invariantes de Cupos
  - El cupo debe controlar la cantidad de turnos de los médicos
  - El cupo debe permitir 10 turnos extras a todos los médicos solo cuando todos hayan llegado a los 60 turnos en el mes
  - El cupo no puede ser negativo ni exceder los límites definidos por el dominio


### **FASE 2 — Casos de Uso**

2.1 **Identificación de Casos de Uso**

Los casos de uso principales del sistema son:

- Casos de Uso de Paciente
  - Consultar Paciente
  - Modificar Paciente

- Casos de Uso de Médico
  - Crear Médico (Administrador)
  - Modificar Médico (Administrador)
  - Desactivar Médico (Administrador)
  - Reactivar Médico (Administrador)
  - Listar Médicos

- Casos de Uso de Turno
  - Crear turno
  - Modificar turno
  - Cancelar turno
  - Abandonar turno
  - Listar turnos

- Casos de Uso de Reportes
  - Generar reporte mensual de turnos

- Casos de Uso del Sistema (Procesos Automáticos) 
  - Confirmación Automática del Turno 
  - Finalización Automática del Turno


2.2 **Descripción de Casos de Uso**

## **Casos de Uso de Paciente**

## **Caso de Uso: Consultar Paciente**

---

## **Objetivo**
Permitir que el Usuario acceda a los datos de un Paciente existente mediante la búsqueda por DNI.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe ingresar un DNI válido.

---

## **Postcondiciones**
- Los datos del Paciente son mostrados por el sistema.

---

## **Flujo principal**
1. El Usuario ingresa el DNI del Paciente.
2. El sistema muestra los datos del Paciente si este existe.

---

## **Flujos alternativos**

### **A1 — El Paciente no existe en la base de datos**
1. El sistema informa que el DNI ingresado no existe.
2. El caso de uso termina.

---

## **Flujos de excepción**

### **E1 — Error al consultar datos**
1. El sistema informa que ocurrió un error al intentar acceder a los datos.   
2. El caso de uso termina sin mostrar datos.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El Paciente debe tener DNI.
- **RN-02:** El Paciente debe existir en la base de datos para poder consultar sus datos.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar un Paciente por DNI. 
- **RF-02:** El sistema debe mostrar los datos actuales del Paciente. 
- **RF-03:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Paciente:** DNI, nombre, apellido, teléfono, email.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Modificar Paciente**

---

## **Objetivo**
Permitir que el Usuario actualice los datos de un Paciente existente mediante la búsqueda por DNI.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe ingresar un DNI válido.

---

## **Postcondiciones**
- Los datos del Paciente quedan actualizados en la base de datos.
- El DNI del paciente no puede ser modificado ya que es único.

---

## **Flujo principal**
1. El Usuario ingresa el DNI del Paciente.
2. El sistema muestra los datos del Paciente si este existe.
3. El Usuario modifica los datos deseados del Paciente.
4. El Usuario confirma la modificación de los datos.
5. El sistema registra los cambios en el Paciente.
6. El sistema muestra la confirmación de los cambios realizados al Paciente.

---

## **Flujos alternativos**

### **A1 — El Paciente no existe en la base de datos**
1. El sistema informa que el DNI ingresado no existe.
2. El caso de uso termina sin realizar modificaciones.

---

## **Flujos de excepción**

### **E1 — Error al guardar los cambios**
1. El sistema informa que ocurrió un error al intentar actualizar los datos.   
2. El caso de uso termina.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El Usuario no puede modificar el DNI.
- **RN-02:** El Paciente debe existir en la base de datos para poder modificar sus datos.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar un Paciente por DNI. 
- **RF-02:** El sistema debe mostrar los datos actuales del Paciente. 
- **RF-03:** El sistema debe permitir modificar los campos editables del Paciente. 
- **RF-04:** El sistema debe validar los datos ingresados por el Usuario. 
- **RF-05:** El sistema debe impedir la modificación del DNI. 
- **RF-06:** El sistema debe registrar los cambios en la base de datos. 
- **RF-07:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Paciente:** DNI, nombre, apellido, teléfono, email.

---

-----------------------------------------------------------------------------------------------------------

## **Casos de Uso de Médico**

## **Caso de Uso: Crear Médico**

---

## **Objetivo**
Permitir el registro de un nuevo Médico.

---

## **Actores**
- **Administrador** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Administrador debe tener acceso al sistema.
- El DNI ingresado debe ser válido.

---

## **Postcondiciones**
- Los datos del Médico quedan registrados en la base de datos.
- El Médico se registra automáticamente con estado "activo".

---

## **Flujo principal**
1. El sistema muestra un formulario vacío.
2. El Administrador ingresa todos los datos, incluido el DNI del nuevo Médico.
3. El Administrador confirma los datos ingresados.
4. El sistema valida que el DNI no exista en la base de datos.
5. El sistema registra el nuevo Médico.
6. El sistema muestra la confirmación del registro del nuevo Médico.

---

## **Flujos alternativos**

### **A1 — El Médico ya existe en la base de datos** 
*Ocurre en el paso 4 del flujo principal.*
1. El sistema informa que el DNI ingresado ya existe.
2. El caso de uso termina sin realizar el registro.

---

## **Flujos de excepción**

### **E1 — Error al registrar los datos**
1. El sistema informa que ocurrió un error al intentar registrar los datos.   
2. El caso de uso termina sin realizar registros.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El DNI del Médico debe ser único.
- **RN-02:** No se puede registrar un Médico ya existente.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe mostrar un formulario para ingresar los datos del Médico.
- **RF-02:** El sistema debe validar que el DNI no exista en la base de datos.
- **RF-03:** El sistema debe registrar los datos en la base de datos. 
- **RF-04:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido, teléfono, email.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Modificar Médico**

---

## **Objetivo**
Permitir que el Administrador actualice los datos de un Médico existente mediante la búsqueda por DNI.

---

## **Actores**
- **Administrador** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Administrador debe ingresar un DNI válido.

---

## **Postcondiciones**
- Los datos del Médico quedan actualizados en la base de datos.
- El DNI del Médico no puede ser modificado ya que es único.

---

## **Flujo principal**
1. El Administrador ingresa el DNI del Médico.
2. El sistema muestra los datos del Médico si este existe.
3. El Administrador modifica los datos deseados.
4. El Administrador confirma la modificación de los datos.
5. El sistema registra los cambios en el Médico.
6. El sistema muestra la confirmación de los cambios realizados.

---

## **Flujos alternativos**

### **A1 — El Médico no existe en la base de datos**
1. El sistema informa que el DNI ingresado no existe.
2. El caso de uso termina.

---

## **Flujos de excepción**

### **E1 — Error al guardar los cambios**
1. El sistema informa que ocurrió un error al intentar actualizar los datos.   
2. El caso de uso termina sin aplicar modificaciones.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El Administrador no puede modificar el DNI.
- **RN-02:** El Médico debe existir en la base de datos para poder modificar sus datos.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar un Médico por DNI. 
- **RF-02:** El sistema debe mostrar los datos actuales del Médico. 
- **RF-03:** El sistema debe permitir modificar los campos editables del Médico. 
- **RF-04:** El sistema debe validar los datos modificados por el Administrador antes de registrarlos.
- **RF-05:** El sistema debe impedir la modificación del DNI. 
- **RF-06:** El sistema debe registrar los cambios en la base de datos. 
- **RF-07:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido, teléfono, email.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Desactivar Médico**

---

## **Objetivo**
Permitir que el Administrador cambie el estado de un Médico a "inactivo" mediante la búsqueda por DNI.

---

## **Actores**
- **Administrador** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Administrador debe ingresar un DNI válido.

---

## **Postcondiciones**
- El estado del Médico queda actualizado como "inactivo" en la base de datos.

---

## **Flujo principal**
1. El Administrador ingresa el DNI del Médico.
2. El sistema muestra los datos del Médico si este existe.
3. El Administrador selecciona la opción de desactivar al Médico.
4. El Administrador confirma la desactivación.
5. El sistema cambia el estado del Médico a "inactivo".
6. El sistema muestra la confirmación de la desactivación.

---

## **Flujos alternativos**

### **A1 — El Médico no existe en la base de datos**
1. El sistema informa que el DNI ingresado no existe.
2. El caso de uso termina.

---

## **Flujos de excepción**

### **E1 — Error al guardar los cambios**
1. El sistema informa que ocurrió un error al intentar actualizar el estado del Médico.   
2. El caso de uso termina sin aplicar modificaciones.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El Médico debe existir en la base de datos para poder ser desactivado.
- **RN-02:** No se puede desactivar un Médico que ya esté inactivo.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar un Médico por DNI. 
- **RF-02:** El sistema debe mostrar los datos actuales del Médico. 
- **RF-03:** El sistema debe permitir desactivar al Médico. 
- **RF-04:** El sistema debe validar que el Médico esté activo antes de desactivarlo.
- **RF-05:** El sistema debe registrar los cambios en la base de datos. 
- **RF-06:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, Estado.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Reactivar Médico**

---

## **Objetivo**
Permitir que el Administrador cambie el estado de un Médico a "activo" mediante la búsqueda por DNI.

---

## **Actores**
- **Administrador** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Administrador debe ingresar un DNI válido.

---

## **Postcondiciones**
- El estado del Médico queda actualizado como "activo" en la base de datos.

---

## **Flujo principal**
1. El Administrador ingresa el DNI del Médico.
2. El sistema muestra los datos del Médico si este existe.
3. El Administrador selecciona la opción de reactivar al Médico.
4. El Administrador confirma la activación.
5. El sistema cambia el estado del Médico a "activo".
6. El sistema muestra la confirmación de la activación.

---

## **Flujos alternativos**

### **A1 — El Médico no existe en la base de datos**
1. El sistema informa que el DNI ingresado no existe.
2. El caso de uso termina.

---

## **Flujos de excepción**

### **E1 — Error al guardar los cambios**
1. El sistema informa que ocurrió un error al intentar actualizar el estado del Médico.   
2. El caso de uso termina sin aplicar modificaciones.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El Médico debe existir en la base de datos para poder ser reactivado.
- **RN-02:** No se puede reactivar un Médico que ya esté activo.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar un Médico por DNI. 
- **RF-02:** El sistema debe mostrar los datos actuales del Médico. 
- **RF-03:** El sistema debe permitir reactivar al Médico. 
- **RF-04:** El sistema debe validar que el Médico esté inactivo antes de reactivarlo.
- **RF-05:** El sistema debe registrar los cambios en la base de datos. 
- **RF-06:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, Estado.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Listar Médicos**

---

## **Objetivo**
Permitir que el Usuario consulte una lista de los Médicos registrados.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema.

---

## **Postcondiciones**
- El Usuario obtiene un listado de los Médicos activos en la base de datos.

---

## **Flujo principal**
1. El Usuario realiza el pedido del listado de Médicos.
2. El sistema muestra los datos de los Médicos activos.

---

## **Flujos alternativos**

### **A1 — No hay Médicos activos en la base de datos**
1. El sistema informa que no existen Médicos activos.
2. El caso de uso termina.

---

## **Flujos de excepción**

### **E1 — Error al consultar datos**
1. El sistema informa que ocurrió un error al intentar consultar los Médicos.   
2. El caso de uso termina sin mostrar datos.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El sistema solo debe mostrar médicos activos en el listado.
- **RN-02:** No se puede listar Médicos inactivos.

---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir buscar los Médicos activos. 
- **RF-02:** El sistema debe validar que el Médico esté activo antes de listarlo. 
- **RF-03:** El sistema debe mostrar los Médicos activos actuales en un listado. 
- **RF-06:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido, teléfono, email, Estado.

---

-----------------------------------------------------------------------------------------------------------

## **Casos de Uso de Turno**

## **Caso de Uso: Crear Turno**

---

## **Objetivo**
Permitir el registro de un nuevo Turno.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema.
- Los datos del Paciente deben ser válidos.
- La fecha y hora del turno debe ser válida.
- El Médico seleccionado debe ser válido.

---

## **Postcondiciones**
- El Turno inicia en estado Pendiente.
- Al crear un Turno se consume 1 cupo mensual del Médico.
- Los datos del Turno quedan registrados en la base de datos.
- El Turno queda ligado al Paciente y al Médico asignado.
- El sistema reserva los horarios de todas las sesiones generadas.

---

## **Flujo principal**
1. El Usuario inicia la creación de un nuevo Turno.
2. El Usuario ingresa el DNI del Paciente.
3. El sistema verifica si el Paciente existe:
   - Si existe, carga sus datos.
   - Si no existe, lo registra.
4. El sistema sugiere al Médico con menor carga de turnos mensuales que tenga disponibilidad.
5. El Usuario selecciona el Médico sugerido o elige otro.
6. El Usuario selecciona la fecha y hora de inicio del Turno.
7. El sistema valida que la fecha y hora sean válidas.
8. El sistema genera automáticamente las 10 sesiones del Turno según las reglas de negocio.
9. El sistema valida que el Médico tenga disponibilidad en todas las sesiones generadas.
10. El sistema valida que en ninguna sesión el Médico supere su capacidad máxima de 2 pacientes simultáneos.
11. El Usuario confirma la creación del Turno.
12. El sistema registra el Turno. Las sesiones no se almacenan como entidades, pero sus horarios sí quedan reservados.
13. El sistema muestra la confirmación del registro.

---

## **Flujos alternativos**

### **A1 — El Médico no tiene cupo disponible**
1. El sistema informa que el Médico no tiene cupos disponibles para el mes.
2. El caso de uso continúa permitiendo elegir otro Médico o cancelar la operación.

---

## **Flujos de excepción**

### **E1 — Error al registrar los datos**
1. El sistema informa que ocurrió un error al intentar registrar los datos.   
2. El caso de uso termina sin realizar registros.  

---

## **Reglas de negocio asociadas**
- **RN-01:** El id del Turno debe ser único.
- **RN-02:** No se puede asignar una sesión en una fecha y hora donde el Médico ya tenga 2 sesiones correspondientes a cualquier Turno.
- **RN-03:** No se puede crear un Turno en una fecha pasada
- **RN-04:** No se puede crear un Turno cuya primera sesión sea posterior a la semana siguiente a la fecha actual.
- **RN-05:** Un Turno está compuesto por 10 sesiones.
- **RN-06:** Cada sesión dura 20 minutos.
- **RN-07:** Las sesiones deben realizarse en días hábiles consecutivos.
- **RN-08:** Las sesiones del Turno se generan dinámicamente para validar disponibilidad, pero no se almacenan como entidades independientes.
- **RN-09:** Todas las sesiones deben realizarse a la misma hora seleccionada por el Usuario.
- **RN-10:** El sistema debe generar automáticamente las 10 sesiones del Turno.  
- **RN-11:** El sistema debe validar que el Médico tenga disponibilidad en todas las sesiones generadas.
- **RN-12:** Un Médico puede atender hasta 2 Pacientes en la misma fecha y hora.
- **RN-13:** Si alguna sesión supera la capacidad máxima del Médico, el Turno no puede crearse.
- **RN-14:** Un Médico puede tener un máximo de 60 turnos mensuales.
- **RN-15:** Si todos los Médicos alcanzan el límite de 60 turnos mensuales, el sistema habilita automáticamente 10 turnos adicionales por Médico.
- **RN-16:** El sistema debe sugerir al Médico con menor carga mensual que además tenga disponibilidad en todas las sesiones del Turno.
- **RN-17:** Un Turno podrá ser cancelado solo dentro del margen permitido (hasta 30 minutos después de la primera sesión).
- **RN-18:** Si no se cancela, pasará automáticamente a estado Confirmado al llegar la fecha y hora de la primera sesión.
- **RN-19:** El estado Abandonado solo puede aplicarse después de concluida la primera sesión, libera las sesiones futuras y no afecta cupos.
- **RN-20:** La reserva de horarios de las sesiones es obligatoria y se realiza al crear el turno.

     
---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir cargar los datos del Paciente, elegir el Médico y la fecha del Turno.
- **RF-02:** El sistema debe validar que el Turno pueda ser asignado al Médico en esa fecha.
- **RF-03:** El sistema debe registrar los datos del Turno en la base de datos. 
- **RF-04:** El sistema debe informar el resultado de la operación (éxito o error). 

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido
- **Paciente:** DNI, nombre, apellido, teléfono, email.
- **Turno:** Id, Paciente, Médico, Diagnóstico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), CantidadSesiones (10), DuraciónSesion (20 minutos), FechaInicio, FechaFin (calculada), HoraInicio. 

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Modificar Turno**

---

## **Objetivo**
Permitir la modificación de un Turno existente, garantizando que los cambios cumplan con las reglas de negocio y la disponibilidad del Médico.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema.
- El Turno debe existir.
- El Turno debe estar en estado Pendiente o Confirmado.
- El Paciente y el Médico deben ser válidos.
- La primera sesión del Turno no debe haber ocurrido aún.

---

## **Postcondiciones**
- El Turno queda actualizado en la base de datos.
- Si se modifica FechaInicio u HoraInicio, el sistema libera las reservas anteriores, valida los nuevos horarios y los reserva.

---

## **Flujo principal**
1. El Usuario selecciona un Turno existente para modificar.
2. El sistema muestra los datos actuales del Turno.
3. El Usuario modifica uno o más de los siguientes datos: 
  - Médico. 
  - FechaInicio. 
  - HoraInicio. 
  - Diagnóstico.
4. El sistema valida que los datos ingresados sean válidos.
5. Si se modifica el Médico, el sistema sugiere al Médico con menor carga mensual que tenga disponibilidad.
6. Si se modificó la FechaInicio o la HoraInicio:
  - El sistema genera dinámicamente las 10 sesiones del Turno según las reglas de negocio.
  - El sistema valida que el Médico tenga disponibilidad en todas las sesiones generadas.
  - El sistema valida que en ninguna sesión el Médico supere su capacidad máxima de 2 pacientes simultáneos.
7. El Usuario confirma la modificación del Turno.
8. El sistema actualiza el Turno en la base de datos. (Las sesiones no se almacenan; solo se generan para validación.)
9. El sistema muestra la confirmación de la modificación.
10. El sistema actualiza las reservas de horarios según las nuevas sesiones generadas.
11. Si se modifica el Médico, el sistema valida que el nuevo Médico tenga cupo disponible.

---

## **Flujos alternativos**

### **A1 — El Médico no tiene cupo disponible**
1. El sistema informa que el Médico no tiene cupos disponibles para el mes.
2. El caso de uso continúa permitiendo elegir otro Médico o cancelar la operación.

### **A2 — El Usuario cancela la operación**
1. El Usuario decide no continuar con la modificación.
2. El caso de uso termina sin realizar cambios.

---

## **Flujos de excepción**

### **E1 — El Turno no existe**
1. El sistema informa que el Turno seleccionado no existe. 
2. El caso de uso termina.  

### **E2 — Error al actualizar los datos**
1. El sistema informa que ocurrió un error al intentar actualizar el Turno.
2. El caso de uso termina sin realizar cambios. 

---

## **Reglas de negocio asociadas**
- **RN-01:** El id del Turno debe ser único.
- **RN-02:** No se puede asignar una sesión en una fecha y hora donde el Médico ya tenga 2 sesiones        provenientes de cualquier Turno. 
- **RN-03:** No se puede modificar un Turno hacia una fecha pasada.
- **RN-04:** Un Turno está compuesto por 10 sesiones. 
- **RN-05:** Cada sesión dura 20 minutos. 
- **RN-06:** Las sesiones deben realizarse en días hábiles consecutivos. 
- **RN-07:** Las sesiones del Turno se generan dinámicamente para validar disponibilidad, pero no se  almacenan como entidades independientes. 
- **RN-08:** Todas las sesiones deben realizarse a la misma hora seleccionada por el Usuario. 
- **RN-09:** El sistema debe generar automáticamente las 10 sesiones del Turno al modificar fecha u hora. - **RN-10:** El sistema debe validar que el Médico tenga disponibilidad en todas las sesiones generadas. 
- **RN-11:** Un Médico puede atender hasta 2 Pacientes en la misma fecha y hora. 
- **RN-12:** Si alguna sesión supera la capacidad máxima del Médico, el Turno no puede modificarse. 
- **RN-13:** Un Médico puede tener un máximo de 60 turnos mensuales. 
- **RN-14:** Si todos los Médicos alcanzan el límite de 60 turnos mensuales, el sistema habilita automáticamente 10 turnos adicionales por Médico. 
- **RN-15:** El sistema debe sugerir al Médico con menor carga mensual que además tenga disponibilidad en todas las sesiones del Turno.
- **RN-16:** Un Turno no puede modificarse si está en estado Cancelado, Finalizado o Abandonado.
- **RN-17:** Un Turno solo puede modificarse antes de la fecha y hora de la primera sesión.
- **RN-18:** Si se cambia el Médico, el sistema debe validar que el nuevo Médico tenga cupo disponible.
- **RN-19:** Si el Turno no se modifica ni cancela antes de la primera sesión, pasará automáticamente a estado Confirmado.
- **RN-20:** Si la primera sesión ya ocurrió, el Turno solo puede pasar a estado Abandonado o Finalizado, pero no puede modificarse.
     
---

## **Requerimientos funcionales derivados** 
- **RF-01:** El sistema debe permitir seleccionar un Turno para modificar. 
- **RF-02:** El sistema debe permitir modificar Médico, FechaInicio, HoraInicio y Diagnóstico. 
- **RF-03:** El sistema debe validar disponibilidad del Médico ante cambios de fecha u hora. 
- **RF-04:** El sistema debe actualizar los datos del Turno en la base de datos. 
- **RF-05:** El sistema debe informar el resultado de la operación.

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido 
- **Paciente:** DNI, nombre, apellido, teléfono, email. 
- **Turno:** Id, Paciente, Médico, Diagnóstico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), CantidadSesiones (10), DuraciónSesion (20 minutos), FechaInicio, FechaFin (calculada), HoraInicio.

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Cancelar Turno**

---

## **Objetivo**
Permitir que el Usuario cancele un Turno existente dentro del margen permitido, cambiando su estado a "Cancelado", devolviendo el cupo consumido y liberando todas las fechas y horarios reservados para las sesiones del turno

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema. 
- El Turno debe existir. 
- El Turno debe estar en estado "Pendiente" o "Confirmado". 
- La cancelación puede realizarse desde la creación del turno y hasta 30 minutos después de la fecha y hora de la primera sesión.

---

## **Postcondiciones**
- El Turno queda con estado "Cancelado". 
- Se devuelve el cupo mensual consumido al crear el turno. 
- Se liberan todas las fechas y horarios reservados para las sesiones del turno. 
- La cancelación queda registrada con fecha y hora.

---

## **Flujo principal**
1. El Usuario selecciona un Turno existente para cancelar. 
2. El Sistema muestra los datos del Turno y solicita confirmación. 
3. El Usuario confirma la cancelación del Turno. 
4. El Sistema valida que el Turno pueda cancelarse según las reglas de negocio: 
    - El Turno está en estado Pendiente o Confirmado. 
    - La cancelación ocurre dentro del margen permitido (hasta 30 minutos después del inicio de la primera sesión). 
5. El Sistema cambia el estado del Turno a "Cancelado". 
6. El Sistema devuelve el cupo consumido al crear el turno. 
7. El Sistema libera todas las fechas y horarios reservados para las sesiones del turno. 
8. El Sistema registra la cancelación (fecha y hora). 
9. El Sistema muestra la confirmación de la cancelación.

---

## **Flujos alternativos**

### **A1 — El Usuario cancela la operación**
1. El Usuario decide no continuar con la cancelación. 
2. El caso de uso termina sin realizar cambios.

---

## **Flujos de excepción**

### **E1 — El Turno no existe** 
1. El Sistema informa que el Turno seleccionado no existe. 
2. El caso de uso termina.

### **E2 — El Turno no puede cancelarse** 
1. El Sistema detecta que el Turno no puede cancelarse porque: 
    - ya pasaron más de 30 minutos desde el inicio de la primera sesión, o 
    - el Turno está en estado Finalizado, Cancelado o Abandonado. 
2. El Sistema informa que la cancelación no está permitida. 
3. El caso de uso termina sin realizar cambios.

### **E3 — Error al procesar la cancelación** 
1. El Sistema informa que ocurrió un error al intentar cancelar el Turno. 
2. El caso de uso termina sin realizar cambios.

---

## **Reglas de negocio asociadas**
- **RN-01:** Al crear un Turno, se consume 1 cupo mensual del Médico (máximo 60). 
- **RN-02:** Un Turno pasa automáticamente a estado Confirmado al llegar la fecha y hora de la primera sesión si no fue cancelado previamente. 
- **RN-03:** Un Turno puede cancelarse desde su creación y hasta 30 minutos después del inicio de la primera sesión. 
- **RN-04:** Si la cancelación ocurre dentro del margen permitido, se devuelve el cupo consumido. 
- **RN-05:** Al cancelar un Turno dentro del margen permitido, se liberan todas las fechas y horarios reservados para sus sesiones. 
- **RN-06:** Toda cancelación queda registrada y es visible para otros Usuarios del sistema. 
- **RN-07:** Un Turno no puede cancelarse si está en estado Finalizado, Cancelado o Abandonado. 
- **RN-08:** El estado Abandonado solo puede aplicarse despues de concluida la primera sesión, libera los horarios de las siguientes sesiones y no afecta cupos.
     
---

## **Requerimientos funcionales derivados** 
- **RF-01:** El Sistema debe permitir seleccionar un Turno para cancelar. 
- **RF-02:** El Sistema debe solicitar confirmación antes de cancelar un Turno. 
- **RF-03:** El Sistema debe validar que el Turno pueda cancelarse según las reglas de negocio. 
- **RF-04:** El Sistema debe actualizar el estado del Turno a Cancelado. 
- **RF-05:** El Sistema debe devolver el cupo consumido. 
- **RF-06:** El Sistema debe liberar todas las fechas y horarios reservados. 
- **RF-07:** El Sistema debe registrar la cancelación. 
- **RF-08:** El Sistema debe informar el resultado de la operación.

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido, cupos consumidos en el mes. 
- **Paciente:** DNI, nombre, apellido, teléfono, email. 
- **Turno:** Id, Paciente, Médico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), Fecha y hora de la primera sesión, Fechas y horarios de todas las sesiones reservadas, CantidadSesiones, DuraciónSesion, Fecha de creación

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Abandonar Turno**

---

## **Objetivo**
Permitir que el Usuario marque un Turno como "Abandonado" cuando la primera sesión ya ocurrió, liberando únicamente las sesiones futuras sin afectar los cupos del Médico.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema.
- El Turno debe existir.
- El Turno debe estar en estado "Confirmado".
- La primera sesión del Turno debe haber concluido.
- El Turno no debe estar en estado Pendiente, Cancelado, Finalizado o Abandonado.

---

## **Postcondiciones**
- El Turno queda con estado "Abandonado".
- El sistema libera los horarios de todas las sesiones futuras del Turno.
- El cupo mensual del Médico no se modifica.
- El registro de abandono queda almacenado con fecha, hora y Usuario responsable.

---

## **Flujo principal**
1. El Usuario selecciona un Turno existente para marcar como Abandonado.  
2. El Sistema muestra los datos del Turno y solicita confirmación.  
3. El Usuario confirma la acción.  
4. El Sistema valida que la primera sesión ya haya concluido.  
5. El Sistema valida que el Turno esté en estado "Confirmado".  
6. El Sistema cambia el estado del Turno a "Abandonado".  
7. El Sistema libera los horarios de todas las sesiones futuras del Turno.  
8. El Sistema registra la acción (fecha y hora).  
9. El Sistema muestra la confirmación de la operación.

---

## **Flujos alternativos**

### **A1 — El Usuario cancela la operación**
1. El Usuario decide no continuar con el abandono del Turno.  
2. El caso de uso termina sin realizar cambios.

---

## **Flujos de excepción**

### **E1 — El Turno no existe**
1. El Sistema informa que el Turno seleccionado no existe.  
2. El caso de uso termina.

---

### **E2 — La primera sesión no ha ocurrido**
1. El Sistema informa que el Turno no puede marcarse como Abandonado porque la primera sesión aún no ocurrió.  
2. El caso de uso termina sin realizar cambios.

---

### **E3 — El Turno está en un estado no permitido**
1. El Sistema detecta que el Turno está en estado Pendiente, Cancelado, Finalizado o Abandonado.  
2. El Sistema informa que no puede marcarse como Abandonado.  
3. El caso de uso termina sin realizar cambios.

---

### **E4 — Error al actualizar los datos**
1. El Sistema informa que ocurrió un error al intentar actualizar el Turno.  
2. El caso de uso termina sin realizar cambios.

---

## **Reglas de negocio asociadas**
- **RN-01:** El id del Turno debe ser único.  
- **RN-02:** Un Turno solo puede marcarse como "Abandonado" si la primera sesión ya ocurrió.  
- **RN-03:** El estado "Abandonado" libera únicamente las sesiones futuras.  
- **RN-04:** El estado "Abandonado" no afecta cupos del Médico.  
- **RN-05:** Un Turno no puede marcarse como "Abandonado" si está en estado Pendiente, Cancelado, Finalizado o Abandonado.  
- **RN-06:** Si la segunda sesión ya inició, el Turno solo puede pasar a estado Abandonado o Finalizado.  
- **RN-07:** El Sistema debe registrar la acción de abandono con fecha y hora.

---

## **Requerimientos funcionales derivados**
- **RF-01:** El Sistema debe permitir seleccionar un Turno para marcar como "Abandonado".  
- **RF-02:** El Sistema debe validar que la primera sesión ya haya ocurrido.  
- **RF-03:** El Sistema debe actualizar el estado del Turno a "Abandonado".  
- **RF-04:** El Sistema debe liberar los horarios de las sesiones futuras.  
- **RF-05:** El Sistema debe registrar la acción realizada.  
- **RF-06:** El Sistema debe informar el resultado de la operación.

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido, cupos consumidos.  
- **Paciente:** DNI, nombre, apellido, teléfono, email.  
- **Turno:** Id, Paciente, Médico, Diagnóstico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), CantidadSesiones (10), DuraciónSesion (20 minutos), FechaInicio, FechaFin (calculada), HoraInicio, Sesiones futuras reservadas (a liberar)

---

-----------------------------------------------------------------------------------------------------------

## **Caso de Uso: Listar Turnos**

---

## **Objetivo**
Permitir que el Usuario visualice los Turnos registrados en el sistema, aplicando filtros opcionales para facilitar la búsqueda y el análisis.

---

## **Actores**
- **Usuario** (actor principal)
- **Sistema** (actor secundario)

---

## **Precondiciones**
- El Usuario debe tener acceso al sistema.

---

## **Postcondiciones**
- El Sistema muestra el listado de Turnos según los filtros seleccionados.
- No se realizan modificaciones sobre los Turnos.

---

## **Flujo principal**
1. El Usuario accede a la opción “Listar Turnos”.  
2. El Sistema muestra los filtros disponibles:  
   - Médico (obligatorio si no se selecciona Paciente)  
   - Paciente (obligatorio si no se selecciona Médico) 
   - Rango de fechas (FechaDesde – FechaHasta) 
   - Estado del Turno  
3. El Usuario selecciona uno o más filtros (opcional).  
4. El Usuario confirma la búsqueda.  
5. El Sistema obtiene los Turnos que cumplen con los criterios seleccionados.  
6. El Sistema muestra el listado de Turnos con la siguiente información mínima:  
   - Id del Turno  
   - Paciente  
   - Médico  
   - FechaInicio  
   - HoraInicio
   - FechaÚltimoCambioEstado  
   - Estado  
   - Diagnóstico 
7. El Usuario visualiza los resultados.

---

## **Flujos alternativos**

### A1 — El Usuario no selecciona filtros obligatorios
1. El Usuario intenta confirmar la búsqueda sin seleccionar al menos un Médico o un Paciente.
2. El Sistema informa que debe seleccionar al menos uno de estos filtros para continuar.
3. El Sistema permite corregir la selección o cancelar la operación.

---

### **A2 — No se encuentran Turnos**
1. El Sistema informa que no existen Turnos que coincidan con los criterios seleccionados.  
2. El caso de uso continúa permitiendo modificar los filtros o finalizar la consulta.

---

## **Flujos de excepción**

### **E1 — Error al obtener los datos**
1. El Sistema informa que ocurrió un error al intentar recuperar los Turnos.  
2. El caso de uso termina sin mostrar resultados.

---

## **Reglas de negocio asociadas**
- **RN-01:** El id del Turno debe ser único.  
- **RN-02:** El Sistema debe permitir filtrar Turnos por Médico, Paciente, Rango de fechas (FechaDesde – FechaHasta) y Estado.  
- **RN-03:** El Sistema debe mostrar únicamente información correspondiente a Turnos existentes.  
- **RN-04:** El Sistema no debe permitir modificar Turnos desde este caso de uso.  
- **RN-05:** El Sistema debe mostrar el estado actual del Turno según su ciclo de vida.
- **RN-06:** El Usuario debe seleccionar al menos un filtro obligatorio (Médico o Paciente) para ejecutar la búsqueda.


---

## **Requerimientos funcionales derivados**
- **RF-01:** El Sistema debe permitir acceder a la opción de Listar Turnos.  
- **RF-02:** El Sistema debe permitir aplicar filtros opcionales adicionales (Rango de fechas y Estado del Turno).  
- **RF-03:** El Sistema debe mostrar los Turnos que coincidan con los filtros seleccionados.  
- **RF-04:** El Sistema debe informar cuando no existan resultados.  
- **RF-05:** El Sistema debe informar errores en la obtención de datos.
- **RF-06:** El Sistema debe validar que el Usuario seleccione al menos un filtro obligatorio antes de ejecutar la búsqueda.

---

## **Datos involucrados**
- **Médico:** DNI, nombre, apellido.  
- **Paciente:** DNI, nombre, apellido.  
- **Turno:** Id, Paciente, Médico, Diagnóstico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), FechaInicio, HoraInicio, CantidadSesiones (10), DuraciónSesion (20 minutos)

---

-----------------------------------------------------------------------------------------------------------

## **Casos de Uso de Reportes**

## Caso de Uso: Generar reporte mensual de turnos

---

## Objetivo
Generar un reporte mensual basado exclusivamente en los Turnos creados durante un mes específico, garantizando que la información corresponda al cupo mensual de cada Médico y permitiendo analizar cuántos turnos fueron cobrados, cancelados y abandonados, discriminados por Médico.

---

## Actores
- Usuario (actor principal)
- Sistema (actor secundario)

---

## Precondiciones
- El Usuario debe tener acceso al Sistema.
- Deben existir Turnos creados en el mes seleccionado.

---

## Postcondiciones
- El Sistema genera y muestra el reporte mensual solicitado.
- No se realizan modificaciones sobre los Turnos.

---

## Flujo principal
1. El Usuario accede a la opción “Generar reporte mensual de turnos”.
2. El Sistema solicita seleccionar el mes y año del reporte.
3. El Usuario selecciona el mes y año deseados.
4. El Usuario confirma la generación del reporte.
5. El Sistema obtiene todos los Turnos creados dentro del mes seleccionado.
6. El Sistema genera el reporte con la siguiente información consolidada:
   - Cantidad total de Turnos creados en el mes
   - Cantidad de Turnos cobrados por Médico  
     (Confirmados + Finalizados + Abandonados)
   - Cantidad de Turnos cancelados por Médico
   - Cantidad de Turnos abandonados por Médico  
     (subconjunto de los cobrados)
7. El Sistema muestra el reporte al Usuario.

---

## Flujos alternativos

### A1 — No existen Turnos creados en el mes seleccionado
1. El Sistema informa que no existen Turnos creados en el mes elegido.
2. El Sistema permite seleccionar otro mes o cancelar la operación.

---

## Flujos de excepción

### E1 — Error al obtener los datos
1. El Sistema informa que ocurrió un error al intentar generar el reporte.
2. El caso de uso termina sin mostrar resultados.

---

## Reglas de negocio asociadas
- RN-01: El reporte debe incluir únicamente Turnos cuyo FechaCreación pertenezca al mes seleccionado.
- RN-02: El Sistema debe consolidar la información en tres categorías principales, discriminadas por Médico:
  - Turnos cobrados (Confirmados + Finalizados + Abandonados)
  - Turnos cancelados
  - Turnos abandonados (detalle dentro de los cobrados)
- RN-03: El Sistema debe consolidar la información por Médico.
- RN-04: El Sistema no debe modificar Turnos durante la generación del reporte.
- RN-05: El reporte debe reflejar el estado actual de cada Turno al momento de la consulta.
- RN-06: La distribución temporal debe basarse exclusivamente en la FechaCreación, para garantizar que los datos correspondan al mismo cupo mensual.

---

## Requerimientos funcionales derivados
- RF-01: El Sistema debe permitir seleccionar el mes y año del reporte.
- RF-02: El Sistema debe obtener los Turnos creados en el período seleccionado.
- RF-03: El Sistema debe consolidar la información en las categorías: cobrados, cancelados y abandonados, discriminadas por Médico.
- RF-04: El Sistema debe mostrar el reporte generado.
- RF-05: El Sistema debe informar cuando no existan datos para el período seleccionado.
- RF-06: El Sistema debe informar errores en la generación del reporte.

---

## Datos involucrados
- Médico: DNI, nombre, apellido.
- Paciente: DNI, nombre, apellido.
- Turno: Id, Paciente, Médico, Diagnóstico, ,Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), FechaCreación, FechaInicio, HoraInicio, FechaÚltimoCambioEstado, CantidadSesiones (10), DuraciónSesion (20 minutos)

---

-----------------------------------------------------------------------------------------------------------

## **Casos de Uso del Sistema (Procesos Automáticos) **

## Caso de Uso: Confirmación Automática del Turno

---

## Objetivo
Permitir que el Sistema confirme automáticamente los Turnos que cumplen las condiciones establecidas, garantizando que los turnos pendientes pasen a estado Confirmado sin intervención del Usuario.

---

## Actores
- Sistema (actor principal)

---

## Precondiciones
- El Turno debe encontrarse en estado Pendiente.
- El Turno debe tener una FechaInicio futura.
- Debe existir una regla de negocio que determine cuándo se realiza la confirmación automática.

---

## Postcondiciones
- El Turno cambia su estado a Confirmado.
- Se actualiza la FechaÚltimoCambioEstado del Turno.

---

## Flujo principal
1. El Sistema ejecuta el proceso automático de confirmación en el momento configurado (Al iniciarse la primera sesión).
2. El Sistema identifica todos los Turnos en estado Pendiente cuya FechaInicio se encuentre dentro del período definido para la confirmación automática.
3. El Sistema cambia el estado de cada Turno identificado a Confirmado.
4. El Sistema actualiza la FechaÚltimoCambioEstado del Turno.
5. El Sistema registra la operación en el log de procesos automáticos.

---

## Flujos alternativos

### A1 — No existen Turnos que cumplan las condiciones
1. El Sistema no encuentra Turnos pendientes dentro del período de confirmación.
2. El proceso finaliza sin realizar cambios.

---

## Flujos de excepción

### E1 — Error al actualizar el estado del Turno
1. El Sistema detecta un error al intentar confirmar un Turno.
2. El Sistema registra el error en el log.
3. El Sistema continúa procesando los demás Turnos.

---

## Reglas de negocio asociadas
- RN-01: Un Turno solo puede ser confirmado automáticamente si se encuentra en estado Pendiente.
- RN-02: La confirmación automática debe ejecutarse únicamente cuando la fecha y hora actuales coincidan con la FechaInicio y HoraInicio del Turno.
- RN-03: La confirmación automática no debe modificar Turnos en estado Cancelado, Finalizado o Abandonado.
- RN-04: La FechaÚltimoCambioEstado debe actualizarse cada vez que el Turno cambie de estado.

---

## Requerimientos funcionales derivados
- RF-01: El Sistema debe ejecutar automáticamente el proceso de confirmación en el momento configurado.
- RF-02: El Sistema debe identificar Turnos pendientes que cumplan las condiciones de confirmación automática.
- RF-03: El Sistema debe actualizar el estado del Turno a Confirmado.
- RF-04: El Sistema debe registrar la operación en el log.
- RF-05: El Sistema debe registrar errores sin interrumpir el proceso.

---

## Datos involucrados
- Turno: Id, Paciente, Médico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), FechaCreación, FechaInicio, HoraInicio, FechaÚltimoCambioEstado, CantidadSesiones (10), DuraciónSesion (20 minutos)

---

-----------------------------------------------------------------------------------------------------------

## Caso de Uso: Finalización Automática del Turno

---

## Objetivo
Permitir que el Sistema finalice automáticamente los Turnos cuando se hayan completado todas las sesiones programadas, considerando que las sesiones se realizan una por día hábil y siempre a la misma hora, garantizando que el estado del Turno refleje correctamente que el tratamiento fue concluido sin intervención del Usuario.

---

## Actores
- Sistema (actor principal)

---

## Precondiciones
- El Turno debe encontrarse en estado "Confirmado".
- Debe existir una FechaInicio, HoraInicio y DuraciónSesion válidas.
- Debe existir un valor válido en CantidadSesiones (10).
- Las sesiones deben realizarse una por día hábil, siempre a la misma hora.

---

## Postcondiciones
- El Turno cambia su estado a "Finalizado".
- Se actualiza la FechaÚltimoCambioEstado del Turno.

---

## Flujo principal
1. El Sistema ejecuta cada 1 minuto el proceso automático de finalización.
2. El Sistema identifica todos los Turnos en estado Confirmado cuya última sesión ya haya concluido.
   - La última sesión se determina sumando "CantidadSesiones días hábiles" a la "FechaInicio", manteniendo la misma HoraInicio.
   - La sesión se considera concluida cuando:
     - FechaÚltimaSesión + DuraciónSesion  
       es anterior o igual a la fecha y hora actuales.
3. El Sistema cambia el estado de cada Turno identificado a "Finalizado".
4. El Sistema actualiza la FechaÚltimoCambioEstado del Turno.
5. El Sistema registra la operación en el log de procesos automáticos.

---

## Flujos alternativos

### A1 — No existen Turnos que cumplan las condiciones
1. El Sistema no encuentra Turnos confirmados cuya última sesión haya concluido.
2. El proceso finaliza sin realizar cambios.

---

## Flujos de excepción

### E1 — Error al actualizar el estado del Turno
1. El Sistema detecta un error al intentar finalizar un Turno.
2. El Sistema registra el error en el log.
3. El Sistema continúa procesando los demás Turnos.

---

## Reglas de negocio asociadas
- RN-01: Un Turno solo puede ser finalizado automáticamente si se encuentra en estado "Confirmado".
- RN-02: La finalización automática debe ejecutarse únicamente cuando la fecha y hora actuales sean posteriores a la finalización de la última sesión.
- RN-03: La última sesión se determina sumando "CantidadSesiones días hábiles" a la "FechaInicio", manteniendo la misma HoraInicio.
- RN-04: La finalización automática no debe modificar Turnos en estado Cancelado, Pendiente o Abandonado.
- RN-05: La FechaÚltimoCambioEstado debe actualizarse cada vez que el Turno cambie de estado.
- RN-06: Si el Turno no fue confirmado automáticamente al inicio de la primera sesión, no puede ser finalizado automáticamente.

---

## Requerimientos funcionales derivados
- RF-01: El Sistema debe ejecutar automáticamente el proceso de finalización cada 1 minuto.
- RF-02: El Sistema debe identificar Turnos confirmados cuya última sesión haya concluido.
- RF-03: El Sistema debe actualizar el estado del Turno a Finalizado.
- RF-04: El Sistema debe registrar la operación en el log.
- RF-05: El Sistema debe registrar errores sin interrumpir el proceso.

---

## Datos involucrados
- Turno: Id, Paciente, Médico, Estado (Pendiente / Confirmado / Cancelado / Finalizado / Abandonado), FechaCreación, ,FechaInicio, HoraInicio, DuraciónSesion (20 minutos), CantidadSesiones (10),FechaÚltimoCambioEstado

---
