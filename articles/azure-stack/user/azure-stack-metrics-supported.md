---
title: Metryki obsługiwane z usługą Azure Monitor w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obsługiwane metryki dla usługi Azure Monitor w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 109ac1b0d9f52211f05e5fc1b177aa775ba66aae
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889897"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Obsługiwane metryki z usługą Azure Monitor w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Możesz pobrać metryki z usługi Azure monitor w usłudze Azure Stack w taki sam jak globalnej platformy Azure. Możesz miary w portalu, pobrać je z interfejsu API REST lub wyszukiwać w nich za pomocą programu PowerShell lub interfejsu wiersza polecenia.

W poniższej tabeli wymieniono dostępne przy użyciu usługi Azure Monitor potoku metryk w usłudze Azure Stack metryki. Aby zbadać i dostęp do tych metryk, konieczne będzie **2018-01-01** wersji api-version profilu interfejsu API. Aby uzyskać więcej informacji o profilach interfejsu API i usługi Azure Stack, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia | Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne | Nie wymiarów |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Używana pojemność | Bajty | Średnia | Używana pojemność konta | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla opóźnienia | Milisekundy | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie E2E dla powodzenia | Milisekundy | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Pojemność obiektu blob | Bajty | Łącznie | Ilość miejsca w magazynie (w bajtach) używana przez usługę obiektów blob konta magazynu. | BlobType |
| BlobCount | Liczba obiektów blob | Licznik | Łącznie | Liczba obiektów blob w usłudze obiektów blob konta magazynu. | BlobType |
| ContainerCount | Liczba kontenerów obiektów blob | Licznik | Średnia | Liczba kontenerów w usłudze obiektów blob konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla opóźnienia | Milisekundy | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie E2E dla powodzenia | Milisekundy | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Pojemność tabeli | Bajty | Średnia | Ilość miejsca w magazynie (w bajtach) używanego przez usługę tabeli konta magazynu. | Nie wymiarów |
| TableCount | Liczba tabel | Licznik | Średnia | Liczba tabel w usłudze tabeli konta magazynu. | Nie wymiarów |
| TableEntityCount | Liczba jednostek tabel | Licznik | Średnia | Liczba jednostek tabel w usłudze tabeli konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla opóźnienia | Milisekundy | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie E2E dla powodzenia | Milisekundy | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Pojemność kolejki | Bajty | Średnia | Ilość miejsca w magazynie (w bajtach) używanego przez usługę kolejki konta magazynu. | Nie wymiarów |
| QueueCount | Liczba kolejek | Licznik | Średnia | Liczba kolejek w usłudze kolejki konta magazynu. | Nie wymiarów |
| QueueMessageCount | Liczba komunikatów w kolejce | Licznik | Średnia | Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Zdarzenia związane z transferem danych przychodzących | Zdarzenia związane z transferem danych przychodzących | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla opóźnienia | Milisekundy | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie E2E dla powodzenia | Milisekundy | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Azure monitorowanie w usłudze Azure Stack](azure-stack-metrics-azure-data.md).