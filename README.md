# DeliveryBot

Bot de Telegram para hacer pedidos en una cafetería. Todo el flujo está construido en n8n y usa Google Sheets como base de datos, así que el negocio puede cambiar el menú y ver los pedidos desde una hoja de cálculo, sin tocar código.

## Qué problema resuelve

En una cafetería pequeña los pedidos por chat se toman a mano: alguien responde, anota, suma y avisa a cocina. El bot hace ese trabajo: muestra el menú, arma el carrito, calcula el total, guarda el pedido y le avisa a cocina y al cliente cuando el pedido cambia de estado.

## Qué hace

- Responde a `/start` o a un saludo con el menú principal.
- Muestra las categorías y los productos con su precio, con botones en línea.
- Agrega productos al carrito, permite verlo y vaciarlo.
- Al confirmar, calcula el total con los precios de la hoja, genera un número de pedido (`PED-fecha-número`) y lo guarda.
- Notifica a cocina, y cada cambio de estado (en preparación, en camino, entregado) se actualiza en la hoja y se le avisa al cliente.
- El cliente puede consultar sus pedidos anteriores, la ayuda y las promociones.

## Tecnologías

- **n8n** para todo el flujo: 39 nodos, entre ellos 15 de Google Sheets, 12 de Telegram y 8 nodos de código.
- **JavaScript** dentro de los nodos de código: el enrutador de acciones, el cálculo del total y el formato de los mensajes.
- **Telegram Bot API** como canal con el usuario.
- **Google Sheets** con tres pestañas: `MENU`, `SESSIONS` (carrito de cada usuario) y `PEDIDOS`.
- **ngrok** para exponer el webhook de n8n a internet mientras corre en local.

## Cómo funciona

```text
Telegram -> Telegram Trigger -> Router Principal (Code) -> Switch de acciones
                                                              |
        menú, categorías, productos, carrito, confirmar, estado, mis pedidos, ayuda
                                                              |
                                     Google Sheets (leer / guardar)  ->  respuesta por Telegram
```

El nodo `Router Principal` lee el mensaje o el botón que tocó el usuario y lo convierte en una acción (`categorias`, `agregar_carrito`, `confirmar`, etc.). El `Switch` manda cada acción a su rama.

## Cómo ejecutarlo

1. Importa `capturas/DeliveryBot_v2_DEFINITIVO.json` en n8n.
2. Crea un bot con @BotFather y configura el token en las credenciales de Telegram de n8n.
3. En el nodo `Enviar Productos` (petición HTTP) reemplaza `TU_TOKEN_DE_TELEGRAM` en la URL por el token del bot.
4. Conecta tu cuenta de Google y crea una hoja con las pestañas `MENU`, `SESSIONS` y `PEDIDOS`.
5. Arranca ngrok sobre el puerto de n8n y levanta n8n con `WEBHOOK_URL` apuntando a la URL pública de ngrok.

## Estructura

```text
capturas/
├── DeliveryBot_v2_DEFINITIVO.json   el workflow exportado de n8n
├── README.md                         explicación de cada captura
└── capturas/                         21 capturas: workflow, nodos, hojas y el bot funcionando
```

## Decisiones y lo que aprendí

- **Borrar y volver a insertar la sesión.** Actualizar la fila del usuario con `appendOrUpdate` usando el `telegram_id` fallaba porque Sheets a veces lo trataba como número y a veces como texto. Lo resolví borrando la sesión anterior e insertando la nueva.
- **Un solo enrutador en código.** En lugar de muchos nodos `IF`, un nodo de código decide la acción y un `Switch` la reparte. Así agregar una opción nueva es agregar una línea y una rama.
- **El total se calcula con los precios de la hoja,** no con lo que guarda el carrito, para que un cambio de precio en el menú se refleje.
- n8n tiene que arrancar con `WEBHOOK_URL` ya configurado, o Telegram no encuentra el webhook.
- Lo dejé funcionando en Windows y en Linux sin permisos de administrador.

## Autor

Camilo Andrés García Almeida · [GitHub](https://github.com/CamiloGarcia079) · [LinkedIn](https://www.linkedin.com/in/camilo-garcia-7570693b7/)
