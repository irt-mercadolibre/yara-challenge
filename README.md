# The Yara API Challenge

Como equipo de **seguridad informática** tenemos la necesidad de buscar en textos y binarios algunos patrones que pueden ir desde información sensible hasta malware. Para eso necesitamos integrar **[Yara](http://virustotal.github.io/yara/)** con una **API** que nos permita manejar reglas y analizar estos archivos o textos en busca de estos patrones.

Es importante que como esta API va a tener bastante trafico, no tenga que cargar las reglas cada vez que tenga que hacer un análisis.

Se puede implementar con el lenguaje de programación que prefieras, frameworks y librerias que creas necesarios pero si es importante usar [Docker](https://www.docker.com/) para que sea reproducible facilmente y podamos probarlo.

El challenge consta de una implementación básica y dos optativas y algunos extras.


## Implementación básica

La API deberá contar con tres métodos (Endpoints) que deben cumplir con este contrato

### Add rule
Metodo: `POST`
Path: `/api/rule`
Body:

    {
	    "name":"esto no es coca papi rule",
	    "rule":"rule EstoNoEsCocaPapiRule\r\n{\r\n strings:\r\n $my_text_string = \"esto no es coca papi\"\r\n condition:\r\n $my_text_string\r\n}"
    }

Response Code: `201` en caso de éxito y en caso de error un status code correspondiente al tipo de error

Response Body:

    {
	    "id": 1,
	    "name": "esto no es coca papi rule",
	    "rule": "rule EstoNoEsCocaPapiRule\r\n{\r\n strings:\r\n $my_text_string = \"esto no es coca papi\"\r\n condition:\r\n $my_text_string\r\n}"
    }
   
Curl de ejemplo:
   
    curl --request POST \
    --url http://localhost:8080/api/rule \
    --header 'content-type: application/json' \
    --data '{
    "name":"esto no es coca papi rule",
    "rule":"rule EstoNoEsCocaPapiRule\r\n{\r\n strings:\r\n $my_text_string = \"esto no es coca papi\"\r\n condition:\r\n $my_text_string\r\n}"
    }'

  ### Analyze text
Metodo: `POST`
Path: `/api/analyze/text`
Body:

    {
	    “text”:”esto es un texto a analizar”,
	    "rules": 
		    [
			    {"rule_id": 1},
			    {"rule_id": 2}
		    ]
    }
 Response Code: `200` en caso de éxito y en caso de error un status code correspondiente al tipo de error
 Response Body:

    {
		"status": "ok",
		"results": [
			{
				"rule_id": 1,
				"matched": true
			},
			{
				"rule_id": 2,
				"matched": false
		  }
		]
	}

   Curl de ejemplo:   

    curl --request POST \
      --url http://localhost:8080/api/analyze \
      --header 'content-type: application/json' \
      --data '{
    	“text”: ”estoesuntextoaanalizar”,
    	"rules": [
    		{
    			"rule_id": 1
    		},
    		{
    			"rule_id": 2
    		}
    	]
    }'

### Analyze file
Metodo: `POST`
Path: `/api/analyze/text`
Body:  

    multipart/form-data
    rules=1,2
    file=archivo.txt

 Response Code: `200` en caso de éxito y en caso de error un status code correspondiente al tipo de error
 Response Body:

    {
		"status": "ok",
		"results": [
			{
				"rule_id": 1,
				"matched": true
			},
			{
				"rule_id": 2,
				"matched": false
			}
		]
	}

Curl de ejemplo:

    curl -X POST \
      http://localhost:8080/api/analyze/file \
      -H 'content-type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' \
      -F file=@file \
      -F 'rules=1,2'

## Implementación optativa 1
Sumar al `POST`, un `PUT` y `GET` de reglas con persistencia en una base de datos para hacer un ABM completo de reglas de Yara.
Persistir en esta base de datos, los resultados de los textos y archivos analizados.

## Implementación optativa 2
Crear una regla Yara que permita encontrar tarjetas de crédito
Crear una regla Yara que permita encontrar `access_token` con el siguiente formato

    token de ejemplo: TOKEN_2014-06-03_112332
    2014-06-03: fecha de creacion del token en formato año-mes-dia
    112332: id de usuario

Crear una regla Yara que permita encontrar `access_token` que hayan sido creados después del 31 de enero de 2016

## Extras
Logging
Agregar tests
Documentación
Autenticación en la api
Sumar funcionalidades (ejemplo: dada una url, bajar el contenido y analizarlo con reglas yara)

**Recordá que cuanto mas fácil sea de reproducir y podamos ver todo lo que hiciste, mejor :D**

