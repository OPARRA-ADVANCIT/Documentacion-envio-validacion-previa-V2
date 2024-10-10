---

### **Manual de Desarrollo: Consumo del Webservice SOAP para Facturación Electrónica**

#### **1. Introducción**
Este manual proporciona la guía técnica para consumir el webservice SOAP que gestiona la facturación electrónica. El webservice recibe peticiones en formato XML con información en formato JSON y realiza el envío a la DIAN, en conformidad con las normas de facturación electrónica en Colombia.

#### **2. Descripción General del Servicio**
El webservice SOAP tiene un endpoint específico para el envío de documentos de facturación electrónica.

##### **2.1. Endpoint del Servicio**
- **URL del WSDL**: [http://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_basic.wsdl](http://mff.advancit.co/facturaelectronicav2/ws_electronic_invoice_basic.wsdl)
- **Nombre de la operación**: `send_document`

##### **2.2. Parámetros de Entrada**
Los datos enviados deben estar en formato JSON, pero codificados en BASE64 dentro del XML. Los principales parámetros que se deben enviar incluyen:

- **CABDOC**: Información de la cabecera del documento.
- **CLIDOC**: Información del cliente.
- **DETDOC**: Detalle de los ítems del documento.
- **DETPRO**: Detalle de productos adicionales.
- **DETIMP**: Detalle de los impuestos.
- **ADIDOC**: Información adicional relacionada con el documento.
- **DETDES**: Detalle de los descuentos aplicados.

#### **3. Estructura de la Petición**
La petición al servicio web se realiza en formato XML con el siguiente esquema, que incluye la cabecera y el cuerpo SOAP. El cuerpo contiene los datos JSON codificados en BASE64.

##### **3.1. Ejemplo de Estructura SOAP**
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
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">{
                        "CABDOC": {
                            "NITEMP": "890202024",
                            "NUMRES": "18760000001",
                            "TDOCU": "01",
                            "NUMERO": "SETP990000027",
                            "FECDOC": "2024-09-24",
                            "FECVEN": "2024-10-24",
                            "NOMVEN": "PROF 136 SOPORTE",
                            "MONEDA": "COP",
                            ...
                        }
                    }</value>
                </item>
                <!-- Otros items -->
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

#### **4. Formato de los Datos JSON**
Los datos dentro del XML están en formato JSON, que debe estar correctamente estructurado. Cada sección representa un conjunto de datos que debe ser enviado.

##### **4.1. Ejemplo de CABDOC**
```json
{
    "CABDOC": {
        "NITEMP": "890202024",
        "NUMRES": "18760000001",
        "TDOCU": "01",
        "NUMERO": "SETP990000027",
        "FECDOC": "2024-09-24",
        "FECVEN": "2024-10-24",
        "MONEDA": "COP"
    }
}
```

##### **4.2. Ejemplo de CLIDOC**
```json
{
    "CLIDOC": {
        "CLAPER": "1",
        "CODDOC": "31",
        "NUMDOC": "900156264",
        "DEPCLI": "SANTANDER",
        "CIUCLI": "FLORIDABLANCA",
        "DIRCLI": "CALLE 35A N 31-151",
        "NOMCON": "NUEVA EPS",
        "RAZSOC": "NUEVA EPS"
    }
}
```

##### **4.3. Ejemplo de DETDOC**
```json
{
    "DETDOC": [
        {
            "IDEPOS": 1,
            "CODITE": "872520",
            "NOMITE": "COLANGIOGRAFIA - TOMOGRAFIA",
            "CANITE": "1.00",
            "VALITE": "225333.00",
            "TOTVAD": "198153.63"
        }
    ]
}
```

#### **5. Validaciones Requeridas**
Antes de enviar la petición, es importante validar que todos los datos sean correctos y cumplan con los requisitos establecidos:
- **CABDOC**: Debe contener el NIT de la empresa, el número de resolución, y el número de la factura, entre otros.
- **CLIDOC**: Debe incluir el tipo de documento del cliente, su identificación, y otros datos.
- **DETDOC**: El detalle de los productos debe incluir cantidad, valor unitario y total de cada ítem.
- Los datos deben estar codificados en **BASE64** antes de ser enviados en el XML.

#### **6. Respuesta del Servicio**
La respuesta del webservice será un mensaje XML que incluirá el estado de la transacción. Este mensaje podrá indicar si el documento fue procesado con éxito o si hubo algún error que deba ser corregido.

#### **7. Manejo de Errores**
En caso de error, el servicio puede devolver códigos y descripciones que deben ser manejados por el sistema que consume el webservice. Se recomienda implementar mecanismos de reintento para errores transitorios.

#### **8. Ejemplo Completo de Petición**
Este es un ejemplo completo de cómo se envían los datos al webservice, incluyendo múltiples ítems dentro del JSON:
```xml
<SOAP-ENV:Body>
    <ns1:send_document>
        <sendoc xsi:type="ns2:Map">
            <item>
                <key xsi:type="xsd:string">secado</key>
                <value xsi:type="xsd:string">{
                    "CABDOC": { ... },
                    "CLIDOC": { ... },
                    "DETDOC": [ ... ]
                }</value>
            </item>
        </sendoc>
    </ns1:send_document>
</SOAP-ENV:Body>
```

#### **9. Consideraciones Finales**
- **Codificación**: Verificar siempre que los datos estén correctamente codificados en BASE64 antes de enviarlos.
- **Pruebas**: Asegurarse de probar en un ambiente de pruebas antes de realizar envíos en producción.
- **Errores comunes**: Revisar que no existan errores de formato en los datos y que todos los campos obligatorios estén presentes.

---
