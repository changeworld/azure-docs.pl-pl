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
ms.date: 12/06/2018
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: c7b0332d2a1df9d61b95b73699bf04f32e3165ff
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240543"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Obsługiwane metryki z usługą Azure Monitor w usłudze Azure Stack

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Możesz pobrać metryki z usługi Azure monitor w usłudze Azure Stack w taki sam jak globalnej platformy Azure. Można utworzyć miary w portalu, je uzyskać z interfejsu API REST lub wyszukiwać w nich za pomocą programu PowerShell lub interfejsu wiersza polecenia.

W poniższej tabeli wymieniono dostępne przy użyciu usługi Azure Monitor potoku metryk w usłudze Azure Stack metryki. Aby zbadać i dostęp do tych metryk, musisz użyć **2018-01-01** wersji api-version profilu interfejsu API. Aby uzyskać więcej informacji o profilach interfejsu API i usługi Azure Stack, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procentowe użycie procesora CPU | Procentowe użycie procesora CPU | Procent | Średnia | Procent przydzielonych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne | Nie wymiarów |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Używana pojemność | Bajty | Średnia | Używana pojemność konta | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Ruch przychodzący | Ruch przychodzący | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Pojemność obiektu blob | Bajty | Łącznie | Ilość miejsca używanego przez usługę obiektów Blob konta magazynu, w bajtach. | BlobType |
| BlobCount | Liczba obiektów blob | Licznik | Łącznie | Liczba obiektów Blob w usłudze obiektów Blob konta magazynu. | BlobType |
| ContainerCount | Liczba kontenerów obiektów blob | Licznik | Średnia | Liczba kontenerów w usłudze obiektów Blob konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Ruch przychodzący | Ruch przychodzący | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Pojemność tabeli | Bajty | Średnia | Ilość miejsca używanego przez usługę tabeli konta magazynu, w bajtach. | Nie wymiarów |
| TableCount | Liczba tabel | Licznik | Średnia | Liczba tabel w usłudze tabeli konta magazynu. | Nie wymiarów |
| TableEntityCount | Liczba jednostek tabel | Licznik | Średnia | Liczba jednostek tabel w usłudze tabeli konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Ruch przychodzący | Ruch przychodzący | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metryka | Nazwa wyświetlana metryki | Jednostka | Typ agregacji | Opis | Wymiary |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Pojemność kolejki | Bajty | Średnia | Ilość miejsca używanego przez usługę kolejki konta magazynu, w bajtach. | Nie wymiarów |
| QueueCount | Liczba kolejek | Licznik | Średnia | Liczba kolejek w usłudze kolejki konta magazynu. | Nie wymiarów |
| QueueMessageCount | Liczba komunikatów w kolejce | Licznik | Średnia | Przybliżona liczba komunikatów w kolejce w usłudze kolejki konta magazynu. | Nie wymiarów |
| Transakcje | Transakcje | Licznik | Łącznie | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje udane i nieudane żądania, a także żądania, które wygenerowany błędy. Korzystanie z wymiaru ResponseType numer innego typu odpowiedzi. | ResponseType, GeoType, ApiName |
| Ruch przychodzący | Ruch przychodzący | Bajty | Łącznie | Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. | GeoType, ApiName |
| Ruch wychodzący | Ruch wychodzący | Bajty | Łącznie | Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. | GeoType, ApiName |
| SuccessServerLatency | Opóźnienie serwera dla powodzenia | MS | Średnia | Średni czas oczekiwania, używane przez usługę Azure Storage do przetwarzania żądania zakończonego powodzeniem w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego w elemencie AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Opóźnienie e2e dla powodzenia | MS | Średnia | Średnie opóźnienie end-to-end żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. | GeoType, ApiName |
| Dostępność | Dostępność | Procent | Średnia | Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests i podzielenie go przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy skutkują od ograniczoną dostępnością usługi magazynu lub określonej operacji interfejsu API. | GeoType, ApiName |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Azure monitorowanie w usłudze Azure Stack](azure-stack-metrics-azure-data.md).
