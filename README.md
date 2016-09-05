![image](docs/img/logo-transparent.png)

[![License](http://img.shields.io/:license-Apache%202-red.svg)](http://www.apache.org/licenses/LICENSE-2.0.txt)

# Suite mCommerce Web

**Suite mCommerce Web** ofrece servicios diseñados para facilitar la captura y procesamiento de la información de la tarjeta de crédito o débito desde tu App, que invocan directamente a nuestros servidores, liberándote del riesgo de la manipulación de información sensible.

## Servicios Post

Los servicios POST que proporciona suite mCommerce son los siguientes:
* **Autenticación**	
* **Cobro con Token**	

Ambos servicios se consumen enviando una cadena cifrada en el parámetro POST `xml`.

## Estructura y Cifrado

En el cifrado de la cadena enviada a los servicios se utiliza el algoritmo AES-128.

**Llave de Cifrado** La llave de cifrado tiene una longitud de 128 bits, la cual se le proporcionará al comercio en formato hexadecimal con longitud de 32 caracteres.

Ejemplo de llave de cifrado: `4BA0A9AF04CAC18CE315B016EA703496`

**Algoritmo de Cifrado**
* **Tipo:** AES-128
* **Modo de operación:** CBC
* **Relleno (Pading):** PKCS5Padding
* **Vector de inicialización:** Se envía en los primeros 16 bytes de la cadena cifrada

Ejemplo de modo de cifrado:`AES/CBC/PKCS5Padding`


La implementación se basa en el envío de una cadena en el parámetro xml por `POST` a los servicios con la siguiente estructura:

```xml
xml=<pgs><data0>CADENA FIJA ASIGNADA ALCOMERCIO>data0><data>CADENA DE COBRO CIFRADA EN AES CODIFICADA EN BASE64</data></pgs>
```

## Autenticación

Servicio `POST` que registra el dispositivo desde el que se invoca la petición y desde el cual se solicita tokenizar una tarjeta.

Ejemplo:
```json
{
  "urlResponse": "https://www.miservidor.com/procesarespuesta.jsp",
  "tokenData": {
    "branch": "001",
    "company": "SNBX",
    "country": "MEX",
    "user": "SNBX00001",
    "password": "S3cr3t0",
    "merchant": "00001",
    "currency": "MXN",
    "operationType": "6",
    "reference": "Autenticacion001"
  },
  "3dsData": {
    "branch": "001",
    "country": "MEX",
    "user": "SNBX00001",
    "password": "S3cr3t0",
    "currency": "MXN",
    "authKey": "PROPORCIONADA_AL_COMERCIO",
    "merchant": "00001",
    "reference": "Autenticacion001"
  }
}
```


Para consumir el servicio se envía una cadena cifrada en el parámetro POST “xml”, la cadena en claro es una cadena JSON con los siguientes atributos:

|Propiedad         |Descripción                                                   |¿Obligatorio?|
|------------------|--------------------------------------------------------------|:-----------:|
|urlREsponse       |URL donde se enviará la respuesta en el parámetro strResponse |Sí| 
|tokenData|||
|branch          |Sucursal para procesar token                                  |Sí| 
|company         |Empresa para procesar token                                   |Sí| 
|country         |País, debe contener el valor MEX                              |Sí| 
|user            |Usuario Centro de Pagos para procesar token                   |Sí| 
|password        |Password del usuario Centro de Pagos para autenticación       |Sí|
|merchant        |Afiliación (ID) con la que procesará el token                 |Sí| 
|currency        |Moneda de la transacción, debe contener el valor MXN          |Sí| 
|operationType   |Forma de operar                                               |Sí| 
|reference       |Referencia del cliente para identificar la petición de token  |Sí| 
|3dsData|||
|branch          |Sucursal para procesar petición 3DS                           |No| 
|country         |País, debe contener el valor MEX                              |No| 
|user            |Usuario centro de pagos para procesar 3DS                     |No| 
|password        |Password del usuario centro de pagos para autenticación       |No|
|currency        |Moneda de la transacción, debe contener el valor MXN          |No|
|authKey         |Cadena proporcionada al comercio        |No| 
|merchant        |Afiliación (ID) con la que procesará 3DS                      |Sí| 
|reference       |Referencia del cliente para identificar la petición de 3DS    |Sí| 

La respuesta será enviada a la URL indicada en el atributo ´urlResponse´, dentro del parámetro ´strResponse´ el cual contendrá una cadena JSON cifrada en AES, con la estructura siguiente:

|Propiedad           |Descripción                                    |¿Obligatorio?|
|--------------------|-----------------------------------------------|-------------|
|cdResponse          |Código de respuesta de la petición             |Sí| 
|nbResponse          |Descripción de la respuesta                   |Sí| 
|token               |Token generado para el dispositivo autenticados| No|

Donde cdRespone=”00” es Respuesta exitosa, cualquier otro valor es de error

Ejemplo:

```json
{
  "cdResponse": "00",
  "nbResponse": "success",
  "token": "8243078589705454"
}
```
