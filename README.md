# FutBot - Documentación del Proyecto

FutBot es una plataforma web cliente/servidor que permite a los usuarios jugar a un simulador de fútbol en formato multijugador, estructurado mediante la programación de comportamientos automáticos. Este repositorio contiene toda la documentación técnica y funcional que respalda la arquitectura, el diseño y las interacciones del sistema.

Toda la documentación detallada se encuentra alojada en el directorio `/docs`. A continuación, se presenta el índice de archivos disponibles para explorar los distintos niveles del diseño:

## Índice de Documentación

* **[Alcance del Proyecto](./docs/alcance.md):** Define la visión general y los límites de FutBot. Incluye las tecnologías propuestas para el stack (FastAPI, React.js, WebSockets, PostgreSQL), los requisitos funcionales modulares (gestión de cuentas, formatos de competición, programación) y las restricciones o exclusiones funcionales del sistema.
* **[Casos de Uso](./docs/CasosDeUso.md):** Especificación completa de las interacciones entre los usuarios y la plataforma. Detalla paso a paso los escenarios principales y alternativos para la gestión de sesiones, administración de equipos, creación de ligas/amistosos y las mecánicas de transmisión en vivo.
* **[Diagrama de Clases](./docs/DiagramaDeClases.md):** Representación del diseño orientado a objetos. Modela la estructura de las entidades principales (Equipo, Jugador, Comportamiento, Liga, Alineación, Estadísticas) detallando sus relaciones, atributos y los métodos públicos o privados que encapsulan la lógica de negocio.
* **[Contrato de API](./docs/api_contrato.md):** Especificación técnica de los endpoints RESTful diseñados para la comunicación entre el cliente y el servidor. Define de manera estricta los métodos HTTP, rutas, parámetros de entorno, estructuras JSON esperadas y los códigos de estado para el manejo de éxito y errores.

