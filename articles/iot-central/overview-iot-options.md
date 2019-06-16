---
title: Opcje usługi Microsoft Azure IoT | Microsoft Docs
description: 'Można wybrać, jak wdrożyć rozwiązanie Azure IoT: przy użyciu usługi Azure IoT Central, akceleratorów rozwiązania IoT lub usługi IoT Hub.'
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 19ec7afeb71f0e9d5602f1c4ba1a2456162cdfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077386"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Porównanie opcji usług Azure IoT Central i Azure IoT

Usługa Microsoft Azure IoT Central i rozwiązanie Azure IoT oferują kilka opcji umożliwiających tworzenie rozwiązań IoT. Te opcje są odpowiednie dla różnych zestawów wymagań klientów:

* [Azure IoT Central](overview-iot-central.md) to rozwiązanie typu „oprogramowanie jako usługa” (SaaS) korzystające z podejścia opartego na modelu w celu ułatwienia tworzenia rozwiązań IoT klasy korporacyjnej bez konieczności znajomości programowania rozwiązań w chmurze.

* [Akceleratory rozwiązań Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) to zbiór [akceleratorów rozwiązań](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) klasy korporacyjnej opartych na ofercie typu „platforma jako usługa” (PaaS) dostępnej w ramach platformy Azure. Ułatwiają one szybsze rozwijanie niestandardowych rozwiązań IoT.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Usługa Azure IoT Hub to podstawowe rozwiązanie PaaS dostępne w ramach platformy Azure, z którego korzysta zarówno rozwiązanie Azure IoT Central, jak i akceleratory rozwiązań usługi Azure IoT. Usługa IoT Hub obsługuje bezpieczną i niezawodną dwukierunkową komunikację między milionami urządzeń IoT i rozwiązaniem w chmurze. Usługa IoT Hub ułatwia spełnienie wymagań dotyczących implementacji IoT, takich jak:

* Utrzymywanie łączności zapewniającej przepływ dużej ilości danych między urządzeniami i zarządzanie nimi
* Pozyskiwanie dużej ilości danych telemetrycznych
* Sterowanie urządzeniami i kontrola nad nimi
* Wymuszanie zabezpieczeń urządzeń

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Porównanie akceleratorów rozwiązań usług Azure IoT i Azure IoT Central

Wybór produktu IoT Azure to decydujący etap planowania rozwiązania IoT. IoT Hub jest autonomiczną usługą platformy Azure, która sama nie zapewnia kompleksowego rozwiązania IoT. Usługa IoT Hub może służyć jako punktu wyjścia dla dowolnego rozwiązania IoT. Nie musisz również używać akceleratorów rozwiązania Azure IoT ani usługi Azure IoT Central, jeśli chcesz używać usługi IoT Hub. Zarówno akceleratory rozwiązań usługi IoT, jak i rozwiązanie Azure IoT Central używają usługi IoT Hub razem z innymi usługami platformy Azure.

W poniższej tabeli przedstawiono podsumowanie najważniejszych różnic między akceleratorami rozwiązań usługi IoT i rozwiązaniem Azure IoT Central, aby ułatwić wybór rozwiązania najbardziej odpowiedniego dla konkretnych wymagań:

|     | Azure IoT Central | Akceleratory rozwiązań Azure IoT |
| --- | ----------- | --------- |
| Podstawowe zastosowanie                      | Skrócenie czasu wprowadzenia na rynek prostych rozwiązań IoT, które nie wymagają znaczącego dostosowania usług.                                                    | Przyspieszenie opracowania niestandardowych rozwiązań IoT wymagających maksymalnej elastyczności.                                                                                                                             |
| Dostęp do podstawowych usług PaaS | SaaS. Ponieważ jest to w pełni zarządzane rozwiązanie, podstawowe usługi nie są widoczne.                                                                                            | Dostęp do podstawowych usług platformy Azure, możliwość zarządzania nimi i zastępowania ich w razie potrzeb.                                                                                                                    |
| Elastyczność                        | Średnia. Wbudowane, oparte na przeglądarce środowisko użytkownika umożliwia dostosowywanie modelu rozwiązania i aspektów interfejsu użytkownika. Infrastruktury nie można modyfikować, ponieważ poszczególne składniki nie są widoczne. | Wysoka. Kod mikrousług jest typu „open source” i można go modyfikować w dowolny sposób w celu dopasowania do własnych potrzeb. Ponadto można dostosować infrastrukturę wdrażania.                                               |
| Poziom umiejętności                        | Niski. Wymagane jest posiadanie umiejętności modelowania w celu dostosowania rozwiązania. Nie są wymagane umiejętności programowania.                                                                          | Średnio wysoki. Konieczna jest znajomość języka Java lub .NET, aby móc dostosować zaplecze rozwiązania. Konieczna jest znajomość języka JavaScript w celu dostosowania wizualizacji.                                                                       |
| Podstawowe umiejętności             | Szablony aplikacji i urządzeń udostępniają wstępnie wbudowane modele. Można je wdrożyć w kilka minut.                                                                                                  | Wstępnie skonfigurowane rozwiązania implementują typowe scenariusze IoT. Można je wdrożyć w kilka minut.                                                                                                                            |
| Cennik                            | Prosta, przewidywalna struktura cen.                                                                                                                           | Koszty można kontrolować, dostosowując usługi.                                                                                                                                                            |

To, jakiego produktu należy użyć do tworzenia swojego rozwiązania IoT, zależy od:

* Wymagania biznesowe.
* Typu budowanego rozwiązania.
* Posiadany przez organizację zestaw umiejętności do tworzenia i obsługi rozwiązania w długim okresie.

## <a name="next-steps"></a>Kolejne kroki

W zależności od wybranego produktu i podejścia sugerowane dalsze czynności są następujące:

* **Azure IoT Central**: [Co to jest rozwiązanie Azure IoT Central?](overview-iot-central.md)
* **Akceleratory rozwiązań IoT**: [Co to są akceleratory rozwiązania Azure IoT?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT Hub**: [Co to jest usługa Azure IoT Hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)