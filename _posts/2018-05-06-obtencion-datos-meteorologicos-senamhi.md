---
layout: post
title: "Obtención de datos meteorológicos libres de la web (SENMAHI) mediante scraping con R"
author: "Novvier Uscuchagua"
---

En muchos paises es muy dificil obtener información meteorológica de forma libre, teniendo muchas veces que realizar una solicitad a entidades publicas y/o comprando información de entidades privadas como única alternativa.

En Perú, el Servicio Nacional de Meteorología e Hidrología() publica en su página web información meteorológica de diversas estaciones ubicadas a lo largo del país, la cual son visualizados por mes y que cuenta con registros diarios.

Es posible extraer esta información con un simple copiar/pegar. Sin embargo, resulta tedioso realizarlo si se quiere obtener información de varias meses o años. Es ahí donde entra R.

R nos ayuda a extraer información de la web con una técnica llama "scraping" mediante el paquete "rvest" y en esta oportunidad utilizaremos esta técnica para obtener de forma rápida la información publicada por SENAMHI en su página web.

El código utilizado es el siguiente: [My page](/path/to/page.html)

```r
file <- NROW(LETTERS)
```
