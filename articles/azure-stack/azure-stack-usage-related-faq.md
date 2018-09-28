---
title: Często zadawane pytania dotyczące wykorzystania interfejsu API | Dokumentacja firmy Microsoft
description: Lista liczników usługi Azure Stack, porównanie interfejs API użycia platformy Azure, czas użycia i podać godzinę kody błędów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: e4bb17bd068076a9ffa9bcab5826f0efa84a19d7
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406009"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Często zadawane pytania dotyczące usługi Azure Stack użycia interfejsu API

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące użycia interfejsu API usługi Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Jakie miernika identyfikatory widzi?
Użycie jest zgłaszany w przypadku następujących dostawców zasobów:

### <a name="network"></a>Sieć
  
**Identyfikator licznika**: F271A8A388C44D93956A063E1D2FA80B  
**Nazwa miernika**: statyczne użycie adresu IP  
**Jednostka**: adresy IP  
**Informacje o**: liczba adresów IP adresy używane. Jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, licznik zwracany jest adres IP pomnożona przez liczbę godzin.  
  
**Identyfikator licznika**: 9E2739BA86744796B465F64674B822BA  
**Nazwa miernika**: dynamiczne użycie adresu IP  
**Jednostka**: adresy IP  
**Informacje o**: liczba adresów IP adresy używane. Jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, licznik zwracany jest adres IP pomnożona przez liczbę godzin.  
  
### <a name="storage"></a>Magazyn
  
**Identyfikator licznika**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nazwa miernika**: TableCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: łączna pojemność zajętą przez tabele.  
  
**Identyfikator licznika**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nazwa miernika**: PageBlobCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: łączna pojemność zajętą przez stronicowych obiektów blob.  
  
**Identyfikator licznika**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nazwa miernika**: QueueCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: łączna pojemność zajętą przez kolejkę.  
  
**Identyfikator licznika**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nazwa miernika**: BlockBlobCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: łączna pojemność zajętą przez blokowych obiektów blob.  
  
**Identyfikator licznika**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nazwa miernika**: TableTransactions  
**Jednostka**: liczba w 10 żądań, 000's  
**Informacje o**: tabelę żądań obsługi (w 10 000).  
  
**Identyfikator licznika**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nazwa miernika**: TableDataTransIn  
**Jednostka**: danych przychodzących w GB  
**Informacje o**: tabeli Usługa transferu danych przychodzących w GB.  
  
**Identyfikator licznika**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nazwa miernika**: TableDataTransOut  
**Jednostka**: ruch wychodzący w GB  
**Informacje o**: Table service wyjście danych w GB  
  
**Identyfikator licznika**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nazwa miernika**: BlobTransactions  
**Jednostka**: żądania count w 10 000's  
**Informacje o**: żądaniami obsługi usługi Blob (w 10 000).  
  
**Identyfikator licznika**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nazwa miernika**: BlobDataTransIn  
**Jednostka**: danych przychodzących w GB  
**Informacje o**: obiektów Blob usługi transferu danych przychodzących w GB.  
  
**Identyfikator licznika**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nazwa miernika**: BlobDataTransOut  
**Jednostka**: ruch wychodzący w GB  
**Informacje o**: obiektów Blob usługi wyjście danych w GB.  
  
**Identyfikator licznika**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nazwa miernika**: QueueTransactions  
**Jednostka**: żądania count w 10 000's  
**Informacje o**: żądania obsługi w kolejce (w 10 000).  
  
**Identyfikator licznika**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nazwa miernika**: QueueDataTransIn  
**Jednostka**: danych przychodzących w GB  
**Informacje o**: kolejki usługi transferu danych przychodzących w GB.  
  
**Identyfikator licznika**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nazwa miernika**: QueueDataTransOut  
**Jednostka**: ruch wychodzący w GB  
**Informacje o**: Queue service wyjście danych w GB  

### <a name="compute"></a>Wystąpienia obliczeniowe 
  
**Identyfikator licznika**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nazwa miernika**: godziny rozmiar maszyny Wirtualnej bazy  
**Jednostka**: godziny rdzeni wirtualnych  
**Informacje o**: liczba rdzeni wirtualnych pomnożona przez godziny uruchomienia maszyny Wirtualnej.  
  
**Identyfikator licznika**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nazwa miernika**: godziny rozmiar maszyn wirtualnych Windows  
**Jednostka**: godziny rdzeni wirtualnych  
**Informacje o**: liczba rdzeni wirtualnych pomnożona przez godziny uruchomienia maszyny Wirtualnej.  
  
**Identyfikator licznika**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nazwa miernika**: godziny rozmiar maszyn wirtualnych  
**Jednostka**: godziny dla maszyn wirtualnych  
**Informacje o**: przechwytuje zarówno podstawowy, jak i Windows maszyny Wirtualnej. Nie może dopasować liczby rdzeni.  
  
### <a name="managed-disks"></a>Managed Disks

**Identyfikator licznika**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Nazwa miernika**: S4   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku – 32 GB 

**Identyfikator licznika**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Nazwa miernika**: S6   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku – 64 GB 

**Identyfikator licznika**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Nazwa miernika**: S10   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku — 128 GB 

**Identyfikator licznika**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Nazwa miernika**: S15   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku — 256 GB 

**Identyfikator licznika**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Nazwa miernika**: S20   
**Jednostka**: liczba dysków      
**Informacje o**: standardowa zarządzana dysku — 512 GB 

**Identyfikator licznika**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Nazwa miernika**: S30   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku – 1024 GB 

**Identyfikator licznika**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Nazwa miernika**: S40   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysku — 2048 GB 

**Identyfikator licznika**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Nazwa miernika**: S50   
**Jednostka**: liczba dysków   
**Informacje o**: standardowa zarządzana dysk — 4096 GB 

**Identyfikator licznika**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Nazwa miernika**: P4   
**Jednostka**: liczba dysków   
**Informacje o**: zarządzane w warstwie Premium dysku – 32 GB 

**Identyfikator licznika**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Nazwa miernika**: P6   
**Jednostka**: liczba dysków   
**Informacje o**: dysk – 64 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Nazwa miernika**: P10   
**Jednostka**: liczba dysków   
**Informacje o**: zarządzane dysku — 128 GB w warstwie Premium  

**Identyfikator licznika**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Nazwa miernika**: P15  
**Jednostka**: liczba dysków   
**Informacje o**: dysk — 256 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Nazwa miernika**: P20   
**Jednostka**: liczba dysków   
**Informacje o**: dysk — 512 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Nazwa miernika**: P30   
**Jednostka**: liczba dysków   
**Informacje o**: zarządzane w warstwie Premium dysku – 1024 GB 

**Identyfikator licznika**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Nazwa miernika**: P40   
**Jednostka**: liczba dysków    
**Informacje o**: dysk — 2048 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Nazwa miernika**: P50   
**Jednostka**: liczba dysków   
**Informacje o**: dysk — 4096 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Nazwa miernika**: ActualStandardDiskSize   
**Jednostka**: bajtów      
**Informacje o**: rzeczywisty rozmiar na dysku dysku zarządzanego w warstwie standardowa  

**Identyfikator licznika**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Nazwa miernika**: ActualPremiumDiskSize   
**Jednostka**: bajtów      
**Informacje o**: dysk zarządzany rzeczywisty rozmiar na dysku premium 

**Identyfikator licznika**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Nazwa miernika**: ActualStandardSnapshotSize   
**Jednostka**: bajtów   
**Informacje o**: rzeczywisty rozmiar na dysku zarządzanego standardową migawkę.  

**Identyfikator licznika**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Nazwa miernika**: ActualPremiumSnapshotSize   
**Jednostka**: bajtów   
**Informacje o**: rzeczywisty rozmiar na dysku zarządzanego w warstwie premium.   

### <a name="sql-rp"></a>SQL RP
  
**Identyfikator licznika**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nazwa miernika**: DatabaseSizeHourSqlMeter  
**Jednostka**: MB\*godzin  
**Informacje o**: DB łączna pojemność podczas tworzenia. Wartość licznika, jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, zwraca MB pomnożona przez liczbę godzin.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**Identyfikator licznika**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nazwa miernika**: DatabaseSizeHourMySqlMeter  
**Jednostka**: MB\*godzin  
**Informacje o**: DB łączna pojemność podczas tworzenia. Wartość licznika, jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, zwraca MB pomnożona przez liczbę godzin.    
### <a name="key-vault"></a>Usługa Key Vault   
  
**Identyfikator licznika**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nazwa miernika**: transakcje usługi Key Vault  
**Jednostka**: liczba w 10 żądań, 000's  
**Informacje o**: numer interfejsu API REST żądań odebranych przez płaszczyzny danych usługi Key Vault.  
  
**Identyfikator licznika**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nazwa miernika**: Zaawansowane klucze transakcji  
**Jednostka**: 10 tys. transakcji  
**Informacje o**: RSA 3 K/4 K, transakcje klucza ECC. (wersja zapoznawcza).  
  
### <a name="app-service"></a>App Service   
  
**Identyfikator licznika**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nazwa miernika**: Usługa App Service  
**Jednostka**: godziny rdzeni wirtualnych  
**Informacje o**: liczba rdzeni wirtualnych, używane do uruchamiania usługi app service. Uwaga: Firma Microsoft używa ten licznik do obciążenia usługi App Service w usłudze Azure Stack. Dostawców usług w chmurze można użyć App Service liczniki (poniżej). Aby obliczyć obciążenie dla swoich dzierżaw.  
  
**Identyfikator licznika**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nazwa miernika**: żądania funkcji  
**Jednostka**: 10 żądań  
**Informacje o**: całkowitej liczby zażądanych wykonań (za 10 wykonań). Wykonania są liczone za każdym razem funkcji jest uruchamiany w odpowiedzi na zdarzenie lub jest wyzwalany przez powiązanie.  
  
**Identyfikator licznika**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nazwa miernika**: funkcje - Compute  
**Jednostka**: gigabajta pamięci  
**Informacje o**: użycia zasobów mierzonego gigabajtosekundach (GB/s). **Zaobserwowane użycie zasobów** jest obliczany przez pomnożenie średniego rozmiaru pamięci w GB przez czas w milisekundach potrzebny na wykonanie funkcji. Pamięć użyta przez funkcję jest zaokrąglana do najbliższej 128 MB, maksymalnie przy maksymalnym rozmiarze pamięci 1,536 MB, z czasem wykonywania obliczonym przez zaokrąglenie do najbliższej 1 ms. Minimalny czas wykonywania i pamięć dla pojedynczego wykonania funkcji to 100 ms i 128 mb odpowiednio.  
  
**Identyfikator licznika**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nazwa miernika**: udostępnione aplikacji godzin korzystania z usługi  
**Jednostka**: 1 godzina  
**Informacje o**: za godzinę użytkowania fragmentów planu usługi App Service. Plany są naliczane na podstawie aplikacji.  
  
**Identyfikator licznika**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nazwa miernika**: bezpłatne godzin korzystania z usługi aplikacji  
**Jednostka**: 1 godzina  
**Informacje o**: za godzinę użytkowania bezpłatnego planu usługi App Service. Plany są naliczane na podstawie aplikacji.  
  
**Identyfikator licznika**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nazwa miernika**: mały standardowych aplikacji godzin korzystania z usługi  
**Jednostka**: 1 godzina  
**Informacje o**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Identyfikator licznika**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nazwa miernika**: średni standardowych aplikacji godzin korzystania z usługi  
**Jednostka**: 1 godzina  
**Informacje o**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Identyfikator licznika**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nazwa miernika**: duże standardowych aplikacji godzin korzystania z usługi  
**Jednostka**: 1 godzina  
**Informacje o**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
### <a name="custom-worker-tiers"></a>Warstwy niestandardowego procesu roboczego   
  
**Identyfikator licznika**: *warstwy niestandardowego procesu roboczego*  
**Nazwa miernika**: niestandardowe warstwy procesu roboczego  
**Jednostka**: godziny  
**Informacje o**: Identyfikator miernika deterministyczne jest tworzony na podstawie jednostki SKU i nazwa warstwy niestandardowego procesu roboczego. Ten identyfikator miernika jest unikatowy dla każdej warstwy niestandardowego procesu roboczego.  
  
**Identyfikator licznika**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nazwa miernika**: SNI SSL  
**Jednostka**: na powiązanie SNI SSL  
**Informacje o**: Usługa App Service obsługuje dwa typy połączeń SSL: połączenia SSL z oznaczaniem nazwy serwera (SNI) oraz połączenia SSL z adresem IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Identyfikator licznika**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nazwa miernika**: połączenie IP SSL  
**Jednostka**: według adresu IP na podstawie powiązania SSL  
**Informacje o**: Usługa App Service obsługuje dwa typy połączeń SSL: połączenia SSL z oznaczaniem nazwy serwera (SNI) oraz połączenia SSL z adresem IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Identyfikator licznika**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nazwa miernika**: proces sieci Web  
**Jednostka**:  
**Informacje o**: obliczane na aktywną witrynę na godzinę.  
  
**Identyfikator licznika**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nazwa miernika**: zewnętrzne przepustowość danych wychodzących  
**Jednostka**: GB  
**Informacje o**: łączna liczba przychodzące żądanie bajtów odpowiedzi + całkowite żądanie Wychodzące bajty + całkowita FTP przychodzące żądania bajtów odpowiedzi i łączna liczba przychodzących sieci web wdrażanie bajtów odpowiedzi żądania.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>W jaki sposób użycie usługi Azure Stack, porównaj interfejsów API [interfejs API użycia platformy Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (obecnie dostępna w publicznej wersji zapoznawczej)?
* Interfejs API użycia dzierżawy jest zgodne z interfejsem API platformy Azure, z jednym wyjątkiem: *showDetails* flagi obecnie nie jest obsługiwana w usłudze Azure Stack.
* Interfejs API użycia dostawcy ma zastosowanie tylko do usługi Azure Stack.
* Obecnie [interfejsu API RateCard](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) oznacza to dostępne na platformie Azure nie jest dostępna w usłudze Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Jaka jest różnica między czasem użycia i uwzględnionego w raporcie?
Raporty danych użycia ma dwie wartości czasu głównego:

* **Zgłoszony czas**. Czas ostatniego zdarzenia użycia wprowadzenia systemu użycia
* **Czas użycia**. Czas, kiedy został wykorzystany zasobów usługi Azure Stack

Możesz zobaczyć niezgodności wartości dla godzinę użycia i zgłaszane w przypadku użycia określonego zdarzenia. Opóźnienie może być tak długo, jak wiele godzin, w każdym środowisku.

Obecnie można tworzyć zapytania tylko przez *zgłaszane czasu*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Co oznaczają te kody błędów interfejsu API użycia?
| **Kod stanu HTTP** | **Kod błędu:** | **Opis** |
| --- | --- | --- |
| / 400 — Nieprawidłowe żądanie |*NoApiVersion* |*Parametru api-version* brakuje parametru zapytania. |
| / 400 — Nieprawidłowe żądanie |*InvalidProperty* |Brak właściwości lub ma nieprawidłową wartość. Komunikat w kodzie błędu w treści odpowiedzi identyfikuje brakuje właściwości. |
| / 400 — Nieprawidłowe żądanie |*RequestEndTimeIsInFuture* |Wartość *ReportedEndTime* przypada w przyszłości. Wartości w przyszłości nie są dozwolone dla tego argumentu. |
| / 400 — Nieprawidłowe żądanie |*SubscriberIdIsNotDirectTenant* |Wywołania interfejsu API dostawcy został użyty identyfikator subskrypcji, który nie jest prawidłową dzierżawę obiektu wywołującego. |
| / 400 — Nieprawidłowe żądanie |*SubscriptionIdMissingInRequest* |Brak Identyfikatora subskrypcji obiektu wywołującego. |
| / 400 — Nieprawidłowe żądanie |*InvalidAggregationGranularity* |Zażądano agregacji nieprawidłowy poziom szczegółowości. Prawidłowe wartości to codziennie i co godzinę. |
| 503 |*ServiceUnavailable* |Błąd umożliwiający ponowienie próby wystąpił, ponieważ usługa jest zajęta lub wywołanie jest ograniczane. |

## <a name="next-steps"></a>Następne kroki
[Klient, rozliczeń i obciążeń zwrotnych w usłudze Azure Stack](azure-stack-billing-and-chargeback.md)

[Interfejs API użycia zasobów dostawcy](azure-stack-provider-resource-api.md)

[Interfejs API użycia zasobów dzierżawy](azure-stack-tenant-resource-usage-api.md)
