## README del Proyecto 📖

**Nombre:** ExamenTipo1 (Aplicación de Gestión de Países)
**Autor:** *\[Su Nombre]*
**Tecnologías:** Android (Kotlin/Java), SDK 33+, Material Design 3, MVVM, Room (opcional), Android Studio Hedgehog o superior.

### Cómo compilar y ejecutar 🔧

1. **Prerrequisitos:** Tener Android Studio **Hedgehog (2023.1)** o superior instalado, con Android SDK 33 o 34.
2. **Clonar el repositorio:** `git clone https://github.com/IARFLOW/ExamenTipo1.git` (o abrir la carpeta del proyecto proporcionado).
3. **Abrir en Android Studio:** Importar el proyecto como proyecto Gradle. Android Studio descargará las dependencias necesarias.
4. **Elegir dispositivo:** Conectar un dispositivo Android real con modo desarrollador activado, o utilizar un AVD (emulador) con al menos Android 13.
5. **Compilar y ejecutar:** Hacer click en *Run* (▶️) en Android Studio. La app se instalará en el dispositivo/emulador seleccionado.
6. **Cambiar idioma (opcional):** Para probar la localización, cambiar el idioma del dispositivo a English (Estados Unidos/Reino Unido) y volver a lanzar la app; observar que los textos y la bandera cambian.

### Funcionalidades Implementadas ✅

* **Listado de países:** Muestra una lista scrollable con los nombres de países y su idioma principal.
* **Filtrado por número:** El usuario puede filtrar cuántos países ver introduciendo un número y pulsando "Filtrar". Si deja vacío, se muestran todos (con mensaje indicativo).
* **Añadir país:** A través del menú "Añadir", se abre la pantalla de edición con campos vacíos para crear un nuevo país. Al guardar, el nuevo país aparece en la lista principal.
* **Editar país:** Pulsando sobre un país del listado, se navega a la pantalla de edición con sus datos cargados, permitiendo modificar el nombre o idioma. Al guardar, los cambios se reflejan en la lista principal.
* **Diálogo de selección de idioma:** En la pantalla de edición, el campo Idioma se establece mediante un diálogo que lista opciones (Español/Inglés). Al elegir, el campo se actualiza y la imagen de la bandera también.
* **Persistencia de datos:** Los países añadidos o editados se mantienen en la fuente de datos. *(Nota: En esta implementación de ejemplo los datos persisten en memoria mientras la app está abierta. Es fácil extenderlo a persistencia permanente usando Room; ver sección de decisiones técnicas.)*
* **Interfaz moderna:** Uso de Material3 para un diseño limpio. Inputs de texto con TextInputLayout, botones Material, etc., siguiendo guías de diseño actuales.
* **Localización al inglés/español:** Interface disponible en ambos idiomas. Por ejemplo, botones "Guardar/Cancelar" se mostrarán como "Save/Cancel" cuando el dispositivo esté en inglés. También la bandera del botón cambiará según el idioma del dispositivo (España o UK).
* **Manejo de errores de entrada:** Se valida que el nombre de país no esté vacío, se evita filtrar con valores no numéricos (tratándolos como "todos"), se controla la navegación entre actividades para evitar crasheos.
* **Feedback al usuario:** Mensajes Toast de confirmación al guardar o cancelar, y mensaje en pantalla al mostrar todos los países, para mantener al usuario informado.

### Arquitectura y Decisiones Técnicas 🏛️

* Se siguió el patrón **MVVM**:

  * Las Activities/Fragment solo se ocupan de presentar datos y recoger acciones del usuario.
  * Los **ViewModel** (ListaPaisesViewModel, EditarPaisViewModel) contienen la lógica de negocio de filtrar datos y prepararlos para la vista, interactuando con el Repositorio.
  * El **Repositorio** (ObtenerDatos) abstrae la fuente de datos. Actualmente usa una lista en memoria con métodos para consulta y actualización. Está preparado para ser reemplazado o complementado con Room fácilmente:

    * Si se integrara Room, ObtenerDatos se encargaría de llamar a `PaisDao` en un hilo de E/S (quizá usando corrutinas o ejecutors) y proveer LiveData al ViewModel.
  * Este desacoplamiento facilita mantenimiento y pruebas (por ejemplo, el ViewModel se probó unitariamente simulando el repositorio).
* **Idioma y Banderas:** Para la selección de idioma de un país, se restringió a dos opciones (Español/Inglés) para ajustarse al tiempo y demostrar la funcionalidad. En un contexto real, se podría listar más idiomas con sus respectivos códigos y banderas. El mecanismo de localización de Android se empleó para traducir la interfaz, y parcialmente para las imágenes (bandera del botón según locale), aunque la lógica de asignación de bandera según idioma seleccionado del país se hizo explícitamente para consistencia.
* **UI/UX Consideraciones:** Se usaron componentes Material por consistencia visual. Se dejó la navegación simple con startActivity/finish por claridad; para una app más compleja se podría usar el **Jetpack Navigation Component**, pero dado que solo hay dos pantallas, se optó por explícito.
* **Compatibilidad:** La app apunta a SDK recientes y usa API modernas (ViewModel, etc.) pero es retrocompatible hasta Android 5.0 (nivel 21) gracias a las libs de soporte. Material3 requiere como mínimo Android 5.0 también.
* **Rendimiento:** El número de países manejado es pequeño (ej. 10-20) en este escenario de examen, por lo que no se implementaron paginaciones ni optimizaciones especiales. RecyclerView se maneja fácilmente con esa cantidad.
* **Pruebas:** Se incluyeron pruebas básicas. Para ejecutar las pruebas unitarias: usar la opción *Run Tests* en Android Studio (ver carpeta `app/src/test`). Para las instrumentadas: conectar un dispositivo o iniciar un emulador y usar *Run Android Tests* (ver carpeta `app/src/androidTest`).
* **Limpieza de código:** Se añadieron comentarios explicativos en las secciones críticas del código para guiar a un estudiante que lo lea. Se mantuvieron nombres autoexplicativos y se evitó lógica compleja en la vista.

### Posibles mejoras 🚀

* **Persistencia completa:** Integrar Room para que los datos sobrevivan cierres de la app. La estructura está preparada; solo faltaría la implementación del DAO y llamadas asíncronas.
* **Más idiomas:** Añadir soporte para más idiomas de interfaz (francés, etc.) y también permitir más idiomas de país en el diálogo con sus banderas.
* **Eliminar país:** No se especificó, pero sería fácil añadir opción de borrar un país (por ejemplo, pulsación larga en la lista).
* **UI refinamientos:** Mejorar el diseño responsivo en landscape, agregar divisores en la lista, etc.
* **Modo oscuro:** Actualmente heredaría de Material3 oscuro si el dispositivo lo pide, pero se podrían ajustar colores.
* **Testing:** Añadir más pruebas de UI (Espresso) para flujo completo, pruebas de rotación (verificar que no se duplica fragment al rotar, etc.).
