---
title: Użyj geoauchy
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak używać maszyny wirtualnej do nauki o danych geoa i autu do analizowania danych i tworzenia modeli na podstawie danych geoprzestrzennych.
keywords: głębokie uczenie się, sztuczna inteligencja, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278413"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Korzystanie z maszyny wirtualnej nauki o danych geo sztucznej inteligencji

Maszyna wirtualna do nauki o danych geoalogicznej umożliwia pobieranie danych do analizy, wykonywanie wranglingu danych i tworzenie modeli dla aplikacji AI, które zużywają informacje geoprzestrzenne. Po zainicjowaniu obsługi administracyjnej maszyny wirtualnej geoauczych i zalogowaniu się do usługi ArcGIS Pro za pośrednictwem konta ArcGIS można rozpocząć interakcję z komputerami ArcGIS i systemem ArcGI w trybie online. Można również uzyskać dostęp do systemu ArcGIS z interfejsów języka Python i mostka języka R, który jest wstępnie skonfigurowany na maszynie wirtualnej nauki o danych geograficznych. Aby tworzyć zaawansowane aplikacje AI, połącz maszynę wirtualną nauki o danych geograficznych z platformami uczenia maszynowego i uczenia głębokiego oraz innym dostępnym na niej oprogramowaniem do nauki o danych.  


## <a name="configuration-details"></a>Szczegóły konfiguracji

Biblioteka Języka Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), która jest używana do interfejsu z arcgis, jest zainstalowana w ```c:\anaconda```globalnym środowisku conda głównego maszyny Wirtualnej nauki danych, który znajduje się w .

- Jeśli korzystasz z pythona w wierszu polecenia, uruchom, ```activate``` aby aktywować w środowisku pythonowym katalogu conda root.
- Jeśli używasz notebooka IDE lub Jupyter, możesz wybrać środowisko lub jądro, aby upewnić się, że znajdujesz się w odpowiednim środowisku conda.

Mostek R do systemu ArcGIS jest instalowany jako biblioteka języka R o nazwie [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) w głównym wystąpieniu autonomicznym programu Microsoft Machine Learning Server, które znajduje się w ```C:\Program Files\Microsoft\ML Server\R_SERVER```programie . Visual Studio, RStudio i Jupyter są już wstępnie skonfigurowane do korzystania ```arcgisbinding``` z tego środowiska języka R i będą miały dostęp do biblioteki języka R. 


## <a name="geo-ai-data-science-vm-samples"></a>Próbki maszyn wirtualnych do nauki o danych geoalogicznych

Oprócz próbek opartych na uczenia maszynowego i uczenia głębokiego opartych na platformie wirtualnej z podstawowej maszyny wirtualnej do nauki o danych zestaw próbek geoprzestrzennych jest również dostarczany jako część maszyny wirtualnej do nauki o danych geoau. Te przykłady mogą pomóc w rozpoczęciu tworzenia aplikacji AI przy użyciu danych geoprzestrzennych i oprogramowania ArcGIS:


1. [Wprowadzenie do analizy geoprzestrzennej w języku Python:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Przykład wprowadzający pokazujący, jak pracować z danymi geoprzestrzennymi za pośrednictwem interfejsu Pythona do arcGIS, jest dostarczany przez bibliotekę [arcpy.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Pokazuje również, jak połączyć tradycyjne uczenie maszynowe z danymi geoprzestrzennymi, a następnie wizualizować wynik na mapie w systemie ArcGIS.

2. [Wprowadzenie do analizy geoprzestrzennej za pomocą języka R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): przykład wprowadzający, który pokazuje, jak pracować z danymi geoprzestrzennymi przy użyciu interfejsu R do systemu ArcGIS dostarczonego przez [bibliotekę arcgisbinding.](https://github.com/R-ArcGIS/r-bridge) 

3. [Klasyfikacja użytkowania gruntów na poziomie piksela:](https://github.com/Azure/pixel_level_land_classification)Samouczek ilustrukt ilustruje, jak utworzyć model głębokiej sieci neuronowej, który akceptuje obraz lotniczy jako dane wejściowe i zwraca etykietę pokrywy lądowej. Przykłady etykiet na pokrycie terenu są *zalesione* i *wodne*. Model zwraca taką etykietę dla każdego piksela obrazu. 


## <a name="next-steps"></a>Następne kroki

Dodatkowe przykłady, które używają maszyny Wirtualnej do nauki o danych są dostępne tutaj:

* [Próbki](dsvm-samples-and-walkthroughs.md)