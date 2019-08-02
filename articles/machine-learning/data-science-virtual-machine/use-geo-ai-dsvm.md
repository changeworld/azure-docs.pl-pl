---
title: Za pomocą geograficznie sztucznej inteligencji maszyny wirtualnej analizy danych — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać maszyny wirtualnej do nauki o danych sztucznej Inteligencji geograficznej do analizowania danych i tworzenie modeli na podstawie danych geoprzestrzennych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, Geoprzestrzenna analiza
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 9bca7089e6137b3780e3d22f50887e880be29d8e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565079"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Za pomocą maszyny wirtualnej analizy danych geograficznych ze sztucznej inteligencji

Maszyna wirtualna do nauki o danych sztucznej Inteligencji geograficznej umożliwia pobranie danych do analizy, wykonywanie danych, inteligencji i twórz modele dotyczące aplikacji sztucznej Inteligencji, które mogą korzystać informacji geoprzestrzennych. Po aprowizacji maszyny Wirtualnej do nauki o geograficznie sztucznej Inteligencji danych i logowanie ArcGIS Pro z Twoim kontem ArcGIS możesz rozpocząć interakcję z komponentu ArcGIS pulpitu i ArcGis online. Można także przejść ArcGIS z interfejsów języka Python i mostka języka R, wstępnie skonfigurowane na maszynie Wirtualnej do nauki o danych geograficznych. Tworzyć zaawansowane aplikacje sztucznej Inteligencji, połącz go z uczenia maszynowego i głębokiego uczenia struktury i inne oprogramowanie do nauki o danych dostępne na maszynie Wirtualnej do nauki o danych.  


## <a name="configuration-details"></a>Szczegóły konfiguracji

Biblioteka języka Python [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), który jest używany do interfejsu z platformy ArcGIS jest zainstalowany w środowiska conda główny globalny maszyna wirtualna do nauki o danych, który znajduje się na ```c:\anaconda```. 

- Jeśli używasz języka Python w wierszu polecenia, uruchom ```activate``` można aktywować w środowisku Python głównego narzędzia conda. 
- Jeśli używasz środowiska IDE lub Jupyter notebook można wybrać środowisko lub jądra, aby upewnić się, że w środowisku poprawne conda. 

Mostek R ArcGIS jest instalowany jako biblioteka języka R, o nazwie [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) w głównym R Microsoft wystąpienia autonomicznego serwera znajdującego się w ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Program Visual Studio, Jupyter i programu RStudio znajdują się już wstępnie skonfigurowana pod kątem użycia tego środowiska języka R i będą mieć dostęp do ```arcgisbinding``` biblioteki języka R. 


## <a name="geo-ai-data-science-vm-samples"></a>Przykłady maszyna wirtualna analizy danych geograficznych sztucznej Inteligencji

Oprócz uczenia Maszynowego i oparte na strukturze próbki z podstawowej maszyny Wirtualnej do nauki o danych do uczenia głębokiego udostępniane są również uzyskać zestaw przykładów geoprzestrzenne jako część maszyna wirtualna do nauki o danych sztucznej Inteligencji geograficznie. Te przykłady mogą pomóc szybko Rozpocznij korzystanie z usługi opracowywanie aplikacji sztucznej Inteligencji przy użyciu danych Geoprzestrzennych i oprogramowania ArcGIS. 


1. [Wprowadzenie do analizy geograficznej przy użyciu języka Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Przykład wprowadzający pokazujący sposób pracy z danymi geograficznymi przy użyciu interfejsu języka Python do ArcGIS zapewnianego przez bibliotekę [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) . Pokazano także, jak połączyć tradycyjnych uczenie maszynowe przy użyciu danych geoprzestrzennych oraz wizualizacja wyników na mapie w ArcGIS. 

2. [Wprowadzenie do analizy geograficznej przy użyciu języka R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Przykład wprowadzający, który pokazuje, jak korzystać z danych geoprzestrzennych przy użyciu interfejsu języka R do ArcGIS dostarczonych przez bibliotekę [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) . 

3. [Klasyfikacja użycia gruntów na poziomie pikseli](https://github.com/Azure/pixel_level_land_classification): Samouczek, który ilustruje sposób tworzenia modelu sieci głębokiego neuronowych, który akceptuje obraz antenowy jako dane wejściowe i zwraca etykietę okładki gruntowej. Pokrywy gruntów etykiet, które należą do nich "leśnych" lub "water". Model zwraca etykietę dla każdego piksela na obrazie. Model został skompilowany przy użyciu typu open source firmy Microsoft [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) platforma do uczenia głębokiego. 


## <a name="next-steps"></a>Następne kroki

Więcej przykładów, korzystanie z maszyny Wirtualnej do nauki o danych, które są dostępne tutaj:

* [Przykłady](dsvm-samples-and-walkthroughs.md)

