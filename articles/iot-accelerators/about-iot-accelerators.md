---
title: Wprowadzenie do akceleratorów rozwiązań Azure IoT | Microsoft Docs
description: Informacje na temat akceleratorów rozwiązań Azure IoT. Akceleratory rozwiązań IoT to kompletne, kompleksowe oraz gotowe do wdrożenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/24/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 7020d8a1756702d8c2b1998eef5a3fc64809ca5e
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308116"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Co to są akceleratory rozwiązań Azure IoT?

Oparte na chmurze rozwiązanie IoT zazwyczaj używa niestandardowego kodu i wielu usług w chmurze do zarządzania łącznością urządzeń, przetwarzaniem i analizą danych oraz prezentacją.

Akceleratory rozwiązań IoT to kolekcja kompletnych, gotowych do wdrożenia rozwiązań IoT, które implementują typowe scenariusze IoT, takie jak zdalne monitorowanie, połączona fabryka, konserwacja predykcyjna i symulacja urządzenia. Podczas wdrażania akceleratora rozwiązania uwzględniane są wszystkie wymagane usługi w chmurze wraz z wszelkim wymaganym kodem aplikacji.

Akceleratory rozwiązań to punkty wyjścia do Twoich własnych rozwiązań IoT. Kod źródłowy wszystkich akceleratorów rozwiązań jest typu open source i jest dostępny w witrynie GitHub. Zachęcamy do pobrania i dostosowania akceleratorów rozwiązań do swoich potrzeb.

Akceleratory rozwiązań mogą również służyć do nauki przed utworzeniem własnego niestandardowego rozwiązania IoT od podstaw. W akceleratorach rozwiązań zaimplementowano sprawdzone praktyki dla opartych na chmurze rozwiązań IoT, do których należy się stosować.

Kod aplikacji każdego akceleratora rozwiązania obejmuje aplikację internetową, która umożliwia zarządzanie akceleratorem rozwiązania.

## <a name="supported-iot-scenarios"></a>Obsługiwane scenariusze IoT

Obecnie dostępne do wdrożenia są cztery akceleratory rozwiązań:

### <a name="remote-monitoring"></a>Zdalne monitorowanie

Ten akcelerator rozwiązania umożliwia zbieranie danych telemetrycznych z wielu urządzeń zdalnych i sterowanie nimi. Przykładowe urządzenia to systemy chłodzenia zamontowane w placówce klienta lub zawory w odległych przepompowniach.

Za pomocą pulpitu nawigacyjnego zdalnego monitorowania można wyświetlać dane telemetryczne z połączonych urządzeń, aprowizować nowe urządzenia lub uaktualniać oprogramowanie układowe połączonych urządzeń:

[![Wyświetlanie pulpitu nawigacyjnego rozwiązania do monitorowania zdalnego](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Połączona fabryka

Ten akcelerator rozwiązania umożliwia zbieranie danych telemetrycznych z zasobów przemysłowych korzystających z interfejsu [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) oraz sterowanie nimi. Zasoby przemysłowe mogą obejmować stanowiska montażu i testowania na fabrycznej linii produkcyjnej.

Pulpit nawigacyjny połączonej fabryki umożliwia monitorowanie urządzeń przemysłowych i zarządzanie nimi:

[![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Konserwacja predykcyjna

Ten akcelerator rozwiązania umożliwia przewidywanie, kiedy urządzenie zdalne ulegnie awarii, dzięki czemu można przeprowadzić konserwację przed oczekiwaną awarią. Akcelerator rozwiązania używa algorytmów uczenia maszynowego do przewidywania awarii na podstawie danych telemetrycznych z urządzenia. Przykładowe urządzenia to silniki lotnicze lub windy.

Za pomocą pulpitu nawigacyjnego konserwacji predykcyjnej można wyświetlać analizę konserwacji predykcyjnej:

[![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Symulacja urządzenia

Ten akcelerator rozwiązania umożliwia uruchamianie wielu symulowanych urządzeń, które generują realistyczne dane telemetryczne. Za pomocą tego akceleratora rozwiązania możesz przetestować zachowanie innych akceleratorów rozwiązań oraz własnych niestandardowych rozwiązań IoT.

Za pomocą aplikacji internetowej symulacji urządzenia można konfigurować i uruchamiać symulacje:

[![Pulpit nawigacyjny rozwiązania połączonej fabryki](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Zasady projektowania

Wszystkie akceleratory rozwiązań zaprojektowano zgodnie z tymi samymi zasadami i pod kątem tych samych celów. Zgodnie z projektem mają one być:

* **Skalowalne** — umożliwiać łączenie milionów połączonych urządzeń oraz zarządzać nimi.
* **Rozszerzalne** — dzięki czemu można je dostosowywać do własnych wymagań.
* **Zrozumiałe** — pozwalają poznać sposób, w jaki działają i jak są implementowane.
* **Modułowe** — pozwalają wymieniać usługi w celu skorzystania z alternatyw.
* **Bezpieczne** — łączą zabezpieczenia platformy Azure z wbudowanymi funkcjami łączności i zabezpieczeń urządzeń.

## <a name="architectures-and-languages"></a>Architektury i języki

Pierwotne akceleratory rozwiązań zostały napisane przy użyciu platformy .NET oraz architektury Model-View-Controller (MVC). Firma Microsoft aktualizuje akceleratory rozwiązań do nowej architektury mikrousług. W poniższej tabeli przedstawiono bieżący stan akceleratorów rozwiązań z linkami do repozytoriów GitHub:

| Akcelerator rozwiązania   | Architektura  | Języki     |
| ---------------------- | ------------- | ------------- |
| Zdalne monitorowanie      | Mikrousługi | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) i [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Konserwacja predykcyjna | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Połączona fabryka      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Symulacja urządzenia      | Mikrousługi | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="deployment-options"></a>Opcje wdrożenia

Akceleratory rozwiązań można wdrażać z witryny [akceleratorów rozwiązań usługi Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) lub przy użyciu wiersza polecenia.

Akcelerator rozwiązania monitorowania zdalnego można wdrażać w następujących konfiguracjach:

* **Standardowa:** rozwinięte wdrożenie infrastruktury na potrzeby opracowywania wdrożenia produkcyjnego. Mikrousługi są wdrażane na wielu maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.
* **Podstawowa:** tańsza wersja przeznaczona do celów demonstracyjnych i do testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Lokalna:** wdrożenie na maszynie lokalnej na potrzeby testowania i opracowywania. W tym podejściu mikrousługi są wdrażane w lokalnym kontenerze Docker, a także nawiązywane jest połączenie z usługą IoT Hub i Azure Cosmos DB oraz usługami Azure Storage w chmurze.

Koszt działania akceleratora rozwiązania jest agregacją [kosztu podstawowych usług platformy Azure](https://azure.microsoft.com/pricing). Szczegóły używanych usług platformy Azure są widoczne podczas wybierania opcji wdrażania.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować jeden z akceleratorów rozwiązań usługi IoT, zapoznaj się z przewodnikami Szybki start:

* [Testowanie rozwiązania do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md)
* [Testowanie rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Testowanie rozwiązania do konserwacji predykcyjnej](quickstart-predictive-maintenance-deploy.md)
* [Testowanie rozwiązania do symulacji urządzeń](quickstart-device-simulation-deploy.md)
