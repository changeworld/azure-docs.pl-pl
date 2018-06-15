---
title: Przy użyciu geograficznie sztucznego analizy danych nauki maszyny wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Jak używać maszynę wirtualną z magazynu geograficznie AI na platformie Azure.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyny wirtualnej analizy danych, dane geograficzne analityka
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f346b086a0269f247d64edf9346b01849ba3d0ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408041"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Przy użyciu maszyny wirtualnej z magazynu geograficznie sztucznego analizy danych nauki

Maszynę Wirtualną z magazynu geograficznie AI danych nauki umożliwia pobieranie danych do analizy, wykonywanie wrangling danych i tworzenie modeli dla aplikacji AI, które wykorzystują informacje geograficzne. Po możesz zainicjowaniu obsługi maszyny Wirtualnej nauki geograficznie AI danych i rejestracji w ArcGIS Pro z Twoim kontem ArcGIS, możesz przystąpić do interakcji z pulpitu ArcGIS i ArcGis online. Można także przejść ArcGIS z interfejsów Python i Mostek języka R wstępnie skonfigurowane na maszynie Wirtualnej nauki replikacji geograficznej — Warstwa danych. Do tworzenia rozbudowanych aplikacji AI, należy połączyć ją z komputera nauki i głębokość uczenia struktury i innego oprogramowania nauki danych dostępnych na maszynie Wirtualnej analizy danych.  


## <a name="configuration-details"></a>Szczegóły konfiguracji

Biblioteka języka Python [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), który służy do interfejsu z ArcGIS jest zainstalowany w środowisku conda globalne głównego maszyny wirtualnej analizy danych, która znajduje się w ```c:\anaconda```. 

- Jeśli używasz języka Python w wierszu polecenia, uruchom ```activate``` aktywować do środowiska Python conda głównego. 
- Jeśli używasz IDE lub Jupyter notebook, możesz wybrać środowiska lub jądra, aby mieć pewność, że w środowisku conda poprawne. 

Mostek R ArcGIS jest instalowany jako biblioteka R o nazwie [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) w głównym R Microsoft wystąpienia autonomicznego serwera znajdującego się w ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, Jupyter i programu RStudio są już wstępnie skonfigurowane do używania tego środowiska R i będą mieć dostęp do ```arcgisbinding``` biblioteki R. 


## <a name="geo-ai-data-science-vm-samples"></a>Przykłady geograficznie AI danych nauki VM

Oprócz ML i bezpośrednie learning framework na podstawie próbki z podstawowej maszyny Wirtualnej analizy danych zestaw próbek dane geograficzne jest również udostępniany w ramach maszyny wirtualnej z magazynu geograficznie AI danych nauki. Te przykłady ułatwia szybkie rozpoczęcie tworzenia programowania aplikacji AI oprogramowanie ArcGIS i dane geograficzne. 


1. [Pobieranie podawane z analizy geograficzne języka Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): wprowadzające przykład przedstawiający sposób pracy z danymi dane geograficzne przy użyciu interfejsu Python ArcGIS dostarczonych przez [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) biblioteki. Przedstawiono również sposób można łączyć maszyny tradycyjnego uczenie przy użyciu dane geograficzne i wizualizacja wyników na mapie w ArcGIS. 

2. [Pobieranie podawane z geograficzne analizy w usłudze R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): wprowadzające próbki, pokazujący sposób pracy z danymi dane geograficzne przy użyciu interfejsu R ArcGIS dostarczonych przez [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) biblioteki. 

3. [Użytkowania ziemi pikseli poziom klasyfikacji](https://github.com/Azure/pixel_level_land_classification): samouczek, która ilustruje sposób tworzenia modelu sieci neuronowej głębokość, który akceptuje satelitarnej obrazu jako dane wejściowe i zwraca etykietę okładce ziemi. Przykłady obejmują ziemi etykiety są "leśnych" lub "limitu górnego." Model zwraca etykietę dla każdego piksela obrazu. Model został zbudowany przy użyciu open source firmy Microsoft [kognitywnych Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) głębokie learning framework. W przykładzie przedstawiono również sposób skalowania w poziomie udział w szkoleniach dotyczących [AI usługi partia zadań Azure](https://docs.microsoft.com/azure/batch-ai/) i korzystanie z modelu prognozy oprogramowania ArcGIS Pro. 


## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, korzystających z maszyny Wirtualnej nauki danych są dostępne w tym miejscu:

* [Przykłady](dsvm-samples-and-walkthroughs.md)

