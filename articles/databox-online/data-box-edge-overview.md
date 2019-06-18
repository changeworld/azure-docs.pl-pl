---
title: Microsoft Azure Data Box Edge — omówienie | Microsoft Docs
description: W tym artykule opisano Azure Data Box Edge — rozwiązanie magazynu, które za pomocą urządzenia fizycznego realizuje oparty na sieci transfer na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/11/2019
ms.author: alkohli
ms.openlocfilehash: 6e1870093dc89856e093267172418e9c7330c479
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080576"
---
# <a name="what-is-azure-data-box-edge"></a>Co to jest usługa Azure Data Box Edge? 

Azure Data Box Edge to rozwiązanie magazynu umożliwiające przetwarzanie danych i wysyłanie ich za pośrednictwem sieci na platformę Azure. Ten artykuł zawiera omówienie rozwiązania Data Box Edge, jego zalety, najważniejsze funkcje oraz scenariusze, w których można wdrożyć to urządzenie. 

Rozwiązanie Data Box Edge przyspiesza bezpieczny transfer danych, używając urządzenia fizycznego dostarczanego przez firmę Microsoft. Urządzenie fizyczne znajduje się w środowisku lokalnym, a dane są na nim zapisywane przy użyciu protokołów NFS i SMB. 

Rozwiązanie Data Box Edge ma wszystkie funkcje bramy usługi Data Box Gateway. Rozwiązanie Data Box jest dodatkowo wyposażone w funkcje obliczeń brzegowych z obsługą sztucznej inteligencji, które ułatwiają analizowanie, przetwarzanie i filtrowanie danych podczas ich przenoszenia do blokowego obiektu blob na platformie Azure, stronicowego obiektu blob lub usługi Azure Files.  

## <a name="use-cases"></a>Przypadki zastosowań

Azure Data Box Edge to brzegowe urządzenie do przetwarzania danych z włączoną sztuczną inteligencją i funkcjami transferu danych sieciowych. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Edge do transferu danych.

- **Wstępne przetwarzanie danych** — analizuj dane ze środowisk lokalnych lub urządzeń IoT, aby szybko uzyskać wynik, pozostając w pobliżu miejsca generowania danych. Rozwiązanie Data Box Edge przenosi pełen zestaw danych do chmury, aby przeprowadzić bardziej zaawansowane przetwarzanie lub głębszą analizę.  Wstępne przetwarzanie umożliwia: 

    - Agregowanie danych.
    - Modyfikowanie danych, na przykład w celu usunięcia danych osobowych.
    - Dzielenie na podzestawy i transferowanie danych potrzebnych do głębszej analizy w chmurze.
    - Analizowanie zdarzeń IoT i reagowanie na nie. 

- **Usługa Azure Machine Learning z wnioskowaniem** — rozwiązanie Data Box Edge umożliwia uruchamianie modeli uczenia maszynowego (Machine Learning, ML) w celu szybkiego uzyskania wyników, na których można pracować przed wysłaniem danych do chmury. Pełny zestaw danych można przenieść w dalszym ciągu wytrenujesz i zwiększyć swoje modele uczenia Maszynowego. Aby uzyskać więcej informacji na temat sposobu korzystania z funkcji sprzętu uczenie Maszynowe Azure jej jako przyspieszonej modele na urządzeniu usługi Databox Edge, zobacz [wdrożenia usługi Azure ML przyspieszane modele na urządzeniach brzegowych Databox](https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Transfer danych za pośrednictwem sieci na platformę Azure** — za pomocą rozwiązania Data Box Edge można szybko i łatwo transferować dane na platformę Azure, aby umożliwić ich dalsze przetwarzanie i analizę lub archiwizację. 

## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Edge oferuje następujące korzyści:

- **Łatwy transfer danych** — sprawia, że przenoszenie danych do i z usługi Azure Storage jest tak proste jak za pomocą lokalnego udziału sieciowego.  
- **Wysoka wydajność** — umożliwia transfery o wysokiej wydajności do i z platformy Azure. 
- **Szybki dostęp** — buforuje najnowsze pliki w celu zapewnienia szybkiego dostępu do plików lokalnych.  
- **Ograniczone użycie przepustowości** — dane mogą zostać zapisane na platformie Azure nawet przy ograniczonych możliwościach użycia sieci w czasie godzin szczytu.  
- **Przekształcanie danych** — umożliwia analizę, przetwarzanie i filtrowanie danych podczas ich przenoszenia na platformę Azure.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Rozwiązanie Data Box Edge oferuje następujące możliwości:

|Możliwości |Opis  |
|---------|---------|
|Wysoka wydajność     | W pełni zautomatyzowany i w wysokim stopniu zoptymalizowany transfer danych oraz wysoka przepustowość.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zapoznaj się z [wymaganiami systemowymi rozwiązania Data Box Edge](https://aka.ms/dbe-docs).|
|Obliczanie       |Umożliwia analizowanie, przetwarzanie i filtrowanie danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze.|
|Szybki dostęp     | Lokalna pamięć podręczna na urządzeniu zapewniająca szybki dostęp do ostatnio używanych plików.|
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*.       |
|Odporność     | Wbudowana odporność sieci.        |


## <a name="components"></a>Składniki

Rozwiązanie Data Box Edge składa się z zasobu rozwiązania Data Box Edge, urządzenia fizycznego Data Box Edge i lokalnego internetowego interfejsu użytkownika.

* **Urządzenie fizyczne Data Box Edge** — dostarczany przez firmę Microsoft, montowany w stojaku serwer 1U, który można skonfigurować na potrzeby wysyłania danych na platformę Azure. 
    
* **Zasób rozwiązania Data Box Edge** — zasób w witrynie Azure Portal, który pozwala na zarządzanie urządzeniem Data Box Edge za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Korzystając z zasobu rozwiązania Data Box Edge, można tworzyć zasoby oraz zarządzać nimi, wyświetlać urządzenia i alerty oraz zarządzać nimi, a także zarządzać udziałami.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Aby uzyskać więcej informacji, przejdź do tematu [Administrowanie urządzeniem Data Box Edge za pomocą usługi Data Box Edge](https://aka.ms/dbe-docs).

* **Lokalny internetowy interfejs użytkownika** — korzystając z lokalnego internetowego interfejsu użytkownika, można uruchamiać diagnostykę, wyłączać i uruchamiać ponownie urządzenie Data Box Edge, wyświetlać dzienniki kopiowania oraz kontaktować się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje na temat korzystania z internetowego interfejsu użytkownika, przejdź do tematu [Administrowanie rozwiązaniem Data Box za pomocą internetowego interfejsu użytkownika](https://aka.ms/dbe-docs).


## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne Data Box Edge, zasób platformy Azure i docelowe konto magazynu, do którego są transferowane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobu** — w tej wersji zasób rozwiązania Data Box Edge jest dostępny w następujących regionach:
    - **Stany Zjednoczone** — wschodnie stany USA
    - **Unia Europejska** — Europa Zachodnia
    - **Azja i Pacyfik** — Azja Południowo-Wschodnia
    
    Krawędź pola danych można wdrożyć w taki sposób, w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. 

    Regiony, w których znajdują się konta magazynu zawierające dane przesłane za pomocą rozwiązania Data Box, powinny mieścić się blisko lokalizacji urządzenia, aby zapewnić optymalną wydajność. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność. 


## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj [wymagania systemowe rozwiązania Data Box Edge](data-box-edge-system-requirements.md).
- Zapoznaj się z [limitami rozwiązania Data Box Edge](data-box-edge-limits.md).
- Wdróż rozwiązanie [Azure Data Box Edge](data-box-edge-deploy-prep.md) w witrynie Azure Portal.




