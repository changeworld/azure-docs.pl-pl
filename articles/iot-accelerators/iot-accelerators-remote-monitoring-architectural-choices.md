---
title: Zdalne monitorowanie rozwiązania architektury opcji - Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano architektury i technicznej wybory dokonane monitorowania zdalnego
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/30/2018
ms.topic: conceptual
ms.openlocfilehash: 6c4bf0e4bf0a6c1a791cf762ec9bb44ed5c0b1bd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627692"
---
# <a name="remote-monitoring-architectural-choices"></a>Zdalne monitorowanie architektury wyborów.

Akcelerator rozwiązań zdalnego monitorowania Azure IoT jest open source, licencją MIT, akcelerator rozwiązań, przedstawiający typowych scenariuszy IoT, takich jak łączność urządzeń, zarządzanie urządzeniami i przetwarzania strumienia, więc klienci można przyspieszyć ich rozwoju proces.  To rozwiązanie monitorowanie zdalnego następuje zalecane architektura referencyjna Azure IoT opublikowane [tutaj](https://aka.ms/iotrefarchitecture).  

W tym artykule opisano architektury i technicznej wyborów dokonanych w każdym podsystemów dla rozwiązania Monitorowanie zdalne i omówiono traktowane jako alternatywy.  Należy pamiętać, że techniczne wyborów dokonanych w rozwiązaniu Monitorowanie zdalne nie są jedynym sposobem wykonania zdalnego monitorowania rozwiązania IoT.  Implementacja techniczna jest linii bazowej do tworzenia aplikacji powiodło się i powinien zostać zmodyfikowany do umiejętności, obsługi i potrzeb pionowy aplikacji do wdrożenia rozwiązania klienta.

## <a name="architectural-choices"></a>Opcje architektury

### <a name="microservices-serverless-and-cloud-native"></a>Mikrousług niekorzystającą i w chmurze natywnego

Architektura zalecamy IoT aplikacje są chmury natywnego, mikrousługi, a pliki na podstawie.  Różne podsystemy aplikacji IoT powinny zostać skompilowane jako odrębny usług, które są możliwe niezależnie i można skalować niezależnie.  Te atrybuty Włącz większa Skala większą elastyczność podczas aktualizowania poszczególnych podsystemów i zapewniają elastyczność wyboru odpowiednich technologii na podstawie na podsystemu.  Mikrousług można stosować wiele technologii. Na przykład przy użyciu technologii, kontenera Docker niekorzystającą technologii, takich jak usługi Azure Functions lub hosting mikrousług w usługach PaaS, takie jak usługi aplikacji Azure.

## <a name="core-subsystem-technology-choices"></a>Wybór technologii podsystemu Core

W tej sekcji szczegółowo technologii wybory dokonane rozwiązanie monitorowania zdalnego dla każdego podsystemów core.

![Core Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Brama chmury
Centrum IoT Azure jest używany jako brama chmury rozwiązanie monitorowania zdalnego.  Centrum IoT zapewnia bezpieczny, dwukierunkową komunikację z urządzeniami. Dowiedz się więcej na temat Centrum IoT [tutaj](https://azure.microsoft.com/services/iot-hub/). Łączność urządzenia IoT są używane oprogramowanie .NET Core i zestawy SDK Centrum IoT Java.  Zestawy SDK oferować otoki wokół interfejsu API REST Centrum IoT i obsługi scenariuszy, takich jak ponownych prób.

### <a name="stream-processing"></a>Przetwarzanie strumienia
Dla strumienia przetwarzania rozwiązanie monitorowania zdalnego używa usługi Azure Stream Analytics przetwarzanie złożonych reguł.  Klienci pożądane reguły prostszy mamy także niestandardowych mikrousługi z obsługą przetwarzanie reguł proste, chociaż ta konfiguracja nie jest częścią poza wdrożenia pole. Architektura referencyjna zaleca się korzystanie z usługi Azure Functions dla usługi Azure Stream Analytics (ASA) i przetwarzania Prosta reguła przetwarzania złożonych reguł.  

### <a name="storage"></a>Magazyn
Dla magazynu bazy danych Azure rozwiązania Cosmos jest używany dla wszystkich potrzeb dotyczących magazynowania: chłodni, magazynu ciepłego magazynu zasad i alarmy. Trwa obecnie przeniesienie do magazynu obiektów blob platformy Azure, zgodnie z zaleceniami architektura referencyjna struktury.  Azure DB rozwiązania Cosmos jest rozwiązanie zalecane magazynu ciepłego ogólnego przeznaczenia aplikacji IoT, chociaż rozwiązań, takich jak Azure Insights serie czasu i usługi Azure Data Lake są odpowiednie dla wielu zastosowań.

### <a name="business-integration"></a>Integracja biznesowa
Integracja biznesowych w rozwiązaniu monitorowania zdalnego jest ograniczona do generowania alarmów, które są umieszczane w magazynie ciepłych. Więcej firm integracji można wykonać dzięki zintegrowaniu rozwiązania w usłudze Azure Logic Apps.

### <a name="user-interface"></a>Interfejs użytkownika
Witryna sieci web interfejsu użytkownika został skompilowany za JavaScript React.  Platformy react zawiera często używane branży Platforma interfejsu użytkownika sieci web i jest podobny do innych popularnych platform, takich jak kątową.  

### <a name="runtime-and-orchestration"></a>Środowisko wykonawcze i aranżacji
Środowisko uruchomieniowe aplikacji wybrane dla podsystemu implementacja rozwiązanie monitorowania zdalnego jest kontenery Docker z Kubernetes orchestrator do skalowania w poziomie.  Taka architektura umożliwia dla definicji poszczególnych skalowania dla podsystemu wiąże się jednak DevOps kosztów w aktualizowanie maszyn wirtualnych i kontenery z punktu widzenia zabezpieczeń.  Alternatywy dla Docker i Kubernetes obejmują hosting mikrousług w usługach PaaS (na przykład usługi Azure App Service) lub przy użyciu usługi Service Fabric, DCOS, Swarm, itd. jako orchestrator.

## <a name="next-steps"></a>Kolejne kroki
* Wdrażanie rozwiązania do monitorowania zdalnego [tutaj](https://www.azureiotsolutions.com/).
* Eksploruj kod GitHub w [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Dowiedz się więcej o architektura referencyjna IoT [tutaj](https://aka.ms/iotrefarchitecture).
