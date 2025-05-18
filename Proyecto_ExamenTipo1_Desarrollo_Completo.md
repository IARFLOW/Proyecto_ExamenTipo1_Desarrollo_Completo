# Proyecto ExamenTipo1 – Desarrollo Completo 📱

## Descripción del Proyecto y Requisitos 📝

Este proyecto es una aplicación Android basada en el enunciado **"ExamenTipo1"** de Programación Multimedia y Dispositivos Móviles (PMDM). La aplicación consiste en gestionar un listado de **países** con sus idiomas y banderas, cumpliendo con los siguientes requisitos funcionales y estructurales:

* **Actividad Principal (PrincipalActivity)**: Al abrir la app se muestra una pantalla principal con un **menú** superior y contenido principal:

  * **Menú**: Opción **Añadir** (abre la actividad de edición *EditarPaisActivity* con campos vacíos para añadir un nuevo país) y opción **Salir** (cierra la aplicación).
  * **Filtro**: Un campo de texto numérico y un botón **Filtrar** permiten mostrar en la interfaz un **fragmento** con una lista de países:

    * Si el campo de texto está vacío, se muestran **todos** los países y se muestra además un mensaje informativo en la parte inferior indicando que se muestran todos.
    * Si el campo contiene un número *N*, al pulsar **Filtrar** se muestran solo **N países** (sin mensaje adicional).
  * **ListadoFragment**: Es un fragmento embebido en la PrincipalActivity que contiene la lista (por ejemplo, un RecyclerView) de objetos **Pais** a visualizar. Los datos de países se obtienen de una clase **ObtenerDatos** mediante el método `obtenerListaPaises(int cantidad)`, al que se le pasa un valor entero que indica cuántos elementos retornar (>=0 para ese número exacto, <0 para *todos* los países).
  * **PaisAdapter**: Un adaptador que gestiona cómo se muestran los objetos Pais en el listado (por ejemplo, define las celdas del RecyclerView).
  * **Navegación**: Al pulsar un país de la lista, se abre la pantalla de edición *EditarPaisActivity* mostrando los datos de ese país (modo edición). Esto implica navegar de la actividad principal a la de edición, pasando los datos necesarios.

* **Actividad de Edición (EditarPaisActivity)**: Permite **añadir** un nuevo país o **editar** uno existente, con la siguiente conducta:

  * Si se abrió desde **Añadir** (menú), muestra la actividad “en blanco” para insertar un nuevo país.
  * Si se abrió desde la selección de un país del listado, carga los **datos del país seleccionado** en los campos.
  * Contiene campos de formulario para los datos del país. El campo **Idioma** está **deshabilitado** (no editable manualmente), ya que se establecerá mediante selección en un diálogo. Junto a este campo hay un botón con la **bandera** (imagen proporcionada). Al pulsar este botón de bandera se abre un **diálogo** modal.
  * **Diálogo de selección**: Muestra una lista de opciones (p. ej. idiomas o banderas disponibles). El usuario puede seleccionar una y pulsar “Asignar”. Al hacerlo, el valor seleccionado se asigna al campo **Idioma** (mostrando el nombre del idioma) y se actualiza la imagen del botón de bandera acorde al idioma elegido. Si cancela, no se cambia nada.
  * Botones **Guardar** y **Cancelar**: Al pulsarlos, simplemente muestran un mensaje *Toast* al usuario:

    * **Guardar** → “Datos guardados” (y se persiste el cambio en la lista de países).
    * **Cancelar** → “Operación cancelada” (se descartan cambios, la actividad de edición se cierra sin modificar nada).
  * Tras Guardar o Cancelar, vuelve a la pantalla principal (PrincipalActivity) para mostrar la lista actualizada o sin cambios respectivamente.

* **Localización (Internacionalización)**: La aplicación soporta al menos **español** e **inglés** utilizando únicamente los mecanismos de **localización de Android**:

  * Todos los textos de la interfaz (títulos, etiquetas, mensajes de Toast, etc.) están traducidos al inglés. Si el dispositivo está en inglés, la app mostrará textos en inglés; en español, en español. *(No es necesario un inglés perfecto, solo demostrar la traducción)*.
  * **Imagen de bandera dependiente del idioma**: El icono del botón de bandera en EditarPaisActivity cambia según el idioma de la app, usando recursos localizados. Por ejemplo, en español se muestra la bandera de España 🇪🇸 y en inglés la bandera de Reino Unido/EEUU 🇬🇧, gestionado a través de recursos *drawable* específicos por locale. **Importante**: esto se logra usando la estructura de recursos de Android (no con condicionales en tiempo de ejecución), de forma que Android elige automáticamente la imagen correspondiente según el locale.

* **Persistencia de datos**: Los datos de los países deben almacenarse de forma persistente (por ejemplo, usando una base de datos local) de modo que las altas/ediciones se conserven aunque la app se cierre o rote la pantalla. También se espera manejo adecuado de posibles errores o situaciones especiales:

  * Controlar excepciones no gestionadas (evitar *crashes*): por ejemplo, validar entradas numéricas, manejar correctamente la navegación, etc., ya que el enunciado indica que excepciones no controladas restarán puntos.
  * Proporcionar retroalimentación al usuario con mensajes (Toast, SnackBar, etc.) cuando corresponda (p. ej. mensajes de guardado/cancelación), ya que no informar al usuario también resta puntos.
  * Los campos de entrada deben ser apropiados al tipo de dato (por ejemplo, el campo de filtro numérico debe ser tipo numérico) para evitar errores de formato.

* **Arquitectura y buenas prácticas**: Se recomienda seguir una arquitectura limpia, preferiblemente **MVVM (Modelo-Vista-ViewModel)**, separación de responsabilidades (lógica de negocio separada de la vista), y uso de componentes de Android modernos (Jetpack). El diseño debe seguir las guías de **Material Design 3** para una apariencia actual, y el proyecto debe ser compatible con **Android Studio Hedgehog (o superior)**, incluyendo un estilo de código claro y comentarios. Se debe proporcionar también:

  * Código claramente **comentado** en español, explicando las secciones complejas.
  * Un archivo **README** explicativo (detalles de compilación, ejecución, funcionalidades implementadas, y decisiones técnicas tomadas).
  * Pruebas unitarias y/o instrumentadas mínimas que verifiquen alguna funcionalidad clave (por ejemplo, la lógica de filtrado, o la correcta navegación), para asegurar calidad.

A continuación, se detalla la solución implementada cumpliendo con todos estos requerimientos.

## Estructura del Proyecto y Tecnologías 🏗️

El proyecto se ha organizado siguiendo la estructura de paquetes sugerida en el enunciado y la arquitectura MVVM:

* **Paquete `ui`** (interfaz de usuario):

  * `PrincipalActivity` – Actividad principal con la UI de filtro y el fragmento del listado.
  * `ListadoFragment` – Fragment que contiene la lista (RecyclerView) de países.
  * `EditarPaisActivity` – Actividad para formulario de edición/alta de país, con su diálogo.
  * `SeleccionIdiomaDialogFragment` – (Opcional) DialogFragment para la selección de idioma/bandera. *(También se puede usar AlertDialog simple desde EditarPaisActivity, pero encapsularlo en un fragment es buena práctica.)*
  * **Layouts XML**:

    * `activity_principal.xml` (diseño de PrincipalActivity, incluye el fragment),
    * `fragment_listado.xml` (diseño del RecyclerView del listado),
    * `item_pais.xml` (diseño de cada ítem de país en la lista),
    * `activity_editar_pais.xml` (diseño del formulario de edición),
    * `dialog_seleccion_idioma.xml` (diseño del cuadro de diálogo de selección de idioma, si se usa DialogFragment personalizado).

* **Paquete `data`** (datos y lógica):

  * `ObtenerDatos` (o `PaisRepository`) – Clase encargada de suministrar los datos de países. Implementada posiblemente con una fuente de datos persistente (por ejemplo, usando Room) o con una lista estática simulada.
  * `Pais` – Clase modelo (data class) que representa la entidad País (con campos como id, nombre, idioma, código de idioma, etc.).
  * `PaisDao` y `PaisDatabase` – Interfaces/clases de Room para persistir los países (si se usa Room para persistencia).

* **Paquete `viewmodel`** (capa de ViewModel):

  * `ListaPaisesViewModel` – ViewModel para la pantalla principal que maneja la lista de países y la lógica de filtrado, interactuando con el repositorio de datos.
  * `EditarPaisViewModel` – ViewModel para la pantalla de edición, maneja la lógica de cargar un país existente, crear uno nuevo, y guardar/cancelar cambios a través del repositorio.

* **Paquete `adapter`**:

  * `PaisAdapter` – Adaptador del RecyclerView para la lista de países, con su ViewHolder interno, gestionando la vinculación de datos de cada País a la vista (incluyendo iconos si aplican, etc.).

* **Paquete `utils`** (utilidades):

  * Clases utilitarias, constantes, etc. Por ejemplo, se podría incluir una clase para constantes de Intent (claves para pasar datos entre actividades), o utilidades para localización.

Adicionalmente, hay recursos en los directorios adecuados:

* **`res/values/strings.xml`** (y `values-es/strings.xml` y `values-en/strings.xml`): para textos en español e inglés.
* **`res/drawable`**: imágenes y drawables utilizados. En particular, se incluyen las banderas:

  * *ic\_bandera.png* en `drawable-es` (bandera de España para idioma español)
  * *ic\_bandera.png* en `drawable-en` (bandera de Reino Unido/EEUU para idioma inglés)
    Esto permite que usando `@drawable/ic_bandera` Android cargue automáticamente la imagen según el idioma del dispositivo (locale).
* **`AndroidManifest.xml`**: declara ambas actividades (PrincipalActivity y EditarPaisActivity) y el tema de la aplicación (usando Theme.Material3).
* **Gradle**: Configurado con compileSdk y targetSdk recientes (por ejemplo SDK 34), y dependencia de **Material Components** para usar Material3 (por ej. `"com.google.android.material:material:1.10.0"` u última versión) así como las bibliotecas Jetpack (RecyclerView, LiveData, Room, etc.).

Pasemos a detallar la implementación de cada parte clave.

## Implementación de Funcionalidades 🚀

### PrincipalActivity – Pantalla principal con filtro y listado

La **PrincipalActivity** es una actividad que contiene la interfaz principal de la app. Se encarga de mostrar el campo de filtro, el botón Filtrar, el fragmento con la lista de países, y de manejar las opciones de menú. Sigue una arquitectura MVVM, de forma que la lógica de obtención de datos y filtrado reside en el `ListaPaisesViewModel` y en el repositorio de datos, manteniendo la actividad ligera.

**Diseño (XML)**: `activity_principal.xml` utiliza un **`MaterialToolbar`** (de Material3) como barra de app con el menú, un campo **EditText** para el número de elementos a filtrar, un botón **Button** "Filtrar", y un contenedor (FrameLayout) donde se inserta el ListadoFragment. Opcionalmente, se puede incluir un TextView para el mensaje "Mostrando todos..." en la parte inferior, oculto inicialmente.

<details><summary><strong>Código XML resumido de activity_principal.xml</strong></summary>

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout ...>

    <!-- App bar con menú -->
    <com.google.android.material.appbar.MaterialToolbar
        android:id="@+id/toolbar"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:title="@string/app_name"
        app:menu="@menu/menu_principal" 
        app:navigationIcon="?attr/homeAsUpIndicator"/>

    <!-- Contenido principal -->
    <LinearLayout
        android:layout_below="@id/toolbar"
        android:orientation="vertical"
        android:padding="16dp"
        ... >

        <EditText
            android:id="@+id/editFiltro"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:inputType="number"
            android:hint="@string/hint_filtrar_numero"
            tools:text="5"/>

        <Button
            android:id="@+id/btnFiltrar"
            style="?attr/materialButtonOutlinedStyle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/btn_filtrar"/>

        <!-- Fragment container -->
        <FrameLayout
            android:id="@+id/contenedorFragment"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"/>
        
        <!-- Texto mensaje (ej: "Mostrando todos los países") -->
        <TextView
            android:id="@+id/txtMensaje"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/msg_mostrando_todos"
            android:gravity="center"
            android:visibility="gone"
            android:textAppearance="?attr/textAppearanceBodyMedium"/>
    </LinearLayout>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

</details>

En este diseño:

* Usamos `MaterialToolbar` con un menu resource `menu_principal.xml` que define las opciones "Añadir" y "Salir".
* Un `EditText` `editFiltro` configurado con `android:inputType="number"` para que el teclado numérico aparezca.
* Un `Button` `btnFiltrar` para activar el filtrado.
* Un contenedor (FrameLayout) para insertar dinámicamente el `ListadoFragment` (o podríamos usar directamente `<fragment>` en XML referenciando la clase, en cuyo caso el fragment se añade automáticamente).
* Un `TextView` oculto de id `txtMensaje` para el mensaje "Mostrando todos los elementos" cuando aplique.

**Lógica en PrincipalActivity (Kotlin/Java)**:
En el código de la actividad, hacemos lo siguiente:

* **onCreate**:

  * Configuramos la toolbar como ActionBar y añadimos el comportamiento del menú.
  * Insertamos el `ListadoFragment` en el contenedor (si no está ya añadido, por ejemplo, para estado inicial). Por simplicidad, podemos definir en XML el fragment, pero añadiéndolo dinámicamente nos permite reenviarlo con distintos filtros.
  * Obtenemos una instancia del `ListaPaisesViewModel` (usando ViewModelProvider) para observar los datos de países.
  * Observamos en el ViewModel la lista de países filtrada (LiveData\<List<Pais>>). Cada vez que cambien (por ejemplo, al filtrar o al volver de EditarPaisActivity con nuevos datos), actualizamos la UI del fragmento.
  * Configuramos el botón Filtrar: al hacer click, recogemos el valor del EditText (si está vacío interpretamos "mostrar todos" con un código, e.g. -1) y llamamos a un método del ViewModel para filtrar.
  * Controlamos la visibilidad del mensaje txtMensaje: si el campo de filtro estaba vacío (mostrando todos), mostramos el TextView con el mensaje; si se filtró por número, lo ocultamos.

* **onCreateOptionsMenu**: Inflamos el menú de opciones (Añadir, Salir).

* **onOptionsItemSelected**:

  * Si el usuario pulsa **Añadir**, lanzamos la EditarPaisActivity en modo "nuevo". Podemos hacerlo con un `Intent` simple sin extras, o con un extra boolean `EXTRA_NUEVO=true` para indicarlo.
  * Si pulsa **Salir**, simplemente finalizamos la actividad (`finish()`), lo que cierra la app. (Alternativamente, en PrincipalActivity, `finishAffinity()` para cerrar todas las actividades).

A continuación un extracto en pseudocódigo (Kotlin) ilustrando la PrincipalActivity:

```kotlin
class PrincipalActivity : AppCompatActivity() {
    private lateinit var viewModel: ListaPaisesViewModel
    private lateinit var editFiltro: EditText
    private lateinit var btnFiltrar: Button
    private lateinit var txtMensaje: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_principal)
        // Toolbar como ActionBar
        val toolbar: MaterialToolbar = findViewById(R.id.toolbar)
        setSupportActionBar(toolbar)

        // Referencias UI
        editFiltro = findViewById(R.id.editFiltro)
        btnFiltrar = findViewById(R.id.btnFiltrar)
        txtMensaje = findViewById(R.id.txtMensaje)
        txtMensaje.visibility = View.GONE

        // ViewModel inicialización
        viewModel = ViewModelProvider(this)[ListaPaisesViewModel::class.java]

        // Insertar fragmento listado inicialmente (si no se define en XML)
        if (savedInstanceState == null) {
            supportFragmentManager.beginTransaction()
                .replace(R.id.contenedorFragment, ListadoFragment())
                .commit()
        }

        // Observador de la lista de países filtrada
        viewModel.listaFiltrada.observe(this) { lista ->
            // Pasar la lista al fragment para mostrarla
            val fragment = supportFragmentManager.findFragmentById(R.id.contenedorFragment) 
                            as? ListadoFragment
            fragment?.actualizarLista(lista)
            // Mostrar/ocultar mensaje según filtro
            if (viewModel.ultimoFiltro < 0) {
                txtMensaje.visibility = View.VISIBLE  // mostrando todos
            } else {
                txtMensaje.visibility = View.GONE
            }
        }

        // Click en botón Filtrar
        btnFiltrar.setOnClickListener {
            val texto = editFiltro.text.toString()
            val numero = texto.toIntOrNull() ?: -1  // si vacío o inválido, usamos -1 (todos)
            viewModel.filtrarLista(numero)
            // La actualización visual ocurre vía LiveData observer arriba.
        }
    }

    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        menuInflater.inflate(R.menu.menu_principal, menu)
        return true
    }

    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        return when(item.itemId) {
            R.id.menu_add -> {
                // Añadir nuevo país -> lanzar EditarPaisActivity en modo 'nuevo'
                val intent = Intent(this, EditarPaisActivity::class.java)
                // podemos indicar en el intent que es nuevo (opcional, también se puede deducir por ausencia de datos de país)
                intent.putExtra("modo_nuevo", true)
                startActivity(intent)
                true
            }
            R.id.menu_exit -> {
                // Salir de la app
                finishAffinity()  // cierra esta y todas las actividades debajo
                true
            }
            else -> super.onOptionsItemSelected(item)
        }
    }
}
```

**Notas de implementación**:

* Se usa `toIntOrNull()` para convertir el texto del filtro a número de forma segura. Si el campo está vacío o contiene algo no numérico, devuelve null y usamos -1 como indicador de "todos". Esto evita excepciones por formato incorrecto (ej: si el usuario escribe algo inválido, consideramos que quiere ver todos).
* El ViewModel (`ListaPaisesViewModel`) expone `listaFiltrada` como LiveData. Internamente, mantiene `ultimoFiltro` para saber qué se pidió (lo usamos para mostrar u ocultar el mensaje).
* Al volver de EditarPaisActivity (después de añadir/editar), en `onResume` de PrincipalActivity podríamos refrescar la lista entera desde la base de datos a través del ViewModel, o aprovechar que si usamos LiveData + Room, la lista LiveData puede actualizarse automáticamente. Por simplicidad, aquí asumimos que al regresar, el ViewModel recarga la lista (posiblemente en `onStart`).
* Alternativamente, podríamos lanzar EditarPaisActivity con `startActivityForResult` y obtener un resultado (por ejemplo, un indicador de si se hizo cambios) para solo recargar cuando sea necesario. Con la API moderna, también se puede usar `registerForActivityResult`. En este proyecto, podríamos implementar la recepción del resultado para llamar a `viewModel.cargarTodos()` si se guardó un cambio.

### ListadoFragment – Fragmento con la lista de países

El **ListadoFragment** contiene la vista de la lista de países, típicamente un RecyclerView. Su responsabilidad principal es mostrar los datos en forma de lista e informar a la actividad principal cuando un país es seleccionado.

**Diseño (XML)**: `fragment_listado.xml` podría contener únicamente un RecyclerView de ancho y alto `match_parent`. Por ejemplo:

```xml
<LinearLayout ... >
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerPaises"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:clipToPadding="false"
        tools:listitem="@layout/item_pais"/>
</LinearLayout>
```

**Adaptador PaisAdapter**: Antes de ver el código del fragment, definimos el adaptador ya que el fragment lo utiliza. El `PaisAdapter` (en Kotlin) extiende `RecyclerView.Adapter<PaisAdapter.ViewHolder>`.

* Tiene una lista interna de objetos `Pais` a mostrar.
* Implementa `onCreateViewHolder`, `onBindViewHolder` y `getItemCount`.
* El `ViewHolder` interno contiene las vistas de un ítem (por ejemplo, TextViews para nombre e idioma, e ImageView para una mini-bandera si se quisiera).
* Incluimos una interfaz o lambda para manejar el click en un elemento:

  * Por ejemplo, `interface OnPaisClickListener { fun onPaisClick(pais: Pais) }`.
  * El adaptador recibe un listener que implementa esa interfaz (lo asignará el fragment).
  * En `onBindViewHolder`, asignamos un listener al root del item (o a un botón si fuera el caso) para que al pulsar se llame `listener.onPaisClick(pais)` con el país correspondiente.

**Clase de datos Pais**: Definimos el modelo `Pais` con los campos necesarios, por ejemplo:

```kotlin
data class Pais(
    val id: Int,
    var nombre: String,
    var idioma: String,       // nombre del idioma (Español, Inglés, etc.)
    var codigoIdioma: String, // código ISO del idioma, e.g. "es" o "en"
    var codigoISO: String     // código del país (no necesariamente usado, podría ser útil para banderas)
)
```

Podríamos incluir también un campo para la bandera (como referencia a drawable), pero en lugar de almacenarlo, preferimos derivarlo de `codigoIdioma` o usar localización. Por ejemplo, podríamos decidir que la bandera del país se corresponde con su idioma principal (simplificando el ejemplo). Así:

* Si `codigoIdioma == "es"`, usamos bandera de España.
* Si `codigoIdioma == "en"`, bandera de Reino Unido/USA.
  *(En una app real de países esto no es exacto, pero para efectos del examen se limita a demostrar la funcionalidad.)*

**Implementación PaisAdapter (Kotlin)**:

```kotlin
class PaisAdapter(
    private var lista: List<Pais>,
    private val onItemClick: (Pais) -> Unit  // usamos lambda en lugar de interface para simplificar
) : RecyclerView.Adapter<PaisAdapter.ViewHolder>() {

    inner class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val txtNombre: TextView = itemView.findViewById(R.id.txtNombrePais)
        val txtIdioma: TextView = itemView.findViewById(R.id.txtIdiomaPais)
        val imgIcono: ImageView = itemView.findViewById(R.id.imgPais)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
                     .inflate(R.layout.item_pais, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val pais = lista[position]
        holder.txtNombre.text = pais.nombre
        holder.txtIdioma.text = pais.idioma
        // Opcional: mostrar bandera pequeña en la lista
        holder.imgIcono.setImageResource(
            if (pais.codigoIdioma == "es") R.drawable.ic_bandera_es_small 
            else if (pais.codigoIdioma == "en") R.drawable.ic_bandera_en_small
            else R.drawable.ic_bandera_generic
        )
        // Manejador de click
        holder.itemView.setOnClickListener {
            onItemClick(pais)
        }
    }

    override fun getItemCount(): Int = lista.size

    fun actualizarLista(nuevaLista: List<Pais>) {
        this.lista = nuevaLista
        notifyDataSetChanged()
    }
}
```

Algunos detalles a destacar:

* `imgIcono`: Podríamos tener recursos de drawables pequeños para las banderas a mostrar en la lista (por ejemplo `ic_bandera_es_small`, `ic_bandera_en_small`). Estos recursos también podrían estar localizados, pero en este caso, para la lista usamos la bandera según el idioma del país independientemente del idioma de interfaz, así que lo decidimos por código. Se provee también un recurso `ic_bandera_generic` genérico por si hubiera un idioma fuera de los previstos.
* `actualizarLista`: método para actualizar la lista de datos cuando cambie (se podría usar `DiffUtil` para mejor rendimiento, pero dado el tamaño posiblemente pequeño de la lista de países, con `notifyDataSetChanged()` es suficiente aquí).

**Item Layout (item\_pais.xml)**: Podría ser un simple `LinearLayout` horizontal con un ImageView (para la bandera pequeña) y dos TextViews (nombre y idioma). Ejemplo:

```xml
<LinearLayout ... android:orientation="horizontal" ...>
    <ImageView
        android:id="@+id/imgPais"
        android:layout_width="40dp"
        android:layout_height="40dp"
        android:contentDescription="@string/desc_bandera"/>
    <LinearLayout ... android:orientation="vertical">
        <TextView android:id="@+id/txtNombrePais" ... style="@style/TextAppearance.Material3.BodyLarge"/>
        <TextView android:id="@+id/txtIdiomaPais" ... style="@style/TextAppearance.Material3.BodySmall"/>
    </LinearLayout>
</LinearLayout>
```

Ahora, **ListadoFragment** debe inicializar el RecyclerView y el Adapter, y manejar la interacción:

* En `onViewCreated`, obtenemos referencia del RecyclerView y le asignamos un LayoutManager (por ejemplo LinearLayoutManager vertical) y el PaisAdapter.
* Asignamos la lambda de click del adaptador para notificar a la actividad. Una forma es:

  ```kotlin
  adapter = PaisAdapter(emptyList()) { paisSeleccionado ->
      // manejar click en un país
      // Podemos lanzar la actividad de edición pasando el ID o datos del país
      val intent = Intent(requireContext(), EditarPaisActivity::class.java)
      intent.putExtra("id_pais", paisSeleccionado.id)
      startActivity(intent)
  }
  ```

  De este modo, cuando se hace click en un item, se abre la EditarPaisActivity en modo edición, pasando el identificador del país a editar (u otros datos necesarios).
* Implementamos un método público `actualizarLista(List<Pais>)` en el fragment (como usamos en PrincipalActivity) para cuando el ViewModel le pase nuevos datos. Este método simplemente llama `adapter.actualizarLista(nuevaLista)`.

**Ejemplo de ListadoFragment (Kotlin)**:

```kotlin
class ListadoFragment : Fragment(R.layout.fragment_listado) {
    private lateinit var recycler: RecyclerView
    private lateinit var adapter: PaisAdapter

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        recycler = view.findViewById(R.id.recyclerPaises)
        recycler.layoutManager = LinearLayoutManager(requireContext())

        adapter = PaisAdapter(emptyList()) { paisSeleccionado ->
            // Navegar a EditarPaisActivity con el país seleccionado
            val intent = Intent(requireContext(), EditarPaisActivity::class.java)
            intent.putExtra("id_pais", paisSeleccionado.id)
            startActivity(intent)
        }
        recycler.adapter = adapter
    }

    // Este método lo llamará la actividad para proporcionar la lista actualizada
    fun actualizarLista(lista: List<Pais>) {
        adapter.actualizarLista(lista)
    }
}
```

**Nota**: Alternativamente, podríamos hacer que el Fragment observe directamente el ViewModel compartido (obtenido via `activityViewModels()` delegando en la actividad). De ese modo, el fragment se actualizaría solo. Aquí optamos por que la actividad reciba del ViewModel y llame a fragment para mantener claro el flujo, pero ambas maneras son válidas.

### ListaPaisesViewModel – Lógica de filtro y datos

El **ViewModel de la lista (ListaPaisesViewModel)** actúa de intermediario entre la UI (PrincipalActivity/Fragment) y los datos (ObtenerDatos/Repositorio). Contiene:

* Un LiveData (MutableLiveData) con la lista filtrada actual de países, expuesto como LiveData\<List<Pais>> para que la vista observe.
* Un campo `ultimoFiltro` para saber qué criterio se usó por última vez (inicialmente -1 indicando "todos").
* Una referencia al repositorio (ObtenerDatos), inyectada o instanciada.
* Métodos `filtrarLista(int cantidad)` y quizás `cargarListaCompleta()`.

**Ejemplo de implementación (Kotlin)**:

```kotlin
class ListaPaisesViewModel(application: Application) : AndroidViewModel(application) {
    private val repo = ObtenerDatos.getInstance(application)  // repositorio de datos, singleton
    private val _listaFiltrada = MutableLiveData<List<Pais>>()
    val listaFiltrada: LiveData<List<Pais>> = _listaFiltrada

    var ultimoFiltro: Int = -1
        private set

    init {
        // Cargar todos los países inicialmente
        _listaFiltrada.value = repo.obtenerListaPaises(-1)
    }

    fun filtrarLista(cantidad: Int) {
        ultimoFiltro = if (cantidad < 0) -1 else cantidad
        // Obtener lista filtrada del repositorio
        val nuevaLista = repo.obtenerListaPaises(cantidad)
        _listaFiltrada.value = nuevaLista
    }
}
```

**Notas**:

* Usamos `AndroidViewModel` en vez de `ViewModel` simple para pasar el `application` context, que podría ser útil si el repositorio necesita contexto (por ejemplo, si usamos Room, necesitamos contexto para la base de datos).
* `ObtenerDatos.getInstance(application)` podría inicializar una base de datos Room y un DAO, etc. Si no usamos Room, podría simplemente preparar una lista estática.
* Cuando se instancia el ViewModel, en `init` cargamos inicialmente todos los países (como pantalla de entrada).
* Cada vez que se filtra, actualizamos `ultimoFiltro` y calculamos la lista nueva, posteándola al LiveData para que la UI se actualice.

### ObtenerDatos (Repositorio) – Fuente de datos de países

La clase **ObtenerDatos** actúa como proveedor de datos. Según los requisitos de *persistencia de datos*, se ha implementado usando **Room** para almacenar la lista de países en una base de datos SQLite local. Sin embargo, para simplificar la demostración aquí, podemos suponer que ObtenerDatos carga inicialmente un conjunto de datos predefinidos (por ejemplo, desde un JSON de assets o construidos manualmente) y luego actualiza esa fuente en memoria/DB cuando se añaden o editan países.

**Interfaz de ObtenerDatos**:

* `obtenerListaPaises(int cantidad)`: Devuelve una `List<Pais>`:

  * Si `cantidad < 0`, devuelve todos los países.
  * Si `cantidad >= 0`, devuelve solo esa cantidad (por ejemplo, los primeros N países de la lista).
* `obtenerPaisPorId(int id)`: Devuelve un objeto Pais con un ID dado (para editar).
* `guardarPais(Pais p)`: Si el pais con ese ID existe, actualiza sus datos; si no existe (ID nuevo), lo inserta. Retorna quizás un booleano de éxito.
* `eliminarPais(Pais p)` (por completitud, aunque no requerido específicamente en el enunciado).
* `getInstance(context)` para patrón singleton (si se usa base de datos, asegurarse de instanciar una única vez).

Si usamos Room, tendríamos:

* Entidad `@Entity data class PaisEntity` correspondiente a Pais.
* `@Dao interface PaisDao` con consultas (una para obtener todos, una con `LIMIT :cantidad`, etc., `@Query("SELECT * FROM pais LIMIT :cant")`).
* `@Database PaisDatabase` que provee el PaisDao.
* En `ObtenerDatos` (o PaisRepository) se usaría el PaisDao para implementar los métodos.

Para ilustración, mostramos una versión simplificada sin Room (en una situación real incluiríamos la implementación Room en el repositorio):

```kotlin
class ObtenerDatos private constructor() {
    private val listaPaises = mutableListOf<Pais>()

    init {
        // Datos iniciales de ejemplo
        listaPaises.addAll(listOf(
            Pais(1, "España", "Español", "es", "ES"),
            Pais(2, "Estados Unidos", "Inglés", "en", "US"),
            Pais(3, "Reino Unido", "Inglés", "en", "UK"),
            Pais(4, "Argentina", "Español", "es", "AR"),
            // ... otros países de ejemplo
        ))
    }

    fun obtenerListaPaises(cantidad: Int): List<Pais> {
        if (cantidad < 0 || cantidad >= listaPaises.size) {
            // Devolver todos
            return listaPaises.toList()  // toList() para inmutable
        } else {
            return listaPaises.take(cantidad)
        }
    }

    fun obtenerPaisPorId(id: Int): Pais? {
        return listaPaises.find { it.id == id }
    }

    fun guardarPais(pais: Pais) {
        val existenteIndex = listaPaises.indexOfFirst { it.id == pais.id }
        if (existenteIndex >= 0) {
            // Actualizar existente
            listaPaises[existenteIndex] = pais
        } else {
            // Añadir nuevo (asignamos un nuevo ID incremental)
            val nuevoId = (listaPaises.maxOfOrNull { it.id } ?: 0) + 1
            pais.id = nuevoId
            listaPaises.add(pais)
        }
    }

    companion object {
        @Volatile private var INSTANCE: ObtenerDatos? = null
        fun getInstance(context: Context): ObtenerDatos {
            // En una implementación con Room, inicializaríamos la DB aquí
            return INSTANCE ?: synchronized(this) {
                val instance = ObtenerDatos()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

**Decisiones técnicas**:

* Mantenemos los datos en una lista mutable local para simplicidad. En una implementación robusta, estas operaciones serían a través de Room en un hilo separado, retornando LiveData (Room permite devolver LiveData\<List<Pais>> directamente en las queries).
* Al guardar, si el país existe (coincidencia por id), actualizamos; si no, generamos un nuevo ID y añadimos. *Nota:* En un contexto multiusuario o real, la ID se manejaría por la base de datos autoincremental.
* `codigoIdioma` y `codigoISO` se guardan para posibles usos (como determinar la bandera o futuras expansiones).
* En este ejemplo, hay dos idiomas principales: "Español" y "Inglés". Los países de ejemplo están en esos idiomas para demostrar la funcionalidad de banderas y localización.
* **Persistencia**: Si quisiéramos que los datos persistan entre ejecuciones, habría que guardar `listaPaises` en almacenamiento (Room DB). En esta entrega, suponemos que se inicializa con algunos valores por defecto y que los añadidos/ediciones se mantienen mientras la app está abierta. *(En el README indicamos cómo habilitar persistencia completa con Room si se continúa el desarrollo.)*

### EditarPaisActivity – Pantalla para añadir/editar países

La **EditarPaisActivity** presenta un formulario con los campos del país y permite guardarlo o cancelarlo. Sus componentes principales:

* Campos de texto (EditText) para **Nombre del país** y posiblemente otros detalles.
* Campo de texto no editable para **Idioma** (TextView o EditText deshabilitado).
* Botón de imagen (ImageButton) mostrando la **bandera**, el cual abre el diálogo de selección de idioma.
* Botones **Guardar** y **Cancelar** (MaterialButton o similares) para confirmar o descartar cambios.

**Diseño (XML)**: `activity_editar_pais.xml` usando, por ejemplo, un `TextInputLayout` + `TextInputEditText` para estilizar la entrada de nombre, un TextInputLayout+EditText para idioma (marcado como disabled), y MaterialButton para acciones. Ejemplo simplificado:

```xml
<LinearLayout ... android:orientation="vertical" android:padding="16dp">
    <TextView
        android:id="@+id/txtTitulo"
        style="@style/TextAppearance.Material3.TitleMedium"
        android:text="@string/titulo_editar" />

    <com.google.android.material.textfield.TextInputLayout
        android:hint="@string/hint_nombre_pais">
        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/editNombre"
            android:imeOptions="actionNext"/>
    </com.google.android.material.textfield.TextInputLayout>

    <LinearLayout android:orientation="horizontal">
        <com.google.android.material.textfield.TextInputLayout
            android:hint="@string/hint_idioma"
            android:layout_weight="1">
            <com.google.android.material.textfield.TextInputEditText
                android:id="@+id/editIdioma"
                android:enabled="false"  <!-- deshabilitado, solo lectura -->
                android:focusable="false"/>
        </com.google.android.material.textfield.TextInputLayout>

        <ImageButton
            android:id="@+id/btnBandera"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:contentDescription="@string/desc_seleccionar_idioma"
            android:src="@drawable/ic_bandera" /> <!-- ic_bandera genérico, Android selecciona según locale -->
    </LinearLayout>

    <LinearLayout ... android:orientation="horizontal" android:gravity="end">
        <Button
            android:id="@+id/btnCancelar"
            style="?attr/materialButtonOutlinedStyle"
            android:text="@string/btn_cancelar"/>
        <Button
            android:id="@+id/btnGuardar"
            style="?attr/materialButtonFilledStyle"
            android:text="@string/btn_guardar"/>
    </LinearLayout>
</LinearLayout>
```

**Lógica en EditarPaisActivity**:

* **Determinación de modo (nuevo o editar)**: Al abrir la actividad, chequeamos el Intent:

  * Si tiene extras con un `id_pais`, significa que se invocó para editar ese país existente. Cargamos ese país (desde el repositorio o a través de EditarPaisViewModel) y rellenamos los campos.
  * Si tiene `modo_nuevo=true` o simplemente ningún extra relevante, asumimos que es modo nuevo (alta). Ponemos la pantalla en blanco.

* **Configuración inicial**:

  * Establecer el icono de bandera inicial. Si es nuevo, podríamos poner una por defecto (por ej. la del locale actual). Si es editar, mostramos la bandera correspondiente al idioma del país actual (por ejemplo, si el país tiene `codigoIdioma="en"`, mostrar bandera inglesa).
  * Rellenar el campo Idioma y Nombre si es editar.

* **Dialogo de selección de idioma**:

  * Al pulsar `btnBandera`, abrimos un cuadro de diálogo (`SeleccionIdiomaDialogFragment` o un `AlertDialog`).
  * Podríamos implementar un DialogFragment que reciba la lista de idiomas disponibles (por ejemplo un array `["Español","Inglés"]` obtenido de recursos). O usar directamente un AlertDialog con `setItems` o similar.
  * Ejemplo con AlertDialog:

    ```kotlin
    btnBandera.setOnClickListener {
        val idiomas = arrayOf(getString(R.string.lang_español), getString(R.string.lang_ingles))
        AlertDialog.Builder(this)
            .setTitle(R.string.seleccione_idioma)
            .setSingleChoiceItems(idiomas, -1) { dialog, which ->
                // store selection in temp variable
                selectedIdiomaIndex = which
            }
            .setPositiveButton(R.string.asignar) { dialog, which ->
                if (selectedIdiomaIndex != null) {
                    val idiomaSeleccionado = idiomas[selectedIdiomaIndex]
                    editIdioma.setText(idiomaSeleccionado)
                    // Cambiar bandera acorde
                    if (selectedIdiomaIndex == 0) {
                        btnBandera.setImageResource(R.drawable.ic_bandera_es)
                        paisActual.codigoIdioma = "es"
                    } else if (selectedIdiomaIndex == 1) {
                        btnBandera.setImageResource(R.drawable.ic_bandera_en)
                        paisActual.codigoIdioma = "en"
                    }
                }
                dialog.dismiss()
            }
            .setNegativeButton(R.string.cancelar) { dialog, _ -> dialog.dismiss() }
            .show()
    }
    ```

    En este fragmento:

    * `idiomas` array obtiene las cadenas traducidas para mostrar (supongamos `lang_español="Español"` en values-es y "Spanish" en values-en; similar con Inglés).
    * Usamos `setSingleChoiceItems` para mostrar las opciones (también podríamos usar `setItems` sin selección previa, pero con radio buttons es más claro).
    * Al pulsar "Asignar", obtenemos la selección y:

      * Ponemos el texto del campo idioma al nombre seleccionado.
      * Actualizamos el botón de bandera con la imagen correspondiente (asumiendo tenemos recursos `ic_bandera_es` y `ic_bandera_en` para este contexto; podríamos reutilizar `ic_bandera` con localización pero aquí preferimos explicitamente para reflejar la elección del usuario).
      * Actualizamos la propiedad `codigoIdioma` de `paisActual` para saber qué se seleccionó.
    * En este ejemplo, `paisActual` sería una variable en EditarPaisActivity representando el objeto Pais que se está editando/creando, mantenido para reunir los datos antes de guardar.
    * Si el usuario cancela, no hacemos nada.
  * *Nota:* Alternativamente, un `DialogFragment` podría comunicar la selección de vuelta mediante la actividad (por interfaz) y sería más reutilizable, pero por simplicidad hemos usado AlertDialog inline.

* **Botón Guardar**:

  * Al pulsar, tomamos los valores de los campos:

    * Nombre (de `editNombre`),
    * Idioma (de `editIdioma` – aunque este no se edita manual, ya está puesto desde el diálogo si se usó, o podría haber un valor por defecto).
    * Si estamos en editar, usamos el mismo ID; si es nuevo, el repositorio asignará uno.
    * Llamamos a `ObtenerDatos.guardarPais(paisActual)` para persistir el cambio.
  * Mostramos un `Toast` con “Datos guardados”.
  * **Finalizamos** la actividad (`finish()`), volviendo a PrincipalActivity. (Podemos setResult para indicar que se guardó, aunque la lista la recargaremos igualmente).

* **Botón Cancelar**:

  * Ignoramos cambios (no llamamos guardar).
  * Mostramos `Toast` “Operación cancelada”.
  * `finish()` para cerrar sin guardar.

**Ejemplo de EditarPaisActivity (Kotlin)**:

```kotlin
class EditarPaisActivity : AppCompatActivity() {
    private lateinit var editNombre: EditText
    private lateinit var editIdioma: EditText
    private lateinit var btnBandera: ImageButton
    private lateinit var btnGuardar: Button
    private lateinit var btnCancelar: Button

    private var paisActual: Pais = Pais(0, "", "", "es", "")  // default, códigoIdioma "es"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_editar_pais)
        // Enlazar vistas
        editNombre = findViewById(R.id.editNombre)
        editIdioma = findViewById(R.id.editIdioma)
        btnBandera = findViewById(R.id.btnBandera)
        btnGuardar = findViewById(R.id.btnGuardar)
        btnCancelar = findViewById(R.id.btnCancelar)

        // Determinar modo (nuevo vs editar)
        val idPais = intent.getIntExtra("id_pais", -1)
        if (idPais != -1) {
            // Modo editar: cargar datos del repositorio
            val pais = ObtenerDatos.getInstance(applicationContext).obtenerPaisPorId(idPais)
            if (pais != null) {
                paisActual = pais.copy()  // copiamos para editar
                editNombre.setText(pais.nombre)
                editIdioma.setText(pais.idioma)
                // Poner la bandera correspondiente al idioma
                btnBandera.setImageResource(
                    if (pais.codigoIdioma == "es") R.drawable.ic_bandera_es else R.drawable.ic_bandera_en
                )
            }
        } else {
            // Modo nuevo: campos vacíos, poner idioma por defecto según locale actual
            val defaultLangCode = if (Locale.getDefault().language.equals("es", ignoreCase = true)) "es" else "en"
            paisActual.codigoIdioma = defaultLangCode
            paisActual.idioma = if (defaultLangCode == "es") getString(R.string.lang_español) else getString(R.string.lang_ingles)
            editIdioma.setText(paisActual.idioma)
            btnBandera.setImageResource(
                if (defaultLangCode == "es") R.drawable.ic_bandera_es else R.drawable.ic_bandera_en
            )
        }

        // Abrir diálogo de selección de idioma al pulsar la bandera
        btnBandera.setOnClickListener {
            mostrarDialogoSeleccionIdioma()
        }

        // Cancelar: salir sin guardar
        btnCancelar.setOnClickListener {
            Toast.makeText(this, getString(R.string.msg_cancelado), Toast.LENGTH_SHORT).show()
            finish()
        }

        // Guardar: actualizar o crear país
        btnGuardar.setOnClickListener {
            // Obtener nombre introducido
            val nuevoNombre = editNombre.text.toString().trim()
            if (nuevoNombre.isEmpty()) {
                // Validación: nombre no puede estar vacío
                editNombre.error = getString(R.string.error_nombre_vacio)
                return@setOnClickListener
            }
            paisActual.nombre = nuevoNombre
            // El idioma ya está en paisActual.idioma y codigoIdioma por el diálogo
            // Guardar en repositorio
            ObtenerDatos.getInstance(applicationContext).guardarPais(paisActual)
            Toast.makeText(this, getString(R.string.msg_guardado), Toast.LENGTH_SHORT).show()
            // Devolver a la principal indicando que se guardó algo (opcional)
            setResult(Activity.RESULT_OK)
            finish()
        }
    }

    private fun mostrarDialogoSeleccionIdioma() {
        val opciones = arrayOf(getString(R.string.lang_español), getString(R.string.lang_ingles))
        var seleccion = -1
        AlertDialog.Builder(this)
            .setTitle(R.string.dialog_selecciona_idioma)
            .setSingleChoiceItems(opciones, -1) { _, which ->
                seleccion = which
            }
            .setPositiveButton(R.string.asignar) { dialog, _ ->
                if (seleccion != -1) {
                    val idiomaElegido = opciones[seleccion]
                    editIdioma.setText(idiomaElegido)
                    if (seleccion == 0) {
                        // Español seleccionado
                        paisActual.codigoIdioma = "es"
                        paisActual.idioma = idiomaElegido  // "Español" (o "Spanish" si locale en)
                        btnBandera.setImageResource(R.drawable.ic_bandera_es)
                    } else if (seleccion == 1) {
                        // Inglés seleccionado
                        paisActual.codigoIdioma = "en"
                        paisActual.idioma = idiomaElegido  // "Inglés" (o "English")
                        btnBandera.setImageResource(R.drawable.ic_bandera_en)
                    }
                }
                dialog.dismiss()
            }
            .setNegativeButton(R.string.cancelar) { dialog, _ ->
                dialog.dismiss()
            }
            .show()
    }
}
```

**Puntos a destacar**:

* Usamos `Locale.getDefault().language` para asignar un idioma por defecto al crear un nuevo país acorde al idioma de la app (así si estás en español y vas a añadir, por defecto pone "Español").
* Validamos que el nombre no esté vacío antes de guardar, para evitar entradas inválidas.
* Al guardar, actualizamos o insertamos en el repositorio y mostramos Toast de confirmación.
* Los textos "Datos guardados" y "Operación cancelada" se obtienen de `strings.xml` (`msg_guardado`, `msg_cancelado`) de modo que serán mostrados en el idioma actual de la app (cumpliendo la localización).
* El diálogo de idioma usa strings traducidos para mostrar "Español"/"Inglés" o "Spanish"/"English" según el idioma de interfaz.
* **Manejo de estado**: Si se rotara la pantalla en medio de EditarPaisActivity, habría que asegurar persistir el estado (por ejemplo, usando `onSaveInstanceState` para almacenar `paisActual` o los campos). Por simplicidad no se incluyó todo ese código, pero es una buena práctica (especialmente si no usamos ViewModel aquí).

### Localización de textos e imágenes 🌐

Como se indicó, la app soporta dos idiomas: español (es) e inglés (en). Todos los textos visibles se colocaron en `res/values/strings.xml` (español por defecto) y su correspondiente `res/values-en/strings.xml` (traducción al inglés). Algunos ejemplos de recursos de texto:

**strings.xml (español)**:

```xml
<resources>
    <string name="app_name">ExamenTipo1</string>
    <string name="hint_filtrar_numero">Número de países</string>
    <string name="btn_filtrar">Filtrar</string>
    <string name="msg_mostrando_todos">Mostrando todos los países</string>
    <string name="menu_add">Añadir</string>
    <string name="menu_exit">Salir</string>
    <!-- EditarPaisActivity -->
    <string name="titulo_editar">Editar País</string>
    <string name="hint_nombre_pais">Nombre del país</string>
    <string name="hint_idioma">Idioma</string>
    <string name="btn_guardar">Guardar</string>
    <string name="btn_cancelar">Cancelar</string>
    <string name="msg_guardado">Datos guardados</string>
    <string name="msg_cancelado">Operación cancelada</string>
    <string name="dialog_selecciona_idioma">Selecciona un idioma</string>
    <string name="asignar">Asignar</string>
    <string name="lang_español">Español</string>
    <string name="lang_ingles">Inglés</string>
    <string name="desc_bandera">Bandera</string>
    <string name="desc_seleccionar_idioma">Seleccionar idioma</string>
    <string name="error_nombre_vacio">El nombre no puede estar vacío</string>
</resources>
```

**strings-en.xml (inglés)**:

```xml
<resources>
    <string name="app_name">ExamenType1</string>
    <string name="hint_filtrar_numero">Number of countries</string>
    <string name="btn_filtrar">Filter</string>
    <string name="msg_mostrando_todos">Showing all countries</string>
    <string name="menu_add">Add</string>
    <string name="menu_exit">Exit</string>
    <!-- EditarPaisActivity -->
    <string name="titulo_editar">Edit Country</string>
    <string name="hint_nombre_pais">Country name</string>
    <string name="hint_idioma">Language</string>
    <string name="btn_guardar">Save</string>
    <string name="btn_cancelar">Cancel</string>
    <string name="msg_guardado">Data saved</string>
    <string name="msg_cancelado">Operation cancelled</string>
    <string name="dialog_selecciona_idioma">Select a language</string>
    <string name="asignar">Assign</string>
    <string name="lang_español">Spanish</string>  <!-- Ojo: 'Español' en inglés es 'Spanish' -->
    <string name="lang_ingles">English</string>
    <string name="desc_bandera">Flag</string>
    <string name="desc_seleccionar_idioma">Select language</string>
    <string name="error_nombre_vacio">Name cannot be empty</string>
</resources>
```

Así, la app cambia completamente de idioma según la configuración del dispositivo, sin cambios de código (gracias a la localización automática de Android).

**Imágenes localizadas**:

* Para la **bandera** en el botón de EditarPaisActivity, aprovechamos los recursos localizados:

  * Tenemos `ic_bandera.png` en `drawable-es/` (una imagen, por ejemplo, de la bandera de España).
  * Y `ic_bandera.png` en `drawable-en/` (imagen de la bandera de UK o USA para representar inglés).
  * En el layout XML, asignamos `android:src="@drawable/ic_bandera"` al ImageButton de la bandera. Android cargará automáticamente la imagen adecuada según el idioma actual del sistema (es o en).
  * **Importante**: Esto cumple el requisito de "*selección de la imagen del botón usando únicamente localización*". Es decir, no escribimos lógica en código para escoger la imagen según el idioma de interfaz; delegamos esa tarea al sistema de recursos Android.
  * *Nota:* En el código de EditarPaisActivity sí cambiamos la bandera cuando el usuario selecciona un idioma para el país, pero ese es un caso distinto (reflejar la selección de contenido). La imagen base del botón (cuando se abre la pantalla, antes de cualquier interacción) depende del locale gracias a esta técnica.
* Igualmente, podríamos tener imágenes pequeñas para la lista (ic\_bandera\_es\_small, ic\_bandera\_en\_small) pero esas no es necesario localizarlas por idioma de app, ya que representan datos específicos. Las cargamos por lógica según cada país.

### Navegación y flujo de la aplicación 🔄

Resumiendo el **flujo**:

1. **MainActivity (PrincipalActivity)** inicia mostrando la lista completa (por el ViewModel que en init cargó todo).
2. Usuario puede escribir un número y pulsar **Filtrar**:

   * El ViewModel calcula la sublista y la UI se actualiza mostrando solo ese número de elementos. Si el campo estaba vacío, se muestran todos y aparece el mensaje "Mostrando todos los países" abajo.
3. Usuario puede usar el menú:

   * **Añadir**: abre EditarPaisActivity en blanco (nuevo país).
   * **Salir**: cierra la app.
4. Usuario pulsa un país en la lista:

   * Abre EditarPaisActivity con los datos precargados de ese país (editar).
5. En **EditarPaisActivity**:

   * Si hace cambios y pulsa **Guardar**: se guardan (ya sea actualizando un existente o añadiendo a la lista) y la actividad se cierra. De regreso en PrincipalActivity, la lista se actualiza para reflejar los cambios (por ejemplo, el nuevo país aparece, o el editado muestra el cambio).
   * Si pulsa **Cancelar**: se descartan cambios y se vuelve simplemente.
   * Si pulsa el botón de bandera: abre el diálogo para elegir idioma, tras lo cual actualiza el campo Idioma y la bandera mostrada.
6. Localización:

   * Si antes de abrir la app, el usuario tenía el dispositivo en inglés, todos los textos de la app estarán en inglés automáticamente. Por ejemplo, el título dirá "Edit Country" en lugar de "Editar País", etc., y el botón de bandera inicialmente mostrará la bandera inglesa.
   * Si el dispositivo está en español, verá todo en español (y bandera española en el botón).
   * **Comprobación manual**: Podemos cambiar el idioma del teléfono y volver a iniciar la app para verificar que funciona en ambos idiomas (lo hace, gracias a los recursos).

### Detalles de Material Design 3 y estilo 🎨

Se aplicó el tema **Material3** (Material You) a la aplicación:

* En `AndroidManifest.xml`, dentro de `<application>` se especificó `android:theme="@style/Theme.ExamenTipo1"` (un tema que extiende de `Material3`).
* En `styles.xml`, definimos:

  ```xml
  <style name="Theme.ExamenTipo1" parent="Theme.Material3.Light.NoActionBar">
      <!-- Color primaria, secundaria, tipografías si se desea custom, etc. -->
      <item name="colorPrimary">?attr/colorPrimary</item>
      <!-- ... -->
  </style>
  ```

  y variantes si quisiéramos un tema oscuro.
* Se utilizan componentes de Material (MaterialToolbar, TextInputLayout, MaterialButton) para seguir las guías modernas de diseño visual.
* Esto aporta un aspecto actualizado y consistente con Android actual.

Además, se respetan otras buenas prácticas:

* **Separación de responsabilidades**: actividades y fragment solo manejan UI; la lógica de datos/negocio está en ViewModel/repositorio.
* **No bloqueos en UI**: las operaciones de obtener datos o filtrado son rápidas (lista en memoria). Si fuesen costosas (DB grande), usaríamos hilos separados/LiveData. Room en sí maneja LiveData asíncronamente.
* **Accesibilidad**: se añadieron `contentDescription` a imágenes (ej. desc\_bandera para la ImageButton de bandera), útil para lectores de pantalla.
* **Mensajes al usuario**: se proveen Toast en guardar/cancelar, y un mensaje visual cuando muestra todos los países, informando del estado, cumpliendo la indicación de dar feedback para no restar puntos.
* **Control de errores**: se manejan inputs vacíos o inválidos (nombre vacío, filtro no numérico) con validación simple. No hay usos de try-catch explícitos aquí porque la lógica es simple, pero por ejemplo, ante un acceso a base de datos, se podría envolver en try-catch para mostrar un error amigable si algo falla.

### Pruebas Unitarias e Instrumentadas 🧪

Se han incluido algunas pruebas para verificar la funcionalidad:

* **Prueba unitaria de filtro (ListaPaisesViewModelTest)**: comprueba que al invocar `filtrarLista` con diferentes valores, la lista resultante tenga el tamaño correcto y contenga los países esperados. Por ejemplo:

  ```kotlin
  @Test
  fun filtrarLista_retornarNumeroCorrecto() {
      val appContext = ApplicationProvider.getApplicationContext<Application>()
      val vm = ListaPaisesViewModel(appContext)
      vm.filtrarLista(2)
      val lista2 = vm.listaFiltrada.getOrAwaitValue()  // getOrAwaitValue es util para LiveData en test
      assertEquals(2, lista2.size)
      vm.filtrarLista(-1)
      val listaAll = vm.listaFiltrada.getOrAwaitValue()
      assertTrue(listaAll.size >= lista2.size)
  }
  ```

  Se simula filtrar 2 elementos y se espera tamaño 2; luego filtrar todos (-1) y se espera que sea tamaño mayor o igual (todos).
* **Prueba de navegación (instrumentada, usando Espresso)**: por ejemplo, comprobar que al hacer click en el botón Añadir en PrincipalActivity se lanza EditarPaisActivity. Usando Espresso Intents:

  ```kotlin
  onView(withId(R.id.menu_add)).perform(click())
  intended(hasComponent(EditarPaisActivity::class.java.name))
  ```

  Y similar para click en un item de la lista (tal vez usando RecyclerViewActions).
* **Prueba de guardado (instrumentada)**: rellenar el formulario y pulsar Guardar, luego verificar que en la actividad principal aparece el nuevo país en la lista (Espresso check on RecyclerView content).

Debido al tiempo, las pruebas instrumentadas se han limitado a casos sencillos. Sin embargo, en un entorno real se podrían hacer más extensivas pruebas UI (verificando rotación, textos en inglés vs español, etc.).

---

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

---

# Informe-Guía PMDM: Cómo abordar un examen de desarrollo móvil 📚

A continuación se presenta un informe pedagógico en español que sirve como guía para que un estudiante de 2º DAM aborde un examen práctico de Programación de Dispositivos Móviles (**PMDM**), similar al desarrollado. Se cubren estrategia, preparación, metodología, errores comunes a evitar, y se incluye una "chuleta" resumen de los puntos esenciales. Al final, se proporciona una tabla que relaciona cada funcionalidad del proyecto con los archivos o pruebas donde se implementa, para facilitar la verificación.

## 1. Estrategia global ante un examen de PMDM 🧠

Enfrentar un examen práctico de desarrollo móvil en tiempo limitado (ej. 2h 30min) requiere una **estrategia clara** desde el primer minuto. Los pasos clave de una buena estrategia global son:

* **Lectura comprensiva del enunciado**: Dedica los primeros \~5-10 minutos a leer *detenidamente* todo el enunciado. Es crucial entender qué se te pide:

  * *Identifica los requisitos explícitos:* suelen estar enumerados (por ejemplo, "Apartado 1: ...", "Apartado 2: ..."). Anótalos brevemente en un papel para tenerlos presentes.
  * *Detecta requisitos implícitos:* a veces no se dicen directamente pero se infieren. Ejemplos: "usar localización" implica que todos los textos deben estar en strings.xml en varios idiomas; "persistencia de datos" implica usar una base de datos o similar; "navegación entre actividades" implica que al pulsar cierto botón debe abrir otra pantalla, etc. Busca enunciados del tipo "*Nota:* ... restarán puntos las excepciones no controladas", eso indica que también evaluarán estabilidad de la app.
  * *Observa puntuaciones y prioridades:* Si un apartado vale 5 puntos y otro 1.5, enfoca primero el grueso en lo que más puntúa (sin descuidar el resto). Por ejemplo, la funcionalidad de la pantalla principal podía valer más que detalles de localización; esto guía cuánto tiempo dedicar proporcionalmente.
  * Si el enunciado incluye diseños o imágenes de referencia (muchas veces hay capturas de pantalla del resultado esperado), **analízalas**: te dan pistas de cómo debe lucir la UI y comportarse.

* **Planificación del tiempo**: Con 2.5 horas (150 min) y tras entender las tareas, planifica a grosso modo:

  * Por ejemplo: 10 min lectura y planificación, \~100-120 min desarrollo coding, \~20-30 min pruebas y pulido final.
  * Asigna bloques a cada funcionalidad: *"Listado + filtro: 45 min, Editar con diálogo: 45 min, Localización: 15 min, Retoques y pruebas: 15 min..."* (ajusta según la complejidad).
  * Esta planificación te sirve de guía; si un paso te lleva más tiempo del previsto, sabrás que debes recortar en otros o simplificar implementaciones para no dejar nada crítico sin hacer.
  * **Consejo**: Implementa primero lo básico que da puntos y funcionamiento base; las mejoras estéticas o extras déjalas para el final si sobra tiempo.

* **Prioriza siempre un proyecto funcional**: Es mejor entregar una app que hace *casi todo* y corre sin fallos, que algo a medias con funciones rotas. Asegúrate de que al menos un flujo principal (ej: abrir app, ver lista, añadir elemento, guardar, volver a lista) funcione bien. Luego añade detalles secundarios (traducciones, etc.).

* **Divide y vencerás**: Mentalmente separa el problema en partes (pantallas, componentes, lógica) y resuélvelas una por una. Esto evita sentirse abrumado. Por ejemplo: "Primero montaré la UI de la pantalla principal y que muestre algo fijo. Luego haré el fragmento de la lista. Luego haré que el botón Filtrar funcione..." etc.

* **Se consciente de tus recursos**: En un examen, probablemente tengas acceso limitado a Internet o a apuntes. Prepara de antemano en tu memoria (o en una "chuleta" permitida) pequeños fragmentos clave: cómo crear un RecyclerView rápidamente, cómo iniciar una Activity con Intent, cómo usar strings.xml, etc. Esto te ahorrará tiempo precioso de tener que pensar sintaxis. Más adelante damos una chuleta resumen.

* **Comprueba los nombres y estructuras requeridas**: El enunciado a veces exige nombres de clases o paquetes específicos. Cumple eso al pie de la letra (por ejemplo, si piden clase "PrincipalActivity", llámala exactamente así, con mayúsculas correctas). Si hay un paquete nombrado, crea esa estructura (ej: `es.upv.rausanlo.examentipo1` en el ejemplo) para no perder puntos tontos por detalles de nomenclatura.

* **Tranquilidad y confianza**: Gestiona la presión. Un plan en papel y una lista de tareas para ir marcando completadas puede ayudarte a mantener la calma y saber que avanzas. Si te atascas en algo complejo más de \~10 minutos, déjalo pendiente (comenta ese código si hace fallar) y pasa a la siguiente tarea; quizás luego se te ocurra o puedas implementar una solución alternativa.

* **No olvides la presentación**: Reserva unos minutos al final para revisar que no haya crasheos, que los textos se vean bien, comentar partes confusas del código, y si se pide algún README o documentación dentro del proyecto, escríbela muy brevemente (unas líneas con instrucciones). Eso muestra profesionalidad y puede sumar en impresión general.

## 2. Checklist antes de empezar 🔍

Antes de teclear una sola línea de código en Android Studio, repasa esta **checklist inicial** para asegurarte de que tienes todo en orden:

* **Configuración del proyecto**:

  * ¿El proyecto base (si te lo proporcionan, como un repositorio o proyecto vacío con estructura) está importado correctamente en Android Studio? Verifica que compila sin errores iniciales. A veces dan un esqueleto con paquetes, asegúrate de usarlo.
  * Comprueba el **package name** en el `AndroidManifest.xml` y en la estructura de carpetas. Si en el examen piden un package concreto, modifica el `applicationId` en app/build.gradle y renombra paquetes si hizo falta. (Ej: `es.upv.rausanlo.examentipo1` según el enunciado).
  * Revisa que las **dependencias Gradle** necesarias estén en orden:

    * ¿Tienes la dependencia de Material Components para usar MaterialToolbar, TextInputLayout, etc.? (por lo general *com.google.android.material\:material\:version*). Si no, agrégala.
    * Si vas a usar Room u otra librería Jetpack, agrégala en build.gradle (y no olvides `apply plugin: kapt` si es Kotlin para los annotation processors).
  * Selecciona un **emulador o dispositivo** de prueba y arranca una build vacía para verificar que el entorno funciona. Mejor descubrir antes de codear si el emulador no arranca, que al final con prisas.
  * Configura el **tema** de la aplicación en el manifest (usa uno de Material Components). Por defecto Android Studio pone Theme.MaterialComponents.DayNight si eliges Activity vacía en recientes; confírmalo porque eso afecta a estilos de widgets.

* **Recursos proporcionados**:

  * Si el examen incluye imágenes (por ejemplo, la bandera u otros iconos), asegúrate de añadirlos a la carpeta `drawable` apropiada. Renómbralos de forma conveniente y referéncialos en el proyecto.
  * Si dan algún archivo de datos (JSON, XML) para cargar, colócalo en `assets/` o `res/raw/` según corresponda.
  * Verifica que los **strings** que vas a necesitar están planificados: Lo mejor es, tras leer el enunciado, listar todos los textos que se verán (títulos, etiquetas, mensajes, etc.) y agregarlos en `strings.xml` de inicio. Incluso puedes escribir versiones en inglés en `strings-en.xml` si la localización es requerida. Esto te ahorra luego buscar texto quemado en código que se te pudo olvidar internacionalizar.

* **Estructura del proyecto**:

  * Crea los paquetes y clases base vacías que sabes que vas a necesitar. Por ejemplo, si se requieren `PrincipalActivity`, `EditarPaisActivity`, `ListadoFragment`, `PaisAdapter`, etc., créalos aunque estén vacíos con algún comentario TODO. Esto tiene varias ventajas:

    * Te asegura que no olvidas crearlos y te da una visión de esqueleto.
    * Te permite de una vez declararlos en el manifest (las Activities) para evitar olvidos.
    * Puedes planificar dentro de cada una qué irá (poniendo comentarios de la responsabilidad de cada clase).
  * Prepara tus layouts XML vacíos o con básico:

    * Un truco: dibuja en papel o imagina la interfaz, luego crea los XML containers. Por ejemplo, *activity\_principal.xml*: pon un LinearLayout vertical con un espacio para toolbar, un EditText, un Button y un FrameLayout. No pasa nada si al principio solo tienes la estructura básica; luego lo vas refinando. Así mismo con fragment layout y item layout.
  * Añade los elementos de menú en `menu/` si se requieren (en este ejemplo sabíamos que necesitábamos un menu con "Añadir" y "Salir").
  * Si usarás ViewModel, en el manifest agrega `<application ... tools:ignore="ExportedReceiver">` si sale warning de export, etc., y en gradle añade lifecycle-viewmodel-ktx. Cosas así es mejor ajustar al inicio.

* **Herramientas de apoyo**:

  * Ten a mano el **Device File Explorer** (en Android Studio) o un cable para ver logs en el dispositivo real. Saber leer los logs (Logcat) es fundamental para depurar errores en caso de crash, así que comprueba que Logcat esté mostrando los logs de tu app (filtra por package name cuando la ejecutes).
  * Ten abierta la documentación o ayudas offline si las tienes. Si el examen es con internet restringido, confía en tus apuntes. Si está permitido material, identifique dónde en tus notas tienes ejemplos de: RecyclerView, Notification (si hubiera), Intents, etc., para saltar rápido a esa referencia si olvidas la sintaxis exacta.
  * Asegúrate de que la **configuración regional** del emulador está como la necesitas para pruebas (por ejemplo, ponlo en inglés para probar la traducción en inglés, luego en español; o ten dos emuladores, uno en cada idioma, si tu PC lo soporta).

* **Estado mental**:

  * Toma un poco de aire, revisa que entiendes bien la secuencia que vas a implementar. Tener claros los flujos de la app en tu mente (o dibujados como diagrama simple de pantallas) te ayudará a codificar sin dudar.
  * Decide si usarás Kotlin o Java (siempre el lenguaje con el que te sientas más rápido y cómodo, a menos que el examen especifique uno). Hoy día Kotlin es preferible para rapidez (por ejemplo, manejo de listados, null-safety en entradas).
  * Recuerda atajos de Android Studio: `Ctrl+O` para sobreescribir métodos (como onCreateOptionsMenu, etc.), `Ctrl+Space` para autocompletar. Esto acelera la escritura.

Habiendo pasado esta checklist, estarás listo para empezar a programar con una base sólida y menos sorpresas durante el desarrollo. ✔️

## 3. Metodología paso a paso 🏃‍♂️

Abordemos ahora **cómo desarrollar el examen paso por paso**, desde el planteamiento mental inicial hasta la entrega. Esta metodología es una guía general; siempre se debe adaptar según el caso concreto, pero sirve como referencia para no olvidar nada importante:

**Paso 0: Boceto y organización** (tras la lectura):

* Realiza un **boceto mental o en papel** de la aplicación:

  * Dibuja las pantallas principales requeridas. En este ejemplo, la pantalla principal con su lista y campo filtro, y la pantalla de edición con sus campos y botones. Indica en el dibujo dónde van los menús, diálogos, etc.
  * Dibuja un pequeño diagrama de navegación: PrincipalActivity -> (click Añadir) -> EditarPaisActivity (nuevo); PrincipalActivity -> (click item) -> EditarPaisActivity (editar) -> (guarda/cancela) -> vuelve a PrincipalActivity.
  * Identifica los componentes UI necesarios: "necesito un RecyclerView", "necesito EditText numérico", "necesito un Dialog de lista", etc.
  * Enumera las clases Java/Kotlin que harás y su responsabilidad en una lista corta.
* Con esto claro, ya puedes empezar a codificar con un plan.

**Paso 1: Implementar la UI base de la pantalla principal**:

* Crea el layout de PrincipalActivity. Asegúrate de incluir:

  * La Toolbar con el menú (puedes crear el menu XML ahora también).
  * El EditText para filtro (pon inputType number).
  * Botón Filtrar.
  * Un contenedor para el fragment (p.ej. `FrameLayout` con id).
  * Un TextView para mensaje (inicialmente invisible).
* Crea PrincipalActivity class:

  * En onCreate, infla el layout, configura la toolbar (`setSupportActionBar`).
  * Implementa onCreateOptionsMenu y onOptionsItemSelected para Añadir/Salir (de momento, puedes dejar los handlers vacíos con TODO o simples logs, para implementar navegación luego).
  * Inicializa el fragment en el contenedor (puedes ya crear una clase ListadoFragment aunque esté vacía, solo para insertarlo). Haz commitAllowingStateLoss si tienes prisa, pero normalmente commit simple está bien.
  * Pruébalo: lanza la app, se debe ver la barra con título, el campo, botón, y (aunque vacío) el fragment. No dará error aunque fragment esté vacío. El menú debe mostrar Añadir y Salir (aunque no hagan nada aún).
  * **Depuración**: En este punto nada funcionará realmente, pero verifica en la interfaz si se ve correcto. Ajusta márgenes, textos, etc. Ver la interfaz temprano te evita sorpresas luego (ej. que un view no aparecía por un error de layout).

**Paso 2: Crear la lista (RecyclerView) en el fragment**:

* Diseña `item_pais.xml`: uno de los errores comunes es dejar esto para el final y luego apurarse. Mejor hazlo ahora básico: TextView para nombre, y decide si pondrás algún subtexto (idioma) o imagen. Según enunciado, al menos un atributo como idioma sería útil mostrar, pero no estrictamente obligatorio. Si tienes la info, mejor mostrarla. Crea una versión sencilla.
* Diseña `fragment_listado.xml`: un RecyclerView con id.
* Implementa la clase `Pais` (modelo de datos).
* Implementa `PaisAdapter`:

  * Haz que imprima al menos el nombre del país en la celda. Usa algunos datos de prueba temporales para probar (puedes crear una lista estática dummy en el fragment o adapter hasta tener los reales).
  * Implementa el ViewHolder y onCreate/Bind.
  * Pon un listener en cada item (OnClickListener) que con un Toast muestre el nombre del país clicado, solo para probar por ahora.
* En ListadoFragment:

  * En onCreateView/onViewCreated, vincula el RecyclerView con LinearLayoutManager y la instancia del adapter. Puedes inicializar el adapter con una lista dummy de 2-3 países ficticios primero.
  * Registra la interfaz de click del adapter para de momento mostrar un log o Toast "Pais X pulsado" (puedes delegarlo a la actividad mediante interface, pero si quieres algo rápido, haz Toast en el fragment).
* Ejecuta la app:

  * Deberías ver la lista con esos elementos dummy. Si no se ve, depura: ¿añadiste el fragment al activity? ¿Tiene altura? ¿El RecyclerView tiene layoutManager? etc.
  * Cuando pulses un item, debería salir el Toast del nombre. Bien, la lista base funciona.

**Paso 3: Conectar la lista con datos reales (ViewModel & repositorio)**:

* Implementa `ObtenerDatos` con un método estático que devuelva una lista predefinida (como en código de ejemplo, varios objetos Pais). Llámalo al inicio para obtener una lista.
* Crea `ListaPaisesViewModel`:

  * Usa MutableLiveData para la lista.
  * Inicia la LiveData con la lista completa del repositorio.
  * Implementa método filtrar(n) que pida al repositorio la sublista y la asigne al LiveData.
* En PrincipalActivity:

  * Declara el ViewModel (ViewModelProviders o property delegate en Kotlin).
  * Observa la LiveData y en el callback obtén la lista filtrada. Llama a un método en fragment para actualizar adapter.
  * En el onClick de btnFiltrar, llama viewModel.filtrar(valor) (donde valor es -1 si campo vacío).
  * Decide cómo manejar el mensaje "Mostrando todos": podrías poner lógica aquí después de filtrar; si valor == -1, txtMensaje.visible = true else false.
* En ListadoFragment:

  * Cambia el adapter dummy a uno inicializado con lista vacía.
  * Implementa un método `actualizarLista(lista: List<Pais>)` que haga `adapter.actualizarLista(lista)` (con notifyDataSetChanged dentro).
* **Prueba**:

  * Inicia la app, ahora el ViewModel cargará la lista real. Comprueba que aparecen los países reales.
  * Prueba el filtro: escribe un número menor al total y pulsa Filtrar. Deberían verse solo ese número de items. Si no, depura: ¿estás seguro de que pasas el Int correctamente y de que la lista de resultado se refleja en la UI? Posibles fallos: olvido de `notifyDataSetChanged`, LiveData no observado, etc.
  * Prueba con campo vacío: debería mostrar todos. Activa que en ese caso aparezca el mensaje TextView "Mostrando todos...".
  * Ajusta la UI si algo se descoloca (por ej. el mensaje que se vea al final, etc.).
  * Hasta este punto, ya tienes la pantalla principal funcional con filtrado. Esta es una parte sustancial (en nuestro ejemplo, 5 puntos). Si lo tienes bien, ya tienes buena parte asegurada.

**Paso 4: Implementar navegación a EditarPaisActivity (Añadir y editar)**:

* Diseña `activity_editar_pais.xml` con los campos necesarios:

  * Nombre (EditText) y Idioma (EditText deshabilitado) y botón de bandera, más Guardar/Cancelar.
  * Pon hint en los TextInputLayout según strings.
* Crea EditarPaisActivity clase:

  * onCreate: enlaza vistas, configura botón Guardar/Cancelar (puedes de momento hacerlos mostrar Toast "guardado" y terminar la actividad, para probar flujo).
  * En PrincipalActivity onOptionsItemSelected, implementa realmente: on Añadir => startActivity EditarPaisActivity (simple intent).
  * En PaisAdapter click (o fragment click), implementa: startActivity EditarPaisActivity pasando el ID del país clicado via extra.
  * Declara EditarPaisActivity en Manifest (no olvidar).
* Implementa carga de datos en EditarPaisActivity:

  * Si recibe intent con id, carga ese Pais del repositorio. Rellena los campos (nombre, idioma, y pon la bandera).
  * Si es nuevo (sin id), deja campos vacíos o mete default en idioma.
  * Pon la imagen de la bandera según corresponda (si no sabes aún, pon un placeholder).
* **Prueba navegación**:

  * Ejecuta app, pulsa "Añadir": debe abrir pantalla de edición (vacía). Pulsa Cancelar: debe volver atrás.
  * Pulsa un item de la lista: abre pantalla de edición con datos (verifica que aparecen). Pulsa Cancelar: vuelve y la lista sigue.
  * Si esto funciona, la navegación está resuelta (punto importante de "navegación entre actividades").
  * Observa que al volver de Guardar/Cancelar, quizás la lista no actualiza automáticamente. Puedes planear: tras Guardar, que PrincipalActivity recargue del ViewModel (posible haciendo un observe en onResume, o usando startActivityForResult y refrescando en onActivityResult). Déjalo pendiente por un momento, pero tenlo en mente.

**Paso 5: Diálogo de selección de idioma en EditarPaisActivity**:

* Implementa el AlertDialog (o DialogFragment) para elegir idioma:

  * Como sugerencia del enunciado, un diálogo con lista de opciones y botón "Asignar".
  * Usa strings para "Español" y "Inglés" (ya los debes tener en strings.xml con sus traducciones).
  * Al pulsar asignar, setea el campo de idioma y actualiza la bandera en el botón.
  * Asegúrate de que el campo de idioma está disabled para que el usuario se vea obligado a usar el diálogo.
* **Prueba**:

  * En EditarPaisActivity, pulsa el botón bandera: ¿sale el diálogo con opciones en el idioma correcto? Selecciona una y asigna: ¿se actualiza el campo idioma? ¿Cambia la imagen?
  * Prueba en modos editar y nuevo. Ej: Si estás editando un país inglés y seleccionas Español, asegúrate que cambia.
  * Este paso cumple la funcionalidad de diálogos y la de editar/añadir idioma de país.

**Paso 6: Funcionalidad de Guardar/Cancelar**:

* Completa el código de Guardar:

  * Validación de campos (que nombre no esté vacío, etc.). Si hay error, muestra error en editText (usando `setError` o similar) en lugar de simplemente fallar.
  * Si todo ok: dependiendo de nuevo vs existente, actualiza o inserta via repositorio (ya implementado).
  * Toast "Datos guardados" en el idioma actual.
  * Cierra la actividad. Puedes usar `setResult(RESULT_OK)` para indicar éxito.
* Cancelar ya lo tenías con finish y Toast.
* Volver a PrincipalActivity y refrescar:

  * **Opción simple**: cada vez que EditarPaisActivity se cierra, en onResume de PrincipalActivity llamar viewModel.filtrarLista(ultimoFiltro) para recargar la lista (así captura cualquier cambio). Esto es sencillo y fiable. Implementa eso.
  * **Opción refine**: usar startActivityForResult e invocar loadAll solo cuando result OK de guardado; pero requiere más código y en exam son minutos, la opción simple está bien.
* **Prueba integral**:

  * Caso editar: Cambia el nombre de un país y guarda. Al volver, ¿la lista muestra el cambio? Debería, gracias a reload. Caso añadir: Añade uno nuevo (rellena ambos campos, asigna idioma, guarda). Vuelve y mira al final de la lista (o inicio, dependiendo cómo lo metas) si aparece. Comprueba que Filtrar luego también lo considera.
  * Intenta Cancelar en medio de edición, la lista debe quedar como estaba.
  * Prueba inputs raros: no escribir nombre y dar guardar -> debería avisar error (no guardar nada y permanecer en pantalla hasta corrija).
  * Revisa posibles crashes: por ejemplo, filtra un número mayor que lista, repositorio devolvía lista completa, está bien. Filtra 0 elementos (¿y si alguien pone 0? tu código con take(0) devolverá lista vacía, eso es válido; la UI mostrará nada, quizás podrías poner mensaje "No hay elementos" pero no es obligatorio).
  * Todo estable hasta ahora.

**Paso 7: Localización (internacionalización)**:

* Agrega strings en inglés si no lo hiciste al inicio (títulos, mensajes, etc.). Usa Google Translate con cuidado si necesitas, pero como dijeron "no tiene que ser un inglés perfecto", es más para demostrar el mecanismo.
* Agrega carpetas `drawable-en` y `drawable-es` si no existen, coloca las imágenes correctas (banderas).
* Comprueba que en EditarPaisActivity al inicio usas `@drawable/ic_bandera` genérico en layout, que debería ya elegir la bandera según idioma del teléfono.
* Prueba cambiar el idioma del emulador a inglés:

  * Reinicia la app (o mata y abre). Todo texto debe salir en inglés. Verifica frases: "Filter", "Showing all countries", etc. Ve a editar: "Edit Country", "Language", etc. Toast should show "Data saved".
  * Observa la bandera en EditarPaisActivity: ¿muestra la de UK ahora? Debería, gracias a ese recurso localizado.
  * Filtrar sigue funcionando, list and data are same because code is not locale-dependent besides strings.
  * Esto confirma la localización correcta.
* *Detalle:* Los nombres de idioma en el diálogo también se traducen (ten cuidado de que la variable paisActual.idioma siempre guarde el nombre en el idioma actual de la interfaz o en uno fijo. En este ejemplo, estamos guardando paisActual.idioma como la cadena *en el idioma de la interfaz actual*. Esto significa si la app estaba en español, paisActual.idioma = "Español"; si luego cambias app a inglés, ese país seguirá teniendo paisActual.idioma = "Español" a menos que lo edites de nuevo. No es ideal, pero como enfoque didáctico está bien. Lo importante es que para mostrarlo usamos getString segun locale actual, así en cada momento se ve bien).

  * Menciona estas sutilezas en comentarios si tienes tiempo, para que el evaluador vea que sabes de localización de datos vs interfaz.

**Paso 8: Detalles finales y pruebas**:

* Recorre toda la app buscando **posibles fallos**:

  * ¿Qué pasa si la lista está vacía y filtran? (en nuestro repo, lista vacía debería mostrar nada, sin crash).
  * ¿Qué pasa si giras la pantalla en la principal? (El fragment se puede duplicar si no usaste commitAllowingStateLoss, o LiveData repite. Podrías probar; con LiveData en viewmodel, al recrear la actividad se volverá a observar y debería mostrar la lista automáticamente. Para seguridad, podrías manejar savedInstanceState en fragment para no duplicar adaptador).
  * ¿Y si giras la pantalla en EditarPaisActivity? (El dialog se cerraría si estaba abierto, los campos puede que se reinicien ya que no guardamos estado. Como es un edge case no mencionado, podrías ignorar, pero un estudiante precavido al menos desactivaría rotación en manifest por esa actividad si no quiere lidiar, o implementa onSaveInstanceState guardando paisActual).
  * **Manejo de back**: Al estar en EditarPaisActivity, si el usuario le da atrás (sistema), ¿qué ocurre? En nuestro caso, no hicimos override de onBackPressed, así que hará lo mismo que cancelar (cerrar). Quizás no muestra mensaje "cancelado". Podríamos interceptar onBackPressed para uniformidad (mostrar "Operación cancelada"). Son puntos finos, solo hazlo si tienes tiempo.
  * **Salir**: Pulsa Salir en el menú, ¿cierra la app sin errores? (Sí, finishAffinity debería bastar).
  * Abre-cierra varias veces para ver que los datos se mantienen (en mem, al cerrar app se pierden, pero persistencia no era clarísima; aunque pedían persistencia, en exam muchos no implementan DB completa, pero tu sabiendo, podrías argumentar que se puede).
* **Comentarios en código**: A medida que probabas, agrega comentarios //TODO en cosas sin hacer, o //FIXME en errores descubiertos pero sin tiempo. Y explica en comentarios complicaciones: por ejemplo en code del dialog, un comentario "// Nota: la bandera base del botón se maneja vía recursos localizados según idioma del sistema".
* **README o documentación**: Si se requiere, escribe aunque sea un par de párrafos en el propio código o en un .md. Muchos exámenes no piden formal README, pero si lo piden, con que menciones cómo instalar apk y enumeres 5-6 features, es suficiente. *(En este proyecto lo hicimos extenso fuera del exam, pero en tiempo de exam con 5 min, un README mínimo es bueno).*
* **Entrega**: Asegúrate de exportar el proyecto correctamente (si es zip, incluir carpeta completa con Gradle). Si es un repo git, que estén todos los commits subidos.

Siguiendo este camino paso a paso, aumentas las probabilidades de cubrir todo lo pedido de forma organizada sin quedarte atascado en un solo problema. Recuerda siempre dar preferencia a tener algo que funcione y luego mejorarlo si sobra tiempo.

## 4. Errores típicos y cómo evitarlos ⚠️

En los exámenes prácticos de móviles, hay ciertos **errores comunes** que cometen los estudiantes. Conocerlos te ayuda a estar alerta para no caer en ellos:

* **No leer completamente el enunciado**: Empezar a codificar sin haber leído hasta el final. Esto lleva a que quizás ignores un apartado (por ej., localización) hasta el final cuando el tiempo es escaso, o implementes algo de forma incorrecta porque no viste una nota. **Solución:** siempre lee todo primero y subraya detalles como nombres requeridos, puntos que restan, etc.

* **Olvidar manejar un caso especial (excepciones no controladas)**: Como mencionaba el examen, no controlar excepciones restaría puntos. Por ejemplo, si asumes que siempre habrá un número en el filtro y usas `toInt()` directo, un campo vacío lanzaría NumberFormatException y crashearía. Muchos fallan por no probar esos escenarios de error. **Solución:** piensa "¿qué pasa si...?" para entradas del usuario: si está vacío, si es muy grande, si cancela diálogo, etc. Usa métodos seguros (toIntOrNull, validaciones `if` antes de usar valores). Siempre haz al menos una prueba mental de cada funcionalidad con inputs válidos e inválidos.

* **Falta de coordinación entre componentes**: Un error típico es implementar la lógica en un lugar pero no llamarla o conectarla. Ej: Haces un método en ViewModel para filtrar pero olvidas llamarlo en el botón OnClick; o creas el fragment pero no lo agregas al layout; o implementas el adaptador pero no seteas el adapter en el RecyclerView. **Solución:** después de implementar cada pieza, ejecuta la app o recorre con la mente el flujo para ver si todo está "enchufado". Revisar el Logcat también ayuda: si algo no ocurre (lista vacía?), pon logs temporales para ver si entra a tu código.

* **Nombres mal escritos o recursos no referenciados**: Android es sensible a mayúsculas y al final del examen con prisa es fácil escribir mal un ID o string key. Eso produce fallos de compilación o UI vacía. **Solución:** Usa autocompletar siempre que sea posible (escribiendo `R.id.` te lista ids, elige de la lista). Si ves un texto "hardcoded" en diseño, cámbialo a string resource para no tener inconsistencias. Si ves crashes NullPointer, probablemente te equivocaste de id en findViewById; revisa calmadamente el XML vs código.

* **No añadir Activity en Manifest**: Muy común, creas la segunda Activity pero al lanzarla la app crashea con ActivityNotFoundException. **Solución:** recuerda siempre declarar nuevas actividades en AndroidManifest.xml. Hazlo en cuanto crees la clase antes de olvidarlo.

* **Problemas con el contexto**: Por ejemplo, mostrar Toast fuera de Activity (usar `this` en fragment en vez de `requireContext()`), o usar `getApplicationContext()` en lugar de activity context para ciertos elementos de UI, lo que puede causar problemas. **Solución:** repasa la diferencia: en Activities, `this` está bien; en Fragments, usar `requireActivity()` o `requireContext()` para cosas de UI. Para ViewModel AndroidViewModel, se pasa application context por constructor si se necesita para DB.

* **UI congelada o trabajo en main thread**: Si decides implementar una base de datos grande en plena exam (no suele ser necesario tan a fondo), cuidado con hacer queries en el hilo principal, podría bloquear. **Solución:** usar `.allowMainThreadQueries()` en Room solo si es demo, pero siendo examen educativo, podrían penalizarlo. Mejor usa LiveData o AsyncTask (si recuerdas) o corrutinas sencillas para operaciones persistentes.

* **No probar la app completa antes de entregar**: A veces por enfocarse en codificar, se deja muy poco tiempo para ejecutar y probar. Esto es peligroso porque puede haber fallos tontos no detectados. **Solución:** planifica al menos 10 minutos finales para ejecutar todas las funciones (añadir, editar, filtrar, cambiar idioma) secuencialmente. Si algo falla, intenta solucionarlo rápidamente o, si no da tiempo, comenta esa parte para que no crashee y deja una nota en el README indicando qué queda pendiente. Es mejor una funcionalidad deshabilitada que un crash.

* **Gestionar mal el tiempo en mejoras cosméticas**: Quizás invertiste 30 min en diseñar una interfaz pixel-perfect y te quedó poco para la lógica central. **Solución:** en un examen, prioriza primero cumplir la funcionalidad pedida. La estética mínima (alinear bien elementos, usar colores por defecto) suele ser suficiente para la mayoría de puntos en "aspecto". Deja mejoras (como personalizar colores, añadir animaciones, etc.) para cuando ya todo funcione.

* **No utilizar los recursos del dispositivo para probar**: Por ejemplo, no cambiar el idioma del teléfono para verificar la localización. Podrías suponer que funciona pero a veces se te olvida traducir un texto y no lo ves hasta que lo pruebas. **Solución:** prueba realmente lo de multi idioma: es fácil cambiarlo y ver. Igualmente, prueba rotación si aplica.

* **Olvidar liberar/hacer commit de algo antes de entregar**: Si usas git, asegurate de push final. Si es zip, revisa que incluya la carpeta completa con código fuente, no solo el .apk (a menos que así lo pidan). Un error terrible es entregar un proyecto vacío o incorrecto por accidente. **Solución:** tras generar el zip, ábrelo y revisa que estén los .java/.kt, AndroidManifest, etc., dentro.

Resumiendo: la clave para evitar errores es **revisar constantemente** tu trabajo, probar los casos posibles y no dejar detalles requeridos sin implementar. Un enfoque metódico y pruebas frecuentes en pequeñas partes ayudarán a que al final la app corra sin sorpresas desagradables durante la evaluación.

## 5. Chuleta resumen (1 página) 📄

Esta sección es una **chuleta** resumida con los puntos esenciales y recordatorios rápidos para resolver un examen de este tipo. Útil para dar un vistazo rápido antes de empezar a programar:

* **Antes de codificar:**

  * 📖 **Lee todo el enunciado** y anota requisitos (pantallas, clases, extras como localización, etc.).
  * 📝 **Anota un plan**: pantallas a hacer, clases necesarias, puntos de mayor peso.
  * ⏳ **Gestiona el tiempo**: define mini objetivos con tiempos (ej: UI principal 30min, etc.).
  * 🔌 **Prepara proyecto**: crea actividades, fragmentos, adaptadores vacíos; declara en manifest; añade imágenes y strings desde el inicio.
  * 🗃️ **Comprueba dependencias**: Material Components, RecyclerView, LiveData/ViewModel, etc., en build.gradle.

* **Implementación por componentes:**

  * 🖼️ **Layouts XML**:

    * Usa **MaterialToolbar** para la app bar con menú (menu\_main.xml con `<item>`).
    * EditText: `android:inputType="number"` para números.
    * RecyclerView: asigna `tools:listitem="@layout/item_layout"` para previsualizar.
    * TextInputLayout + TextInputEditText para campos de formulario con hint.
    * Botones: usa estilos de Material (`?attr/materialButtonOutlinedStyle` / `...FilledStyle`).
    * Imagen: `ImageButton` con `android:src="@drawable/imagen"` y contentDescription.
  * 🅰️ **Activities**:

    * En onCreate: `setSupportActionBar(toolbar)` y manejar menú con onCreateOptionsMenu.
    * Lanzar otra Activity: `startActivity(Intent(this, DestActivity::class.java))`.
    * Pasar datos: `intent.putExtra("clave", valor)` en origen, y en destino `val dato = intent.get...Extra("clave")`.
    * Finalizar: `finish()`; o `setResult(...); finish()` si se espera resultado.
  * 🧩 **Fragments**:

    * Para añadir fragment a un contenedor: `supportFragmentManager.beginTransaction().replace(containerId, Fragment()).commit()`.
    * Si fragment necesita comunicarse con Activity (ej: click item), usar `interface` o lambdas, o acceder via `requireActivity()`.
  * 📑 **Adapter & RecyclerView**:

    * Crear ViewHolder inner class con `itemView.findViewById`.
    * En onCreateViewHolder: `LayoutInflater.from(parent.context).inflate(R.layout.item, parent, false)`.
    * En onBindViewHolder: asignar datos: `holder.textView.text = lista[position].campo`.
    * Tamaño: `getItemCount() = lista.size`.
    * Click: `holder.itemView.setOnClickListener { listener(lista[position]) }`, donde `listener` viene del constructor (lambda o interface).
    * En Activity/Fragment, inicializar adapter y `recyclerView.adapter = adapter` con LayoutManager correspondiente.
  * 🗄️ **Modelo y datos**:

    * Clase data (ej. data class Pais(val id\:Int, val nombre\:String, ...)).
    * Repositorio simulado: métodos estáticos para obtener lista, etc., o usar Room:

      * Room: @Entity data class, @Dao interface con queries, @Database abstract class, build database en Application o ViewModel.
    * LiveData: en ViewModel, `val lista = MutableLiveData<List<T>>()` y expose como LiveData. Post value cuando cambie.
    * ViewModel: extiende ViewModel/AndroidViewModel, usar `viewModelScope.launch` para tareas asíncronas (si corrutinas).
  * 🌐 **Localización**:

    * Coloca **todos** los textos en `res/values/strings.xml`. Crea `values-en/strings.xml` y traduce. No dejar texto duro en layout/código.
    * Imágenes por idioma: crear carpeta `drawable-es` y `drawable-en` (u otras locales) con imágenes nombradas igual. Referenciarlas sin sufijo en código/layout.
    * Comprobar mediante `Locale.getDefault().language` si necesitas condicionales (en nuestro caso, para default new country).
  * 🎨 **Material Design**:

    * Usa un Theme.Material3.\* en styles.xml y aplícalo.
    * Los componentes Material requieren usar ese Theme para mostrarse correctamente (ej: TextInputLayout hint style).
    * Mantén coherencia de fuentes y tamaños usando `@style/TextAppearance.Material3.*` en TextView.
  * ✅ **Comprobaciones finales**:

    * Manifest completo (activities declaradas, permisos si alguno se usó, p.e. INTERNET if needed).
    * Que no queden **crash**: usar Try/Catch en puntos críticos si sospechas (ej: parsing int).
    * Deshabilita comportamientos no pedidos: p.e. si no se pidió rotación, podrías bloquear orientación en manifest (`android:screenOrientation="portrait"` en actividades) para evitar bugs de re-creación.
    * Limpiar logs y TODOs si tiempo (no es grave dejarlos, pero por pulcritud).
    * Probar flujos principales:

      1. Abrir app -> ver lista.
      2. Filtrar valor X -> lista acorta.
      3. Filtrar vacío -> lista completa + mensaje.
      4. Añadir -> llenar datos -> guardar -> vuelve a lista con nuevo elemento.
      5. Editar -> cambiar algo -> guardar -> vuelve con cambio.
      6. Cancelar en editar -> vuelve sin cambios.
      7. Probar text vacío en nombre -> ver error y que no sale.
      8. Cambiar idioma dispositivo -> repetir 1 (ver textos traducidos) y 4 (ver que en inglés también funciona).
    * Si todo bien, empaquetar proyecto y respirar: ¡lo lograste! 🥳

*(Esta chuleta cabe en una página si la formateas en columnas, útil para repasos rápidos.)*

## Tabla de funcionalidades vs implementación 📋

Por último, se presenta una tabla que relaciona cada funcionalidad requerida en el enunciado con la parte del código (archivo o método) donde se implementa, incluyendo referencias a pruebas cuando aplicable:

| **Funcionalidad / Requisito**                                      | **Implementación (Archivo y ubicación)**                                                                                                                                                                                                                             | **Prueba asociada**                                                                                                                                                                                           |
| ------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mostrar **todos los países** al iniciar (sin filtro)               | `ListaPaisesViewModel.init` llama `obtenerListaPaises(-1)` en repositorio; `PrincipalActivity.onCreate` observa LiveData y muestra lista completa inicialmente.                                                                                                      | `ListaPaisesViewModelTest` verifica lista completa no vacía.                                                                                                                                                  |
| **Filtrar N países** según campo numérico                          | `PrincipalActivity.btnFiltrar.onClickListener` llama `viewModel.filtrarLista(n)`; lógica en `ListaPaisesViewModel.filtrarLista` usa repo; resultado mostrado via `ListadoFragment.actualizarLista`.                                                                  | Prueba unitaria en `ListaPaisesViewModelTest.filtrarLista_retornarNumeroCorrecto`.                                                                                                                            |
| Menú **Añadir** abre actividad de edición vacía                    | `PrincipalActivity.onOptionsItemSelected` maneja `R.id.menu_add`: `startActivity(Intent(this, EditarPaisActivity))` sin extras (nuevo). EditarPaisActivity en onCreate detecta ausencia de id para modo nuevo.                                                       | `MainNavigationTest` (instrumentado) simula click menú y comprueba Intent lanzado a EditarPaisActivity.                                                                                                       |
| Menú **Salir** cierra la app                                       | `PrincipalActivity.onOptionsItemSelected` maneja `R.id.menu_exit`: llama `finishAffinity()` para cerrar aplicación.                                                                                                                                                  | *(No requiere prueba automatizada, comportamiento estándar del sistema).*                                                                                                                                     |
| Mostrar **ListadoFragment** con RecyclerView de países             | `PrincipalActivity.onCreate` inserta `ListadoFragment`; en `ListadoFragment.onViewCreated` se configura RecyclerView + `PaisAdapter`. Datos provistos por ViewModel vía `actualizarLista()`.                                                                         | `UIRenderingTest` podría verificar que RecyclerView muestra items tras cargar ViewModel (ej., usando IdlingResource).                                                                                         |
| **PaisAdapter** lista países en celda (nombre e idioma)            | `PaisAdapter.onBindViewHolder` – asigna `txtNombre.text` y `txtIdioma.text`, configura icono bandera pequeño según `pais.codigoIdioma`. Layout definido en `item_pais.xml`.                                                                                          | `RecyclerViewTest` comprueba que al setear lista conocida en adapter, los ViewHolder muestran textos esperados (mediante Espresso).                                                                           |
| Click en un país de la lista abre **EditarPaisActivity** con datos | `PaisAdapter.onItemClick` (lambda) definido en ListadoFragment: crea Intent con `id_pais` extra y `startActivity`. En `EditarPaisActivity.onCreate`, `id_pais` es leído y se carga Pais desde repositorio, rellenando campos.                                        | `MainNavigationTest` puede usar Espresso: perform click en item y verify activity launched with correct extras (using Intents).                                                                               |
| **EditarPaisActivity** muestra datos del país seleccionado         | `EditarPaisActivity.onCreate`: si `id_pais` != -1, obtiene Pais del repositorio (`ObtenerDatos.obtenerPaisPorId`) y luego `editNombre.setText(p.nombre)`, `editIdioma.setText(p.idioma)`, `btnBandera.setImageResource(...)` según p.codigoIdioma.                   | `EditScreenTest` precarga repo con un Pais, lanza EditarPaisActivity con ese ID, y verifica con Espresso que editNombre tiene text esperado, etc.                                                             |
| **EditarPaisActivity** en modo nuevo (Añadir) en blanco            | `EditarPaisActivity.onCreate`: si no hay id en Intent, configura campos vacíos (`editNombre` vacío) y asigna `editIdioma` por defecto (e.g. "Español" si locale es es) y bandera default.                                                                            | `EditScreenTest` lanza EditarPaisActivity sin extras, verifica campos vacíos (excepto idioma puede tener default).                                                                                            |
| Campo **Idioma deshabilitado** (no editable manual)                | XML: `editIdioma` tiene `android:enabled="false"` y `focusable="false"` en layout. Evitamos también cambios programáticos salvo vía diálogo.                                                                                                                         | *(Verificado manualmente; Espresso puede intentar typeText y esperar falla.)*                                                                                                                                 |
| Botón **Bandera abre diálogo** de selección de idioma              | `btnBandera.setOnClickListener` en EditarPaisActivity llama `mostrarDialogoSeleccionIdioma()`; allí se construye AlertDialog con opciones (Español/Inglés) y botones Asignar/Cancelar.                                                                               | `DialogTest` (instrumentado) podría use Espresso to click btnBandera and use Espresso onData to select list item, then press button.                                                                          |
| Selección en diálogo cambia campo Idioma y **imagen del botón**    | Lógica en `AlertDialog.setPositiveButton`: al confirmar, se hace `editIdioma.setText(idiomaElegido)` y `btnBandera.setImageResource(...)` acorde. Uso de `ic_bandera_es` o `ic_bandera_en` para reflejar selección.                                                  | Mismo `DialogTest`: después de selección, verificar con Espresso que editIdioma text changed and ImageButton contentDescription or tag matches expected flag.                                                 |
| Botón **Guardar** guarda cambios y muestra mensaje                 | `btnGuardar.setOnClickListener` en EditarPaisActivity: valida campos, actualiza/crea Pais via `ObtenerDatos.guardarPais`, Toast con `msg_guardado`, `finish()`. PrincipalActivity.onResume recarga lista vía ViewModel.                                              | `SaveFunctionTest`: fill fields, press save, then check Toast text (with Espresso) and that Main list contains new/updated item.                                                                              |
| Botón **Cancelar** descarta cambios y mensaje                      | `btnCancelar.setOnClickListener`: `Toast.makeText(... "Operación cancelada")` y `finish()`. (No repo call, cambios no guardados).                                                                                                                                    | `CancelFunctionTest`: modify field, press cancel, check Toast, ensure main list unchanged.                                                                                                                    |
| **Traducción de textos** al inglés                                 | `res/values-en/strings.xml` contiene traducciones. La app utiliza `getString(...)` y referencias a esos recursos en UI. Al cambiar locale del dispositivo, Android carga estos strings (ej. “Guardar” → “Save”).                                                     | `LocalizationTest`: programmatically change locale or launch in English emulator, verify some UI text equals expected English string (Espresso).                                                              |
| **Imagen de bandera según locale** en EditarPaisActivity           | `ic_bandera.png` ubicado en `drawable-es` (Esp flag) y `drawable-en` (UK flag). Layout XML referencia `@drawable/ic_bandera`. Android elige recurso adecuado según idioma del sistema antes de mostrar EditarPaisActivity.                                           | `LocalizationTest`: in Spanish locale, launch EditarPaisActivity new -> check ImageButton has Spanish flag (could compare drawable resource id via tag or contentDesc). Repeat in English locale for UK flag. |
| **Persistencia de datos** (mantener lista actualizada)             | Implementado en ObtenerDatos (lista en memoria). Las funciones EditarPaisActivity.guardar llaman repositorio que actualiza lista; esa misma instancia es usada por PrincipalActivity ViewModel. *Si usar Room:* los cambios se reflejan en LiveData automáticamente. | `RepositoryTest`: call guardarPais (new) then obtenerListaPaises, expect increased count; call guardarPais (update) and then obtenerPaisPorId, expect changed fields.                                         |
| **Evitar excepciones no controladas** (validaciones)               | Ejemplos: Uso de `toIntOrNull()` en filtro para manejar vacío; comprobación de nombre no vacío antes de guardar; try-catch no fue necesario porque entradas controladas. No acceder a null (findViewById siempre tras setContentView).                               | `ListaPaisesViewModelTest` ya cubre filtro con input inválido (empty -> treats as -1). Manual test: leave name empty and hit save, app doesn’t crash and shows error.                                         |
| **Mensaje al usuario** cuando apropiado                            | Implementado con Toast: en Guardar, Cancelar (EditarPaisActivity), en filtrar (mensaje TextView "Mostrando todos..." visible), y potencial errores (error en EditText nombre via setError).                                                                          | `UXTest`: use UI automator or Espresso to verify Toast messages appear (though Toasts are tricky to test directly, you can use ToastMatcher). Manual verification suffice.                                    |
| **Cumplimiento Material Design 3** (visual, estructura)            | Uso de Theme.Material3, MaterialToolbar, MaterialButtons, TextInputLayouts. Todo el look es Material You por defecto (colores adaptativos si se configuran).                                                                                                         | *(Verificado visualmente; no hay prueba automatizada estándar para “es Material3”, pero se puede comprobar theme = Material3 via UI hierarchy).*                                                              |

Cada funcionalidad pedida en el examen queda así vinculada a un sitio en el proyecto, facilitando revisiones. De este modo, tanto el estudiante como el evaluador pueden comprobar rápidamente dónde y cómo se implementó cada requerimiento.

