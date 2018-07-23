---
title: Azure IoT Hub wysokiej dostępności i odzyskiwania po awarii | Dokumentacja firmy Microsoft
description: Opisano funkcje platformy Azure i usługi IoT Hub, które ułatwiają tworzenie wysoko dostępnych rozwiązań Azure IoT przy użyciu po awarii możliwości odzyskiwania.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 992c42511f7bc9e9af71ff552ee91bc2472ebcf8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185707"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Usługa IoT Hub o wysokiej dostępności i odzyskiwania po awarii
Jako usługi Azure IoT Hub udostępnia wysokiej dostępności (HA) przy użyciu nadmiarowość na poziomie regionu platformy Azure, bez konieczności wykonywania dodatkowej pracy wymaganego przez rozwiązanie. Platforma Microsoft Azure zawiera funkcje ułatwiające tworzenie rozwiązań za pomocą funkcji odzyskiwania po awarii lub dostępności między regionami. Jeśli chcesz zapewnić globalnego, między regionami wysokiej dostępności dla urządzeń lub użytkowników, korzystać z tych funkcji odzyskiwania po awarii z platformy Azure. Artykuł [wskazówek technicznych ciągłości biznesowej Azure](../resiliency/resiliency-technical-guidance.md) opis wbudowanych funkcji na platformie Azure zapewniającej ciągłość biznesową i odzyskiwanie po awarii. [Odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure] [ Disaster recovery and high availability for Azure applications] dokument zawiera wskazówki dotyczące architektury, strategii dla aplikacji platformy Azure w celu osiągnięcia wysokiej dostępności i odzyskiwania po awarii.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Oprócz HA wewnątrz regionu IoT Hub zaimplementowano mechanizmów trybu failover odzyskiwania po awarii, które wymagają interwencji użytkownika. IoT Hub odzyskiwania po awarii własnym jest inicjowane, a ma cel czasu odzyskiwania (RTO), 2-26 godzin, a także następujące cele punktu odzyskiwania (RPO):

| Funkcjonalność | RPO |
| --- | --- |
| Dostępność usługi operacje rejestru i komunikacji |Możliwa utrata rekordu CName |
| Dane tożsamości w rejestrze tożsamości |0 – 5 minutach utraty danych |
| Komunikaty z urządzenia do chmury |Wszystkie nieprzeczytane wiadomości są tracone |
| Monitorowanie komunikatów operacji |Wszystkie nieprzeczytane wiadomości są tracone |
| Komunikaty z chmury do urządzenia |0 – 5 minutach utraty danych |
| Kolejki opinii chmury do urządzenia |Wszystkie nieprzeczytane wiadomości są tracone |
| Dane bliźniaczej reprezentacji urządzenia |0 – 5 minutach utraty danych |
| Zadania nadrzędnego i urządzenia |0 – 5 minutach utraty danych |

## <a name="regional-failover-with-iot-hub"></a>Za pomocą usługi IoT Hub rozwiązania regionalnej pracy awaryjnej
Pełne traktowania topologie wdrożenia w rozwiązaniach IoT znajduje się poza zakres tego artykułu. W tym artykule omówiono *rozwiązania regionalnej pracy awaryjnej* modelu wdrażania na potrzeby wysokiej dostępności i odzyskiwania po awarii.

W modelu rozwiązania regionalnej pracy awaryjnej rozwiązania, należy utworzyć kopię końcowy uruchomień głównie w jednej lokalizacji centrum danych. Pomocnicze Centrum IoT i zapleczem są wdrażane w innej lokalizacji centrum danych. Jeśli usługi IoT hub w podstawowym centrum danych niska ulegnie awarii lub zostanie przerwane, łączności sieciowej z urządzenia do głównego centrum danych, urządzenia używają punktu końcowego usługi dodatkowej. Może zwiększyć dostępność rozwiązanie z zastosowaniem modelu trybu failover między regionami, zamiast pozostając w jednym regionie. 

Na wysokim poziomie do wdrożenia modelu rozwiązania regionalnej pracy awaryjnej przy użyciu usługi IoT Hub potrzebne są następujące elementy:

* **Pomocnicze Centrum IoT i urządzenia routingu logiki**: Jeśli usługi w danym regionie podstawowym zostanie przerwany, urządzenia należy uruchomić nawiązywania połączenia z regionu pomocniczego. Ze względu na charakter rozpoznawaniem stanu większości usług zaangażowani, jest typowe dla administratorów rozwiązania do wyzwalania procesu trybu failover między regionami. Najlepszym sposobem komunikacji nowego punktu końcowego na urządzeniach, przy jednoczesnym zachowaniu kontroli procesu, jest do nich regularnie sprawdzać *concierge* usługi, aby aktywny punkt końcowy. Usługi Konsjerż może być aplikacja sieci web, które są replikowane i przechowywane dostępny przy użyciu technik przekierowania DNS (na przykład za pomocą [usługi Azure Traffic Manager][Azure Traffic Manager]).
* **Replikacja rejestru tożsamości**: może być używany, pomocniczego Centrum IoT hub musi zawierać wszystkie tożsamości urządzeń, które można połączyć z rozwiązaniem. Rozwiązanie powinno replikowanej geograficznie dane o tożsamości urządzeń i przekazać je do pomocniczego Centrum IoT, przed zmianą aktywny punkt końcowy dla urządzeń. Funkcja eksportu tożsamość urządzenia usługi IoT Hub przydaje się w tym kontekście. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub developer guide - rejestr tożsamości][IoT Hub developer guide - identity registry].
* **Scalanie logiki**: gdy region podstawowy staje się ponownie dostępny, wszystkie stan i dane, które zostały utworzone w lokacji dodatkowej muszą być migrowane z powrotem do regionu podstawowego. Ten stan i dane odnoszą się przede wszystkim do tożsamości urządzeń i metadanych aplikacji, które muszą zostać połączone z głównej usługi IoT hub i innych magazynach specyficzne dla aplikacji w regionie podstawowym. Aby uprościć ten krok, należy użyć operacje idempotentne. Operacje Idempotentne zminimalizować skutki uboczne, z ostateczną spójne dystrybucję zdarzeń oraz duplikatów lub dostarczania zdarzeń poza kolejnością. Ponadto logiki aplikacji powinny zostać tak zaprojektowane, tolerować potencjalne niezgodności lub stan "nieco" nieaktualne. Taka sytuacja może wystąpić z powodu dodatkowy czas, jaki zajmuje systemowi "poprawianie" oparte na cele punktu odzyskiwania (RPO).

## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure IoT Hub:

* [Rozpoczynanie pracy z usługą IoT Hub (samouczek)][lnk-get-started]
* [Co to jest usługa Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
