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

### **Paso 1: Construcción de la Petición SOAP**
La petición debe seguir la estructura proporcionada en el WSDL y debe incluir los siguientes elementos codificados en base64:

```xml
<SOAP-ENV:Envelope>
    <SOAP-ENV:Body>
        <ns1:send_document>
            <sendoc xsi:type="ns2:Map">
                <item>
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <item>
                    <key xsi:type="xsd:string">secldo</key>
                    <value xsi:type="xsd:string">{JSON codificado}</value>
                </item>
                <!-- Otros campos como sededo, sedepr, sedeim -->
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
