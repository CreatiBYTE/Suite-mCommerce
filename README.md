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

⋅⋅⋅**Tipo:** AES-128
⋅⋅⋅**Modo de operación:** CBC
⋅⋅⋅**Relleno (Pading):** PKCS5Padding
⋅⋅⋅**Vector de inicialización:** Se envía en los primeros 16 bytes de la cadena cifrada

Ejemplo de modo de cifrado:`AES/CBC/PKCS5Padding`


La implementación se basa en el envío de una cadena xml a los servicios con la siguiente estructura:

```xml
xml=<pgs><data0>CADENA_FIJA_ASIGNADA_ALCOMERCIO>data0><data>CADENA_DE_COBRO_CIFRADA_EN_AES_CODIFICADA_EN_BASE64</data></pgs>
```

La cadena se envía en el parámetro “xml” por POST a los servicios de cobro
