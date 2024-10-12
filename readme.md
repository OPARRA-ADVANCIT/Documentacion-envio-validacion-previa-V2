# Manual de Desarrollo para Consumo de Webservice

## 1. **Introducción**
Este documento técnico describe cómo los desarrolladores deben consumir el Web Service de facturación electrónica, el cual permite enviar documentos a la DIAN para su validación previa, mediante el método `send_document`. La comunicación con el servicio se realiza mediante una petición SOAP que incluye información en formato JSON encriptado en base64.

### **Requisitos previos**
Para integrar con este servicio, los desarrolladores deben tener en cuenta lo siguiente:
- **URL del WSDL**: `http://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_basic.wsdl`
- El servicio requiere enviar los datos en formato JSON, codificados en base64.
- Implementar la seguridad requerida, como certificados digitales si aplica.
- Uso de la librería SOAP en PHP (u otro lenguaje que soporte SOAP).

## 2. **Especificaciones del Webservice**

### **Métodos disponibles**
- **send_document**: Este método envía a la DIAN la información de facturas electrónicas y otros documentos.

### **Parámetros de entrada**
Los datos que se deben enviar en la petición SOAP incluyen las siguientes secciones codificadas en base64:

- **CABDOC**: Información de la cabecera del documento.
- **CLIDOC**: Información del cliente.
- **DETDOC**: Detalle del documento.
- **DETPRO**: Detalle de productos.
- **DETIMP**: Detalle de impuestos.
- **ADIDOC**: Información adicional del documento.
- **DETDES**: Detalle de descuentos.

Cada una de estas secciones se incluye como un elemento `item` dentro del cuerpo SOAP.

## 3. **Proceso de Integración**

### **Construcción de la Petición SOAP**
La petición al servicio web debe seguir la estructura proporcionada en el WSDL, utilizando formato XML con la cabecera y el cuerpo SOAP. El cuerpo debe contener los datos JSON codificados en base64:

##### **Ejemplo de Estructura SOAP**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope 
    SOAP-ENV:encodingStyle="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENC="http://schemas.xmlsoap.org/soap/encoding/" 
    xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" 
    xmlns:ns1="urn:documents" 
    xmlns:ns2="http://xml.apache.org/xml-soap" 
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <SOAP-ENV:Body>
        <ns1:send_document>
            <sendoc xsi:type="ns2:Map">
                <item>
                    <key xsi:type="xsd:string">sedaws</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <item>
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <!-- Otros items - campos como sededo, sedepr, sedeim -->
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

### **Formato de los Datos JSON**

Los datos dentro del XML están en formato JSON, que debe estar correctamente estructurado. Cada sección representa un conjunto de datos que debe ser enviado.

##### **Ejemplo de DATOEMP**



```json
{
	"DATOEMP": [
		{
			"NITEMP": "123456789",
			"DIGVER": "1",
			"NOMEMP": "Clinica de Pruebas Advancit S.A.",
			"RASOCI": "Clinica de Pruebas Advancit S.A.",
			"COREMP": "contacto@advancit.com",
			"FECREG": "2022-07-15",
			"SOFTWA": "BILLINGSOFT",
			"IDESOF": "123abc45-678d-90ef-gh12-3456ijkl789m",
			"PINSOT": "65432",
			"URLENV": "https://vpfe.dian.gov.co/WcfDianCustomerServices.svc?wsdl",
			"PREFIJ": "FACV",
			"NUMRES": "98765432100",
			"FECRES": "2020-05-10",
			"RESINI": "100000001",
			"RESFIN": "100500000",
			"FEREIN": "2020-05-10",
			"FEREFI": "2035-05-10",
			"DIREMP": "Avenida Central 123",
			"TELEMP": "3001234567",
			"CERTIF": "CertificadoFicticio.p12",
			"PASCER": "claveSegura123",
			"TECKEY": "ab12cd34ef56gh78ij90klmn123opqrs456tuvwx",
			"TESIDE": "1a2b3c4d-5678-9e0f-gh12-ijklmno34567",
			"DEPEMP": "11",
			"CIUEMP": "11001000",
			"REGID": "",
			"REGNAM": ""
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

### **Paso 3: Envío de la Petición**
Una vez construida la petición, se puede enviar utilizando una librería SOAP en PHP, por ejemplo:

```php
$client = new SoapClient('http://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_basic.wsdl', array('trace' => 1));
$argsen = array('secado' => $B64secado, 'secldo' => $B64secldo);  // Agregar más parámetros codificados en base64
$resins = $client->__soapCall('send_document', array('sendoc' => $argsen));
```

### **Paso 4: Manejo de Respuestas**
La respuesta de la DIAN puede incluir la aceptación o rechazo de la factura enviada, junto con los errores detectados. Es importante manejar y registrar tanto la solicitud como la respuesta para futuras referencias.

### **Paso 5: Manejo de Errores**
Si ocurre un error, es necesario manejar las excepciones de SOAP y analizar el contenido de las respuestas de error para corregir posibles problemas.

## 4. **Validaciones y Errores Comunes**
### **Validaciones antes de enviar la petición**
- Verifica que los campos obligatorios en el JSON estén correctamente formateados y completos (por ejemplo, NIT, número de resolución, tipo de documento, etc.).
- Asegúrate de que los valores numéricos, como totales e impuestos, coincidan.

### **Manejo de errores comunes**
- **Errores de formato**: Asegúrate de que los campos obligatorios estén presentes y de que los valores numéricos estén correctamente calculados.
- **Errores de conexión**: Verifica la conectividad con el servicio y que la URL del WSDL esté accesible.

## 5. **Ejemplos Prácticos**

### **Ejemplo completo de petición SOAP**
```xml
<SOAP-ENV:Envelope>
    <SOAP-ENV:Body>
        <ns1:send_document>
            <sendoc xsi:type="ns2:Map">
                <item>
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">eyJDQUJE...</value> <!-- JSON codificado en base64 -->
                </item>
                <item>
                    <key xsi:type="xsd:string">secldo</key>
                    <value xsi:type="xsd:string">eyJDTElET...</value> <!-- JSON codificado en base64 -->
                </item>
                <!-- Más secciones -->
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## 6. **Seguridad**
- El servicio puede requerir la autenticación mediante certificados, asegúrate de configurar los certificados correctamente en el servidor donde se consume el webservice.
  
## 7. **Conclusión**
Este documento cubre los aspectos generales para consumir el webservice de facturación electrónica, proporcionando ejemplos prácticos y recomendaciones para un uso adecuado. Es importante realizar pruebas en ambientes de desarrollo antes de integrar este servicio en producción.

---

## Campos de la Cabecera del Documento (CABDOC)
| Descripción                     | Nombre  | Ejemplo                     | Especificaciones    | Nota |
|----------------------------------|---------|-----------------------------|---------------------|------|
| Nit de la empresa que factura    | NITEMP  | 900225722                   | CHAR(30)            | SI   |
| Número de resolución autorizada  | NUMRES  | 1234567891000AA             | CHAR(40)            | SI   |
| Tipo de documento a enviar       | TDOCU   | 01                          | CHAR(2)             | SI   |
| Número de la factura             | NUMERO  | 15111                       | CHAR(20)            | SI   |
| Fecha de generación de la factura| FECDOC  | 2019-05-31                  | CHAR(40)            | SI   |
| Fecha de vencimiento             | FECVEN  | 2019-05-31                  | CHAR(40)            | SI   |
| Nombre del vendedor              | NOMVEN  | Inversiones Piedra Del Sol S.A. | CHAR(255)        | NO   |
| Moneda de la factura             | MONEDA  | COP                         | CHAR(3)             | SI   |

## Campos de Cliente (CLIDOC)
| Descripción                     | Nombre  | Ejemplo                     | Especificaciones    | Nota |
|----------------------------------|---------|-----------------------------|---------------------|------|
| Clase de persona                 | CLAPER  | 1                           | CHAR(1)             | SI   |
| Código del documento             | CODDOC  | 31                          | CHAR(2)             | SI   |
| Número de documento              | NUMDOC  | 900496336                   | CHAR(20)            | SI   |
| Dígito de verificación del NIT    | DIVECL  | 2                           | CHAR(1)             | SI   |
| País del cliente                 | PAICLI  | CO                          | CHAR(2)             | SI   |
| Departamento del cliente         | DEPCLI  | Santander                   | CHAR(40)            | SI   |
| Ciudad del cliente               | CIUCLI  | San Gil                     | CHAR(40)            | SI   |
| Dirección del cliente            | DIRCLI  | Kilometro 1 Via San Gil      | CHAR(200)           | SI   |

## Detalle del Documento (DETDOC)
| Descripción                     | Nombre  | Ejemplo                     | Especificaciones    | Nota |
|----------------------------------|---------|-----------------------------|---------------------|------|
| Consecutivo del detalle          | IDEPOS  | 1                           | INT(6)              | SI   |
| Código del producto              | CODITE  | 872520                      | CHAR(40)            | SI   |
| Nombre del producto              | NOMITE  | COLANGIOGRAFIA - TOMOGRAFIA  | CHAR(255)           | SI   |
| Cantidad del producto            | CANITE  | 1.00                        | DEC(8,2)            | SI   |
| Valor unitario del producto      | VALITE  | 225333.00                   | DEC(15,2)           | SI   |
| Valor total sin impuestos        | TOTVAD  | 198153.63                   | DEC(15,2)           | SI   |
| Valor del impuesto               | VALIMD  | 0.00                        | DEC(15,2)           | NO   |
 
