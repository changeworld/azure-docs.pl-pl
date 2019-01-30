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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 11/08/2018
ms.openlocfilehash: 0e5d799a5128f578900e4fa326b2e129964d0873
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243787"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Często zadawane pytania dotyczące usługi Azure Stack użycia interfejsu API

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące użycia interfejsu API usługi Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Jakie miernika identyfikatory widzi?
Użycie jest zgłaszany w przypadku następujących dostawców zasobów:

### <a name="network"></a>Sieć
  
**Identyfikator licznika**: F271A8A388C44D93956A063E1D2FA80B  
**Nazwa miernika**: Użycie adresu statycznego adresu IP  
**Jednostka**: Adresy IP  
**Informacje o**: Liczba adresów IP adresy używane. Jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, licznik zwracany jest adres IP pomnożona przez liczbę godzin.  
  
**Identyfikator licznika**: 9E2739BA86744796B465F64674B822BA  
**Nazwa miernika**: Użycie adresu dynamicznego adresu IP  
**Jednostka**: Adresy IP  
**Informacje o**: Liczba adresów IP adresy używane. Jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, licznik zwracany jest adres IP pomnożona przez liczbę godzin.  
  
### <a name="storage"></a>Magazyn
  
**Identyfikator licznika**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nazwa miernika**: TableCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: Całkowita pojemność zajętą przez tabele.  
  
**Identyfikator licznika**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nazwa miernika**: PageBlobCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: Całkowita pojemność zajętą przez stronicowych obiektów blob.  
  
**Identyfikator licznika**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nazwa miernika**: QueueCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: Całkowita pojemność zajętą przez kolejkę.  
  
**Identyfikator licznika**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nazwa miernika**: BlockBlobCapacity  
**Jednostka**: GB\*godzin  
**Informacje o**: Całkowita pojemność zajętą przez blokowych obiektów blob.  
  
**Identyfikator licznika**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nazwa miernika**: TableTransactions  
**Jednostka**: Liczba żądań w 10 000's  
**Informacje o**: Żądania obsługi tabeli (w 10 000).  
  
**Identyfikator licznika**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nazwa miernika**: TableDataTransIn  
**Jednostka**: Danych przychodzących w GB  
**Informacje o**: Tabela usługi dane przychodzące w GB.  
  
**Identyfikator licznika**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nazwa miernika**: TableDataTransOut  
**Jednostka**: Ruch wychodzący w GB  
**Informacje o**: Wyjście danych usługi Table w GB  
  
**Identyfikator licznika**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nazwa miernika**: BlobTransactions  
**Jednostka**: Żądania count w 10 000's  
**Informacje o**: Żądania obsługi obiektów blob (w 10 000).  
  
**Identyfikator licznika**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nazwa miernika**: BlobDataTransIn  
**Jednostka**: Danych przychodzących w GB  
**Informacje o**: Blob service transferu danych przychodzących w GB.  
  
**Identyfikator licznika**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nazwa miernika**: BlobDataTransOut  
**Jednostka**: Ruch wychodzący w GB  
**Informacje o**: Blob service wyjście danych w GB.  
  
**Identyfikator licznika**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nazwa miernika**: QueueTransactions  
**Jednostka**: Żądania count w 10 000's  
**Informacje o**: Dodaj do kolejki żądań usługi (w 10 000).  
  
**Identyfikator licznika**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nazwa miernika**: QueueDataTransIn  
**Jednostka**: Danych przychodzących w GB  
**Informacje o**: Kolejka usługi dane przychodzące w GB.  
  
**Identyfikator licznika**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nazwa miernika**: QueueDataTransOut  
**Jednostka**: Ruch wychodzący w GB  
**Informacje o**: Kolejka usługi danych wychodzących w GB  

### <a name="compute"></a>Wystąpienia obliczeniowe 
  
**Identyfikator licznika**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nazwa miernika**: Godziny rozmiar bazowej maszyny Wirtualnej  
**Jednostka**: Wirtualne podstawowe godziny  
**Informacje o**: Liczba rdzeni wirtualnych mnożona przez godziny, w których uruchomiono maszyny Wirtualnej.  
  
**Identyfikator licznika**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nazwa miernika**: Godziny rozmiar maszyn wirtualnych Windows  
**Jednostka**: Wirtualne podstawowe godziny  
**Informacje o**: Liczba rdzeni wirtualnych mnożona przez godziny, w których uruchomiono maszyny Wirtualnej.  
  
**Identyfikator licznika**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nazwa miernika**: Godziny rozmiar maszyn wirtualnych  
**Jednostka**: Godziny dla maszyn wirtualnych  
**Informacje o**: Przechwytuje maszynę Wirtualną zarówno podstawowy, jak i Windows. Nie może dopasować liczby rdzeni.  
  
### <a name="managed-disks"></a>Managed Disks

**Identyfikator licznika**: 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Nazwa miernika**: S4   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Standardowa zarządzana dysku – 32 GB 

**Identyfikator licznika**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Nazwa miernika**: S6   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Standardowa zarządzana dysku – 64 GB 

**Identyfikator licznika**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**Nazwa miernika**: S10   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Standardowa zarządzana dysku — 128 GB 

**Identyfikator licznika**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Nazwa miernika**: S15   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Standardowa zarządzana dysku — 256 GB 

**Identyfikator licznika**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Nazwa miernika**: S20   
**Jednostka**: Liczba dysków\*miesiąca      
**Informacje o**: Standardowa zarządzana dysku — 512 GB 

**Identyfikator licznika**: 5b1db88a-8596-4002-8052-347947c26940   
**Nazwa miernika**: S30   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Standardowa zarządzana dysku – 1024 GB 

**Identyfikator licznika**: 7660b45b-b29d-49cb-b816-59f30fbab011   
**Nazwa miernika**: P4   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Zarządzane dysku – 32 GB w warstwie Premium 

**Identyfikator licznika**: 817007fd-a077-477f-bc01-b876f27205fd   
**Nazwa miernika**: P6   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Zarządzane dysku – 64 GB w warstwie Premium 

**Identyfikator licznika**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Nazwa miernika**: P10   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Zarządzane dysku — 128 GB w warstwie Premium  

**Identyfikator licznika**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Nazwa miernika**: P15  
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Dysk — 256 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Nazwa miernika**: P20   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Dysk — 512 GB zarządzane w warstwie Premium 

**Identyfikator licznika**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Nazwa miernika**: P30   
**Jednostka**: Liczba dysków\*miesiąca   
**Informacje o**: Zarządzane w warstwie Premium dysku – 1024 GB 

**Identyfikator licznika**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Nazwa miernika**: ActualStandardDiskSize   
**Jednostka**: GB\*miesiąca      
**Informacje o**: Rzeczywisty rozmiar na dysku dysku zarządzanego w warstwie standardowa  

**Identyfikator licznika**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Nazwa miernika**: ActualPremiumDiskSize   
**Jednostka**: GB\*miesiąca      
**Informacje o**: Rzeczywisty rozmiar na dysku premium dysku zarządzanego 

**Identyfikator licznika**: 75d4b707-1027-4403-9986-6ec7c05579c8  
**Nazwa miernika**: ActualStandardSnapshotSize   
**Jednostka**: GB\*miesiąca   
**Informacje o**: Rzeczywisty rozmiar na dysku zarządzanego standardową migawkę.  

**Identyfikator licznika**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e   
**Nazwa miernika**: ActualPremiumSnapshotSize   
**Jednostka**: GB\*miesiąca   
**Informacje o**: Rzeczywisty rozmiar na dysku zarządzanego w warstwie premium.   

**Identyfikator licznika**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Nazwa miernika**: S4   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Standardowa zarządzana dysku – 32 GB (przestarzałe) 

**Identyfikator licznika**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Nazwa miernika**: S6   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Standardowa zarządzana dysku – 64 GB (przestarzałe) 

**Identyfikator licznika**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Nazwa miernika**: S10   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Standardowa zarządzana dysku — 128 GB (przestarzałe) 

**Identyfikator licznika**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Nazwa miernika**: S15   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Standardowa zarządzana dysku — 256 GB (przestarzałe) 

**Identyfikator licznika**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Nazwa miernika**: S20   
**Jednostka**: Liczba dysków\*godzin      
**Informacje o**: Standardowa zarządzana dysku — 512 GB (przestarzałe) 

**Identyfikator licznika**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Nazwa miernika**: S30   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Standardowa zarządzana dysku – 1024 GB (przestarzałe) 

**Identyfikator licznika**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Nazwa miernika**: P4   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku – 32 GB (przestarzałe) 

**Identyfikator licznika**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Nazwa miernika**: P6   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku – 64 GB (przestarzałe) 

**Identyfikator licznika**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Nazwa miernika**: P10   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku — 128 GB (przestarzałe)  

**Identyfikator licznika**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Nazwa miernika**: P15  
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku — 256 GB (przestarzałe) 

**Identyfikator licznika**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Nazwa miernika**: P20   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku — 512 GB (przestarzałe) 

**Identyfikator licznika**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Nazwa miernika**: P30   
**Jednostka**: Liczba dysków\*godzin   
**Informacje o**: Zarządzane w warstwie Premium dysku – 1024 GB (przestarzałe) 

**Identyfikator licznika**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Nazwa miernika**: ActualStandardDiskSize   
**Jednostka**: Bajt\*godzin      
**Informacje o**: Rzeczywisty rozmiar na dysku dysku zarządzanego w warstwie standardowa (przestarzałe)  

**Identyfikator licznika**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Nazwa miernika**: ActualPremiumDiskSize   
**Jednostka**: Bajt\*godzin      
**Informacje o**: Rzeczywisty rozmiar na dysku premium managed dysku (przestarzałe) 

**Identyfikator licznika**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Nazwa miernika**: ActualStandardSnapshotSize   
**Jednostka**: Bajt\*godzin   
**Informacje o**: Rzeczywisty rozmiar na dysku zarządzanego standardową migawkę (przestarzałe) 

**Identyfikator licznika**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Nazwa miernika**: ActualPremiumSnapshotSize   
**Jednostka**: Bajt\*godzin   
**Informacje o**: Rzeczywisty rozmiar na dysku zarządzanego w warstwie premium (przestarzałe) 

### <a name="sql-rp"></a>SQL RP
  
**Identyfikator licznika**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nazwa miernika**: DatabaseSizeHourSqlMeter  
**Jednostka**: MB\*godzin  
**Informacje o**: Całkowita pojemność baz danych podczas tworzenia. Wartość licznika, jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, zwraca MB pomnożona przez liczbę godzin.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**Identyfikator licznika**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nazwa miernika**: DatabaseSizeHourMySqlMeter  
**Jednostka**: MB\*godzin  
**Informacje o**: Całkowita pojemność baz danych podczas tworzenia. Wartość licznika, jeśli chcesz wywołać interfejs API użycia z dokładnością co do dnia, zwraca MB pomnożona przez liczbę godzin.    
### <a name="key-vault"></a>Usługa Key Vault   
  
**Identyfikator licznika**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nazwa miernika**: Transakcje usługi Key Vault  
**Jednostka**: Liczba żądań w 10 000's  
**Informacje o**: Liczba żądań interfejsu API REST odebranych przez płaszczyzny danych usługi Key Vault.  
  
**Identyfikator licznika**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nazwa miernika**: Klucze zaawansowane transakcji  
**Jednostka**:  10 tys. transakcji  
**Informacje o**: RSA 3K / 4K, ECC klucza transakcji. (wersja zapoznawcza).  
  
### <a name="app-service"></a>App Service   
  
**Identyfikator licznika**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nazwa miernika**: App Service  
**Jednostka**: Wirtualne podstawowe godziny  
**Informacje o**: Liczba rdzeni wirtualnych, używane do uruchamiania usługi app service. Uwaga: Firma Microsoft używa ten licznik do obciążenia usługi App Service w usłudze Azure Stack. Dostawców usług w chmurze można użyć App Service liczniki (poniżej). Aby obliczyć obciążenie dla swoich dzierżaw.  
  
**Identyfikator licznika**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nazwa miernika**: Żądania funkcji  
**Jednostka**: 10 żądań  
**Informacje o**: Całkowita liczba zażądanych wykonań (za 10 wykonań). Wykonania są liczone za każdym razem funkcji jest uruchamiany w odpowiedzi na zdarzenie lub jest wyzwalany przez powiązanie.  
  
**Identyfikator licznika**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nazwa miernika**: Functions - Compute  
**Jednostka**:  GB-s  
**Informacje o**:  Użycia zasobów mierzonego gigabajtosekundach (GB/s). **Zaobserwowane użycie zasobów** jest obliczany przez pomnożenie średniego rozmiaru pamięci w GB przez czas w milisekundach potrzebny na wykonanie funkcji. Pamięć użyta przez funkcję jest zaokrąglana do najbliższej 128 MB, maksymalnie przy maksymalnym rozmiarze pamięci 1,536 MB, z czasem wykonywania obliczonym przez zaokrąglenie do najbliższej 1 ms. Minimalny czas wykonywania i pamięć dla pojedynczego wykonania funkcji to 100 ms i 128 mb odpowiednio.  
  
**Identyfikator licznika**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nazwa miernika**: Godziny udostępnionych usługi App Service  
**Jednostka**: 1 godzina  
**Informacje o**: Za godzinę użytkowania fragmentów planu usługi App Service. Plany są naliczane na podstawie aplikacji.  
  
**Identyfikator licznika**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nazwa miernika**: Godziny wolne usługi App Service  
**Jednostka**: 1 godzina  
**Informacje o**: Za godzinę użytkowania bezpłatnego planu usługi App Service. Plany są naliczane na podstawie aplikacji.  
  
**Identyfikator licznika**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nazwa miernika**: Godziny małych usługi aplikacji — warstwa standardowa  
**Jednostka**: 1 godzina  
**Informacje o**: Obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Identyfikator licznika**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nazwa miernika**: Godziny średniej usług aplikacji w warstwie standardowa  
**Jednostka**: 1 godzina  
**Informacje o**: Obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Identyfikator licznika**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nazwa miernika**: Godziny dużych usługi aplikacji — warstwa standardowa  
**Jednostka**: 1 godzina  
**Informacje o**: Obliczana na podstawie rozmiaru i liczby wystąpień.  
  
### <a name="custom-worker-tiers"></a>Warstwy niestandardowego procesu roboczego   
  
**Identyfikator licznika**: *Warstwy niestandardowego procesu roboczego*  
**Nazwa miernika**: Warstwy niestandardowego procesu roboczego  
**Jednostka**: Godziny  
**Informacje o**: Identyfikator miernika deterministyczne jest tworzony na podstawie jednostki SKU i nazwa warstwy niestandardowego procesu roboczego. Ten identyfikator miernika jest unikatowy dla każdej warstwy niestandardowego procesu roboczego.  
  
**Identyfikator licznika**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nazwa miernika**: SNI SSL  
**Jednostka**: Na powiązanie SNI SSL  
**Informacje o**: Usługa App Service obsługuje dwa typy połączeń SSL: Połączenia SSL z oznaczaniem nazwy serwera (SNI) oraz Połączenia SSL z adresem IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Identyfikator licznika**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nazwa miernika**: IP SSL  
**Jednostka**: Na adres IP na podstawie powiązania SSL  
**Informacje o**: Usługa App Service obsługuje dwa typy połączeń SSL: Połączenia SSL z oznaczaniem nazwy serwera (SNI) oraz Połączenia SSL z adresem IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Identyfikator licznika**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nazwa miernika**:  Proces sieci Web  
**Jednostka**:  
**Informacje o**: Obliczona na aktywną witrynę na godzinę.  
  
**Identyfikator licznika**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nazwa miernika**: Przepustowość danych wychodzących zewnętrznych  
**Jednostka**: GB  
**Informacje o**: Całkowita liczba bajtów odpowiedzi przychodzące żądania i żądania wychodzącego łączna liczba bajtów i łączna liczba FTP przychodzące żądania bajtów odpowiedzi i łączna liczba przychodzących sieci web należy wdrożyć bajtów odpowiedzi żądania.  
  

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
