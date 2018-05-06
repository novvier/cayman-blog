---
layout: post
title: "Obtención de datos meteorológicos libres de la web (SENMAHI) mediante scraping con R"
author: "Novvier Uscuchagua"
---

En muchos paises es muy dificil obtener información meteorológica de forma libre, teniendo muchas veces que realizar una solicitad a entidades publicas y/o comprando información de entidades privadas como única alternativa.

En Perú, el Servicio Nacional de Meteorología e Hidrología({% SENMAHI {{ http://www.senamhi.gob.pe/ }} %}) publica en su página web información meteorológica de diversas estaciones ubicadas a lo largo del país, la cual son visualizados por mes y que cuenta con registros diarios.

Es posible extraer esta información con un simple copiar/pegar. Sin embargo, resulta tedioso realizarlo si se quiere obtener información de varias meses o años. Es ahí donde entra R.

R nos ayuda a extraer información de la web con una técnica llama "scraping" mediante el paquete "rvest" y en esta oportunidad utilizaremos esta técnica para obtener de forma rápida la información publicada por SENAMHI en su página web.

El código utilizado es el siguiente:

También pueden ver el código y sus futuras modificaciones en mi {% GITHUB {{ https://github.com/novvier/ImportData }} %}

La función "senamhi" tres informaciones:
1. La fecha de inicio de los datos requeridos
2. La fecha de término de los datos requeridos
3. Código de la estación

Las fechas de inicio y término deberán ser ingresadas como texto (character) con el formato "año-mes" (Ejem: "2017-1"). Para obtener el código de la estación simplemente realizamos una búsqueda en el página web del SENAMHI o nos dirigimos al siguiente enlace: {% CLICK {{ http://senamhi.gob.pe/include_mapas/_map_data_hist.php }} %}. Al momento de solicitar la visualización de los datos, podemos observar el código de la estación en la barra de direcciones:

La imagen muestra el código de la estación "Cerro de Pasco" resaltada: 000593.
