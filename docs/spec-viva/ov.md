# FlowSync — Especificación de Comportamiento: Cuentas y Acceso

## Purpose
Cuentas y Acceso permite que cada persona se registre en FlowSync, inicie sesión con email y contraseña, y mantenga una sesión autenticada mediante una credencial de acceso. Con esa sesión consulta su perfil (nombre, email e iniciales) y puede cerrarla, de modo que el resto de la aplicación de gestión de tareas de equipo solo es accesible para usuarios identificados.

## Requirements

### Requirement: Registro de cuenta
El sistema SHALL permitir crear una cuenta indicando email, contraseña, confirmación de contraseña y, opcionalmente, nombre completo, y SHALL devolver el usuario creado junto con una credencial de acceso para iniciar sesión de inmediato.

#### Scenario: Registro con todos los datos
- **WHEN** un visitante envía un email no registrado, una contraseña válida, su confirmación idéntica y un nombre completo
- **THEN** el sistema crea la cuenta y devuelve el usuario y una credencial de acceso

#### Scenario: Registro sin nombre completo
- **WHEN** un visitante envía un email no registrado, una contraseña válida y su confirmación idéntica, omitiendo el nombre o ingresando únicamente espacios
- **THEN** el sistema crea la cuenta almacenando el nombre vacío y devuelve el usuario junto con una credencial de acceso


### Requirement: Validación del email en el registro
El sistema SHALL exigir un email con formato válido, de máximo 254 caracteres y no registrado previamente, e informar del error en el propio campo; cuando esté duplicado, el mensaje SHALL ser "Ese email ya está registrado. Inicia sesión en su lugar."

#### Scenario: Email duplicado
- **WHEN** un visitante envía el formulario de registro con un email que ya pertenece a otra cuenta
- **THEN** el sistema responde con estado 422, no crea la cuenta y la interfaz muestra "Ese email ya está registrado. Inicia sesión en su lugar." bajo el campo del email

#### Scenario: Email con formato inválido
- **WHEN** un visitante envía el formulario de registro con un texto que no tiene formato de email
- **THEN** el sistema responde con estado 422 y la interfaz muestra "Introduce una dirección de email válida." bajo el campo del email


### Requirement: Validación de la contraseña en el registro
El sistema SHALL exigir una contraseña de entre 8 y 32 caracteres y SHALL rechazar el registro, con el mensaje "Las contraseñas no coinciden", si la confirmación no es igual a la contraseña.

#### Scenario: Contraseña fuera de longitud
- **WHEN** un visitante intenta registrarse con una contraseña de menos de 8 o más de 32 caracteres
- **THEN** el sistema rechaza el registro y muestra un error de longitud en el campo de la contraseña

#### Scenario: Confirmación distinta
- **WHEN** un visitante intenta registrarse con una confirmación que no es igual a la contraseña antes de enviar la solicitud al servidor
- **THEN** la interfaz detiene el envío y muestra "Las contraseñas no coinciden" en el campo de confirmación


### Requirement: Formularios incompletos
El sistema SHALL rechazar con estado 422 los envíos de registro o de inicio de sesión a los que les falten campos obligatorios, y la interfaz SHALL indicar bajo cada campo vacío "Falta rellenar [campo]."

#### Scenario: Registro con todos los campos obligatorios vacíos
- **WHEN** un visitante envía el formulario de registro sin email, sin contraseña y sin confirmación
- **THEN** el sistema responde con estado 422, no crea la cuenta y la interfaz muestra un mensaje "Falta rellenar…" bajo cada uno de esos campos

#### Scenario: Registro sin confirmación de contraseña
- **WHEN** un visitante envía el formulario de registro con email y contraseña válidos pero sin confirmación
- **THEN** el sistema rechaza el registro y la interfaz muestra "Falta rellenar la confirmación de la contraseña." bajo el campo de confirmación

#### Scenario: Inicio de sesión sin contraseña
- **WHEN** un visitante envía el formulario de inicio de sesión con email pero sin contraseña
- **THEN** el sistema responde con estado 422 y la interfaz muestra "Falta rellenar la contraseña." bajo el campo de la contraseña

#### Scenario: Inicio de sesión sin email
- **WHEN** un visitante envía el formulario de inicio de sesión con contraseña pero sin email
- **THEN** el sistema responde con estado 422 y la interfaz muestra "Falta rellenar el email." bajo el campo del email


### Requirement: Errores de validación por campo
El sistema SHALL responder con estado 422 ante datos inválidos, y la interfaz SHALL mostrar los mensajes en castellano junto a cada campo afectado.

#### Scenario: Varios campos inválidos
- **WHEN** un visitante envía el formulario de registro con más de un campo inválido
- **THEN** el sistema responde con estado 422 y la interfaz muestra en castellano el mensaje junto a cada campo afectado


### Requirement: Inicio de sesión
El sistema SHALL permitir iniciar sesión con email y contraseña y SHALL devolver el usuario autenticado junto con una credencial de acceso.

#### Scenario: Inicio de sesión correcto
- **WHEN** un visitante con una cuenta existente envía su email y su contraseña correctos
- **THEN** el sistema devuelve el usuario y una credencial de acceso, y la interfaz lo lleva a su perfil


### Requirement: Rechazo de credenciales incorrectas
El sistema SHALL responder con estado 400 y el mensaje "El email o la contraseña no son correctos." cuando las credenciales no coincidan, sin revelar cuál de los dos datos es erróneo, y la interfaz SHALL mostrarlo como aviso general del formulario.

#### Scenario: Contraseña incorrecta
- **WHEN** un visitante envía el formulario de inicio de sesión con un email registrado y una contraseña equivocada
- **THEN** el sistema responde con estado 400 y la interfaz muestra "El email o la contraseña no son correctos." como aviso general del formulario

#### Scenario: Email inexistente
- **WHEN** un visitante envía el formulario de inicio de sesión con un email que no pertenece a ninguna cuenta
- **THEN** el sistema responde con estado 400 y la interfaz muestra el mismo mensaje, indistinguible del caso de contraseña incorrecta

#### Scenario: Email con formato inválido en el login
- **WHEN** un visitante envía el formulario de inicio de sesión con un texto que no tiene formato de email
- **THEN** el sistema responde con estado 422 y la interfaz muestra "Introduce una dirección de email válida." bajo el campo del email


### Requirement: Errores de servidor y de conexión
La interfaz SHALL mostrar un aviso general comprensible cuando el servidor falle o no esté disponible, sin perder lo que el usuario ha escrito.

#### Scenario: Servidor no disponible
- **WHEN** un visitante envía el formulario de registro o de inicio de sesión y no es posible conectar con el servidor
- **THEN** la interfaz muestra "No se pudo conectar con el servidor. Comprueba que el backend está arrancado." y el botón de envío vuelve a estar disponible

#### Scenario: Error interno del servidor
- **WHEN** un visitante envía el formulario y el servidor responde con un error interno
- **THEN** la interfaz muestra "Algo ha ido mal en el servidor. Inténtalo de nuevo en un momento." y el botón de envío vuelve a estar disponible


### Requirement: Acceso autenticado mediante credencial
El sistema SHALL exigir una credencial de acceso válida para consultar el perfil y cerrar sesión, y SHALL responder con estado 401 cuando falte o no sea válida.

#### Scenario: Petición sin credencial
- **WHEN** se solicita el perfil o el cierre de sesión sin enviar la credencial de acceso
- **THEN** el sistema responde con estado 401

#### Scenario: Petición con credencial inválida
- **WHEN** se solicita el perfil con una credencial revocada, caducada o inexistente
- **THEN** el sistema responde con estado 401


### Requirement: Consulta del perfil
El sistema SHALL permitir al usuario autenticado consultar su perfil con identificador, nombre completo (que puede estar vacío), email, iniciales y fechas de creación y actualización.

#### Scenario: Perfil de usuario con nombre
- **WHEN** un usuario autenticado con nombre completo solicita su perfil
- **THEN** el sistema devuelve identificador, nombre completo, email, iniciales y fechas de creación y actualización

#### Scenario: Perfil de usuario sin nombre
- **WHEN** un usuario autenticado sin nombre completo solicita su perfil
- **THEN** el sistema devuelve el nombre vacío y unas iniciales calculadas a partir de su email


### Requirement: Visualización del perfil
La interfaz SHALL mostrar en la página de perfil un avatar con las iniciales, el nombre completo, el email, la fecha de alta y un botón para cerrar sesión.

#### Scenario: Página de perfil
- **WHEN** un usuario autenticado abre la página de perfil
- **THEN** la interfaz muestra el avatar con sus iniciales, su nombre completo, su email, la fecha de alta y el botón de cerrar sesión


### Requirement: Cierre de sesión
El sistema SHALL permitir al usuario cerrar sesión revocando su credencial de acceso, y la interfaz SHALL eliminar la sesión local y devolver al usuario a la pantalla de inicio de sesión aunque el servidor responda con error.

#### Scenario: Cierre de sesión desde el perfil
- **WHEN** un usuario autenticado pulsa el botón de cerrar sesión
- **THEN** la interfaz elimina la sesión local de inmediato y redirige a la página de inicio de sesión, intentando revocar la credencial en el servidor

#### Scenario: Credencial revocada tras cerrar sesión
- **WHEN** se solicita el perfil con la credencial de una sesión ya cerrada
- **THEN** el sistema responde con estado 401


### Requirement: Persistencia de la sesión
La interfaz SHALL conservar la sesión al recargar la aplicación y SHALL validarla contra el perfil del usuario al arrancar.

#### Scenario: Recarga con sesión activa
- **WHEN** un usuario con sesión válida recarga la aplicación o la abre de nuevo en el mismo navegador
- **THEN** la interfaz valida la sesión consultando su perfil y lo mantiene autenticado sin pedirle credenciales


### Requirement: Sesión caducada o invalida
La interfaz SHALL descartar la sesión y mostrar el mensaje "Tu sesión ha caducado" cuando el servidor rechace la credencial con estado 401, y SHALL informar al usuario si ocurre un fallo de red al validar la sesión conservando la credencial almacenada.

#### Scenario: Credencial rechazada al arrancar
- **WHEN** la aplicación arranca con una credencial guardada y el servidor la rechaza con estado 401
- **THEN** la interfaz descarta la sesión, muestra "Tu sesión ha caducado. Vuelve a iniciar sesión." y redirige al inicio de sesión

#### Scenario: Fallo de red al arrancar
- **WHEN** la aplicación arranca con una credencial guardada y la validación falla por un error de conectividad
- **THEN** la interfaz deja al usuario en estado no autenticado indicando el motivo en el inicio de sesión, pero conserva la credencial guardada para reintentar cuando se restablezca la red


### Requirement: Protección de páginas privadas
La interfaz SHALL redirigir a los usuarios sin sesión activa a la página de inicio de sesión al intentar acceder a páginas privadas y SHALL mostrar un indicador de carga mientras se verifica la sesión; la API SHALL bloquear con estado 401 los recursos privados sin credencial válida.

#### Scenario: Acceso anónimo al perfil
- **WHEN** un visitante sin sesión intenta abrir directamente la página de perfil
- **THEN** la interfaz lo redirige a la página de inicio de sesión sin mostrar datos de ningún usuario

#### Scenario: Sesión caducada al abrir una página privada
- **WHEN** un usuario con una credencial guardada ya revocada o caducada abre la página de perfil
- **THEN** la interfaz descarta la sesión, lo redirige al inicio de sesión y muestra "Tu sesión ha caducado. Vuelve a iniciar sesión."

#### Scenario: Verificación de sesión en curso
- **WHEN** un usuario abre la página de perfil mientras la aplicación aún está verificando su sesión
- **THEN** la interfaz muestra un indicador de carga a pantalla completa hasta conocer el resultado, sin redirigir antes de tiempo

#### Scenario: Consulta del perfil por API sin sesión
- **WHEN** se solicita el perfil por API sin credencial de acceso o con una credencial inválida
- **THEN** el sistema responde con estado 401 y no devuelve datos del usuario

#### Scenario: Cierre de sesión por API sin sesión
- **WHEN** se solicita el cierre de sesión por API sin credencial de acceso válida
- **THEN** el sistema responde con estado 401


### Requirement: Páginas solo para anónimos
La interfaz SHALL redirigir a la página de perfil a los usuarios ya autenticados que intenten acceder al inicio de sesión o al registro.

#### Scenario: Usuario autenticado abre el login
- **WHEN** un usuario con sesión activa intenta abrir la página de inicio de sesión
- **THEN** la interfaz lo redirige a la página de perfil

#### Scenario: Usuario autenticado abre el registro
- **WHEN** un usuario con sesión activa intenta abrir la página de registro
- **THEN** la interfaz lo redirige a la página de perfil


### Requirement: Formularios de acceso
La interfaz SHALL ofrecer una página de inicio de sesión con email y contraseña y una página de registro con nombre completo (marcado como opcional), email, contraseña y confirmación de contraseña.

#### Scenario: Formulario de inicio de sesión
- **WHEN** un visitante sin sesión abre la página de inicio de sesión
- **THEN** la interfaz muestra un formulario con los campos email y contraseña

#### Scenario: Formulario de registro
- **WHEN** un visitante sin sesión abre la página de registro
- **THEN** la interfaz muestra un formulario con nombre completo marcado como opcional, email, contraseña y confirmación de contraseña


---

## Parte B: Las tres listas

### 1. Conteo de requisitos
- Requisitos escritos por el agente: 15
- Requisitos comprobados abriendo el código: 12

### 2. Incoherencias detectadas
- Mensajes de error por campo vacío: el frontend valida algunos campos en cliente con mensajes propios, mientras que el backend retorna un formato genérico de error de validación cuando se le consulta directamente.
- Comportamiento al cerrar sesión con fallo de servidor: la pantalla destruye la sesión local inmediatamente ante la acción del usuario, aunque la petición HTTP de revocación enviada al backend falle o devuelva un error de red.
- Manejo de token inválido en el arranque: al cargar la aplicación con una credencial vencida se limpia la sesión y se muestra la alerta de caducidad, pero si el fallo es por desconexión de red se conserva el token en almacenamiento local.

### 3. Comportamiento ambiguo (Bug vs. Contrato)
- No fue posible determinar si el registro de usuario con el campo de nombre compuesto únicamente por espacios en blanco debía ser rechazado como un error de validación de formulario (bug de validación) o si el comportamiento intencional del sistema es sanitizar la entrada guardando una cadena vacía en la base de datos (contrato).
