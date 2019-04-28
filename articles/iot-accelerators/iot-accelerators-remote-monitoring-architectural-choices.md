---
title: Zdalne monitorowanie rozwiązań wyborów związanych z architekturą — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano architektury i technicznej wybory dokonane zdalnego monitorowania
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447191"
---
# <a name="remote-monitoring-architectural-choices"></a>Zdalne monitorowanie wyborów związanych z architekturą

Akcelerator rozwiązań Azure IoT zdalnego monitorowania jest typu open source, MIT licencji, akcelerator rozwiązań. Aby pomóc przyspieszyć procesie tworzenia aplikacji IoT, takich jak pokazuje obsługę typowych scenariuszy IoT:

- Łączność urządzeń
- Zarządzanie urządzeniami
- Przetwarzanie strumienia

Rozwiązanie monitorowania zdalnego następuje zalecanym [architektura referencyjna IoT platformy Azure](https://aka.ms/iotrefarchitecture).

W tym artykule opisano kluczowe architektury i technicznej wybory dokonane każdego podsystemu zdalne monitorowanie. Jednak możliwości techniczne Microsoft wprowadzonych w rozwiązaniu do zdalnego monitorowania nie są jedynym sposobem, aby zaimplementować rozwiązanie IoT monitorowania zdalnego. Implementacja techniczna powinno być traktowane jako podstawę do tworzenia zakończonym powodzeniem i należy zmodyfikować, aby:

- Dopasuj dostępnych umiejętności i doświadczenia w Twojej organizacji.
- Dostosowania do potrzeb aplikacji pionowy.

## <a name="architectural-choices"></a>Wybory dotyczące architektury

Architekturę, którą firma Microsoft zaleca, aby uzyskać aplikację IoT to chmura natywnego, mikrousługi, i bez użycia serwera na podstawie. Należy pamiętać, różnych podsystemów aplikacji IoT jako osobne usługi, które można wdrożyć i skalowanie niezależnie. Te atrybuty Włącz lepsze skalowanie i zapewnia większą elastyczność w aktualizacji poszczególnych podsystemów i zapewnia elastyczność wyboru odpowiedniej technologii dla każdego podsystemu.

Możesz zaimplementować mikrousługi korzystające z więcej niż jedna technologia. Na przykład można wybrać jedną z poniższych opcji, aby zaimplementować mikrousługi:

- Technologia kontenera, takich jak platforma Docker za pomocą technologii bezserwerowej, takich jak Azure Functions.
- Hostowanie mikrousługi na usługach PaaS, takich jak usługi Azure App Services.

## <a name="technology-choices"></a>Wybór technologi

W tej sekcji przedstawiono Wybór technologii wprowadzonych w rozwiązaniu do zdalnego monitorowania dla poszczególnych podsystemów core.

![Core Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Cloud Gateway

Usługa Azure IoT Hub jest używany jako brama chmury rozwiązanie monitorowania zdalnego. [Usługa IoT Hub](https://azure.microsoft.com/services/iot-hub/) oferuje bezpiecznej, dwukierunkowej komunikacji z urządzeniami.

Łączności między urządzeniami IoT można użyć:

- [Zestawy SDK urządzeń IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) implementacji natywnej aplikacji klienckiej dla Twojego urządzenia. Zestawy SDK oferują otok wokół interfejsu API REST Centrum IoT i obsługiwać scenariusze, takie jak ponownych prób.
- Integracja z usługą Azure IoT Edge, wdrażanie i zarządzanie nimi na Twoich urządzeniach działa w kontenerach moduły niestandardowe.
- Integracja zarządzania urządzeniami automatyczne w usłudze IoT Hub do zarządzania podłączone urządzenia w trybie zbiorczym.

### <a name="stream-processing"></a>Przetwarzanie strumienia

Do przetwarzania strumieniowego rozwiązania do zdalnego monitorowania korzysta z usługi Azure Stream Analytics do przetwarzania reguł złożonych. Jeśli chcesz użyć reguł prostsze występuje niestandardowe mikrousługach dzięki obsłudze przetwarzania prostej reguły, chociaż tej konfiguracji, które nie należą do wdrożenia poza pole. Architektura referencyjna zaleca usługi Azure Functions dla usługi Azure Stream Analytics i prosta reguła przetwarzania do przetwarzania reguł złożonych.

### <a name="storage"></a>Magazyn

W przypadku usługi storage akceleratora rozwiązania monitorowania zdalnego używa usługi Azure Time Series Insights i Azure Cosmos DB. Usługa Azure Time Series Insights są przechowywane komunikaty przechodzącego przez usługę IoT Hub z połączonych urządzeń. Akcelerator rozwiązań używa usługi Azure Cosmos DB na potrzeby wszystkie magazyny takie jak zimnego magazynu, definicje zasad, alertów i ustawień konfiguracji.

Usługa Azure Cosmos DB to rozwiązanie zalecane magazynu ciepłego ogólnego przeznaczenia dla aplikacji IoT. Jednak rozwiązań, takich jak Azure Time Series Insights i Azure Data Lake są odpowiednie dla wielu przypadków użycia. Za pomocą usługi Azure Time Series Insights możesz uzyskać lepszy wgląd w dane szeregów czasowych, czujników, wykrywając trendów i anomalii. Ta funkcja umożliwia przeprowadzanie analiz głównych przyczyn i unikanie kosztownych przestojów.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze Azure (Chiny). Nowych wdrożeń akceleratora zdalne monitorowanie rozwiązań w chmurze Azure (Chiny) używać usługi Cosmos DB do przechowywania wszystkich.

### <a name="business-integration"></a>Integracja biznesowa

Integracja biznesowa w rozwiązaniu do zdalnego monitorowania jest ograniczona do generowania alertów, które są umieszczane w magazynie bez wyłączania zasilania. Połącz rozwiązania z usługą Azure Logic Apps, aby zaimplementować bardziej scenariusze integracji.

### <a name="user-interface"></a>Interfejs użytkownika

Internetowy interfejs użytkownika jest tworzony przy użyciu języka JavaScript React. React to platforma interfejsu użytkownika sieci web branży powszechnie używane i jest podobne do innych popularnych platform, takich jak Angular.

### <a name="runtime-and-orchestration"></a>Środowisko uruchomieniowe i aranżacji

Rozwiązania do zdalnego monitorowania używa kontenerów platformy Docker, aby uruchamiać podsystemów przy użyciu rozwiązania Kubernetes jako orkiestrator dla skalowania w poziomie. Ta architektura umożliwia skalowanie poszczególnych definicji dla każdego podsystemu. Jednak w tej architekturze naliczone koszty metodyki DevOps, aby zapewnić maszyny wirtualne i kontenery, aktualizacje i bezpieczeństwo.

Alternatywy dla platformy Docker obejmują hostingu mikrousług na usługach PaaS, takich jak usługa Azure App Service. Alternatywy dla rozwiązania Kubernetes obejmują koordynatorów, takich jak Usługa Service Fabric, DC/OS lub Swarm.

## <a name="next-steps"></a>Kolejne kroki

* Wdrażanie rozwiązania do zdalnego monitorowania [tutaj](https://www.azureiotsolutions.com/).
* Zapoznaj się z kodu GitHub w [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Dowiedz się więcej o architektura referencyjna IoT [tutaj](https://aka.ms/iotrefarchitecture).
