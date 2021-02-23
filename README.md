# Nodo-SparkFun-LoRa-Gateway---1-canal-ESP32-

Utilice la biblioteca Arduino-LMIC
--
Puede utilizar  biblioteca la biblioteca bifurcada por mcci-catena  : https://github.com/mcci-catena/arduino-lmic
Si no desea utilizar GitHub, puede descargar el enlace a  un archivo .zip archivado aquí. Aunque es posible que no esté completamente actualizada
https://cdn.sparkfun.com/assets/learn_tutorials/8/2/6/arduino-lmic-master.zip

Para instalar la biblioteca, descargue el archivo ZIP y use la función "Agregar biblioteca ZIP" de Arduino ( Sketch> Incluir biblioteca> Agregar biblioteca .ZIP ) para importar el archivo zip en su cuaderno de bocetos Arduino.

Configurar LMIC
-
Antes de  usar un boceto de ejemplo en la biblioteca LMIC, debe configurarlo. Para configurar la biblioteca, navegue hasta la biblioteca "" en su carpeta .. {Arduino Sketchbook} / libraries . Luego vaya a project_config y abra lmic_project_config.h 

Asegúrese de descomentar una  de las declaraciones "CFG ...".En última instancia, esta frecuencia debe coincidir con la establecida en la puerta de enlace.


NOTA: La Secretaría de modernización de Argentina [https://www.argentina.gob.ar/sites/default/files/informe_consulta_publica_de_espectro_02.pdf]. Al servicio de comunicaciones móviles avanzadas le fue asignado un segmento de la banda ISM, lo que hizo que en Argentina se tuviera que utilizar la banda de frecuencias conocida como AU915 en lugar de la banda US915 de Estados Unidos que estaba prevista inicialmente. La banda AU915 definida para Australia es utilizada además en otros países de Latinoamérica y presenta una única desventaja respecto a US915 consistente en que no es posible hacer comunicación full duplex y en cambio los transceptores LoRa deben operar en modo half duplex, es decir transmitiendo en un instante y recibiendo en otro.


A continuación se muestra el archivo de configuración: 
// project-specific definitions
//#define CFG_eu868 1
//#define CFG_us915 1
//#define CFG_au921 1   
#define CFG_au915 1 
 
//#define CFG_as923 1
// #define LMIC_COUNTRY_CODE LMIC_COUNTRY_CODE_JP   /* for as923-JP */
//#define CFG_in866 1
#define CFG_sx1276_radio 1
 
//#define LMIC_PRINTF_TO Serial
#define LMIC_DEBUG_LEVEL 2
 
//#define DISABLE_INVERT_IQ_ON_RX
 
Si intenta utilizar el ejemplo "sin formato" en esta biblioteca, deberá descomentar DISABLE_INVERT_IQ_ON_RX; de lo contrario, manténgalo sin comentarios.

Cargar el ejemplo de dispositivo de un solo canal
-
Haga clic en el link  de abajo para descargar el ejemplo. 
https://cdn.sparkfun.com/assets/learn_tutorials/8/2/6/ESP-1CH-TTN-Device-ABP-v01.zip

Luego abra ESP-1CH-TTN-Device-ABP.ino :

Entre las modificaciones en este ejemplo se encuentran el mapa de pines entre la radio y ESP32, configurado con estas líneas:

const lmic_pinmap lmic_pins = {
  .nss = 16,
  .rxtx = LMIC_UNUSED_PIN,
  .rst = 5,
  .dio = {26, 33, 32},
};
También se han modificado los canales habilitados para usar solo un canal con las líneas siguientes : 


// First disable all sub-bands
for (int b = 0; b < 8; ++b) {
  LMIC_disableSubBand(b);
}
// Then enable the channel(s) you want to use
LMIC_enableChannel(8); // 903.9 MHz



No puede compilar correctamente hasta que complete estas líneas:
 
// LoRaWAN NwkSKey, network session key
// This is the default Semtech key, which is used by the early prototype TTN
// network.
static const PROGMEM u1_t NWKSKEY[16] = { PASTE_NWKSKEY_KEY_HERE };
 
// LoRaWAN AppSKey, application session key
// This is the default Semtech key, which is used by the early prototype TTN
// network.
static const u1_t PROGMEM APPSKEY[16] = { PASTE_APPSKEY_KEY_HERE };
 
// LoRaWAN end-device address (DevAddr)
static const u4_t DEVADDR = 0xPASTE_DEV_ADDR_HERE;


 Deje  en blanco:
 PASTE_NWKSKEY_KEY_HERE
 PASTE_APPSKEY_KEY_HERE 
 0xPASTE_DEV_ADDR_HERE   
Hasta que obtenga sus claves reales y la dirección del dispositivo de The Things Network. 


Registro del dispositivo en TTN 
-
 Diríjase a :  https://www.thethingsnetwork.org/ y cree una cuenta. Una vez hecho esto, vaya a su Consola .
Si ya  tiene una cuenta vaya a su consola .

Crear una aplicación
-
Para registrar un dispositivo, primero debe crear una aplicación para alojarlo. En la consola, haga clic en " Aplicaciones " y luego haga clic en " Agregar aplicación ".

Complete cualquier identificación y descripción que desee. La aplicación EUI se generará automáticamente al crear la aplicación. También puede elegir su controlador  para la aplicación (por ejemplo meshed-handler).

![image](https://user-images.githubusercontent.com/72763026/108861282-cf347880-75cd-11eb-9ada-70f73d6daaa8.png)

![image](https://user-images.githubusercontent.com/72763026/108862353-e9228b00-75ce-11eb-84c7-7bc95e3f7d19.png)




Una vez agregada la aplicación debería verla en la descripción general .

![image](https://user-images.githubusercontent.com/72763026/108862771-4fa7a900-75cf-11eb-98d0-76108480216e.png)





Crear y configurar un dispositivo
--

A continuación, registre un dispositivo en su aplicación. En la sección " Dispositivos ", haga clic en " registrar dispositivo ".

Complete un ID de dispositivo único, haga clic en el botón " generar " debajo de " Dispositivo EUI " para generar automáticamente un EUI. Luego haga clic en " Registrarse ".


Este boceto de ejemplo solo admite la activación de ABP , por lo que deberá modificarlo en la configuración del dispositivo. En la página "Descripción general del dispositivo", haga clic en "Configuración". Desde allí, en "Método de activación", haga clic en ABP . Deshabilitar las comprobaciones del contador de tramas . La puerta de enlace es capaz de realizar comprobaciones de contador de tramas, pero puede desincronizarse, especialmente si tiene otra puerta de enlace cerca.

Guarde la configuración y vuelva a la página " Descripción general del dispositivo ". Debería ver claves nuevas, incluidas " Clave de sesión de red ", " Clave de sesión de la aplicación " y " Dirección del dispositivo ". 

Actualizar el código de ejemplo-
-
Con su aplicación, sesión de red y claves de dispositivo en la mano ya puede terminar de configurar el boceto del dispositivo ESP32 LoRa y comenzar a publicar datos. 

En la página " Descripción general del dispositivo ", haga clic en el símbolo de " código " ( <>) junto a " Clave de sesión de red " y " Clave de sesión de la aplicación "; esto hará que la clave sea visible y la mostrará como una matriz de 16 bytes. Copie cada una de esas claves y péguelas en lugar de los {PASTE KEY HERE}marcadores de posición. La " Clave de sesión de red " debe pegarse en la NWKSKEYvariable y la " Clave de sesión de la aplicación " debe pegarse en la APPSKEYvariable.

La DEVADDRvariable espera una única variable de 32 bits, así que copie la clave " Dirección del dispositivo " como se muestra y péguela en el PASTE_DEV_ADDR_HERE marcador de posición.

Las  tres constantes una vez hechas deberían verse como en el siguiente ejemplo:



static const PROGMEM u1_t NWKSKEY[16] = { 0xD6, 0x6D, 0x3D, 0x6D, 0x03, 0xF5, 0x83, 0xB4, 0x11, 0x4A, 0x96, 0x9C, 0x85, 0x64, 0xCB, 0x90 };// matriz 16 bytes
static const u1_t PROGMEM APPSKEY[16] = { 0xB6, 0x8A, 0x4D, 0xBC, 0xB6, 0x6E, 0xC1, 0xF7, 0x1F, 0x9A, 0x67, 0x13, 0x6C, 0xC0, 0x48, 0xB1 };// matriz 16 bytes
static const u4_t DEVADDR = 0x260622C1;// un número de 4 bytes



Prueba del código
-

Después de la instalación, el dispositivo debería enviar inmediatamente un mensaje "Hola, mundo". Continuará enviando un mensaje cada minuto o cada vez que se presione el botón "0" en el tablero.
Para verificar si su puerta de enlace está recibiendo el mensaje, puede verificar el Monitor serial o monitorear la página de configuración de la puerta de enlace ESP servida por la puerta de enlace. Cada vez que se recibe un mensaje, debe agregarse a la tabla "Historial de mensajes".
Si los mensajes llegan a su puerta de enlace, haga clic en la pestaña " Datos " de su dispositivo para comprobar si hay mensajes nuevos.

La carga útil del mensaje, que se cifró entre dejar el dispositivo y entrar en su aplicación, debe ser una serie de valores hexadecimales correspondientes a "Hola, mundo".




Decodificacion del mensaje
-
Es posible que haya notado en la ventana Datos de la aplicación que su carga útil se muestra en bytes sin procesar. Para ver "¡Hola, mundo!" codificado en ASCII, de la forma en que lo envió, deberá decodificar la carga útil. The Things Network incluye herramientas para hacer esto directamente en la consola. Vaya a la página Descripción general de la aplicación para su aplicación y haga clic en la pestaña Formatos de carga útil . Este menú le permite escribir funciones que se aplicarán a todos los paquetes entrantes para esta aplicación.

Para escribir un propio decodificador. Necesitamos tomar los datos de bytes sin procesar y devolver una cadena que contenga todos los caracteres correspondientes a cada byte. Solución  posible :
 
function Decoder(bytes, port) {
 
    return {
        ASCII: String.fromCharCode.apply(null, bytes)
    };
 
}




Decoder es una función de Javascript que The Things Network ya ha configurado para nosotros. Se necesitan dos argumentos llamados bytes , una matriz que contiene nuestra carga útil y el puerto , el LoRaWAN ™ "FPort" del paquete. FPort identifica la aplicación final o el servicio al que está destinado el paquete. El puerto 0 está reservado para mensajes MAC. No necesitamos saber nada sobre el número de puerto para nuestro ejemplo.

Podemos devolver cualquier valor que queramos de la Decoderfunción y aparecerá junto a nuestra carga útil en la ventana Datos de la aplicación . En el ejemplo anterior, he creado una nueva propiedad llamada "ASCII" que es igual a String.fromCharCode.apply(null, bytes). Para desglosar esto un poco más, estamos devolviendo un nuevo objeto String llamado "ASCII" y estamos usando el apply()método Javascript para llamar fromCharCode()con el argumento bytes y rellenar el resultado en nuestro nuevo String. El fromCharCode() método simplemente recorre cada byte en la matriz bytes(que, recuerde, contiene nuestra carga útil) y devuelve el carácter ASCII representado por ese código de carácter.

Después de copiar el código anterior en su función de decodificador, desplácese hacia abajo y haga clic en el botón Guardar funciones de carga útil . Ahora regrese a la ventana Datos de la aplicación y debería ver que todos los paquetes recibidos después de que se cambió la función del decodificador ahora tienen una nueva propiedad:


Solución de problemas:
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Si los mensajes no llegan a su puerta de enlace, primero asegúrese de que el canal y el factor de propagación coincidan. Si no se modifica, el código de ejemplo del dispositivo debería enviar datos en el canal de 903,9 MHz con un factor de dispersión de 7 (suponiendo que haya configurado la biblioteca LMIC en CFG_au915.

Puede utilizar el servidor web de la puerta de enlace para ajustar esta configuración sobre la marcha.
Tenga en cuenta que los números de canal deben coincidir secuencialmente con la freqsmatriz en loraModem.h , por ejemplo, el canal 0 debe ser 903.9MHz (nuevamente, asumiendo frecuencias estadounidenses).

Si los mensajes llegan a su puerta de enlace, pero no aparecen en la consola de su dispositivo TTN, considere cambiar la _TTNSERVERvariable en " ESP-sc-gway.h ". 
Hay que corroborar si funiona con :  au915.thethings.meshed.com.au 
 
"router.eu.thethings.network"ha funcionado perfectamente (incluso en los EE. UU.).
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------




