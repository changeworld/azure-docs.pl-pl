---
title: Usługa Azure IoT Hub wysokiej dostępności i odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji Azure i Centrum IoT, które ułatwiają tworzenie wysokiej dostępności rozwiązania Azure IoT z po awarii możliwości odzyskiwania.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 428209defa554599c01789e6f2a8b62f155b0f2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633710"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Centrum IoT wysokiej dostępności i odzyskiwania po awarii
Jako usługi Azure IoT Hub zapewnia wysokiej dostępności (HA), za pomocą zwolnienia na poziomie region platformy Azure, bez konieczności wykonywania dodatkowych działań wymaganych przez to rozwiązanie. Platforma Microsoft Azure obejmuje również funkcje ułatwiające tworzenie rozwiązań z możliwości odzyskiwanie po awarii lub dostępności między regionu. Jeśli chcesz zapewnić globalnych, region między wysoką dostępność dla urządzeń lub użytkowników, korzystać z tych funkcji odzyskiwania po awarii usługi Azure. Artykuł [wskazówki techniczne ciągłości biznesowej Azure](../resiliency/resiliency-technical-guidance.md) opisuje wbudowane funkcje w systemie Azure ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii. [Odzyskiwania po awarii i wysoką dostępność aplikacji Azure] [ Disaster recovery and high availability for Azure applications] dokument zawiera wskazówki architektura strategie aplikacjami platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Oprócz HA region wewnątrz Centrum IoT implementuje mechanizmów pracy awaryjnej dla odzyskiwania po awarii, które wymagają interwencji użytkownika. DR Centrum IoT własnym jest inicjowana i ma celu czasu odzyskiwania (RTO), 2-26 godzin i następujących celów punktu odzyskiwania (RPO):

| Funkcjonalność | RPO |
| --- | --- |
| Dostępność usługi dla operacji rejestru i komunikacji |Możliwa utrata CName |
| Danych tożsamości w rejestrze tożsamości |0 – 5 minutach utrata danych |
| Komunikaty z urządzenia do chmury |Wszystkie komunikaty nieprzeczytana zostaną utracone |
| Operacje monitorowania wiadomości |Wszystkie komunikaty nieprzeczytana zostaną utracone |
| Komunikaty chmury do urządzenia |0 – 5 minutach utrata danych |
| Kolejki opinii chmury do urządzenia |Wszystkie komunikaty nieprzeczytana zostaną utracone |
| Dane dwie urządzenia |0 – 5 minutach utrata danych |
| Zadania nadrzędnego i urządzenia |0 – 5 minutach utrata danych |

## <a name="regional-failover-with-iot-hub"></a>Regionalne trybu failover z Centrum IoT
Pełne traktowania topologii wdrażania w rozwiązania IoT wykracza poza zakres tego artykułu. W tym artykule omówiono *regionalnej pracy awaryjnej* modelu wdrażania na potrzeby wysokiej dostępności i odzyskiwania po awarii.

W modelu regionalnej pracy awaryjnej rozwiązania kopii końcowy uruchomień głównie w jednym miejscu centrum danych. Dodatkowej Centrum IoT i zaplecza są wdrażane w innym miejscu centrum danych. Jeśli Centrum IoT w centrum danych głównej wystąpi awaria lub łączność sieciową z urządzenia podstawowego centrum danych zostało przerwane, urządzenia używają punkt końcowy usługi dodatkowej. Dostępności rozwiązania można zwiększyć dzięki implementacji modelu trybu failover między region zamiast przebywających w pojedynczym regionie. 

Na wysokim poziomie Aby zaimplementować modelu regionalnej pracy awaryjnej z Centrum IoT potrzebne są następujące elementy:

* **Dodatkowej Centrum IoT i urządzenia routingu logiki**: Jeśli zostanie przerwany usługi w danym regionie podstawowym, urządzenia musi rozpoczynać się łączenie z regionu pomocniczego. Charakter obsługujący stanu większość usług, jest typowe dla administratorów rozwiązania do wyzwalania procesu trybu failover między regionu. Najlepszym sposobem komunikacji nowy punkt końcowy do urządzeń, zachowując kontrolę nad procesem, jest do nich regularne sprawdzanie *Konsjerż* usługi dla aktywnego punktu końcowego. Usługi Konsjerż mogą być replikowane i przechowywane dostępny aplikacji sieci web przy użyciu technik przekierowania DNS (na przykład za pomocą [usługi Azure Traffic Manager][Azure Traffic Manager]).
* **Replikacja rejestru tożsamości**: może być używany, pomocniczego Centrum IoT musi zawierać wszystkie tożsamości urządzenia, które można łączyć z rozwiązania. Rozwiązania należy zachować replikacją geograficzną kopii zapasowych tożsamości urządzenia i przekazać je do pomocniczego Centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. Funkcja eksportu tożsamości urządzenia z Centrum IoT jest przydatne w tym kontekście. Aby uzyskać więcej informacji, zobacz [Centrum IoT — przewodnik dewelopera - rejestru tożsamości][IoT Hub developer guide - identity registry].
* **Scalanie logiki**: kiedy regionu podstawowego będzie ponownie dostępny, wszystkie stan i dane, które zostały utworzone w lokacji dodatkowej muszą być migrowane z powrotem do regionu podstawowego. Ten stan i dane odnoszą się przede wszystkim do tożsamości urządzenia i metadanych aplikacji, które muszą zostać połączone z głównej Centrum IoT i innych magazynach specyficzne dla aplikacji w regionie podstawowym. Aby uprościć ten krok, należy użyć operacji idempotentności. Operacje idempotentności zminimalizować efekty uboczne, od ostatecznego spójne dystrybucję zdarzeń oraz duplikatów ani poza kolejnością dostarczania zdarzeń. Ponadto należy tak zaprojektować logiki aplikacji, do tolerować potencjalne niespójności lub stan "nieco" nieaktualne. Taka sytuacja może wystąpić z powodu dodatkowy czas, jaki zajmuje systemu "poprawianego" zależności od celów punktu odzyskiwania (RPO).

## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat Centrum IoT Azure:

* [Rozpoczynanie pracy z centra IoT (samouczek)][lnk-get-started]
* [Co to jest Centrum IoT Azure?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
