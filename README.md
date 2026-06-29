🤖 DeliveryBot — Bot de pedidos para cafetería

Bot de Telegram que permite hacer pedidos en una cafetería de forma conversacional, construido con n8n y Google Sheets como backend en tiempo real. El usuario abre el chat, navega el menú por categorías, arma su carrito y confirma el pedido, todo sin salir de Telegram.


¿Qué hace?

DeliveryBot atiende a los clientes paso a paso:


Da la bienvenida y muestra las categorías del menú.
Lista los productos de cada categoría con su precio.
Permite agregar productos al carrito y revisarlo en cualquier momento.
Registra cada pedido en una hoja de cálculo para que el negocio lo gestione.
Controla el horario de atención: fuera de horario, avisa que está cerrado en vez de tomar pedidos.


Toda la información (menú, sesiones de cada usuario y pedidos) vive en Google Sheets, así que el negocio puede actualizar el menú sin tocar el código.


🛠️ Tecnologías

HerramientaPara qué se usan8nOrquestación de todo el flujo conversacional (el "cerebro" del bot)Telegram Bot APICanal de chat con el usuarioGoogle SheetsBase de datos: menú, sesiones y pedidosngrokExponer el webhook local de n8n a internet para que Telegram lo alcance


¿Cómo funciona por dentro?

El flujo está organizado así:


Webhook de Telegram → recibe cada mensaje del usuario.
Router principal → decide qué hacer según lo que escribió (ver menú, agregar al carrito, confirmar, etc.).
Switch de acciones → ramifica entre las distintas opciones del bot.
Lectura/escritura en Google Sheets → consulta el menú, guarda la sesión del usuario y registra el pedido.
Respuesta al usuario → envía de vuelta los productos, el carrito o la confirmación.


Las hojas de Google Sheets están divididas en tres pestañas:


MENU → catálogo de productos (categoría, nombre, precio).
SESSIONS → estado actual de cada usuario (qué tiene en el carrito).
PEDIDOS → historial de pedidos confirmados.



📸 Evidencias

En la carpeta capturas/ está toda la documentación visual del proyecto: el workflow completo en n8n, la configuración de cada nodo, las tres hojas de Google Sheets y el bot funcionando en Telegram (bienvenida, categorías, carrito, promociones, etc.).

El workflow exportado está disponible en capturas/DeliveryBot_v2_DEFINITIVO.json para importarlo directamente en n8n.


🚀 Cómo ponerlo a correr


Importar el archivo DeliveryBot_v2_DEFINITIVO.json en una instancia de n8n.
Crear un bot en Telegram con @BotFather y configurar el token en las credenciales de n8n.
Conectar una cuenta de Google y crear una hoja con las pestañas MENU, SESSIONS y PEDIDOS.
Levantar n8n con la variable WEBHOOK_URL apuntando a la URL pública de ngrok.
Iniciar ngrok sobre el puerto de n8n para exponer el webhook.



💡 Nota: las credenciales (token de Telegram, IDs de Google) deben configurarse de forma privada en n8n, nunca dentro del repositorio.




📚 Lo que aprendí


En Google Sheets, actualizar filas con appendOrUpdate usando el telegram_id falla por diferencias de tipo (número vs texto); el patrón confiable es borrar y volver a insertar.
n8n debe arrancar con WEBHOOK_URL ya configurado, o Telegram no encuentra el webhook.
En una instalación nueva de n8n conviene borrar la carpeta ~/.n8n antes del primer arranque para evitar configuraciones viejas.
Logré desplegar el bot tanto en Windows como en Linux sin permisos de administrador.



👤 Autor

Camilo Andrés García Almeida
Estudiante de Desarrollo de Software — Campuslands
GitHub · LinkedIn
