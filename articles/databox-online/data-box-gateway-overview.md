---
title: Omówienie usługi Microsoft Azure Data Box Gateway | Microsoft Docs
description: Ten artykuł dotyczy usługi Azure Data Box Gateway — rozwiązania umożliwiającego transfer danych na platformę Azure przy użyciu wirtualnego urządzenia magazynowego
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: e7a65a6b49544783ed3f40194e2338540819536b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400955"
---
# <a name="what-is-azure-data-box-gateway"></a>Co to jest brama pola danych platformy Azure?

Azure Data Box Gateway to rozwiązanie do przechowywania, które umożliwia bezproblemowe przesyłanie danych na platformę Azure. Ten artykuł zawiera omówienie usługi Azure Data Box Gateway, korzyści wynikających z jego stosowania, kluczowych funkcji oraz scenariuszy, w których można wdrożyć to urządzenie.

Data Box Gateway to urządzenie wirtualne działające na maszynie wirtualnej zainicjowanej w środowisku zwirtualizowanym lub za pomocą funkcji hypervisor. Urządzenie wirtualne znajduje się w środowisku lokalnym, a dane są na nim zapisywane przy użyciu protokołów NFS i SMB. Następnie urządzenie przesyła dane na platformę Azure — do blokowego obiektu blob, stronicowego obiektu blob lub funkcji Azure Files.

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Gateway może być używana do transferu danych do chmury, na przykład archiwizowania w chmurze, odzyskiwania po awarii lub (w razie konieczności) przetwarzania danych ze skalowaniem w chmurze. Oto przykłady scenariuszy, w których można użyć usługi Data Box Gateway do transferu danych.

- **Archiwizowanie w chmurze** — usługa Data Box Gateway umożliwia bezpieczne i wydajne kopiowanie setek terabajtów danych do usługi Azure Storage. Dane mogą być pozyskiwane jednorazowo lub w trybie ciągłym w scenariuszach dotyczących archiwizacji.

- **Pozyskiwanie danych ciągłe** -stale pozyskiwania danych w urządzeniu kopiowania do chmury, niezależnie od rozmiaru danych. Ponieważ dane są zapisywane z urządzeniem bramy, urządzenie przekazywanie danych do usługi Azure Storage.  

- **Początkowy przeniesienia zbiorczego następuje transferu przyrostowego** — pole danych użycia dla zbiorczego przenieść w tryb offline (inicjatora początkowej) i bramy pola danych dla Transfery przyrostowe, zwiększając (bieżące źródło danych) za pośrednictwem sieci.

## <a name="benefits"></a>Korzyści

Usługa Data Box Gateway daje następujące korzyści:

- **Łatwy transfer danych**— przenoszenie danych do i z usługi Azure Storage jest tak proste jak praca z lokalnym udziałem sieciowym.  
- **Wysoka wydajność** — bezproblemowe przesyłanie danych między sieciami dzięki wydajnym transferom na platformę Azure i z platformy Azure.
- **Szybki dostęp i wysokie tempo pozyskiwania danych w godzinach pracy** — usługa Data Box Gateway ma lokalną pamięć podręczną definiowaną jako rozmiar pojemności lokalnej podczas aprowizacji urządzenia wirtualnego. Rozmiar dysku danych należy określić zgodnie z [minimalnymi wymaganiami dla urządzenia wirtualnego](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). Lokalna pamięć podręczna zapewnia następujące korzyści:
    - Lokalna pamięć podręczna umożliwia pozyskiwanie danych z dużą szybkością. Jeśli duże ilości danych są pozyskiwane w godzinach szczytu, pamięć podręczna może przechować te dane i przekazać je do chmury.
    - Lokalna pamięć podręczna umożliwia szybki dostęp do odczytu do chwili osiągnięcia określonego progu. Dopóki urządzenie nie zostanie zapełnione w 50-60%, dostęp do wszystkich odczytów z urządzenia jest uzyskiwany z pamięci podręcznej, dzięki czemu są one szybsze. Gdy zajęte miejsce na urządzeniu przestanie się powyżej wartości progowej, urządzenie zostanie uruchomione do usunięcia plików lokalnych.
 
- **Ograniczone użycie przepustowości** — dane mogą zostać zapisane na platformie Azure nawet przy ograniczonych możliwościach użycia sieci w czasie godzin szczytu.  

## <a name="key-capabilities"></a>Najważniejsze możliwości

Usługa Data Box Gateway daje następujące możliwości:

|Możliwości |Opis  |
|---------|---------|
|Szybkość     | W pełni zautomatyzowany i w wysokim stopniu zoptymalizowany transfer danych oraz wysoka przepustowość.|
|Obsługiwane protokoły     | Obsługa standardowych protokołów SMB i NFS do pozyskiwania danych. <br> Aby uzyskać więcej informacji na temat obsługiwanych wersji, zapoznaj się z [wymaganiami systemowymi rozwiązania Data Box Gateway](data-box-gateway-system-requirements.md).|
|Dostęp do danych     | Po dane wysyłane przez urządzenie w chmurze może można dalej modyfikować, uzyskując bezpośrednio dostęp do interfejsów API w chmurze.|
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
| Memory (Pamięć)  |Minimum 8 GB|
| Dostępność|Jeden węzeł|
| Dyski|Dysk systemu operacyjnego: 250 GB <br> Dysk z danymi: minimum 2 TB, alokowany elastycznie, wymagane dyski SSD|
| Interfejsy sieciowe |Co najmniej 1 wirtualny interfejs sieciowy|
| Natywne protokoły udostępniania plików|Protokoły SMB i NFS  |
| Bezpieczeństwo|Odblokowanie dostępu do urządzenia i danych wymaga uwierzytelnienia <br> Transmitowane dane są szyfrowane przy użyciu 256-bitowego algorytmu AES|
| Zarządzanie|Lokalny internetowy interfejs użytkownika — konfiguracja początkowa, diagnostyka, zarządzanie zasilaniem urządzenia <br> Portal Azure — bieżące zarządzanie urządzeniami Data Box Gateway       |

## <a name="components"></a>Składniki

Rozwiązanie Data Box Gateway składa się z zasobu rozwiązania Data Box Gateway, urządzenia wirtualnego Data Box Gateway i lokalnego internetowego interfejsu użytkownika.

- **Urządzenie wirtualne Data Box Gateway** — urządzenie działające na maszynie wirtualnej zainicjowanej w środowisku zwirtualizowanym lub za pomocą funkcji hypervisor, umożliwiające wysyłanie danych na platformę Azure.
    
- **Zasób rozwiązania Data Box Gateway** — zasób w witrynie Azure Portal, który pozwala na zarządzanie urządzeniem Data Box Gateway za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Użyj zasobów bramy pola danych do wyświetlania i zarządzania urządzeniami, udziały, użytkowników i alerty. Aby uzyskać więcej informacji, zobacz instrukcje [zarządzanych za pomocą witryny Azure portal](data-box-gateway-manage-shares.md).

- **Dane pole lokalnego Interfejsu w przeglądarce** — Uruchom funkcję diagnostyki, zamknij i ponownie uruchomić urządzenie, generowanie pakietu dla pomocy technicznej za pomocą lokalnego Interfejsu w przeglądarce lub skontaktuj się z Microsoft Support do pliku żądania obsługi. Aby uzyskać więcej informacji, zobacz instrukcje [zarządzanie przy użyciu lokalnego Interfejsu w przeglądarce](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Dostępność w danym regionie

Urządzenie fizyczne bramy pola danych, zasobów platformy Azure i docelowe konto magazynu, do którego transfer danych nie wszystkie mają znajdować się w tym samym regionie.

- **Dostępność zasobów** — w tej wersji zasobu bramy pole danych jest dostępna w następujących regionach, które obsługują chmury publicznej:
    - **Stany Zjednoczone** — wschodnie stany USA
    - **Unia Europejska** — Europa Zachodnia
    - **Azja i Pacyfik** — południowo-wschodnia

    Brama pola danych można wdrożyć w taki sposób, w chmurze Azure Government. Aby uzyskać więcej informacji, zobacz [co to jest platforma Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Docelowe konta magazynu** — konta magazynu, w których są przechowywane dane, są dostępne we wszystkich regionach platformy Azure.

    Regiony, w których znajdują się konta magazynu zawierające dane przesłane za pomocą rozwiązania Data Box, powinny mieścić się blisko lokalizacji urządzenia, aby zapewnić optymalną wydajność. Duża odległość konta magazynu od urządzenia powoduje długie opóźnienia i mniejszą wydajność.


## <a name="next-steps"></a>Kolejne kroki

- Przejrzyj [wymagania systemowe usługi Data Box Gateway](data-box-gateway-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Gateway](data-box-gateway-limits.md).
- Wdróż rozwiązanie [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) w witrynie Azure Portal.

