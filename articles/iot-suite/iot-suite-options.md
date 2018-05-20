---
title: Opcje usługi Microsoft Azure IoT | Microsoft Docs
description: 'Wybierz sposób implementowania rozwiązania IoT: przy użyciu akceleratorów rozwiązań usługi Azure IoT, rozwiązania Azure IoT Central lub usługi Azure IoT Hub.'
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0d6fc2a49034b2e01a0397fe256a2b44030777b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="compare-azure-iot-options"></a>Porównanie opcji rozwiązania Azure IoT

W artykule [Platforma Azure i Internet rzeczy](iot-suite-what-is-azure-iot.md) opisano typową architekturę rozwiązania IoT mającą następujące warstwy:

* Łączność urządzeń i zarządzanie
* Przetwarzanie danych i analiza
* Prezentacja i integracja biznesowa

Aby umożliwić zaimplementowanie tej architektury, rozwiązanie Azure IoT oferuje kilka opcji dopasowanych do różnych zestawów wymagań klientów:

* [Akceleratory rozwiązań Azure IoT](index.md) to zbiór [akceleratorów rozwiązań](iot-suite-what-are-solution-accelerators.md) klasy korporacyjnej opartych na ofercie platformy jako usługi (PaaS) dostępnej w ramach platformy Azure. Umożliwiają one przyspieszenie rozwoju niestandardowych rozwiązań IoT.

* [Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) to rozwiązanie typu oprogramowanie jako usługa (SaaS) korzystające z podejścia opartego na modelu w celu umożliwienia tworzenia rozwiązań IoT klasy korporacyjnej bez konieczności znajomości programowania rozwiązań w chmurze.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Usługa Azure IoT Hub to podstawowe rozwiązanie PaaS dostępne w ramach platformy Azure, z którego korzysta zarówno rozwiązanie Azure IoT Central, jak i akceleratory rozwiązań usługi Azure IoT. Usługa IoT Hub umożliwia bezpieczną i niezawodną dwukierunkową komunikację między milionami urządzeń IoT i rozwiązaniem w chmurze. Usługa IoT Hub ułatwia spełnienie wymagań dotyczących implementacji IoT, takich jak:

* Utrzymywanie łączności zapewniającej przepływ dużej ilości danych między urządzeniami i zarządzanie nimi.
* Pozyskiwanie dużej ilości danych telemetrycznych.
* Sterowanie urządzeniami i kontrola nad nimi.
* Wymuszanie zabezpieczeń urządzeń.

## <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>Porównanie akceleratorów rozwiązań usługi Azure IoT i rozwiązania Azure IoT Central

Wybór produktu IoT Azure to decydujący etap planowania rozwiązania IoT. IoT Hub jest autonomiczną usługą platformy Azure, która sama nie zapewnia kompleksowego rozwiązania IoT. Usługa IoT Hub może służyć jako punkt początkowy dla dowolnego rozwiązania IoT. Aby z niej korzystać, nie trzeba używać akceleratorów rozwiązań usługi Azure IoT ani rozwiązania Azure IoT Central. Zarówno akceleratory rozwiązań usługi Azure IoT, jak i rozwiązanie Azure IoT Central używają usługi IoT Hub razem z innymi usługami platformy Azure. W poniższej tabeli przedstawiono podsumowanie najważniejszych różnic między akceleratorami rozwiązań usługi Azure IoT i rozwiązaniem Azure IoT Central, aby ułatwić wybór rozwiązania najbardziej odpowiedniego dla konkretnych wymagań:

|                        | Akceleratory rozwiązań Azure IoT | Azure IoT Central |
| ---------------------- | --------- | ----------- |
| Podstawowe zastosowanie | Przyspieszenie opracowania niestandardowych rozwiązań IoT wymagających maksymalnej elastyczności. | Skrócenie czasu wprowadzenia na rynek prostych rozwiązań IoT, które nie wymagają znaczącego dostosowania usług. |
| Dostęp do podstawowych usług PaaS          | Dostęp do podstawowych usług platformy Azure, możliwość zarządzania nimi i zastępowania ich w razie potrzeb. | SaaS. W pełni zarządzane rozwiązanie, podstawowe usługi nie są widoczne. |
| Elastyczność            | Wysoka. Kod mikrousług jest typu „open source”, można go modyfikować w dowolny sposób w celu dopasowania do własnych potrzeb. Ponadto można dostosować infrastrukturę wdrażania.| Średnia. Wbudowane, oparte na przeglądarce środowisko użytkownika umożliwia dostosowywanie modelu rozwiązania i aspektów interfejsu użytkownika. Infrastruktury nie można modyfikować, ponieważ poszczególne składniki nie są widoczne.|
| Poziom umiejętności                 | Średnio wysoki. Konieczna jest znajomość języka Java lub .NET, aby móc dostosować zaplecze rozwiązania. Konieczna jest znajomość języka JavaScript w celu dostosowania wizualizacji. | Niski. Wymagane jest posiadanie umiejętności modelowania w celu dostosowania rozwiązania. Nie są wymagane umiejętności programowania. |
| Rozpoczynanie pracy | Akceleratory rozwiązań implementują typowe scenariusze IoT. Można je wdrożyć w kilka minut. | Szablony aplikacji i urządzeń udostępniają wstępnie wbudowane modele. Można je wdrożyć w kilka minut. |
| Cennik                | Koszty można kontrolować, dostosowując usługi. | Prosta, przewidywalna struktura cen. |

Podjęcie decyzji, którego produktu użyć w celu zbudowania rozwiązania IoT, zależy od następujących czynników:

* Wymagania biznesowe.
* Typ budowanego rozwiązania.
* Posiadany przez organizację zestaw umiejętności do tworzenia i obsługi rozwiązania w długim okresie.

## <a name="next-steps"></a>Następne kroki

W zależności od wybranego produktu i podejścia sugerowane dalsze czynności są następujące:

* **Akceleratory rozwiązań Azure IoT**: [Co to są akceleratory rozwiązań Azure IoT?](iot-suite-what-are-solution-accelerators.md)
* **Azure IoT Central**: [rozwiązanie Azure IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub**: [Omówienie usługi Azure IoT Hub](../iot-hub/iot-hub-what-is-iot-hub.md).
