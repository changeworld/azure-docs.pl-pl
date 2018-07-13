---
title: Wprowadzenie do akceleratorów rozwiązań Azure IoT | Microsoft Docs
description: Informacje na temat akceleratorów rozwiązań Azure IoT. Akceleratory rozwiązań IoT to kompletne, kompleksowe oraz gotowe do wdrożenia rozwiązania IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 2a4f0b035ce80809a678731a50921791fc0db928
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097992"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Co to są akceleratory rozwiązań Azure IoT?

Oparte na chmurze rozwiązanie IoT zazwyczaj używa niestandardowego kodu i wielu usług w chmurze do zarządzania łącznością urządzeń, przetwarzaniem i analizą danych oraz prezentacją.

Akceleratory rozwiązań IoT to kolekcja kompletnych, gotowych do wdrożenia rozwiązań IoT, które implementują typowe scenariusze IoT, takie jak zdalne monitorowanie, połączona fabryka i konserwacja predykcyjna. Podczas wdrażania akceleratora rozwiązania uwzględniane są wszystkie wymagane usługi w chmurze wraz z wszelkim wymaganym kodem aplikacji.

Akceleratory rozwiązań to punkty wyjścia do Twoich własnych rozwiązań IoT. Kod źródłowy wszystkich akceleratorów rozwiązań jest typu open source i jest dostępny w witrynie GitHub. Zachęcamy do pobrania i [dostosowania](iot-accelerators-remote-monitoring-customize.md) akceleratorów rozwiązań do swoich potrzeb.

Akceleratory rozwiązań mogą również służyć do nauki przed utworzeniem własnego niestandardowego rozwiązania IoT od podstaw. W akceleratorach rozwiązań zaimplementowano sprawdzone praktyki dla opartych na chmurze rozwiązań IoT, do których należy się stosować.

Kod aplikacji każdego akceleratora rozwiązania obejmuje pulpit nawigacyjny, który umożliwia zarządzanie akceleratorem rozwiązania. Za pomocą pulpitu nawigacyjnego można na przykład wyświetlać dane telemetryczne z połączonych urządzeń, aprowizować nowe urządzenia lub uaktualniać oprogramowanie układowe połączonych urządzeń:

[![Pulpit nawigacyjny rozwiązania](./media/about-iot-accelerators/dashboard-inline.png)](./media/about-iot-accelerators/dashboard-expanded.png#lightbox)

## <a name="supported-iot-scenarios"></a>Obsługiwane scenariusze IoT

Obecnie dostępne do wdrożenia są cztery akceleratory rozwiązań:

### <a name="remote-monitoring"></a>Zdalne monitorowanie

Ten akcelerator rozwiązania umożliwia zbieranie danych telemetrycznych z wielu urządzeń zdalnych i sterowanie nimi. Przykładowe urządzenia to systemy chłodzenia zamontowane w placówce klienta lub zawory w odległych przepompowniach.

### <a name="connected-factory"></a>Połączona fabryka

Ten akcelerator rozwiązania umożliwia zbieranie danych telemetrycznych z zasobów przemysłowych korzystających z interfejsu [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) oraz sterowanie nimi. Zasoby przemysłowe mogą obejmować stanowiska montażu i testowania na fabrycznej linii produkcyjnej.

### <a name="predictive-maintenance"></a>Konserwacja predykcyjna

Ten akcelerator rozwiązania umożliwia przewidywanie, kiedy urządzenie zdalne ulegnie awarii, dzięki czemu można przeprowadzić konserwację przed oczekiwaną awarią. Akcelerator rozwiązania używa algorytmów uczenia maszynowego do przewidywania awarii na podstawie danych telemetrycznych z urządzenia. Przykładowe urządzenia to silniki lotnicze lub windy.

### <a name="device-simulation"></a>Symulacja urządzenia

Ten akcelerator rozwiązania umożliwia uruchamianie wielu symulowanych urządzeń, które generują realistyczne dane telemetryczne. Za pomocą tego akceleratora rozwiązania możesz przetestować zachowanie innych akceleratorów rozwiązań oraz własnych niestandardowych rozwiązań IoT.

## <a name="design-principles"></a>Zasady projektowania

Wszystkie akceleratory rozwiązań zaprojektowano zgodnie z tymi samymi zasadami i pod kątem tych samych celów. Zgodnie z projektem mają one być:

* **Skalowalne** — umożliwiać łączenie milionów połączonych urządzeń oraz zarządzać nimi.
* **Rozszerzalne** — dzięki czemu można je dostosowywać do własnych wymagań.
* **Zrozumiałe** — pozwalają poznać sposób, w jaki działają i jak są implementowane.
* **Modułowe** — pozwalają wymieniać usługi w celu skorzystania z alternatyw.
* **Bezpieczne** — łączą zabezpieczenia platformy Azure z wbudowanymi funkcjami łączności i zabezpieczeń urządzeń.

## <a name="architectures-and-languages"></a>Architektury i języki

Pierwotne akceleratory rozwiązań zostały napisane przy użyciu platformy .NET oraz architektury Model-View-Controller (MVC). Firma Microsoft aktualizuje akceleratory rozwiązań do nowej architektury mikrousług. W witrynie GitHub są dostępne wersje każdej mikrousługi zarówno w języku [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java), jak i [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet). W poniższej tabeli przedstawiono bieżący stan akceleratorów rozwiązań:

| Akcelerator rozwiązania   | Architektura  | Języki     |
| ---------------------- | ------------- | ------------- |
| Zdalne monitorowanie      | Mikrousługi | Java i .NET |
| Konserwacja predykcyjna | MVC           | .NET          |
| Połączona fabryka      | MVC           | .NET          |

Aby dowiedzieć się więcej na temat architektury mikrousług, zobacz [Architektura aplikacji .NET](https://www.microsoft.com/net/learn/architecture) i [Mikrousługi: rewolucja w aplikacjach wspierana przez chmurę](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="deployment-options"></a>Opcje wdrożenia

Akceleratory rozwiązań oparte na mikrousługach możesz wdrażać w następujących konfiguracjach:

* **Standardowa:** rozwinięte wdrożenie infrastruktury na potrzeby opracowywania wdrożenia produkcyjnego. Mikrousługi są wdrażane na wielu maszynach wirtualnych platformy Azure za pomocą usługi Azure Container Service. Platforma Kubernetes zarządza kontenerami aparatu Docker, w których są hostowane poszczególne mikrousługi.
* **Podstawowa:** tańsza wersja przeznaczona do celów demonstracyjnych i do testowania wdrożenia. Wszystkie mikrousługi są wdrażane na jednej maszynie wirtualnej platformy Azure.
* **Lokalna:** wdrożenie na maszynie lokalnej na potrzeby testowania i opracowywania. W tym podejściu mikrousługi są wdrażane w lokalnym kontenerze Docker, a także nawiązywane jest połączenie z usługą IoT Hub i Azure Cosmos DB oraz usługami Azure Storage w chmurze.

Koszt działania akceleratora rozwiązania jest agregacją [kosztu podstawowych usług platformy Azure](https://azure.microsoft.com/pricing). Szczegóły używanych usług platformy Azure są widoczne podczas wybierania opcji wdrażania.

## <a name="next-steps"></a>Następne kroki

Aby wypróbować akcelerator rozwiązania IoT, zapoznaj się z przewodnikiem Szybki start [Wdrażanie opartego na chmurze rozwiązania do zdalnego monitorowania](quickstart-remote-monitoring-deploy.md).
