---
title: Microsoft Azure Data Box Edge — omówienie | Microsoft Docs
description: Opisuje Azure Data Box Edge, rozwiązanie magazynu, które korzysta z urządzenia fizycznego na potrzeby transferu sieciowego na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305436"
---
# <a name="what-is-azure-data-box-edge"></a>Co to jest usługa Azure Data Box Edge? 

Azure Data Box Edge to brzegowe urządzenie do przetwarzania danych z włączoną sztuczną inteligencją i funkcjami transferu danych sieciowych. Ten artykuł zawiera omówienie rozwiązania Data Box Edge, jego zalety, najważniejsze funkcje oraz scenariusze, w których można wdrożyć to urządzenie. 

Data Box Edge to rozwiązanie typu "sprzęt jako usługa". Firma Microsoft dostarcza urządzenie zarządzane przez chmurę z wbudowaną, programowalną bramą Gateway Array (FPGA), która umożliwia przyspieszenie AI i inferencing i oferuje wszystkie możliwości bramy magazynu. 

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej znajdują się różne scenariusze, w których Data Box Edge mogą być używane do szybkiego Machine Learning (ML) inferencing na granicy i przetwarzania wstępnego danych przed wysłaniem ich do platformy Azure.

- **Wnioskowanie z Azure Machine Learning** -with Data Box Edge, można uruchamiać modele ml, aby uzyskać szybkie wyniki, które mogą być przetwarzane przed wysłaniem danych do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować ponowne uczenie i ulepszanie modeli ML. Aby uzyskać więcej informacji na temat korzystania z szybszych modeli sprzętu Azure ML na urządzeniu Data Box Edge, zobacz [wdrażanie przyspieszanych modeli sprzętu Azure ml na Data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- Wstępnie Przetwarzaj dane przekształceń **danych** przed wysłaniem ich do platformy Azure w celu utworzenia bardziej funkcjonalnego zestawu danych. Wstępne przetwarzanie umożliwia: 

    - Agregowanie danych.
    - Zmodyfikuj dane, na przykład aby usunąć dane osobowe.
    - Podzbiór danych w celu zoptymalizowania magazynu i przepustowości lub do dalszej analizy.
    - Analizowanie zdarzeń IoT i reagowanie na nie. 

- **Transfer danych za pośrednictwem sieci na platformę Azure** — za pomocą rozwiązania Data Box Edge można szybko i łatwo transferować dane na platformę Azure, aby umożliwić ich dalsze przetwarzanie i analizę lub archiwizację. 


## <a name="key-capabilities"></a>Najważniejsze możliwości

Rozwiązanie Data Box Edge oferuje następujące możliwości:

|Możliwość |Opis  |
|---------|---------|
|Przyspieszone inferencing AI| Włączone przez wbudowaną FPGA.|
|Obliczanie       |Umożliwia analizowanie, przetwarzanie i filtrowanie danych.|
|Wysoka wydajność | Obliczenia o wysokiej wydajności i transfery danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze. Lokalna pamięć podręczna na urządzeniu służy do szybkiego dostępu do ostatnio używanych plików.|
|Zarządzane przez chmurę     |Urządzenia i usługi są zarządzane za pośrednictwem Azure Portal.  |
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zapoznaj się z [wymaganiami systemowymi rozwiązania Data Box Edge](data-box-edge-system-requirements.md).|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*.|
|Ograniczanie przepustowości| Ograniczanie użycia przepustowości w godzinach szczytu.|


## <a name="components"></a>Składniki

Rozwiązanie Data Box Edge składa się z zasobu rozwiązania Data Box Edge, urządzenia fizycznego Data Box Edge i lokalnego internetowego interfejsu użytkownika.

* **Urządzenie fizyczne Data Box Edge** — dostarczany przez firmę Microsoft, montowany w stojaku serwer 1U, który można skonfigurować na potrzeby wysyłania danych na platformę Azure. 
    
* **Zasób rozwiązania Data Box Edge** — zasób w witrynie Azure Portal, który pozwala na zarządzanie urządzeniem Data Box Edge za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Korzystając z zasobu rozwiązania Data Box Edge, można tworzyć zasoby oraz zarządzać nimi, wyświetlać urządzenia i alerty oraz zarządzać nimi, a także zarządzać udziałami.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Aby uzyskać więcej informacji, przejdź do [pozycji Utwórz zamówienie dla Data Box Edge urządzenia](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Lokalny internetowy interfejs użytkownika** — korzystając z lokalnego internetowego interfejsu użytkownika, można uruchamiać diagnostykę, wyłączać i uruchamiać ponownie urządzenie Data Box Edge, wyświetlać dzienniki kopiowania oraz kontaktować się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje na temat korzystania z internetowego interfejsu użytkownika, przejdź do tematu [Administrowanie rozwiązaniem Data Box za pomocą internetowego interfejsu użytkownika](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne Data Box Edge, zasób platformy Azure i docelowe konto magazynu, do którego są transferowane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — Aby uzyskać listę wszystkich regionów, w których dostępny jest zasób Data Box Edge, przejdź do pozycji [dostępne produkty platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Edge można również wdrożyć w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których przechowywane są konta magazynu Data Box Edge dane, powinny znajdować się w pobliżu lokalizacji urządzenia w celu uzyskania optymalnej wydajności. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność. 


## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj [wymagania systemowe rozwiązania Data Box Edge](data-box-edge-system-requirements.md).
- Zapoznaj się z [limitami rozwiązania Data Box Edge](data-box-edge-limits.md).
- Wdróż rozwiązanie [Azure Data Box Edge](data-box-edge-deploy-prep.md) w witrynie Azure Portal.




