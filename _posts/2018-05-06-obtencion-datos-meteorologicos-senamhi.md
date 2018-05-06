---
layout: post
title: "Obtención de datos meteorológicos libres de la web (SENMAHI) mediante scraping con R"
author: "Novvier Uscuchagua"
---

En muchos países es muy difícil obtener información meteorológica de forma libre, teniendo muchas veces que realizar una solicitud a entidades publicas y/o comprAndo información de entidades privadas como única alternativa.

En Perú, el Servicio Nacional de Meteorología e Histología ([SENAMHI](http://www.senamhi.gob.pe/)) publica en su página web información meteorológica de diversas estaciones ubicadas a lo largo del país, la cual son visualizados por mes y que cuenta con registros diarios.

Es posible extraer esta información con un simple copiar/pegar. Sin embargo, resulta tedioso realizarlo si se quiere obtener información de varias meses o años. **Es ahí donde entra R**.

R nos ayuda a extraer información de la web con una técnica llama "scraping" mediante el paquete "rvest" y en esta oportunidad utilizaremos esta técnica para obtener de forma rápida la información publicada por SENAMHI en su página web.

El código utilizado es el siguiente:
```r
senamhi <- function(start, end, station){
  #
  # Load libraries
  pkgTest <- function(x) {
    if (!require(x,character.only = TRUE)) {
      install.packages(x,dep=TRUE)
        if(!require(x,character.only = TRUE))
          stop("Package not found")
    }
  }
  pkgTest("rvest")
  pkgTest("plyr")
  #
  # Dates generate by month mes
  start <- paste0(start, "-1")
  end <- paste0(end, "-1")
  date <- seq(as.Date(start), as.Date(end), by = "month")
  date <- gsub(' |-','',substr(as.character(date),1,7))
  
  # Extract web-senamhi data for each month
  extraer <- function(date, station){
    url <- paste0("http://www.senamhi.gob.pe/include_mapas/",
                  "_dat_esta_tipo02.php?estaciones=",
                  station,"&tipo=CON&CBOFiltro=",date,"&t_e=M")
    webpage <- read_html(url)
    tbls <- html_nodes(webpage, "table")
    tbls_ls <- webpage %>%
      html_nodes("table") %>%
      html_table(fill = TRUE)
    tbls_ls[[1]][-1:-2,]
  }
  
  # Bucle for estract every month 
  x <- list()
  for (i in 1:length(date)){
    x[[i]] <- extraer(date[i],station)
  }
  x <- ldply(x) # merge data
  colnames(x) <- c("date","Tmax","Tmin","Tdry07","Tdry13","Tdry19",
          "Twed07","Twed13","Twed19","PP07","PP09","wd","ws")
  #
  # Change nominal to numeric the wind speed values
  WinDirNum <- function(x) {
    lx = length(x)
    y = rep("",lx)
    name = c("C","NNE","NE","ENE","E","ESE","SE",
      "SSE","S","SSW","SW","WSW","W","WNW","NW","NNW","N")
    value = seq(0,360,22.5)
    #
    for (i in 1:lx){
      z = value[which(name == x[i])]
      if(length(z) == 0) y[i] = ""
      else y[i] = z 
    }
    #
    return(y)
  }
  x$wd.n <- x$wd
  x$wd.n[x$wd.n == ""] <- NA
  x$wd.n <- as.factor(x$wd.n)
  x$wd <- WinDirNum(x$wd)
  #
  # format date
  x$date <- gsub('-', '.-', x$date)
  x$date <- gsub('Sep', 'Set', x$date)
  x$date <- as.POSIXct(strptime(x$date, "%d.-%b-%Y"))
  #
  # all to numeric
  x[, 2:13] <- lapply(x[, 2:13], as.numeric)
  #
  return(x)
}
```
También pueden ver el código y sus futuras modificaciones en mi [github](https://github.com/novvier/ImportData).
La función "senamhi" posee tres parámetros:

1. La fecha de inicio de los datos requeridos
2. La fecha de término de los datos requeridos
3. Código de la estación

Las fechas de inicio y término deberán ser ingresadas como texto (_character_) con el formato "año-mes" (Ejem: "2017-1"). Para obtener el código de la estación simplemente realizamos una búsqueda en el página web del SENAMHI o nos dirigimos al siguiente enlace: [CLICK](http://senamhi.gob.pe/include_mapas/_map_data_hist.php). Al momento de solicitar la visualización de los datos, podemos observar el código de la estación en la barra de direcciones:

![alt text][logo](https://novvier.github.io/img/data_senamhi.JPG)

La imagen muestra el código de la estación "Cerro de Pasco" resaltada: 000593.

Si queremos obtener la información de todo el año 2017, entonces nuestro código será el siguiente:

```r
datos <- senamhi("2017-1 ","2017-12 ","000593")
```
La función "senamhi" ademas de extraer los datos de la estación y de entre las fechas indicadas, también convierte los registros de dirección de viento en datos numéricos (grados sexagecimales). Ejemplo: NNE (Nor-noreste) = 22.5°.

Los datos son importados al entorno de R. Si queremos importar a una hoja de EXCEL podemos utilizar el paquete "writexl" con el siguiente código:

```r
write_xlsx(datos, "datos.xlsx")
```

No olviden que los datos obtenidos carecen de control de calidad. Si requieren datos fiables pueden contactarse directamente con el [SENAMHI](http://www.senamhi.gob.pe/).
