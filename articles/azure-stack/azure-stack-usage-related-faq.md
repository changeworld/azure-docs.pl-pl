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
ms.date: 03/09/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0df9b02e7672faec3a1d94997c9b27ffca275e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Często zadawane pytania w stosie Azure użycia interfejsu API
Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące interfejsu API Azure stosu użycia.

## <a name="what-meter-ids-can-i-see"></a>Jakie miernika identyfikatorów może wyświetlać?
Użycie jest zgłaszany w przypadku następujących dostawców zasobów:

| **Dostawca zasobów** | **Identyfikator miernika** | **Nazwa licznika** | **Unit** | **Dodatkowe informacje** |
| --- | --- | --- | --- | --- |
| **Sieć** |F271A8A388C44D93956A063E1D2FA80B |Użycie adresu statycznego adresu IP |Adresy IP| Adresy IP liczba używane. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca adres IP pomnożona przez liczbę godzin. |
| |9E2739BA86744796B465F64674B822BA |Użycie adresu dynamicznego adresu IP |Adresy IP| Adresy IP liczba używane. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca adres IP pomnożona przez liczbę godzin. |
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*godziny |Łączna pojemność używane przez tabel. |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*godziny |Łączna pojemność używane przez stronicowych obiektów blob. |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*godziny |Łączna pojemność używane przez kolejkę. |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*godziny |Łączna pojemność używane przez blokowych obiektów blob. |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Liczba żądań w 000's 10 |Żądania obsługi tabeli (w 10 000). |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Transfer danych przychodzących danych w GB |Usługa tabeli danych wejściowych w GB. |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Transfer danych wychodzących w GB |Wyjście danych usługi tabeli w GB |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Żądania liczba 10, 000's |Żądania obsługi obiektów blob (w 10 000). |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Transfer danych przychodzących danych w GB |Usługa obiektu blob danych wejściowych w GB. |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Transfer danych wychodzących w GB |Wyjście danych usługi obiektów blob w GB. |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Żądania liczba 10, 000's |Żądania obsługi kolejki (w 10 000). |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Transfer danych przychodzących danych w GB |Usługa kolejki danych wejściowych w GB. |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Transfer danych wychodzących w GB |Wyjście danych usługi kolejki w GB |
| **SQL RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*godziny   | Całkowita pojemność baz danych podczas tworzenia. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca MB pomnożona przez liczbę godzin. |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*godziny    | Całkowita pojemność baz danych podczas tworzenia. Jeśli wywołujesz interfejs API użycia z dzienne licznika zwraca MB pomnożona przez liczbę godzin. |
| **Obliczanie** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Godziny rozmiar podstawowej maszyny Wirtualnej |Wirtualne podstawowe godziny | Liczba rdzeni wirtualnego pomnożona przez godziny, w których uruchomiono maszyny Wirtualnej. |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Godziny rozmiar maszyny Wirtualnej systemu Windows |Wirtualne podstawowe godziny | Liczba rdzeni wirtualnego pomnożona przez godziny uruchomienia maszyny Wirtualnej. |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Godziny rozmiar maszyny Wirtualnej |Liczba godzin korzystania z maszyny Wirtualnej |Przechwytuje zarówno podstawowy, jak i systemu Windows maszyny Wirtualnej. Nie dostosowywać rdzeni. |
| **Usługa Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transakcje magazynu kluczy | Liczba żądań w 000's 10| Liczba żądań interfejsu API REST odebranych przez płaszczyzna danych magazynu kluczy. |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |Zaawansowane klucze transakcji | 10 tys transakcji|     RSA 3K / 4K, ECC klucza transakcji. (wersja zapoznawcza). |
| **Usługi aplikacji** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service | Wirtualne podstawowe godziny | Liczba rdzeni wirtualnego używane do uruchamiania usługi aplikacji. Uwaga: Firma Microsoft używa tego miernika do obciążania usługi aplikacji Azure stosu. Dostawcy usług w chmurze można użyć usługi App Service liczników (poniżej) można obliczyć obciążenie dla swoich dzierżaw. |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Żądania funkcji | 10 żądań | Całkowita liczba żądanych wykonaniami (na 10 wykonaniami). Wykonaniami zliczane są zawsze funkcji jest uruchamiany w odpowiedzi na zdarzenie lub jest wyzwalany przez powiązanie. |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Funkcje — obliczeń | GB s | Zużycie zasobów mierzony w sekundach gigabajtów (GB/s). **Obserwowanych zużycie zasobów** jest obliczany przez pomnożenie pamięci średni rozmiar w GB przez czas w milisekundach potrzebny do wykonania funkcji. Pamięci używanej przez funkcję jest podawana przez zaokrąglenie do najbliższej 128 MB, zgodnie z rozmiarem maksymalna ilość pamięci 1,536 MB, z czasem wykonywania obliczana na podstawie zaokrąglenie do najbliższej 1 ms. Minimalny czas wykonywania i pamięci do wykonania jednej funkcji jest 100 ms i 128 mb odpowiednio. |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Liczba godzin korzystania z usługi aplikacji — warstwa | 1 godzina | Na godzinę użycie niezależnych planu usługi App Service. Plany są naliczane na podstawie poszczególnych aplikacji. |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Liczba godzin korzystania z usługi aplikacji — warstwa bezpłatna | 1 godzina | Na użycie godziny wolne planu usługi App Service. Plany są naliczane na podstawie poszczególnych aplikacji. |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Liczba godzin korzystania z małych standardowe usługi aplikacji | 1 godzina | Obliczana na podstawie rozmiaru i liczby wystąpień. |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Godziny standardowe usługi aplikacji — średnia | 1 godzina | Obliczana na podstawie rozmiaru i liczby wystąpień. |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Liczba godzin korzystania z dużych standardowe usługi aplikacji | 1 godzina | Obliczana na podstawie rozmiaru i liczby wystąpień. |
|  | *Niestandardowe procesu roboczego warstw* | Niestandardowe procesu roboczego warstw | Godziny | Zostanie utworzony na podstawie jednostki SKU i nazwa warstwy worker niestandardowy identyfikator deterministyczne miernika. Ten licznik identyfikator jest unikatowy dla każdej warstwy niestandardowego procesu roboczego. |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | Na powiązanie SNI SSL | Usługi aplikacji obsługuje dwa typy połączeń SSL: połączeń SSL oznaczenia nazwy serwera (SNI) i połączeń SSL adresów IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach. |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | Według adresu IP na podstawie powiązania SSL | Usługi aplikacji obsługuje dwa typy połączeń SSL: połączeń SSL oznaczenia nazwy serwera (SNI) i połączeń SSL adresów IP. Połączenie SSL oparte na SNI działa w nowoczesnych przeglądarkach, natomiast połączenie SSL oparte na adresie IP działa we wszystkich przeglądarkach. |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Proces sieci Web |  | Obliczone dla każdej witryny aktywny na godzinę. |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | Wyjście zewnętrzne przepustowości | GB | Całkowita liczba bajtów odpowiedzi żądania przychodzące + całkowita żądania wychodzącego bajty + całkowita FTP przychodzącego żądania bajtów odpowiedzi + całkowita przychodzący sieci web wdrażanie bajtów odpowiedzi żądania. |

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
