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
- **DETALLEPRO**: Detalle de productos.
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

## Campos de la Cabecera del Documento (DATOEMP)
| Descripción                        | Nombre   | Ejemplo                       | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|-------------------------------|-----------------|---------------------------------------------------------|--------|
| Nit de la empresa                  | NITEMP   | 123456789                     | CHAR(9)          |                                                        | SI     |
| Dígito de verificación             | DIGVER   | 8                             | CHAR(1)          |                                                        | SI     |
| Nombre de la empresa               | NOMEMP   | ABC+SOLUTIONS+SAS              | CHAR(255)        |                                                        | SI     |
| Razón social                       | RASOCI   | ABC+SOLUTIONS+SAS              | CHAR(255)        |                                                        | SI     |
| Correo electrónico                 | COREMP   | info@abcsolutions.com          | CHAR(200)        |                                                        | SI     |
| Fecha de registro                  | FECREG   | 2020-06-30                    | DATE             |                                                        | SI     |
| Nombre del software                | SOFTWA   | EASYBILLING                    | CHAR(200)        |                                                        | SI     |
| ID del software                    | IDESOF   | 11111111-2222-3333-4444-555555555555 | CHAR(36)         |                                                        | SI     |
| PIN del software                   | PINSOT   | 67890                         | CHAR(5)          |                                                        | NO     |
| URL de envío                       | URLENV   | https:\/\/dian-fake.com\/WcfDianCustomerServices.svc?wsdl | CHAR(255) |                                                        | SI     |
| Prefijo                            | PREFIJ   | FACT                          | CHAR(4)          |                                                        | SI     |
| Número de resolución               | NUMRES   | 9876543210                    | CHAR(30)         |                                                        | SI     |
| Fecha de la resolución             | FECRES   | 2021-01-01                    | DATE             |                                                        | SI     |
| Rango inicial de la resolución     | RESINI   | 100000000                     | CHAR(30)         |                                                        | SI     |
| Rango final de la resolución       | RESFIN   | 200000000                     | CHAR(30)         |                                                        | SI     |
| Fecha inicio de resolución         | FEREIN   | 2021-02-01                    | DATE             |                                                        | SI     |
| Fecha fin de resolución            | FEREFI   | 2031-02-01                    | DATE             |                                                        | SI     |
| Dirección de la empresa            | DIREMP   | CALLE 123 # 45-67 BOGOTA       | CHAR(255)        |                                                        | SI     |
| Teléfono de la empresa             | TELEMP   | 6019876543                    | CHAR(10)         |                                                        | SI     |
| Certificado                        | CERTIF   | CertificadoFicticio.p12        | CHAR(255)        |                                                        | NO     |
| Contraseña del certificado         | PASCER   | abc1234XYZ                    | CHAR(16)         |                                                        | NO     |
| Clave técnica                      | TECKEY   | a1b2c3d4e5f67890abcdef1234567890 | CHAR(40) |                                                      | NO     |
| Tesid                              | TESIDE   | aabbccdd-eeff-1122-3344-556677889900 | CHAR(36) |                                                    | NO     |
##### **Ejemplo de DATOEMP**
```json

{
  "DATOEMP": [
    {
      "NITEMP": "123456789",
      "DIGVER": "8",
      "NOMEMP": "ABC+SOLUTIONS+SAS",
      "RASOCI": "ABC+SOLUTIONS+SAS",
      "COREMP": "info@abcsolutions.com",
      "FECREG": "2020-06-30",
      "SOFTWA": "EASYBILLING",
      "IDESOF": "11111111-2222-3333-4444-555555555555",
      "PINSOT": "67890",
      "URLENV": "https://dian-fake.com/WcfDianCustomerServices.svc?wsdl",
      "PREFIJ": "FACT",
      "NUMRES": "9876543210",
      "FECRES": "2021-01-01",
      "RESINI": "100000000",
      "RESFIN": "200000000",
      "FEREIN": "2021-02-01",
      "FEREFI": "2031-02-01",
      "DIREMP": "CALLE 123 # 45-67 BOGOTA",
      "TELEMP": "6019876543",
      "CERTIF": "CertificadoFicticio.p12",
      "PASCER": "abc1234XYZ",
      "TECKEY": "a1b2c3d4e5f67890abcdef1234567890",
      "TESIDE": "aabbccdd-eeff-1122-3344-556677889900"
    }
  ]
}
```
Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de la Cabecera del Documento (CABDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Nit de la empresa que factura      | NITEMP   | 900225722        | CHAR(30)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Numero de resolución autorizada    | NUMRES   | 1234567891000AA  | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Tipo de documento a enviar         | TDOCU    | 01               | CHAR(2)        | 01: Facturas, 02: Notas débito, 03: Notas crédito. | SI     |
| Numero de factura                  | NUMERO   | 15111            | CHAR(20)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Fecha de generación de factura     | FECDOC   | 2019-05-31       | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Fecha de vencimiento de factura    | FECVEN   | 2019-05-31       | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Nombre del vendedor                | NOMVEN   | Inversiones Piedra Del Sol S. A. | CHAR(255) | No completar con nada ni a la izquierda ni a la derecha. | NO     |
| Código de moneda de la factura     | MONEDA   | COP              | CHAR(3)        | Código de la moneda.                              | SI     |
| Tipo de envío para notas crédito   | TIPCRU   |                  | CHAR(1)        | Aplica solo para notas crédito.                       | SI     |
| Código sucursal o tienda           | CODSUC   |                  | CHAR(50)       | Código sucursal o tienda.                            | NO     |
| Número de factura referenciada     | NUMREF   |                  | CHAR(40)       | Solo aplica para notas crédito de tipo F.               | SI     |
| Código de formato de diseño        | FORIMP   |                  | CHAR(2)        | Código de formato de diseño para el PDF.          | NO     |
| Clasificación del documento        | CLADET   |                  | CHAR(1)        | D: Detallado, U: Único.                           | NO     |
| Formas de Pago                     | FORPAG   |                  | CHAR(2)        | Ver tabla Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf (13.2.8.4.1 Formas de Pago: cbc:PaymentMeans/ID)                | SI     |
| Orden de compra                    | ORDENC   |                  | CHAR(40)       | Si requiere manejar número de orden de compra.    | NO     |
| Número de remisión                 | NUREMI   |                  | CHAR(40)       | Si requiere manejar número de remisión.           | NO     |
| Nota de recepción                  | NORECE   |                  | CHAR(40)       | Si requiere manejar nota de recepción.            | NO     |
| EAN tienda entrega                 | EANTIE   |                  | CHAR(40)       | Código EAN de tienda de entrega.                   | NO     |
| Código moneda origen               | COMODE   |                  | CHAR(3)        | Código moneda de origen.                           | SI si es factura de exportación |
| Código moneda destino              | COMOHA   |                  | CHAR(3)        | Código moneda de destino.                          | SI si es factura de exportación |
| Factor cálculo moneda              | FACCAL   |                  | CHAR(12)       | Valor tasa de cambio de moneda extranjera.         | SI si es factura de exportación |
| Fecha de tasa de cambio            | FETACA   |                  | CHAR(20)       | Fecha en la que se tomó la tasa de cambio.         | SI si es factura de exportación |
| Fecha del documento referenciado   | FECREF   |                  | CHAR(20)       | Fecha en la que se generó el documento referenciado. | SI para notas crédito |
| Observaciones documento referenciado | OBSREF |                  | CHAR(200)      | Observaciones del documento referenciado.         | SI para notas crédito |
| Observaciones generales            | OBSERV   |                  | CHAR(200)      | Si requiere una observación en la factura.        | NO     |
| Texto de la factura                | TEXDOC   |                  | CHAR(950)      | Texto adicional de la factura.                    | NO     |
| Motivo devolución DIAN             | MODEDI   |                  | CHAR(1)        | Ver tabla DIAN 6.2.5.                             | SI para notas crédito |
| Número de entrega                  | NUMENT   |                  | CHAR(10)       | Si requiere manejar número de entrega.            | NO     |
| Número de factura DIAN             | NDIAN    |                  | CHAR(18)       | Número de la factura para pruebas.                 | NO     |
| Organización de ventas             | SOCIED   |                  | CHAR(40)       | Código de la organización de ventas.               | NO     |
| Código del vendedor                | CODVEN   |                  | CHAR(40)       | Si requiere código de vendedor en la factura.      | NO     |
| Motivo de devolución               | MOTDEV   |                  | CHAR(200)      | Motivo de devolución de la nota crédito.           | NO     |
| Subtotal del documento             | SUBTO   | 46218.00         | DEC(15,2)      | Valor sin impuestos, descuentos y anticipos.     | SI     |
| Base para calcular impuestos       | BASIMP   | 46218.00         | DEC(15,2)      | Valor sobre el cual se calculan los impuestos.    | SI     |
| Total a pagar                      | TOTFAC   | 54999.42         | DEC(15,2)      | Resultado de SUBTOT + TOTIMP.                   | SI     |
| Total de impuestos                 | TOTIMP   | 8781.42          | DEC(15,2)      | Multiplicación de SUBTOT por el porcentaje de impuestos. | SI     |
| Valor total de descuentos          | TOTDES   | 0.00             | DEC(15,2)      |                                                 | NO     |
| Días para realizar el pago         | DIPAPA   | 0                | INT(4)         | Si se otorgan días para el pago de la factura.    | NO     |
| Tipo de operación                  | TIPOPE   |                  | CHAR(2)        | Ver tabla DIAN 6.1.5. Tipos de operación.         | SI     |
| Medios de Pago		     | MEDPAG 	| 40		   | ||SI|
| Usuario                            | USUAR    |                  | CHAR(30)       |                                                 | SI     |
| Clave                              | CLAVE    |                  | CHAR(30)       |                                                 | SI     |


##### **Ejemplo de CABDOC**

```json
{
	"CABDOC": {
		"NITEMP": "123456789",
		"NUMRES": "98765432100",
		"TDOCU": "01",
		"NUMERO": "FACV100011337",
		"FECDOC": "2024-10-10",
		"FECVEN": "2024-10-10",
		"NOMVEN": "SOPORTE ADVANCIT",
		"MONEDA": "COP",
		"TIPCRU": "",
		"CODSUC": "1",
		"NUMREF": "",
		"FORIMP": "",
		"CLADET": "D",
		"FORPAG": "1",
		"ORDENC": "",
		"NUREMI": "",
		"NORECE": "",
		"EANTIE": "",
		"COMODE": "COP",
		"COMOHA": "",
		"FACCAL": "",
		"FETACA": "",
		"FECREF": "",
		"OBSREF": "",
		"OBSERV": "",
		"TEXDOC": "",
		"MODEDI": "",
		"NUMENT": "",
		"NDIAN": "FACV100011337",
		"SOCIED": "",
		"CODVEN": "200",
		"MOTDEV": "",
		"SUBTO": "5500.00",
		"BASIMP": "0.00",
		"TOTFAC": "5500.00",
		"TOTIMP": "0.00",
		"TOTDES": "0.00",
		"DIPAPA": "0",
		"TIPOPE": "SS-Recaudo",
		"MEDPAG": "10",
		"CLAVE": "123456789",
		"USUAR": "ADVANCIT"
	}
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de Datos del Cliente (CLIDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Clase persona                      | CLAPER   | 1                | CHAR(1)        | Ver tabla DIAN 6.2.3.                           | SI     |
| Código documento                   | CODDOC   | 31               | CHAR(2)        | Código de documento de identificación.           | SI     |
| Número de documento                | NUMDOC   | 900496336        | CHAR(20)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Dígito de verificación             | DIVECL   | 2                | CHAR(1)        | Solo aplica si es código de documento 31.        | SI     |
| Código país                        | PAICLI   | CO               | CHAR(2)        | CO=Colombia (ISO 3166-1).                        | SI     |
| Departamento del cliente           | DEPCLI   | Santander        | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Ciudad del cliente                 | CIUCLI   | San Gil          | CHAR(40)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Barrio del cliente                 | LOCCLI   |                  | CHAR(40)       | Si requiere indicar el barrio del cliente.        | NO     |
| Dirección del cliente              | DIRCLI   | Kilómetro 1 Vía San Gil Charalá | CHAR(200) | No completar con nada ni a la izquierda ni a la derecha. | SI |
| Teléfono del cliente               | TELCLI   | 3208376408       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Nombre y apellidos completos       | NOMCON   | Fyg S.a.s.       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Régimen tributario                 | REGTRI   | 2                | CHAR(4)         | Tabla DIAN - Régimen Tributario (0 Simplificado, 2 Común)| SI     |
| Razón social cliente               | RAZSOC   | Fyg S.a.s.       | CHAR(200)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 31 |
| Primer nombre                      | PNOMBR   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Segundo nombre                     | SNOMBR   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Apellidos                          | APELLI   |                  | CHAR(100)       | No completar con nada ni a la izquierda ni a la derecha. | SI si código de documento 13 |
| Mail de notificación               | MAICLI   | siac_frr@hotmail.com | CHAR(255)    | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Número de matrícula                | NUMMAT   |                  | CHAR(150)       | Si requiere manejar número de matrícula.                  | NO     |
| Nombre país                        | NOMPAI   | Colombia         | CHAR(150)       | No completar con nada ni a la izquierda ni a la derecha. | SI     |
| Código ciudad                      | CODCIU   | 68001            | CHAR(20)        | Fuente tabla DANE.                                        | SI     |
| Código departamento                | CODDEP   | 68               | CHAR(20)        | Fuente tabla DANE.                                        | SI     |
| Obligaciones o responsabilidades   | OBLCON   | O-09             | CHAR(200)       | Ver tabla DIAN 6.2.7.                                     | SI     |


##### **Ejemplo de CLIDOC**

```json
{
	"CLIDOC": {
		"CLAPER": "1",
		"CODDOC": "31",
		"NUMDOC": "123456789",
		"DIVECL": "5",
		"PAICLI": "CO",
		"DEPCLI": "BOGOTA D.C.",
		"CIUCLI": "BOGOTA",
		"LOCCLI": "",
		"DIRCLI": "AVENIDA 45 N 67-123",
		"TELCLI": "3201234567",
		"NOMCON": "CLINICA DE PRUEBAS ADVANCIT",
		"REGTRI": "4",
		"RAZSOC": "CLINICA DE PRUEBAS ADVANCIT S.A.",
		"PNOMBR": "",
		"SNOMBR": "",
		"APELLI": "",
		"MAICLI": "contacto@advancit.com",
		"NUMMAT": "",
		"NOMPAI": "COLOMBIA",
		"CODCIU": "11001",
		"CODDEP": "11",
		"OBLCON": "O-15"
	}
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.

## Campos de Detalle del Documento (DETDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Consecutivo detalle documento      | IDEPOS   | 1                | INT(6)          | Número Consecutivo.                                       | SI     |
| Código de producto                 | CODITE   | 1                | CHAR(40)        | No completar con nada ni a la izquierda ni a la derecha.  | SI     |
| Descripción del producto           | NOMITE   | Triturado De 1/2 Menor Calidad | CHAR(255) | No completar con nada ni a la izquierda ni a la derecha.  | SI     |
| Unidad de medida                   | UNIMED   | 94               | CHAR(5)         | Ver tabla DIAN 6.3.6.                                     | SI     |
| Cantidad del producto              | CANITE   | 2.00             | DEC(8,2)        |                                                          | SI     |
| Valor unitario del producto        | VALITE   | 23109.00         | DEC(15,2)       | Valor sin impuestos y unitario del artículo.              | SI     |
| Valor total de los productos       | TOTVAD   | 46218.00         | DEC(15,2)       | Resultado de multiplicar VALITE * CANITE.                 | SI     |
| Valor del impuesto                 | VALIMD   | 8781.42          | DEC(15,2)       | Resultado de multiplicar TOTVAD * PORIMD.                 | SI     |
| Valor del descuento                | VALDED   | 0.00             | DEC(15,2)       |                                                          | NO     |
| Porcentaje del impuesto            | PORIMD   | 19.00            | DEC(15,2)       |                                                          | SI     |
| Código del impuesto                | CODIMD   | 01               | CHAR(2)         | Código de impuesto tabla DIAN 6.2.2.                      | SI     |
| Nombre del impuesto                | NOMIMD   | IVA              | CHAR(255)       | Nombre del impuesto tabla DIAN 6.2.2.                     | SI     |

##### **Ejemplo de DETDOC**

```json
{
	"DETDOC": [
		{
			"IDEPOS": 1,
			"CODITE": "123456",
			"NOMITE": "RESONANCIA MAGNETICA CEREBRAL",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "300000.00",
			"TOTVAD": "264000.00",
			"VALIMD": "0.00",
			"VALDED": "36000.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa"
		},
		{
			"IDEPOS": 2,
			"CODITE": "654321",
			"NOMITE": "CONSULTA GENERAL DE PRIMERA VEZ",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "85000.00",
			"TOTVAD": "74560.00",
			"VALIMD": "0.00",
			"VALDED": "10440.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa"
		},
		{
			"IDEPOS": 3,
			"CODITE": "789012",
			"NOMITE": "EXAMEN DE SANGRE COMPLETO",
			"UNIMED": "",
			"CANITE": "1.00",
			"VALITE": "45000.00",
			"TOTVAD": "39560.00",
			"VALIMD": "0.00",
			"VALDED": "5440.00",
			"PORIMD": "0.00",
			"CODIMD": "ZY",
			"NOMIMD": "No causa"
		}
	]
}

```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Impuestos (DETIMPDOC)

| Descripción                        | Nombre   | Ejemplo          | Especificación | Nota                                          | Oblig. |
|------------------------------------|----------|------------------|----------------|-----------------------------------------------|--------|
| Consecutivo impuestos documento    | IDEIMP   | 1                | INT(6)          | Número Consecutivo.                                       | SI     |
| Código impuesto                    | CODIMP   | 01               | CHAR(2)         | Código de impuesto tabla DIAN 6.2.2.                      | SI     |
| Nombre del impuesto                | NOMIMP   | IVA              | CHAR(255)       | Nombre del impuesto tabla DIAN 6.2.2.                     | SI     |
| Porcentaje impuesto                | PORIMP   | 19.00            | DEC(15,2)       |                                                          | SI     |
| Valor del impuesto                 | VALIMP   | 8781.42          | DEC(15,2)       | Valor impuesto resultado de multiplicar BASIMP * PORIMP.  | SI     |
| Base gravable impuesto             | BASIMP   | 46218.00         | DEC(15,2)       | Valor sobre el cual se calculan los impuestos.            | SI     |

##### **Ejemplo de DETIMP**

```json
{
	"DETIMPDOC": [
		{
			"IDEIMP": 1,
			"CODIMP": "ZY",
			"NOMIMP": "No causa",
			"PORIMP": "0.00",
			"VALIMP": "0.00",
			"BASIMP": "0.00"
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Detalle de Descuento (DETDES)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Consecutivo de descuento           | CONDES   |                  | INT(6)          | Número Consecutivo del descuento.                        | SI     |
| Código de descuento                | CODDES   |                  | CHAR(2)         | Ver tabla DIAN 6.3.8.                                    | SI     |
| Porcentaje de descuento            | PORDES   |                  | DEC(15,2)       |                                                          | SI     |
| Valor de descuento                 | VALDES   |                  | DEC(15,2)       |                                                          | SI     |
| Base sobre la cual se aplica el descuento | VABADE  |                  | DEC(15,2)       |                                                          | SI     |
| Texto abierto del descuento        | TEXDES   |                  | CHAR(255)       |                                                          | SI     |


##### **Ejemplo de DETDES**

```json
{
	"DETDES": [
		{
			"CONDES": "",
			"CODDES": "",
			"PORDES": "",
			"VALDES": "",
			"VABADE": "",
			"TEXDES": ""
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos Adicionales Documento (ADIDOC)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Identificador adicional            | IDEADD   | 1                | CHAR(6)         | Identificador adicional del documento.                    | SI     |
| Descripción adicional              | TEXADD   | Información adicional | CHAR(100)  | Descripción adicional del documento.                    | SI     |
| Valor adicional                    | VALNDD   | 1000.00          | DEC(15,2)       | Valor adicional en números.                              | NO     |
| Valor adicional en texto           | VALTDD   | Mil Pesos        | CHAR(255)       | Valor adicional en texto.                                | NO     |


##### **Ejemplo de ADIDOC**

```json
{
	"ADIDOC": [
		{
			"IDEADD": "1",
			"TEXADD": "",
			"VALNDD": "",
			"VALTDD": ""
		}
	]
}
```

Este JSON debe codificarse en base64 antes de ser incluido en el valor correspondiente dentro del XML SOAP.


## Campos de Detalle del Producto (DETALLEPRO)
| Descripción                        | Nombre   | Ejemplo          | Especificación  | Nota                                                    | Oblig. |
|------------------------------------|----------|------------------|-----------------|---------------------------------------------------------|--------|
| Consecutivo de detalle de productos | IDEPOS   | 1                | INT(6)          | Consecutivo de productos en el documento.                | SI     |
| Código de producto                 | CODITE   | 123456789        | CHAR(40)        | Código del producto en el detalle.                       | SI     |
| Código de posición del producto    | COPODP   | 01               | CHAR(2)         | Código de la posición del producto.                      | NO     |
| Nombre de la posición del producto | NOPODP   | Producto A       | CHAR(200)       | Nombre asignado a la posición del producto.              | NO     |
| Valor del producto                 | VAPODP   | 10000.00         | DEC(15,2)       | Valor del producto.                                      | SI     |
| Porcentaje del producto            | POPODP   | 19.00            | DEC(15,2)       | Porcentaje aplicado al producto.                         | SI     |



##### **Ejemplo de DETALLEPRO**

```json
{
	"DETALLEPRO": [
		{
			"IDEPOS": 1,
			"CODITE": "123456",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "264000.00",
			"POPODP": "0.00"
		},
		{
			"IDEPOS": 2,
			"CODITE": "654321",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "74560.00",
			"POPODP": "0.00"
		},
		{
			"IDEPOS": 3,
			"CODITE": "789012",
			"COPODP": "ZY",
			"NOPODP": "No causa",
			"VAPODP": "39560.00",
			"POPODP": "0.00"
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

## 5. **Relación entre las Cabeceras del XML y los Datos JSON**
El cuerpo de la petición SOAP incluye un bloque `<sendoc>` que contiene varios ítems donde cada uno tiene una clave (key) en el XML y un valor (value) que corresponde a un conjunto de datos JSON codificado en BASE64. A continuación, se describe la relación entre los nombres de las cabeceras XML y los datos JSON:

#### **Formato General de la Relación XML-JSON**
Cada bloque en el XML tiene la siguiente estructura:
```xml
<item>
    <key xsi:type="xsd:string">[nombre_campo_XML]</key>
    <value xsi:type="xsd:string">{JSON codificado}</value>
</item>
```
Donde `[nombre_campo_XML]` hace referencia a la sección específica del JSON que contiene la información relevante.

#### **Relación de Campos:**

| **Nombre en XML** | **Nombre en JSON** | **Descripción**                                      |
|-------------------|--------------------|------------------------------------------------------|
| `secado`          | `CABDOC`           | Información de la cabecera del documento (factura)   |
| `secldo`          | `CLIDOC`           | Información del cliente                              |
| `sededo`          | `DETDOC`           | Detalle de los productos o ítems del documento       |
| `sedepr`          | `DETALLEPRO`           | Detalle del producto adicional                       |
| `sedeim`          | `DETIMP`           | Detalle de los impuestos aplicados                   |
| `seaddo`          | `ADIDOC`           | Información adicional del documento                  |
| `sedaws`          | `DATOEMP`          | Datos de la empresa que emite el documento           |
| `datsal`          | `DATSAL`           | Información adicional relacionada con los ítems      |
| `pursal`          | `PURSAL`           | Datos adicionales de archivos, como anexos          |
| `prepai`          | `PREPAI`           | Información relacionada con el pago                  |


## 6. **Ejemplos Prácticos**

### **Ejemplo completo de petición SOAP**
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
                <!-- CABDOC - Información de la cabecera del documento -->
                <item>
                    <key xsi:type="xsd:string">secado</key>
                    <value xsi:type="xsd:string">{JSON CABDOC codificado en BASE64}</value>
                </item>

                <!-- CLIDOC - Información del cliente -->
                <item>
                    <key xsi:type="xsd:string">secldo</key>
                    <value xsi:type="xsd:string">{JSON CLIDOC codificado en BASE64}</value>
                </item>

                <!-- DETDOC - Detalle de los ítems del documento -->
                <item>
                    <key xsi:type="xsd:string">sededo</key>
                    <value xsi:type="xsd:string">{JSON DETDOC codificado en BASE64}</value>
                </item>

                <!-- DETALLEPRO - Detalle del producto adicional (vacío en este caso) -->
                <item>
                    <key xsi:type="xsd:string">sedepr</key>
                    <value xsi:type="xsd:string">e30=</value>
                </item>

                <!-- DETIMP - Detalle de los impuestos -->
                <item>
                    <key xsi:type="xsd:string">sedeim</key>
                    <value xsi:type="xsd:string">{JSON DETIMP codificado en BASE64}</value>
                </item>

                <!-- Información adicional del documento (vacío en este caso) -->
                <item>
                    <key xsi:type="xsd:string">seaddo</key>
                    <value xsi:type="xsd:string">e30=</value>
                </item>

                <!-- DATOEMP - Información de la empresa -->
                <item>
                    <key xsi:type="xsd:string">sedaws</key>
                    <value xsi:type="xsd:string">{JSON DATOEMP codificado en BASE64}</value>
                </item>

                <!-- Información adicional del cliente (vacío en este caso) -->
                <item>
                    <key xsi:type="xsd:string">sedecu</key>
                    <value xsi:type="xsd:string">e30=</value>
                </item>
            </sendoc>
        </ns1:send_document>
    </SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

**Nota:** Los json encriptados en BASE64 no deben ir contenidos entre las llaves cuando nos refereimos a {JSON DATOEMP codificado en BASE64} dentro del valor del item.
En este ejemplo, cada campo dentro del XML (`secado`, `secldo`, `sededo`, etc.) hace referencia a un bloque JSON específico que contiene datos codificados en BASE64. Es importante asegurarse de que todos los datos estén correctamente formateados y codificados antes de su envío.

---

### **7. Documentación Relacionada**

Para cumplir con los requisitos técnicos y normativos de la facturación electrónica en Colombia, es importante consultar la documentación oficial proporcionada por la DIAN. A continuación, se incluyen enlaces a los documentos clave y herramientas que son referenciados a lo largo de este manual:

#### **7.1. Documentación Oficial de la DIAN**
Este documento detalla las especificaciones técnicas para la implementación de la facturación electrónica de acuerdo con las normativas vigentes en Colombia:

- [Anexo Técnico Factura Electrónica de Venta - Versión 1.9](https://www.dian.gov.co/impuestos/factura-electronica/Documents/Anexo-Tecnico-Factura-Electronica-de-Venta-vr-1-9.pdf)

#### **7.2. Caja de Herramientas Técnica**
La DIAN proporciona una serie de recursos y herramientas técnicas que apoyan el proceso de integración de la facturación electrónica. Estos recursos incluyen documentación técnica adicional, ejemplos, y herramientas de validación:

- [Caja de Herramientas Técnica - Sistema de Facturación Electrónica](https://micrositios.dian.gov.co/sistema-de-facturacion-electronica/documentacion-tecnica/)

---
