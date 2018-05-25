---
title: Opcje usługi Microsoft Azure IoT | Microsoft Docs
description: Wybierz sposób implementowania rozwiązania Azure IoT przy użyciu usługi Azure IoT Central, IoT Suite lub IoT Hub.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Porównanie opcji usług Azure IoT Central i Azure IoT

Aby zaimplementować rozwiązanie IoT, usługi Microsoft Azure IoT Central i Azure IoT oferują różne opcje, z których każda spełnia różne wymagania klientów:

* [Azure IoT Central](overview-iot-central.md) to rozwiązanie typu oprogramowanie jako usługa (SaaS) korzystające z podejścia opartego na modelu w celu umożliwienia tworzenia rozwiązań IoT klasy korporacyjnej bez konieczności znajomości programowania rozwiązań w chmurze.

* [Pakiet IoT Azure](https://docs.microsoft.com/azure/iot-suite/) to zbiór [wstępnie skonfigurowanych rozwiązań](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions) klasy korporacyjnej opartych na ofercie platformy jako usługi (PaaS) dostępnej w ramach platformy Azure. Umożliwia on przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Azure IoT Hub to podstawowe rozwiązanie PaaS dostępne na platformie Azure. Korzystają z niego usługi Azure IoT Central i Azure IoT Suite. Usługa IoT Hub umożliwia bezpieczną i niezawodną dwukierunkową komunikację między milionami urządzeń IoT i rozwiązaniem w chmurze. Usługa IoT Hub ułatwia spełnienie wymagań dotyczących implementacji IoT, takich jak:

* Utrzymywanie łączności zapewniającej przepływ dużej ilości danych między urządzeniami i zarządzanie nimi.
* Pozyskiwanie dużej ilości danych telemetrycznych.
* Sterowanie urządzeniami i kontrola nad nimi.
* Wymuszanie zabezpieczeń urządzeń.

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Porównanie usług Azure IoT Central i Azure IoT Suite

Wybór produktu IoT Azure to decydujący etap planowania rozwiązania IoT. IoT Hub jest autonomiczną usługą platformy Azure, która sama nie zapewnia kompleksowego rozwiązania IoT. Usługa IoT Hub może służyć jako punkt początkowy dla dowolnego rozwiązania IoT. Aby z niej korzystać, nie trzeba używać pakietu Azure IoT Suite ani rozwiązania Azure IoT Central. Usługi IoT Suite i Azure IoT Central używają usługi IoT Hub razem z innymi usługami platformy Azure. W poniższej tabeli przedstawiono podsumowanie najważniejszych różnic między usługami IoT Suite i Azure IoT Central, aby ułatwić wybór najbardziej odpowiedniego rozwiązania:

|     | Azure IoT Central | Azure IoT Suite |
| --- | ----------- | --------- |
| Podstawowe zastosowanie                      | Skrócenie czasu wprowadzenia na rynek prostych rozwiązań IoT, które nie wymagają znaczącego dostosowania usług.                                                    | Przyspieszenie opracowania niestandardowych rozwiązań IoT wymagających maksymalnej elastyczności.                                                                                                                             |
| Dostęp do podstawowych usług PaaS | SaaS. W pełni zarządzane rozwiązanie, podstawowe usługi nie są widoczne.                                                                                            | Dostęp do podstawowych usług platformy Azure, możliwość zarządzania nimi i zastępowania ich w razie potrzeb.                                                                                                                    |
| Elastyczność                        | Średnia. Wbudowane, oparte na przeglądarce środowisko użytkownika umożliwia dostosowywanie modelu rozwiązania i aspektów interfejsu użytkownika. Infrastruktury nie można modyfikować, ponieważ poszczególne składniki nie są widoczne. | Wysoka. Kod mikrousług jest typu „open source”, można go modyfikować w dowolny sposób w celu dopasowania do własnych potrzeb. Ponadto można dostosować infrastrukturę wdrażania.                                               |
| Poziom umiejętności                        | Niski. Wymagane jest posiadanie umiejętności modelowania w celu dostosowania rozwiązania. Nie są wymagane umiejętności programowania.                                                                          | Średnio wysoki. Konieczna jest znajomość języka Java lub .NET, aby móc dostosować zaplecze rozwiązania. Konieczna jest znajomość języka JavaScript w celu dostosowania wizualizacji.                                                                       |
| Rozpoczynanie pracy             | Szablony aplikacji i urządzeń udostępniają wstępnie wbudowane modele. Można je wdrożyć w kilka minut.                                                                                                  | Wstępnie skonfigurowane rozwiązania implementują typowe scenariusze IoT. Można je wdrożyć w kilka minut.                                                                                                                            |
| Cennik                            | Prosta, przewidywalna struktura cen.                                                                                                                           | Koszty można kontrolować, dostosowując usługi.                                                                                                                                                            |

Podjęcie decyzji, którego produktu użyć w celu zbudowania rozwiązania IoT, zależy od następujących czynników:

* Wymagania biznesowe.
* Typ budowanego rozwiązania.
* Posiadany przez organizację zestaw umiejętności do tworzenia i obsługi rozwiązania w długim okresie.

## <a name="next-steps"></a>Następne kroki

W zależności od wybranego produktu i podejścia sugerowane dalsze czynności są następujące:

* **Azure IoT Central**: [rozwiązanie Azure IoT Central](overview-iot-central.md).
* **IoT Suite**: [Co to są wstępnie skonfigurowane rozwiązania usługi Azure IoT?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions)
* **IoT Hub**: [Omówienie usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).