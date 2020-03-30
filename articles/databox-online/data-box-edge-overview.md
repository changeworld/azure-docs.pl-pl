---
title: Omówienie usługi Microsoft Azure Stack Edge | Dokumenty firmy Microsoft
description: W tym artykule opisano usługę Azure Stack Edge, rozwiązanie magazynu, które używa urządzenia fizycznego do transferu opartego na sieci na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: f463e8883efd5e2dfc4d7fff80912c193665b850
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399806"
---
# <a name="what-is-azure-stack-edge"></a>Co to jest rozwiązanie Azure Stack Edge?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Usługa Azure Stack Edge to urządzenie do przetwarzania danych brzegowych obsługujących sztuczną inteligencję z możliwością transferu danych sieciowych. W tym artykule przedstawiono omówienie rozwiązania usługi Azure Stack Edge, korzyści, możliwości klucza i scenariuszy, w których można wdrożyć to urządzenie.

Usługa Azure Stack Edge to rozwiązanie sprzętowe jako usługa. Firma Microsoft dostarcza urządzenie zarządzane w chmurze z wbudowaną macierzą FPGA (Field Programmable Gate Array), która umożliwia przyspieszone wnioskowanie o interfejsie AI i ma wszystkie możliwości bramy sieciowej pamięci masowej.

## <a name="use-cases"></a>Przypadki zastosowań

Poniżej przedstawiono różne scenariusze, w których usługa Azure Stack Edge może służyć do wnioskowania w trybie rapid Machine Learning (ML) na krawędzi i wstępnego przetwarzania danych przed wysłaniem ich na platformę Azure.

- **Wnioskowanie z usługi Azure Machine Learning** — za pomocą usługi Azure Stack Edge można uruchamiać modele uczenia maszynowego, aby uzyskać szybkie wyniki, które można podjąć, zanim dane zostaną wysłane do chmury. Pełny zestaw danych można opcjonalnie przesłać, aby kontynuować przekwalifikowanie i ulepszanie modeli uczenia maszynowych. Aby uzyskać więcej informacji na temat używania modeli przyspieszanych sprzętowo usługi Azure ML na urządzeniu usługi Azure Stack Edge, zobacz [Wdrażanie modeli przyspieszanych sprzętowo usługi Azure ML w usłudze Azure Stack Edge.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server)

- **Przetwarzanie wstępne danych** — przekształcanie danych przed wysłaniem ich na platformę Azure w celu utworzenia bardziej użytecznego zestawu danych. Wstępne przetwarzanie umożliwia: 

    - Agregowanie danych.
    - Modyfikuj dane, na przykład w celu usunięcia danych osobowych.
    - Podzbiór danych w celu optymalizacji pamięci masowej i przepustowości lub do dalszej analizy.
    - Analizowanie zdarzeń IoT i reagowanie na nie. 

- **Przenoszenie danych za pośrednictwem sieci na platformę Azure** — umożliwia łatwe i szybkie przesyłanie danych na platformę Azure za pomocą usługi Azure, aby umożliwić dalsze obliczenia i analizy lub do celów archiwalnych. 

## <a name="key-capabilities"></a>Najważniejsze możliwości

Usługa Azure Stack Edge ma następujące możliwości:

|Możliwości |Opis  |
|---------|---------|
|Przyspieszone wnioskowanie o SI| Włączone przez wbudowaną fpga.|
|Obliczanie       |Umożliwia analizowanie, przetwarzanie i filtrowanie danych.|
|Wysoka wydajność | Obliczenia o wysokiej wydajności i transfer danych.|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze. Lokalna pamięć podręczna na urządzeniu służy do szybkiego dostępu do ostatnio używanych plików.|
|Zarządzane w chmurze     |Urządzenia i usługa są zarządzane za pośrednictwem witryny Azure portal.  |
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zobacz [Wymagania systemowe usługi Azure Stack Edge](data-box-edge-system-requirements.md).|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*.|
|Ograniczanie przepustowości| Przepustnica w celu ograniczenia wykorzystania przepustowości w godzinach szczytu.|
|ExpressRoute | Dodano bezpieczeństwo za pośrednictwem usługi ExpressRoute. Użyj konfiguracji komunikacji równorzędnej, w której ruch z urządzeń lokalnych do punktów końcowych magazynu w chmurze jest przesurzany przez usługę ExpressRoute. Aby uzyskać więcej informacji, zobacz [Omówienie usługi ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Składniki

Rozwiązanie usługi Azure Stack Edge składa się z zasobów usługi Azure Stack Edge, urządzenia fizycznego usługi Azure Stack Edge i lokalnego interfejsu użytkownika sieci web.

* **Urządzenie fizyczne usługi Azure Stack Edge** — serwer montowany w stelażu 1U dostarczony przez firmę Microsoft, który można skonfigurować do wysyłania danych na platformę Azure.
    
* **Zasób usługi Azure Stack Edge** — zasób w portalu Azure portal, który umożliwia zarządzanie urządzeniem usługi Azure Stack Edge za pomocą interfejsu sieci web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Zasób usługi Azure Stack Edge służy do tworzenia zasobów, wyświetlania i zarządzania urządzeniami i alertami oraz zarządzania nimi oraz zarządzania nimi.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Aby uzyskać więcej informacji, przejdź do [tworzenia zamówienia dla urządzenia usługi Azure Stack Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Lokalny interfejs użytkownika sieci Web usługi Azure Stack Edge** — użyj lokalnego interfejsu użytkownika sieci Web, aby uruchomić diagnostykę, zamknąć i ponownie uruchomić urządzenie usługi Azure Stack Edge, wyświetlić dzienniki kopiowania i skontaktować się z pomocą techniczną firmy Microsoft, aby złożyć żądanie usługi.

    <!--![The Azure Stack Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Aby uzyskać informacje dotyczące korzystania z interfejsu użytkownika opartego na sieci Web, przejdź do [witryny Interfejsu użytkownika opartego na sieci Web do administrowania usługą Azure Stack Edge](data-box-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne usługi Azure Stack Edge, zasób platformy Azure i docelowe konto magazynu, do którego przesyłane są dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobów** — aby uzyskać listę wszystkich regionów, w których dostępny jest zasób usługi Azure Stack Edge, zobacz [Produkty platformy Azure dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Usługę Azure Stack Edge można również wdrożyć w chmurze azure dla instytucji rządowych. Aby uzyskać więcej informacji, zobacz [Co to jest platforma Azure government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. Regiony, w których konta magazynu przechowują dane usługi Azure Stack Edge powinny znajdować się w pobliżu miejsca, w którym znajduje się urządzenie, aby uzyskać optymalną wydajność. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [wymaganiami systemowymi usługi Azure Stack Edge](data-box-edge-system-requirements.md).
- Poznaj [limity usługi Azure Stack Edge](data-box-edge-limits.md).
- Wdrażanie [usługi Azure Stack Edge](data-box-edge-deploy-prep.md) w witrynie Azure portal.
