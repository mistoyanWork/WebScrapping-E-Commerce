# Introducción
Bienvenidos a la guía técnica del "Sitio de Comercio Electrónico de Mantenimiento Cero". Este proyecto es un sitio web de comercio electrónico conceptual que automatiza el raspado de productos de plataformas de comercio electrónico importantes. Está diseñado para apoyar a los negocios de dropshipping mostrando productos que no poseen, facilitando su expansión del catálogo de productos sin esfuerzo manual. Esta solución consta de dos componentes principales: `scrapping.py` y `logic.py`, ambos parte del sistema de backend. La aplicación `scrapping.py` se encarga del raspado de productos, proporcionando APIs para varios tipos de productos, mientras que `logic.py` gestiona operaciones relacionadas con los usuarios, como inicio/cierre de sesión, registro, compras, etc. Todos los datos se almacenan y gestionan en una base de datos SQL Azure.

## PARTE 1.1 Solución de Backend - Lógica de Raspado

### Configuración y Requisitos Previos:
- Python 3.x
- Flask
- BeautifulSoup4
- Requests
- Flask-CORS
- pyODBC

### Instalación:
1. Instalar Python 3.x desde el sitio web de Python.
2. Instalar las bibliotecas necesarias usando pip.

### Comprensión de la Lógica de Raspado:
La lógica de raspado se implementa en `scrapping.py`. Es responsable de raspar datos de productos de categorías predefinidas de comercio electrónico y servir estos datos a través de APIs de Flask.

### Componentes Clave:
1. **Configuración de Flask**: La aplicación Flask se configura con CORS (Cross-Origin Resource Sharing) habilitado para permitir solicitudes de diferentes orígenes.
2. **Función de Raspado de Categoría**: Esta función (`scrape_category`) toma una URL de categoría y su nombre, raspa la página web para obtener datos de productos y devuelve una lista de diccionarios de productos.
3. **Puntos de API**:
   - `/api/search`: Permite buscar productos en todas las categorías basándose en una cadena de consulta.
   - `/api/<nombre_de_categoría>`: Recupera todos los productos para una categoría específica.
   - `/api/all`: Fusiona todos los datos de productos de otras APIs en una sola respuesta JSON.
4. **Conexión de Base de Datos**: La función `get_db_connection` configura la conexión a la base de datos SQL Azure usando variables de entorno para la seguridad.

### Uso de la API:
- **Buscar Productos**: Para buscar productos, envíe una solicitud GET a `/api/search` con un parámetro de consulta, por ejemplo, `http://localhost:5020/api/search?query=teléfono`.
- **Obtener Datos de Categoría**: Para obtener datos de una categoría específica, envíe una solicitud GET a `/api/<nombre_de_categoría>`, por ejemplo, `http://localhost:5020/api/phones%20&%20accessories`.
- **Obtener Todos los Productos**: Para obtener todos los productos de todas las categorías, envíe una solicitud GET a `/api/all`.

### Pasos Siguientes:
En las siguientes secciones, profundizaremos en el script `logic.py`, que maneja la lógica operacional del sitio web, incluyendo la autenticación de usuarios, el procesamiento de pedidos y otras funcionalidades.

## PARTE 1.2 Solución de Backend - Lógica Operativa (`logic.py`)

### Visión General:
El script `logic.py` es una parte crucial del backend, gestionando interacciones de usuarios, autenticación y operaciones de compra. Está construido usando Flask e integra con una base de datos SQL Azure.

### Funciones Clave:
1. **Autenticación de Usuario**: Incluye endpoints para inicio y cierre de sesión de usuarios y registro.
2. **Registro de Administrador**: Endpoint especial para registrar usuarios administradores.
3. **Manejo de Compras**: Endpoint para procesar solicitudes de compra.
4. **Verificación de Disponibilidad de Nombre de Usuario**: Endpoint para verificar si un nombre de usuario ya está tomado.

### Configuración y Requisitos Previos:
- Asegurarse de que todas las dependencias requeridas estén instaladas, similar a `scrapping.py`.

### Ejecución del Script:
- Ejecutar `logic.py` como una aplicación Flask:


### Funcionalidad Detallada:
- **Endpoint de Inicio de Sesión (`/login`)**: Valida credenciales de usuario y establece una sesión de usuario tras un inicio de sesión exitoso.
- **Endpoint de Cierre de Sesión (`/logout`)**: Elimina los detalles de la sesión de usuario, cerrando la sesión efectivamente.
- **Endpoint de Registro (`/register`)**: Maneja el registro de nuevos usuarios, valida entradas y verifica nombres de usuario existentes.
- **Endpoint de Registro de Administrador (`/register_admin`)**: Específicamente para registrar usuarios administradores.

### Prácticas Recomendadas en `logic.py`:
- **Seguridad**: Las contraseñas se cifran usando `generate_password_hash` de Werkzeug.
- **Gestión de Sesiones**: Se utilizan sesiones de Flask para rastrear a los usuarios que han iniciado sesión.
- **Manejo de Errores**: Manejo adecuado de errores y formateo de respuestas para endpoints de API.
- **Conexiones de Base de Datos**: Las conexiones de base de datos se gestionan usando administradores de contexto para la seguridad.
- **Validación de Entradas**: Cheques básicos de validación para las entradas de usuario.
- **Soporte CORS**: CORS configurado para integración con el frontend.

### Pasos Siguientes:
Nos sumergiremos en la estructura y el esquema de la base de datos.

## PARTE 1.3 Solución de Backend - Estructura de Base de Datos (Azure SQL DB)

### Visión General:
La base de datos SQL Azure es una parte fundamental del Sitio de Comercio Electrónico de Mantenimiento Cero, almacenando todos los datos necesarios para gestionar pedidos, productos e información de usuarios. Desglosemos la estructura de la base de datos y sus diversas tablas.

### Tablas de Base de Datos y Relaciones:
1. **dbo.order_products**:
   - **Descripción**: Enlaza pedidos con productos y almacena la cantidad y el precio de cada producto en un pedido.
   - **Campos**: order_id, product_id, quantity, price.
2. **dbo.user_info**:
   - **Descripción**: Almacena información sobre los usuarios que realizan pedidos.
   - **Campos**: user_info_id, name, address, email, phone.
3. **dbo.users**:
   - **Descripción**: Gestiona cuentas de usuario, incluyendo autenticación y estado de administrador.
   - **Campos**: user_id, username, password, is_admin.
4. **dbo.products**:
   - **Descripción**: Contiene información sobre los productos disponibles para la venta.
   - **Campos**: product_id, title, picture_url, price, link.
5. **dbo.orders**:
   - **Descripción**: Gestiona los pedidos realizados por los usuarios.
   - **Campos**: order_id, user_id, order_date, delivery_status, product_url, user_info_id.

### Relaciones:
- **dbo.order_products** se relaciona con **dbo.orders** y **dbo.products** a través de order_id y product_id respectivamente.
- **dbo.orders** tiene una relación de clave foránea con **dbo.users** a través de user_id y con **dbo.user_info** a través de user_info_id.

### Schema:

### Consideraciones:
- **Normalización**: La base de datos está diseñada para evitar redundancias y asegurar la integridad de los datos.
- **Indexación**: Indexación adecuada en claves primarias y foráneas para optimización del rendimiento.
- **Seguridad**: Datos sensibles como contraseñas se almacenan en forma cifrada.
- **Escalabilidad**: La estructura es escalable y puede manejar un aumento en el volumen de datos a medida que el sitio de comercio electrónico crece.

### Pasos Siguientes:
Con la estructura de la base de datos definida, podemos proceder a la especificación del Front-End.

## PARTE 2.1 Solución Frontend - Visión General del HTML de Inicio

### Estructura del HTML de Inicio
La página de inicio sirve como la página principal de tu "Sitio de Comercio Electrónico sin Mantenimiento". Está diseñada con un enfoque en la experiencia del usuario y la consistencia de la marca.

#### Componentes Clave:
1. **Sección de Encabezado**: Reservada para elementos de navegación y marca. El encabezado se carga a través de un marcador de posición `<div id="header-placeholder"></div>`.
2. **Área de Contenido Principal**:
   - **Sección de Productos Destacados**: Destaca productos seleccionados. Los productos se cargarán dinámicamente en esta sección.
   - **Sección de Carrusel**: Muestra productos o promociones en un formato de carrusel deslizante. Esto se maneja dinámicamente con JavaScript.
3. **Pie de página**: Contiene derechos de autor y cualquier información adicional relevante para tu sitio.
4. **Recursos Externos**:
   - **IndexStyles.css**: Este archivo CSS asegura que la página se adhiera a tus directrices de marca cohesivas.
   - **script.js**: Centraliza varios scripts utilizados en diferentes páginas para una funcionalidad consistente.

#### Elementos Clave del HTML
- Etiquetas `<meta>` para el conjunto de caracteres y diseño responsivo.
- `<link>` para adjuntar el archivo CSS.
- Etiquetas `<script>` para la integración de JavaScript, tanto en línea para acciones específicas como externas para funcionalidad compartida.

#### Integración de JavaScript
- Un fragmento de JavaScript en línea da la bienvenida a los usuarios si su nombre de usuario está almacenado en localStorage.
- `script.js` se enlaza en la parte inferior del cuerpo, asegurando que todos los elementos HTML se carguen antes de cualquier manipulación con JavaScript.

#### Estilo y Responsividad
- CSS se utiliza no solo para la marca, sino también para asegurar que la página sea responsiva y accesible en varios dispositivos.
- Los controles del carrusel (anterior y siguiente) están integrados en el diseño para la interacción del usuario.

#### Mejores Prácticas
- **Separación de Preocupaciones**: HTML para estructura, CSS para estilo y JavaScript para interactividad.
- **Accesibilidad**: Uso adecuado de elementos semánticos HTML y diseño responsivo.
- **Optimización del Rendimiento**: Cargar JavaScript al final del cuerpo para un renderizado de página más rápido.
- **Experiencia del Usuario**: Características atractivas como carruseles y saludos personalizados mejoran la experiencia del usuario.

#### Próximos Pasos
Nos adentraremos en otras páginas como la de Registro, Consola de Administración, Páginas de Filtro de Productos y sus respectivos estilos CSS para mantener una marca cohesiva en todo el sitio.

## PARTE 2.2 Solución Frontend - Visión General del HTML del Encabezado

### Estructura del HTML del Encabezado
El HTML del Encabezado es un componente crucial de tu "Sitio de Comercio Electrónico sin Mantenimiento", proporcionando una experiencia de navegación consistente y amigable en todas las páginas.

#### Componentes Clave:
1. **Logotipo**: Una imagen clickeable que redirige a los usuarios a la página de inicio (index.html). Mejora el reconocimiento de la marca.
2. **Barra de Búsqueda**: Permite a los usuarios buscar productos. Esencial para una experiencia de usuario fluida en un entorno de comercio electrónico.
3. **Enlaces de Navegación**: Acceso rápido a la página de Inicio y a la página de Filtro de Productos. Estos enlaces aseguran una navegación fácil a través del sitio.
4. **Botón de Inicio/Cierre de Sesión**: Un botón dinámico para la autenticación del usuario, que alterna entre 'Iniciar Sesión' y 'Cerrar Sesión' basado en el estado del usuario.
5. **Modal de Inicio de Sesión**:
   - Interfaz con Pestañas: Separa 'Inicio de Sesión de Usuario' y 'Inicio de Sesión de Administrador' para mayor claridad.
   - Formulario de Inicio de Sesión de Usuario: Recopila nombre de usuario y contraseña para el inicio de sesión del usuario.
   - Formulario de Inicio de Sesión de Administrador: Similar al de inicio de sesión de usuario, pero para administradores.

   - Enlace de Registro: Redirige a los nuevos usuarios a la página de registro.

6. **Recursos Externos**:
   - **HeaderStyles.css**: Estiliza los componentes del encabezado para un diseño consistente en todo el sitio.
   - **script.js**: Maneja las funcionalidades dinámicas como abrir y cerrar modales, manejar el inicio de sesión, etc.

#### Elementos y Atributos HTML
- Etiquetas `<meta>` para asegurar una codificación de caracteres adecuada y responsividad.
- `<link>` para adjuntar la hoja de estilos CSS.
- `<nav>` para una estructuración semántica de enlaces de navegación.
- Manejadores de eventos `onclick` de JavaScript para elementos interactivos como botones e íconos de cierre de modal.

#### Integración de JavaScript y CSS
- El encabezado depende de `script.js` para manejar interacciones de usuario, particularmente para el modal de inicio de sesión y la interfaz de inicio de sesión con pestañas.
- `HeaderStyles.css` asegura que la apariencia y sensación del encabezado estén alineadas con la marca general del sitio.

#### Mejores Prácticas
- **Diseño Modular**: El encabezado es separado, permitiendo una fácil inclusión en diferentes páginas sin redundancia.
- **Diseño Responsivo**: Asegura que el encabezado sea funcional y visualmente atractivo en diferentes dispositivos.
- **Accesibilidad**: Uso de HTML semántico y etiquetas alt para imágenes.
- **Interfaz Amigable para el Usuario**: Navegación intuitiva y clara distinción entre inicio de sesión de usuario y administrador.

#### Próximos Pasos
La siguiente fase involucrará detallar otras páginas como la de Registro, Consola de Administración y Página de Filtro de Productos, asegurando que todas se integren sin problemas con el encabezado para una experiencia de usuario consistente.

## PARTE 2.3 Solución Frontend - Visión General del HTML de Registro

### Estructura del HTML de Registro
La página de Registro es un componente esencial para la incorporación de usuarios en tu "Sitio de Comercio Electrónico sin Mantenimiento". Diseñada para ser amigable con el usuario y segura, asegura un proceso de registro fluido para los nuevos usuarios.

#### Componentes Clave:
1. **Espacio Reservado para el Encabezado**: Para incluir el encabezado común en todo el sitio para una navegación consistente.
2. **Área de Contenido Principal**:
   - **Modal/Formulario de Registro**: El enfoque principal de esta página donde los usuarios pueden crear sus cuentas.
   - Campos del formulario para nombre de usuario y contraseña, junto con un campo de confirmación de contraseña.
3. **Casilla de Verificación de Términos y Condiciones**: Un componente necesario para el cumplimiento legal y el acuerdo del usuario.
4. **Manejo de Envío del Formulario de Registro**: Un fragmento de JavaScript para gestionar el proceso de envío del formulario, incluyendo la validación e interacción con el backend.

5. **Recursos Externos**:
   - **RegisterStyle.css**: Proporciona un estilo específico para la página de registro, asegurando que se alinee con el diseño general del sitio.
   - **script.js**: Incluye funcionalidades compartidas de JavaScript que podrían usarse en diferentes páginas.

#### Elementos y Atributos HTML
- `<form>` con id="registration-form" para un fácil acceso con JavaScript.
- Campos de entrada para nombre de usuario, contraseña y confirmación de contraseña.
- Una casilla de verificación para el acuerdo de términos y condiciones.
- Un botón de envío para enviar los datos del formulario.

#### Integración de JavaScript
- JavaScript en línea para manejar los envíos de formularios, incluyendo:
  - Prevenir la acción de envío del formulario por defecto.
  - Validar la coincidencia de las contraseñas.
  - Realizar una solicitud POST al endpoint `/register` del backend.
  - Manejar la respuesta y proporcionar retroalimentación al usuario.
  - Redirigir al usuario tras un registro exitoso.

#### Estilo y Experiencia del Usuario
- El archivo CSS `RegisterStyle.css` asegura que el formulario de registro sea visualmente atractivo y fácil de usar.
- Los campos del formulario y los botones están diseñados para una mejor experiencia del usuario y consistencia visual con el resto del sitio.

#### Mejores Prácticas
- **Validación del Lado del Cliente**: JavaScript se utiliza para la validación inicial, como la coincidencia de contraseñas, mejorando la experiencia del usuario y reduciendo las solicitudes innecesarias al servidor.
- **Seguridad**: Las contraseñas se manejan de manera segura, sin exponerse en la URL ni de ninguna manera insegura.
- **Retroalimentación al Usuario**: Se muestran mensajes claros al usuario en caso de registro exitoso o errores.
- **Diseño Responsivo**: Asegura que la página de registro sea accesible y funcional en varios dispositivos.

#### Próximos Pasos
Con la página de registro configurada, el siguiente paso implica integrarla con el backend para manejar los datos del usuario de manera segura y eficiente. Además, será crucial enfocarse en otras páginas como la Consola de Administración y la página de Filtro de Productos para la funcionalidad completa del sitio.

## PARTE 2.4 Solución Frontend - Visión General del HTML de Filtro de Productos

### Estructura del HTML de Filtro de Productos
La página de Filtro de Productos es una característica clave de tu "Sitio de Comercio Electrónico sin Mantenimiento", permitiendo a los usuarios navegar y filtrar productos según sus preferencias.

#### Componentes Clave:
1. **Barra de Categoría**:
   - Lista dinámicamente poblada de categorías para que los usuarios elijan.
   - Escuchadores de eventos de JavaScript para resaltar la categoría seleccionada.
2. **Modales de Producto y Compra**:
   - **Modal de Producto**: Muestra detalles del producto y un botón de 'Comprar Ahora'.
   - **Modal de Compra**: Facilita el proceso de pedido, incluyendo un formulario para detalles del usuario y un botón de confirmación.
3. **Barra de Filtro**:
   - Un control deslizante de rango y entrada para filtrar por precio.
   - Un desplegable para ordenar los productos (p.ej., por precio).

4. **Rejilla de Productos**:
   - El área principal donde los productos se cargarán dinámicamente.
   - Incluye una pantalla de carga con una animación para mejorar la experiencia del usuario durante la obtención de datos.

5. **Recursos Externos**:
   - **ProductFilterStyles.css**: CSS personalizado para estilizar la página de filtro de productos.
   - **script.js**: Contiene funcionalidades compartidas de JavaScript en todo el sitio.
# Elementos y Atributos HTML
- **Barra de navegación** (`<nav>`) con una lista desordenada (`<ul>`) para categorías.
- Elementos de entrada para filtrar y ordenar productos.
- Contenedores Div para modales, incluyendo detalles de productos y formularios de compra.
- Área de contenido principal para mostrar la rejilla de productos.

# Integración de JavaScript
- Se utilizan scripts de JavaScript en línea para:
  - Manejar la selección de categorías y su resaltado.
  - Abrir y cerrar modales.
  - Redirigir a la página de registro cuando sea necesario.
  - Funciones definidas para actualizar el rango de precios y ordenar productos.

# Estilo y Experiencia del Usuario
- El archivo CSS asegura que la página sea visualmente atractiva y mantenga la consistencia con la marca del sitio.
- El diseño está pensado para ser intuitivo y fácil de navegar, mejorando la experiencia de compra del usuario.

# Mejores Prácticas
- **Diseño Modular**: Separa preocupaciones al tener secciones distintas para categorías, filtros y visualización de productos.
- **Interacción del Usuario**: Mejora el compromiso a través de elementos interactivos como modales, controles deslizantes de rango y opciones de ordenamiento.
- **Optimización del Rendimiento**: Incluye una pantalla de carga para manejar las expectativas del usuario durante la recuperación de datos.
- **Accesibilidad y Responsividad**: Asegura que la página sea accesible y utilizable en diferentes dispositivos.

# PARTE 3.1 Solución Frontend - Visión General de JavaScript
## Interacción del Encabezado
- **Carga Dinámica del Encabezado**: El script obtiene `header.html` e inyecta en `#header-placeholder`. Este enfoque asegura la consistencia del encabezado en diferentes páginas.
- **Escuchador del Botón de Búsqueda**: El script adjunta un escuchador de eventos al botón de búsqueda, activando la función `handleSearch` al hacer clic.

## Modal de Inicio de Sesión e Interacción de Pestañas
- **Control de Pestañas en Modales**: Las funciones `openTab`, `openLoginModal` y `closeLoginModal` gestionan la visualización de formularios de inicio de sesión de usuario y administrador dentro de un modal.
- **Escuchador de Clic Global**: Cierra modales al hacer clic fuera, mejorando la UX.

## Funcionalidad de Inicio y Cierre de Sesión
- **Manejo de Envío de Formulario**: `handleUserLogin` y `handleAdminLogin` previenen el envío predeterminado del formulario, recopilan datos del formulario y llaman a `sendLoginRequest`.
- **Solicitud de Inicio de Sesión**: Envía solicitudes AJAX al endpoint `/login` del backend, manejando los inicios de sesión de usuario y administrador por separado.
- **Gestión de Sesión**: Utiliza `sessionStorage` para mantener los estados de inicio de sesión y la información del usuario.

## Filtrado y Visualización de Productos
- **Selección de Categoría**: Población de categorías y adjunta escuchadores de eventos para la selección de categorías.
- **Obtención y Visualización de Productos**: Las funciones `fetchAllProducts` y `fetchProducts` obtienen productos del backend y los muestran en la rejilla de productos.
- **Filtrado y Ordenamiento de Precios**: `updatePriceRange` y `sortProducts` permiten a los usuarios filtrar y ordenar productos según el precio.

## Modales para Detalles de Productos y Compra
- **Modal de Detalles del Producto**: Muestra detalles del producto en un modal al hacer clic en un producto.
- **Modal de Compra**: Gestiona el proceso de compra, incluyendo el formulario de detalles del usuario y la presentación del pedido.

## Funcionalidad del Carrusel
- **Carrusel de Productos Destacados**: Carga dinámicamente productos destacados en un carrusel.
- **Navegación del Carrusel**: Las funciones `showSlides` y `nextSlide` controlan la visualización y navegación de los elementos del carrusel.

## Misceláneos
- **MutationObserver**: Monitorea cambios en el DOM para actualizar dinámicamente el botón de inicio/cierre de sesión.
- **Escuchadores de Eventos**: Varios escuchadores de eventos para interacciones de modales, envíos de formularios e interacciones de usuario.

## Mejores Prácticas
- **Funciones Modulares**: Cada funcionalidad está encapsulada en funciones específicas, haciendo que el código sea organizado y mantenible.
- **Operaciones Asíncronas**: Utiliza `async/await` para la obtención de datos, mejorando la legibilidad y el manejo del código asíncrono.
- **Manejo de Errores**: Incluye un manejo de errores adecuado para solicitudes de red e interacciones del DOM.
- **Experiencia del Usuario**: Se enfoca en interacciones de usuario suaves e intuitivas a través de controles modales, carga dinámica de contenido y características responsivas como el carrusel.

# Reflexiones Finales sobre la Guía del Sitio de Comercio Electrónico sin Mantenimiento
Al concluir esta guía, es esencial reconocer que, aunque el **Sitio de Comercio Electrónico sin Mantenimiento** es actualmente un concepto de prueba, encarna un paso significativo hacia la realización de un modelo de dropshipping casi sin esfuerzo a través del web scraping.

## Etapa de Concepto de Prueba
- El proyecto, en esta etapa, es un testimonio del pensamiento innovador en el espacio del comercio electrónico.
- La idea de automatizar el scraping de productos para facilitar un negocio de dropshipping con mínimo esfuerzo es ambiciosa y visionaria.
- Este concepto de prueba sienta las bases para un enfoque potencialmente transformador para el comercio minorista en línea.

## Reconociendo las Brechas
- **Consideraciones Legales y Éticas**: Asegurar el cumplimiento con las leyes de scraping de datos y los términos de servicio de los sitios web fuente.
- **Escalabilidad y Fiabilidad**: Mientras el concepto funciona a menor escala, es crucial probar y asegurar la escalabilidad y fiabilidad a medida que crece la base de usuarios y las listas de productos.
- **Refinamiento de la Experiencia del Usuario**: Se necesita desarrollo y pruebas de usuario continuos para refinar la UX, haciéndola intuitiva y fluida.
- **Integración con Pasarelas de Pago**: Para un sitio de comercio electrónico completamente funcional, la integración de pagos segura y fiable es imprescindible.
- **Manejo Robusto de Errores y Registro**: Esencial para diagnosticar problemas en un entorno en vivo.

## Direcciones Futuras
- **Explorar Técnicas de Scraping Avanzadas**: Adaptarse al contenido dinámico e implementar algoritmos de scraping más sofisticados.
- **Desarrollar una Infraestructura de Backend Robusta**: Asegurar que el backend pueda manejar cargas aumentadas y procesamiento de datos complejo.
- **Mejorar Medidas de Seguridad**: Particularmente en torno a datos de usuario y transacciones.
- **Automatizar Actualizaciones de Contenido**: Implementar mecanismos para mantener las listas de productos actuales y precisas.
