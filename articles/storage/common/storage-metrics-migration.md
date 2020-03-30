---
title: Migracja metryk usługi Azure Storage | Dokumenty firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację starych metryk do nowych metryk zarządzanych przez usługę Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855337"
---
# <a name="azure-storage-metrics-migration"></a>Migracja metryk usługi Azure Storage

Zgodnie ze strategią ujednolicenia środowiska monitora na platformie Azure usługa Azure Storage integruje metryki z platformą Azure Monitor. W przyszłości usługa starych metryk zakończy się wczesnym powiadomieniem opartym na zasadach platformy Azure. Jeśli korzystasz ze starych metryk magazynu, musisz przeprowadzić migrację przed datą zakończenia usługi, aby zachować informacje o metrykach.

W tym artykule pokazano, jak przeprowadzić migrację ze starych metryk do nowych metryk.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Informacje o starych metrykach zarządzanych przez usługę Azure Storage

Usługa Azure Storage zbiera stare wartości metryki i agreguje je i przechowuje w tabelach $Metric w ramach tego samego konta magazynu. Za pomocą witryny Azure Portal można skonfigurować wykres monitorowania. Można również użyć zestawów SDK usługi Azure Storage do odczytu danych z tabel $Metric, które są oparte na schemacie. Aby uzyskać więcej informacji, zobacz [Analiza pamięci masowej](./storage-analytics.md).

Stare metryki zapewniają metryki pojemności tylko w magazynie obiektów Blob platformy Azure. Stare metryki zapewniają metryki transakcji w magazynie obiektów Blob, magazynie tabel, plikach azure i magazynie kolejki.

Stare metryki są zaprojektowane w schemacie płaskim. Projekt powoduje zero wartości metryki, gdy nie masz wzorców ruchu wyzwalających metrykę. Na przykład **ServerTimeoutError** wartość jest ustawiona na 0 w tabelach $Metric nawet wtedy, gdy nie są odbierane błędy limitu czasu serwera z ruchu na żywo do konta magazynu.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Informacje o nowych metrykach zarządzanych przez usługę Azure Monitor

W przypadku nowych metryk magazynu usługa Azure Storage emituje dane metryki do zaplecza usługi Azure Monitor. Usługa Azure Monitor zapewnia ujednolicone środowisko monitorowania, w tym dane z portalu, a także pozyskiwania danych. Aby uzyskać więcej informacji, można znaleźć w tym [artykule](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nowe metryki zapewniają metryki pojemności i metryki transakcji na obiektach Blob, Table, File, Queue i premium storage.

Wielowymiarze to jedna z funkcji udostępnianych przez usługę Azure Monitor. Usługa Azure Storage przyjmuje projekt przy definiowaniu nowego schematu metryki. Aby uzyskać obsługiwane wymiary metryk, szczegółowe informacje można znaleźć w [metrykach usługi Azure Storage w usłudze Azure Monitor.](./storage-metrics-in-azure-monitor.md) Wielowymiarowa konstrukcja zapewnia oszczędność kosztów zarówno przepustowości od pozyskiwania, jak i pojemności z przechowywania metryk. W związku z tym jeśli ruch nie wyzwolił powiązanych metryk, powiązane dane metryki nie zostaną wygenerowane. Na przykład jeśli ruch nie wyzwolił żadnych błędów limitu czasu serwera, usługa Azure Monitor nie zwraca żadnych danych podczas kwerendy wartości metryki **Transakcje** z wymiarem **ResponseType** równym **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapowanie metryk między starymi i nowymi metrykami

Jeśli dane metryki są odczytywane programowo, należy przyjąć nowy schemat metryki w programach. Aby lepiej zrozumieć zmiany, można odwołać się do mapowania wymienionych w poniższej tabeli:

**Metryki pojemności**

| Stara metryka | Nowa metryka |
| ------------------- | ----------------- |
| **Pojemność**            | **BlobCapacity** z wymiarem **BlobType** równa **BlockBlob** lub **PageBlob** |
| **Liczba obiektów**        | **Obiekt BlobCount** o wymiarach **BlobType** równy **BlockBlob** lub **PageBlob** |
| **Liczba kontenerów**      | **Liczba kontenerów** |

Następujące dane to nowe oferty, których stare metryki nie obsługują:
* **TableCapacity (Zdolność do pońoliczy)**
* **Liczba tabel**
* **Liczba entity tabeli**
* **QueueCapacity (Zdolność do kolejek)**
* **Liczba kolejek**
* **KolejkaLiczna liczba**
* **Zdolność do pończęcia plików**
* **Liczba plików**
* **Liczba plików ShareCount**
* **UżywaneCapacity**

**Metryki transakcji**

| Stara metryka | Nowa metryka |
| ------------------- | ----------------- |
| **AnonimowaAutoryzacjaError** | Transakcje z wymiarem **ResponseType** równym **AuthorizationError** i dimension **Authentication** równe **anonimowemu** |
| **AnonymousClientOtherError** | Transakcje z wymiarem **ResponseType** równym **ClientOtherError** i **uwierzytelnianiu** wymiaru równe **anonimowemu** |
| **AnonymousClientTimeoutError** | Transakcje o wymiarze **ResponseType** równe **ClientTimeoutError** i **uwierzytelnianie** wymiaru równe **anonimowemu** |
| **AnonymousNetworkError** | Transakcje z wymiarem **ResponseType** równym **NetworkError** i **uwierzytelniania** wymiaru równe **anonimowemu** |
| **AnonimowyServerOtherError** | Transakcje z wymiarem **ResponseType** równym **ServerOtherError** i **uwierzytelnianiu** wymiaru równe **anonimowemu** |
| **AnonymousServerTimeoutError** | Transakcje o wymiarze **ResponseType** równym **ServerTimeoutError** i **uwierzytelnianiu** wymiaru równe **anonimowemu** |
| **AnonimSukces** | Transakcje o wymiarze **ResponseType** równe **uwierzytelniania** **sukcesu** i wymiaru równe **anonimowemu** |
| **AnonimowyThrottlingError** | Transakcje z wymiarem **ResponseType** równym **ClientThrottlingError** lub **ServerBusyError** i **uwierzytelnianiu** wymiaru równe **anonimowemu** |
| **AutoryzacjaEror** | Transakcje z wymiarem **ResponseType** równym **AuthorizationError** |
| **Dostępność** | **Dostępność** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **KlientOtherError** | Transakcje z wymiarem **ResponseType** równym **ClientOtherError** |
| **KlientTimeoutError** | Transakcje o wymiarze **ResponseType** równe **ClientTimeoutError** |
| **Usługa NetworkError** | Transakcje z wymiarem **ResponseType** równym **NetworkError** |
| **ProcentAutoryzacjaError** | Transakcje z wymiarem **ResponseType** równym **AuthorizationError** |
| **ProcentClientOtherError** | Transakcje z wymiarem **ResponseType** równym **ClientOtherError** |
| **ProcentNetworkError** | Transakcje z wymiarem **ResponseType** równym **NetworkError** |
| **PercentServerOtherError** | Transakcje z wymiarem **ResponseType** równym **ServerOtherError** |
| **ProcentSukces** | Transakcje o wymiarze **ResponseType** równe **sukcesowi** |
| **ProcentThrottlingError** | Transakcje z wymiarem **ResponseType** równym **ClientThrottlingError** lub **ServerBusyError** |
| **ProcentCzaserror** | Transakcje o wymiarze **ResponseType** równe **ServerTimeoutError** lub **ResponseType** równe **ClientTimeoutError** |
| **SasAutoryzacjaError** | Transakcje z wymiarem **ResponseType** równym **AuthorizationError** i dimension Authentication równe **sygnatury dostępu Współdzielonego** **Authentication** |
| **SASClientOtherError** | Transakcje z wymiarem **ResponseType** równym **ClientOtherError** i uwierzytelnianiu wymiaru równe **sygnaturze dostępu Współdzielonego** **Authentication** |
| **SASClientTimeoutError** | Transakcje o wymiarze **ResponseType** równe **ClientTimeoutError** i uwierzytelnianie wymiaru równe **sygnaturze dostępu Współdzielonego** **Authentication** |
| **SASNetworkError** | Transakcje z wymiarem **ResponseType** równym **NetworkError** i **uwierzytelniania wymiarów** równe **sygnaturze dostępu Współdzielonego** |
| **SASServerOtherError** | Transakcje z wymiarem **ResponseType** równym **ServerOtherError** i uwierzytelnianiu wymiarów równym **sygnatury dostępu Współdzielonego** **Authentication** |
| **SasServerTimeoutError** | Transakcje o wymiarze **ResponseType** równym **ServerTimeoutError** i **uwierzytelnianiu wymiarów równemu sygnatury** **dostępu Współdzielonego** |
| **Sukces SASSuccess** | Transakcje o wymiarze **ResponseType** równe **uwierzytelniania sukcesu** i **uwierzytelniania wymiarów** równe **sygnatury dostępu Współdzielonego** |
| **SASThrottlingError** | Transakcje z wymiarem **ResponseType** równym **ClientThrottlingError** lub **ServerBusyError** i uwierzytelnianiu wymiaru równe **sygnaturze dostępu Współdzielonego** **Authentication** |
| **SerwerOtherError** | Transakcje z wymiarem **ResponseType** równym **ServerOtherError** |
| **ServerTimeoutError** | Transakcje o wymiarze **ResponseType** równe **ServerTimeoutError** |
| **Powodzenie** | Transakcje o wymiarze **ResponseType** równe **sukcesowi** |
| **ThrottlingError** | **Transakcje** z wymiarem **ResponseType** równym **ClientThrottlingError** lub **ServerBusyError**|
| **TotalBillableRequests (Liczba zaliczek na zobowiązania do)** | **Transakcje** |
| **TotalEgress** | **Ruch wychodzący** |
| **TotalIngress** | **Ruch przychodzący** |
| **TotalRequests** | **Transakcje** |

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak przeprowadzić migrację istniejących reguł alertów?

Jeśli utworzono klasyczne reguły alertów na podstawie starych metryk magazynu, należy utworzyć nowe reguły alertów na podstawie nowego schematu metryki.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Czy nowe dane metryki są domyślnie przechowywane na tym samym koncie magazynu?

Nie. Aby zarchiwizować dane metryki na koncie magazynu, użyj [interfejsu API ustawień diagnostycznych usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Następne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metryki magazynu w usłudze Azure Monitor](./storage-metrics-in-azure-monitor.md)
