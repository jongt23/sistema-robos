## 📄 Guía de Instalación Detallada (PDF)
Puedes descargar nuestro manual completo en PDF desde el siguiente enlace:
[Descargar Manual en PDF](https://drive.google.com/file/d/1jFVQJj1rndXKyV86GUX9SnZlAyyNgEoL/view?usp=sharing)

# [🏪 Ir a la página del script](https://sistema-gestion-robos.tebex.io/category/3021331)


# Sistema de Solicitud de Robos v4.1

Este script para FiveM proporciona un sistema completo y avanzado para que los jugadores soliciten robos y la policía los gestione. Incluye requisitos dinámicos de policía, cooldowns, campos de información personalizados por robo y un sistema de auditoría completo a través de webhooks de Discord.

## 🚀 Características Principales

- **Menú de Solicitud para Civiles (`/robos`):** Interfaz limpia para que los jugadores soliciten robos disponibles.
- **Menú de Gestión para Policía (`/policiaops`):** Panel para que la policía autorice, deniegue o finalice operativos.
- **Requisito de Policías Dinámico:** Configura un número mínimo de policías en servicio para cada robo.
- **Sistema de Policías Ocupados:** Un robo activo "reserva" a sus policías requeridos, afectando la disponibilidad de otros robos grandes.
- **Campos Personalizados por Robo:** Define información extra que se debe solicitar para robos específicos (ej: número de asaltantes, armamento, etc.).
- **ID de Solicitante Automática:** El sistema captura automáticamente el nombre y el ID del jugador que solicita el robo y lo muestra a la policía.
- **Auditoría Completa en Discord:**
    - Envía registros detallados a un canal de Discord para cada acción: **autorización**, **denegación** (con motivo) y **finalización**.
    - Al finalizar un robo, pregunta al oficial si se **recuperó el botín** (Sí/No) y lo incluye en el registro de Discord con emojis (✔️/❌) para una fácil visualización.
- **Cooldowns Configurables:** Establece tiempos de espera globales o por robo.
- **Notificaciones Claras:** Avisos en tiempo real para la policía sobre nuevas solicitudes y para los civiles sobre el estado de su petición.
- **Interfaz Intuitiva:** Menús y modales limpios para una experiencia de usuario fluida, incluyendo contadores de caracteres.
- **Protección del Sistema:**
    - Detecta si un solicitante se desconecta antes de la autorización.
    - Evita que se abran menús si no hay opciones disponibles, previniendo congelamientos de la UI.
- **Compatibilidad:** Diseñado para funcionar con **ESX** y **QBCore**.

## 🛠️ Instalación

1.  Crea una carpeta (ej: `sistema_robos`) en el directorio `resources` o dentro de otro de tu servidor FiveM.
2.  Coloca todos los archivos del script (`config.lua`, `server/main.lua`, `client/main.lua`, `html/`, `fxmanifest.lua`, `README.md`) dentro de esa carpeta.
3.  Asegúrate de añadir la siguiente línea en tu archivo `server.cfg` (o `resources.cfg`):
    ```
    ensure sistema_robos
    ```
4.  Realiza la configuración necesaria en el archivo `config.lua` (ver sección abajo).
5.  Reinicia tu servidor o el recurso (`restart sistema_robos`).

## ⚙️ Configuración (`config.lua`)

El archivo `config.lua` es el centro de control del script.

### 1. Webhook de Discord (MUY IMPORTANTE)

Esta es la configuración clave para la función de auditoría.

```lua
Config.DiscordWebhookURL = "URL_DE_TU_WEBHOOK_AQUI"
```
- **Cómo obtener la URL:**
    1. En tu servidor de Discord, ve a `Ajustes del servidor` > `Integraciones`.
    2. Haz clic en `Webhooks` > `Nuevo Webhook`.
    3. Dale un nombre (ej: "Registros de Robos"), elige el canal donde se publicarán los mensajes y **Copia la URL del webhook**.
    4. Pégala entre las comillas.
- Si dejas la URL vacía (`""`), la función de registro en Discord se desactivará por completo.

### 2. Configuración de Robos

Aquí es donde defines cada robo disponible.

**Estructura de un robo:**
```lua
['id_del_robo'] = { 
    label = 'Nombre que se muestra',
    minPolice = 5,
    cooldown = 60 * 60, -- Opcional
    customFields = { -- Opcional
        { id = 'id_interno', label = 'Pregunta al jugador', maxlength = 50 }
    }
},
```
- `['id_del_robo']`: Un identificador único y sin espacios (ej: `'fleeca_main'`).
- `label`: El nombre del robo que verán los jugadores en el menú.
- `minPolice`: **(Obligatorio)** El número mínimo de policías que deben estar en servicio para que este robo esté disponible.
- `cooldown`: **(Opcional)** Tiempo de espera específico para este robo en segundos. Si no se define, usará `Config.DefaultCooldown`.
- `customFields`: **(Opcional)** Una tabla para solicitar información extra al jugador.
    - `id`: Identificador interno para el dato (sin espacios).
    - `label`: La pregunta que se le hará al jugador (el `placeholder` del campo).
    - `maxlength`: El número máximo de caracteres permitidos para la respuesta.

## 📋 Comandos

- `/robos` - (Civiles) Abre el menú para solicitar un operativo. Si no hay robos disponibles, mostrará una notificación de error.
- `/policiaops` - (Policía) Abre el menú para gestionar las solicitudes pendientes y los robos activos.
- `/updatejob` - (Admin/Debug) Fuerza la re-sincronización del trabajo del jugador (policía/civil). Útil para depuración.

## 📖 Flujo de Uso y Auditoría

1.  **Solicitud:** Un civil usa `/robos`. El servidor comprueba si cumple los requisitos de policías. Si es así, se abre el menú. El jugador elige un robo, rellena la información (banda, vehículo y campos personalizados si los hay) y lo solicita.
2.  **Notificación a Policía:** Todos los policías reciben un aviso en pantalla y un blip en el mapa.
3.  **Gestión:** Un policía usa `/policiaops`. Ve la lista de solicitudes con el **nombre e ID del solicitante**, la banda, el vehículo y los datos personalizados.
4.  **Decisión:**
    - **Autorizar:** El robo pasa a estado "activo". Se envía un webhook a Discord notificando la **autorización**.
    - **Denegar:** El policía introduce un motivo. El robo vuelve a estar disponible. Se envía un webhook a Discord notificando la **denegación** y el motivo.
5.  **Finalización:** Una vez el robo ha terminado, un policía usa `/policiaops` y pulsa "Finalizar".
6.  **Resultado:** Se abre un modal que pregunta si se recuperó el botín. El policía elige "Sí" o "No".
7.  **Registro Final:** El robo entra en cooldown. Se envía un último webhook a Discord notificando la **finalización** del operativo y el **resultado del botín** (✔️/❌).

Ej: Hummane requiere saber el número de asaltantes y el armamento que usarán para ajustarse a ello.
>![image](https://github.com/user-attachments/assets/ca52e068-55b2-44f7-9052-7e893dce0e45)
>![image](https://github.com/user-attachments/assets/35baefb1-8b82-4e50-a22f-f70b1590bfbe)

La parte POLICIAL verá esta información:
>![image](https://github.com/user-attachments/assets/4157a6eb-a969-4058-aeef-8cf0d430a578)

Si es autorizado la LSPD verá en el chat
>![image](https://github.com/user-attachments/assets/7f351a42-4d63-42de-ad5f-3473c9af741a)

Y en Discord se puede ver un registro, si se activa, como el siguiente ejemplo:
>![image](https://github.com/user-attachments/assets/90f537d9-7641-4ce8-b943-a0b68e7bece3)

El asaltante que solicitó el robo verá algo como esto:
>![image](https://github.com/user-attachments/assets/f527f6cf-2396-47a1-8584-95e2487a4b0d)

Si no se encuentra ONLINE cuando le aceptan un ROBO la LSPD lo sabrá y se cancelará, dejándolo libre para el resto de usuarios.

Cuando un robo está en curso se ve de esta forma:
>![image](https://github.com/user-attachments/assets/7d9ee152-639b-499d-a0d0-db0e8e83d105)

Al terminar se indica si se recuperó el botín o no de esta forma:
>![image](https://github.com/user-attachments/assets/56d41e53-2a88-44f0-a432-3d260eb4e375)

Y si se tiene activado el webhook de Discord, se guardará un registro como el siguiente:
>![image](https://github.com/user-attachments/assets/6f78f802-1942-4c98-a24d-5369673d1e08)

[VÍDEO DEMOSTRATIVO - algunas nuevas funciones como campos adicionales y log en discord se han añadido a posteriori en las imágenes anteriores](https://www.youtube.com/watch?v=T9CL5uX9XqI)



