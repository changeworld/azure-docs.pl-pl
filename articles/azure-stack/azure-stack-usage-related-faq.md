---
title: Często zadawane pytania dotyczące wykorzystania interfejsu API | Dokumentacja firmy Microsoft
description: Lista liczników Azure stosu, porównanie Azure użycia interfejsu API, czas użytkowania i zgłoszony czas kody błędów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051507"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Często zadawane pytania w stosie Azure użycia interfejsu API

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące interfejsu API Azure stosu użycia.

## <a name="what-meter-ids-can-i-see"></a>Jakie miernika identyfikatorów może wyświetlać?
Użycie jest zgłaszany w przypadku następujących dostawców zasobów:

**Sieć**  
  
**Pomiarowe identyfikator**: F271A8A388C44D93956A063E1D2FA80B  
**Nazwa licznika**: statyczne użycia adresów IP  
**Jednostka**: adresy IP  
**Uwagi dotyczące**: adresy IP liczba używane. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca adres IP pomnożona przez liczbę godzin.  
  
**Pomiarowe identyfikator**: 9E2739BA86744796B465F64674B822BA  
**Nazwa licznika**: dynamiczne użycie adresu IP  
**Jednostka**: adresy IP  
**Uwagi dotyczące**: adresy IP liczba używane. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca adres IP pomnożona przez liczbę godzin.  
  
**Storage**  
  
**Pomiarowe identyfikator**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nazwa licznika**: TableCapacity  
**Jednostka**: GB\*godziny  
**Uwagi dotyczące**: łączna pojemność używane przez tabel.  
  
**Pomiarowe identyfikator**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nazwa licznika**: PageBlobCapacity  
**Jednostka**: GB\*godziny  
**Uwagi dotyczące**: łączna pojemność używane przez stronicowych obiektów blob.  
  
**Pomiarowe identyfikator**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nazwa licznika**: QueueCapacity  
**Jednostka**: GB\*godziny  
**Uwagi dotyczące**: łączna pojemność używane przez kolejkę.  
  
**Pomiarowe identyfikator**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nazwa licznika**: BlockBlobCapacity  
**Jednostka**: GB\*godziny  
**Uwagi dotyczące**: łączna pojemność używane przez blokowych obiektów blob.  
  
**Pomiarowe identyfikator**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nazwa licznika**: TableTransactions  
**Jednostka**: żądanie zliczania, 10, 000's  
**Uwagi dotyczące**: Tabela żądania usług (w 10 000).  
  
**Pomiarowe identyfikator**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nazwa licznika**: TableDataTransIn  
**Jednostka**: danych wejściowych w GB  
**Uwagi dotyczące**: Tabela usługi danych wejściowych w GB.  
  
**Pomiarowe identyfikator**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nazwa licznika**: TableDataTransOut  
**Jednostka**: wyjście w GB  
**Uwagi dotyczące**: Tabela wyjście danych usługi w GB  
  
**Pomiarowe identyfikator**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nazwa licznika**: BlobTransactions  
**Jednostka**: żądań liczba 10, 000's  
**Uwagi dotyczące**: żądań obsługi obiektu Blob (w 10 000).  
  
**Pomiarowe identyfikator**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nazwa licznika**: BlobDataTransIn  
**Jednostka**: danych wejściowych w GB  
**Uwagi dotyczące**: obiektów Blob usługi danych wejściowych w GB.  
  
**Pomiarowe identyfikator**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nazwa licznika**: BlobDataTransOut  
**Jednostka**: wyjście w GB  
**Uwagi dotyczące**: obiektów Blob usługi wyjście danych w GB.  
  
**Pomiarowe identyfikator**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nazwa licznika**: QueueTransactions  
**Jednostka**: żądań liczba 10, 000's  
**Uwagi dotyczące**: kolejka żądań usług (w 10 000).  
  
**Pomiarowe identyfikator**: E518E809-E369-4A45-9274-2017B29FFF25  
**Nazwa licznika**: QueueDataTransIn  
**Jednostka**: danych wejściowych w GB  
**Uwagi dotyczące**: kolejka usługi danych wejściowych w GB.  
  
**Pomiarowe identyfikator**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nazwa licznika**: QueueDataTransOut  
**Jednostka**: wyjście w GB  
**Uwagi dotyczące**: kolejka usługi wyjście danych w GB  
  
**SQL RP**  
  
**Pomiarowe identyfikator**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nazwa licznika**: DatabaseSizeHourSqlMeter  
**Jednostka**: MB\*godziny  
**Uwagi dotyczące**: DB całkowita pojemność podczas tworzenia. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca MB pomnożona przez liczbę godzin.  
  
**MySql RP**  
  
**Pomiarowe identyfikator**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nazwa licznika**: DatabaseSizeHourMySqlMeter  
**Jednostka**: MB\*godziny  
**Uwagi dotyczące**: DB całkowita pojemność podczas tworzenia. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca MB pomnożona przez liczbę godzin.  
  
**Obliczanie**  
  
**Pomiarowe identyfikator**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nazwa licznika**: godziny rozmiar dla podstawowej maszyny Wirtualnej  
**Jednostka**: wirtualnego podstawowe godziny  
**Uwagi dotyczące**: liczba rdzeni wirtualnego pomnożona przez godziny uruchomienia maszyny Wirtualnej.  
  
**Pomiarowe identyfikator**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nazwa licznika**: godziny rozmiar maszyny Wirtualnej systemu Windows  
**Jednostka**: wirtualnego podstawowe godziny  
**Uwagi dotyczące**: liczba rdzeni wirtualnego pomnożona przez godziny uruchomienia maszyny Wirtualnej.  
  
**Pomiarowe identyfikator**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nazwa licznika**: godziny rozmiar maszyny Wirtualnej  
**Jednostka**: godziny dla maszyny Wirtualnej  
**Uwagi dotyczące**: przechwytuje zarówno podstawowy, jak i systemu Windows maszyny Wirtualnej. Nie dostosowywać rdzeni.  
  
**Usługa Key Vault**  
  
**Pomiarowe identyfikator**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nazwa licznika**: transakcji magazynu kluczy  
**Jednostka**: żądanie zliczania, 10, 000's  
**Uwagi dotyczące**: numer REST API żądań odebranych przez płaszczyzna danych magazynu kluczy.  
  
**Pomiarowe identyfikator**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nazwa licznika**: Zaawansowane klucze transakcji  
**Jednostka**: 10 tys transakcji  
**Uwagi dotyczące**: RSA 3 K/4 K, transakcje klucza ECC. (wersja zapoznawcza).  
  
*Usługi aplikacji**  
  
**Pomiarowe identyfikator**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nazwa licznika**: usługi aplikacji  
**Jednostka**: wirtualnego podstawowe godziny  
**Uwagi dotyczące**: liczba rdzeni wirtualnego używane do uruchamiania usługi aplikacji. Uwaga: Firma Microsoft używa tego miernika do obciążania usługi aplikacji Azure stosu. Dostawcy usług w chmurze można użyć usługi App Service liczników (poniżej) można obliczyć obciążenie dla swoich dzierżaw.  
  
**Pomiarowe identyfikator**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nazwa licznika**: żądania funkcji  
**Jednostka**: 10 żądań  
**Uwagi dotyczące**: łączna liczba wykonań żądanego (na 10 wykonaniami). Wykonaniami zliczane są zawsze funkcji jest uruchamiany w odpowiedzi na zdarzenie lub jest wyzwalany przez powiązanie.  
  
**Pomiarowe identyfikator**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nazwa licznika**: funkcje — obliczeniowe  
**Jednostka**: GB s  
**Uwagi dotyczące**: zużycie zasobów mierzony w sekundach gigabajtów (GB/s). **Obserwowanych zużycie zasobów** jest obliczany przez pomnożenie pamięci średni rozmiar w GB przez czas w milisekundach potrzebny do wykonania funkcji. Pamięci używanej przez funkcję jest podawana przez zaokrąglenie do najbliższej 128 MB, zgodnie z rozmiarem maksymalna ilość pamięci 1,536 MB, z czasem wykonywania obliczana na podstawie zaokrąglenie do najbliższej 1 ms. Minimalny czas wykonywania i pamięci do wykonania jednej funkcji jest 100 ms i 128 mb odpowiednio.  
  
**Pomiarowe identyfikator**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nazwa licznika**: udostępnione godziny usługi aplikacji  
**Jednostka**: 1 godzina  
**Uwagi dotyczące**: na godzinę użycia niezależnego fragmentu planu usługi App Service. Plany są naliczane na podstawie poszczególnych aplikacji.  
  
**Pomiarowe identyfikator**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nazwa licznika**: wolnego godziny korzystania z usługi aplikacji  
**Jednostka**: 1 godzina  
**Uwagi dotyczące**: na użycie godziny wolne planu usługi App Service. Plany są naliczane na podstawie poszczególnych aplikacji.  
  
**Pomiarowe identyfikator**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nazwa licznika**: mały standardowe godziny usługi aplikacji  
**Jednostka**: 1 godzina  
**Uwagi dotyczące**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Pomiarowe identyfikator**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nazwa licznika**: średni standardowe godziny usługi aplikacji  
**Jednostka**: 1 godzina  
**Uwagi dotyczące**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Pomiarowe identyfikator**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nazwa licznika**: duże standardowe godziny usługi aplikacji  
**Jednostka**: 1 godzina  
**Uwagi dotyczące**: obliczana na podstawie rozmiaru i liczby wystąpień.  
  
**Niestandardowe procesu roboczego warstw**  
  
**Pomiarowe identyfikator**: *warstw niestandardowego procesu roboczego*  
**Nazwa licznika**: niestandardowy proces roboczy warstw  
**Jednostka**: godziny  
**Uwagi dotyczące**: identyfikator deterministyczne miernika jest tworzony na podstawie jednostki SKU i nazwa warstwy worker niestandardowych. Ten licznik identyfikator jest unikatowy dla każdej warstwy niestandardowego procesu roboczego.  
  
**Pomiarowe identyfikator**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nazwa licznika**: SNI SSL  
**Jednostka**: na powiązanie SNI SSL  
**Uwagi dotyczące**: App Service obsługuje dwa typy połączeń SSL: połączeń SSL oznaczenia nazwy serwera (SNI) i połączeń SSL adresów IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Pomiarowe identyfikator**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nazwa licznika**: SSL z adresu IP  
**Jednostka**: według adresu IP na podstawie powiązania SSL  
**Uwagi dotyczące**: App Service obsługuje dwa typy połączeń SSL: połączeń SSL oznaczenia nazwy serwera (SNI) i połączeń SSL adresów IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach.  
  
**Pomiarowe identyfikator**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nazwa licznika**: proces sieci Web  
**Jednostka**:  
**Uwagi dotyczące**: obliczone dla każdej witryny aktywny na godzinę.  
  
**Pomiarowe identyfikator**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nazwa licznika**: przepustowości wyjście zewnętrzne  
**Jednostka**: GB  
**Uwagi dotyczące**: łączna żądania przychodzącego, jeśli odpowiedź bajty + całkowita żądania wychodzącego bajty + całkowita FTP przychodzące żądanie odpowiedzi bajty + całkowita przychodzący sieci web wdrażanie bajtów odpowiedzi żądania.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Jak wykorzystanie stosu Azure, interfejsy API porównania [użycia usługi Azure API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (obecnie w publicznej wersji zapoznawczej)?
* Interfejs API użycia dzierżawcy jest zgodna z interfejsu API platformy Azure, z jednym wyjątkiem: *showDetails* flagi obecnie nie jest obsługiwana w stosie Azure.
* Interfejs API użycia dostawcy dotyczy tylko stosu Azure.
* Obecnie [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) czyli dostępnej na platformie Azure nie jest dostępna w stosie Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Jaka jest różnica między czasem użycia i zgłoszony czas?
Raporty danych użycia są dwie wartości czasu głównego:

* **Zgłoszony czas**. Czasu wprowadzenia systemu użycia zdarzenia użycia
* **Czas użytkowania**. Gdy zasobów Azure stos został wykorzystany czas

Można napotkać rozbieżności w wartości do użycia czas i czas zgłoszone zdarzenia użycia określonego. Opóźnienie może być tak długo, jak wiele godzin, w każdym środowisku.

Obecnie można wysyłać zapytania tylko przez *zgłoszony czas*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Co oznaczają one użycia interfejsu API?
| **Kod stanu HTTP** | **Kod błędu:** | **Opis** |
| --- | --- | --- |
| 400/Niewłaściwe żądanie |*NoApiVersion* |*Wersja interfejsu api* brakuje parametru zapytania. |
| 400/Niewłaściwe żądanie |*InvalidProperty* |Właściwość nie istnieje lub ma nieprawidłową wartość. Komunikat w kodzie błędu w treści odpowiedzi identyfikuje brakuje właściwości. |
| 400/Niewłaściwe żądanie |*RequestEndTimeIsInFuture* |Wartość *ReportedEndTime* przypada w przyszłości. Wartości w przyszłości nie są dozwolone dla tego argumentu. |
| 400/Niewłaściwe żądanie |*SubscriberIdIsNotDirectTenant* |Wywołanie interfejsu API dostawcy został użyty identyfikator subskrypcji, która nie jest prawidłową dzierżawy obiektu wywołującego. |
| 400/Niewłaściwe żądanie |*SubscriptionIdMissingInRequest* |Brak Identyfikatora subskrypcji obiektu wywołującego. |
| 400/Niewłaściwe żądanie |*InvalidAggregationGranularity* |Zażądano szczegółowości nieprawidłowy agregacji. Prawidłowe wartości to dzienne i co godzinę. |
| 503 |*ServiceUnavailable* |Wystąpił błąd powtarzający operację, ponieważ usługa jest zajęta lub wywołanie jest ograniczane. |

## <a name="next-steps"></a>Następne kroki
[Odbiorcy rozliczeń i obciążenia zwrotnego w stosie Azure](azure-stack-billing-and-chargeback.md)

[Użycie zasobów dostawcy interfejsu API](azure-stack-provider-resource-api.md)

[Interfejs API użycia zasobów dzierżawcy](azure-stack-tenant-resource-usage-api.md)
