---
title: Użyj geograficznego AI
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak używać maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficznej do analizowania danych i tworzenie modeli na podstawie danych geoprzestrzennych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278413"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Za pomocą maszyny wirtualnej analizy danych geograficznych ze sztucznej inteligencji

Maszyna wirtualna do nauki o danych sztucznej Inteligencji geograficznej umożliwia pobranie danych do analizy, wykonywanie danych, inteligencji i twórz modele dotyczące aplikacji sztucznej Inteligencji, które mogą korzystać informacji geoprzestrzennych. Po zainicjowaniu obsługi Data Science VM geograficznym AI i zalogowaniu się w usłudze ArcGIS Pro za pomocą konta ArcGIS możesz rozpocząć korzystanie z usługi ArcGIS Desktop i ArcGIs online. Możesz również uzyskać dostęp do ArcGIS z interfejsów języka Python i mostka językowego R, który jest wstępnie skonfigurowany w Data Science VM geograficznym. Aby tworzyć bogate aplikacje AI, Połącz Data Science VM geograficzną z platformami uczenia maszynowego i głębokiego uczenia oraz innym oprogramowaniem do nauki o danych, które są dla niego dostępne.  


## <a name="configuration-details"></a>Szczegóły konfiguracji

Biblioteka języka Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), która jest używana do interfejsu z ArcGIS, jest instalowana w globalnym głównym środowisku conda środowiska Data Science VM, które znajduje się w ```c:\anaconda```lokalizacji.

- Jeśli używasz języka Python w wierszu polecenia, uruchom ```activate``` polecenie, aby aktywować w głównym środowisku Python Conda.
- Jeśli używasz notesu IDE lub Jupyter, możesz wybrać środowisko lub jądro, aby upewnić się, że jesteś w odpowiednim środowisku Conda.

Mostek R do ArcGIS jest instalowany jako biblioteka języka R o nazwie [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) w głównym wystąpieniu autonomicznym Microsoft Machine Learning Server, które znajduje ```C:\Program Files\Microsoft\ML Server\R_SERVER```się w. Programy Visual Studio, RStudio i Jupyter są już wstępnie skonfigurowane do korzystania z tego środowiska r i będą miały dostęp do ```arcgisbinding``` biblioteki języka r. 


## <a name="geo-ai-data-science-vm-samples"></a>Przykłady maszyna wirtualna analizy danych geograficznych sztucznej Inteligencji

Oprócz przykładów opartych na środowisku uczenia maszynowego i głębokiego uczenia z Data Science VM podstawowej zestaw próbek geograficznych jest również dostarczany jako część geograficznego Data Science VM AI. Te przykłady mogą ułatwić szybkie rozpoczęcie tworzenia aplikacji AI przy użyciu danych geoprzestrzennych i oprogramowania ArcGIS:


1. [Wprowadzenie do analizy geograficznej przy użyciu języka Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Przykład wprowadzający pokazujący sposób pracy z danymi geograficznymi za pomocą interfejsu języka Python do ArcGIS jest udostępniany przez bibliotekę [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Pokazano również, jak połączyć tradycyjne Uczenie maszynowe z danymi geograficznymi, a następnie wizualizować wynik mapy w ArcGIS.

2. [Wprowadzenie do analizy geograficznej przy użyciu języka R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Przykład wprowadzający, który pokazuje, jak korzystać z danych geoprzestrzennych przy użyciu interfejsu języka R do ArcGIS, który jest udostępniany przez bibliotekę [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Klasyfikacja użycia gruntów na poziomie pikseli](https://github.com/Azure/pixel_level_land_classification): Samouczek, który ilustruje sposób tworzenia modelu sieci głębokiego neuronowych, który akceptuje obraz antenowy jako dane wejściowe i zwraca etykietę okładki gruntowej. Przykłady etykiet okładki gruntowej są *zalesione* i *wodne*. Model zwraca etykietę dla każdego piksela na obrazie. 


## <a name="next-steps"></a>Następne kroki

Więcej przykładów, korzystanie z maszyny Wirtualnej do nauki o danych, które są dostępne tutaj:

* [Przykłady](dsvm-samples-and-walkthroughs.md)