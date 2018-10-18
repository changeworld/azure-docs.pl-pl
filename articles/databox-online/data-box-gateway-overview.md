---
title: Omówienie usługi Microsoft Azure Data Box Gateway | Microsoft Docs
description: Ten artykuł dotyczy usługi Azure Data Box Gateway — rozwiązania umożliwiającego transfer danych na platformę Azure przy użyciu wirtualnego urządzenia magazynowego
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 4f1ab6d955c81ce6f7b141eef42341f43bb379f6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165321"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Co to jest usługa Azure Data Box Gateway (wersja zapoznawcza)? 

Azure Data Box Gateway to rozwiązanie do przechowywania, które umożliwia bezproblemowe przesyłanie danych na platformę Azure. Ten artykuł zawiera omówienie usługi Azure Data Box Gateway, korzyści wynikających z jego stosowania, kluczowych funkcji oraz scenariuszy, w których można wdrożyć to urządzenie. 

Data Box Gateway to urządzenie wirtualne działające na maszynie wirtualnej zainicjowanej w środowisku zwirtualizowanym lub za pomocą funkcji hypervisor. Urządzenie wirtualne znajduje się w środowisku lokalnym, a dane są na nim zapisywane przy użyciu protokołów NFS i SMB. Następnie urządzenie przesyła dane na platformę Azure — do blokowego obiektu blob, stronicowego obiektu blob lub funkcji Azure Files. 

> [!IMPORTANT]
> Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Zapoznaj się z [warunkami użytkowania wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) przed wdrożeniem tego rozwiązania.

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Gateway może być używana do transferu danych do chmury, na przykład archiwizowania w chmurze, odzyskiwania po awarii lub (w razie konieczności) przetwarzania danych ze skalowaniem w chmurze. Oto przykłady scenariuszy, w których można użyć usługi Data Box Gateway do transferu danych.

- **Archiwizowanie w chmurze** — usługa Data Box Gateway umożliwia bezpieczne i wydajne kopiowanie setek terabajtów danych do usługi Azure Storage. Dane mogą być pozyskiwane jednorazowo lub w trybie ciągłym w scenariuszach dotyczących archiwizacji.

- **Agregacja danych** — agregowanie danych z wielu źródeł do jednej lokalizacji w usłudze Azure Storage w celu ich przetwarzania i analizy.  

- **Integracja z lokalnymi pakietami roboczymi** — integracja z lokalnymi pakietami roboczymi, na przykład tworzenia kopii zapasowych lub przywracania plików, które korzystają z magazynu w chmurze oraz wymagają lokalnego dostępu do często używanych plików. 

## <a name="benefits"></a>Korzyści

Usługa Data Box Gateway daje następujące korzyści:

- **Łatwy transfer danych**— przenoszenie danych do i z usługi Azure Storage jest tak proste jak praca z lokalnym udziałem sieciowym.  
- **Wysoka wydajność** — bezproblemowe przesyłanie danych między sieciami dzięki wydajnym transferom na platformę Azure i z platformy Azure. 
- **Szybki dostęp** — buforuje najnowsze pliki w celu zapewnienia szybkiego dostępu do plików lokalnych.  
- **Ograniczone użycie przepustowości** — dane mogą zostać zapisane na platformie Azure nawet przy ograniczonych możliwościach użycia sieci w czasie godzin szczytu.  

## <a name="key-capabilities"></a>Najważniejsze możliwości

Usługa Data Box Gateway daje następujące możliwości:

|Możliwości |Opis  |
|---------|---------|
|Szybkość     | W pełni zautomatyzowany i w wysokim stopniu zoptymalizowany transfer danych oraz wysoka przepustowość.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zapoznaj się z [wymaganiami systemowymi rozwiązania Data Box Gateway](data-box-gateway-system-requirements.md).|
|Dostęp do danych     | Bezpośredni dostęp do danych z usług Azure Storage Blob i Azure Files przy użyciu interfejsów API w chmurze w celu dodatkowego przetwarzania danych w chmurze.|
|Szybki dostęp     | Lokalna pamięć podręczna na urządzeniu zapewniająca szybki dostęp do ostatnio używanych plików.|
|Przekazywanie w trybie offline     | Tryb bez połączenia obsługuje scenariusze przekazywania w trybie offline.|
|Odświeżanie danych     | Możliwość odświeżania plików lokalnych za pomocą najnowszych wersji z chmury.|
|Szyfrowanie    | Obsługa funkcji BitLocker w celu lokalnego szyfrowania danych i zabezpieczania transferu danych do chmury za pośrednictwem protokołu *https*       |
|Odporność     | Wbudowana odporność sieci        |


## <a name="specifications"></a>Specyfikacje

Urządzenie wirtualne Data Box Gateway ma następujące parametry:

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Procesory wirtualne (rdzenie)   | Minimum 4 |            
| Memory (Pamięć)  | Minimum 8 GB|
| Dostępność|Jeden węzeł|
| Dyski| Dysk systemu operacyjnego: 250 GB <br> Dysk danych: minimum 2 TB, alokowany elastycznie, wymagane dyski SSD|
| Interfejsy sieciowe|Co najmniej 1 wirtualny interfejs sieciowy|
| Natywne protokoły udostępniania plików|Protokoły SMB i NFS  |
| Bezpieczeństwo| Odblokowanie dostępu do urządzenia i danych wymaga uwierzytelnienia <br> Transmitowane dane są szyfrowane przy użyciu 256-bitowego algorytmu AES|
| Zarządzanie| Lokalny internetowy interfejs użytkownika — konfiguracja początkowa, diagnostyka, zarządzanie zasilaniem urządzenia <br> Portal Azure — bieżące zarządzanie urządzeniami Data Box Gateway       |


## <a name="components"></a>Składniki

Rozwiązanie Data Box Gateway składa się z zasobu rozwiązania Data Box Gateway, urządzenia wirtualnego Data Box Gateway i lokalnego internetowego interfejsu użytkownika.

* **Urządzenie wirtualne Data Box Gateway** — urządzenie działające na maszynie wirtualnej zainicjowanej w środowisku zwirtualizowanym lub za pomocą funkcji hypervisor, umożliwiające wysyłanie danych na platformę Azure. 
    
* **Zasób rozwiązania Data Box Gateway** — zasób w witrynie Azure Portal, który pozwala na zarządzanie urządzeniem Data Box Gateway za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Korzystając z zasobu rozwiązania Data Box Gateway, można tworzyć zasoby oraz zarządzać nimi, wyświetlać urządzenia i alerty oraz zarządzać nimi, a także zarządzać udziałami.  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Lokalny internetowy interfejs użytkownika urządzenia Data Box** — korzystając z lokalnego internetowego interfejsu użytkownika, można uruchamiać diagnostykę, wyłączać i uruchamiać ponownie urządzenie Data Box Gateway, wyświetlać dzienniki kopiowania oraz kontaktować się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne Data Box Edge, zasób platformy Azure i docelowe konto magazynu, do którego są transferowane dane, nie muszą znajdować się w tym samym regionie.

- **Dostępność zasobu** — w tej wersji zasób rozwiązania Data Box Edge jest dostępny w następujących regionach:
    - **Stany Zjednoczone** — Zachodnie stany USA 2 i Wschodnie stany USA
    - **Unia Europejska** — Europa Zachodnia
    - **Azja i Pacyfik** — Azja Południowo-Wschodnia

- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure. 

    Regiony, w których znajdują się konta magazynu zawierające dane przesłane za pomocą rozwiązania Data Box, powinny mieścić się blisko lokalizacji urządzenia, aby zapewnić optymalną wydajność. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność. 


## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Gateway](data-box-gateway-limits.md).
- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.




