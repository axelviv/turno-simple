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
- Medico
- Turno
- Cupos *(entidad global que define los límites y parámetros de cupos del sistema)*


1.2 **Definir relaciones entre entidades del dominio**

- Paciente 1 → N Turnos
- Medico 1 → N Turnos
- Turno N → 1 Paciente
- Turno N → 1 Medico
- Cupos → afecta a Medico *(relación conceptual)*


1.3 **Definir invariantes del dominio**

- Invariantes de Paciente
  - Un paciente solo puede tener un turno en progreso
  - El número de documento del paciente debe ser único

- Invariantes de Medico
  - El médico solo podrá tener 60 turnos mensuales
  - Cada médico tendrá un ID único

- Invariantes de Turno
  - Un turno solo puede crearse para un médico que esté disponible
  - Un turno solo puede crearse en fechas y horarios disponibles
  - Un turno solo puede crearse para el futuro
  - Un turno pasado no puede ser eliminado de la base de datos si fue confirmado
  - Un turno no puede volver a un estado anterior una vez confirmado
  - Un turno debe tener siempre un médico asignado
  - Un turno debe tener siempre un paciente asignado
  - Un turno no puede existir sin una fecha y un horario válidos
  - Un turno no puede cambiar su paciente una vez confirmado
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
  - Registrar paciente
  - Modificar paciente
  - Consultar paciente

- Casos de Uso de Medico
  - Crear médico
  - Modificar médico
  - Eliminar médico
  - Listar médicos

- Casos de Uso de Turno
  - Crear turno
  - Modificar turno
  - Eliminar turno
  - Confirmar turno
  - Cancelar turno
  - Listar turnos

- Casos de Uso de Cupos
  - Extender cupos
  - Reiniciar cupos

- Casos de Uso de Reportes
  - Generar reporte mensual de turnos
