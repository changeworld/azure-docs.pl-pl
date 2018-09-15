---
title: Zdalne monitorowanie rozwiązań wyborów związanych z architekturą — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano architektury i technicznej wybory dokonane zdalnego monitorowania
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 09c5981701ffdee5f2e5dba47cc98c91d5df7526
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603910"
---
# <a name="remote-monitoring-architectural-choices"></a>Zdalne monitorowanie wyborów związanych z architekturą

Akcelerator rozwiązań Azure IoT zdalnego monitorowania jest typu open source, licencji MIT, akcelerator rozwiązań, które wprowadza typowych scenariuszy IoT, takich jak łączność urządzeń, zarządzanie urządzeniami i przetwarzania strumienia, dzięki czemu klienci mogą przyspieszyć ich rozwoju proces.  Rozwiązanie monitorowania zdalnego następuje zalecana architektura referencyjna IoT platformy Azure, opublikowane [tutaj](https://aka.ms/iotrefarchitecture).  

W tym artykule opisano opcje architektury i technicznej wprowadzone w poszczególnych podsystemów rozwiązania do monitorowania zdalnego i omówiono rozważane alternatywy.  Należy zauważyć, że techniczne wybory dokonane w rozwiązania do zdalnego monitorowania nie są jedynym sposobem, aby zaimplementować rozwiązanie IoT monitorowania zdalnego.  Implementacja techniczna jest punkt odniesienia dla tworzenia zakończonym powodzeniem i powinny być modyfikowane w celu dopasowania umiejętności, doświadczenie i pionowy aplikacji informacji dotyczących wdrażania rozwiązania klienta.

## <a name="architectural-choices"></a>Wybory dotyczące architektury

### <a name="microservices-serverless-and-cloud-native"></a>Mikrousługi, bez użycia serwera, a natywna usługa w chmurze

Architektura zaleca się dla aplikacji IoT są chmury natywnego, mikrousługi, i bez użycia serwera na podstawie.  Różne podsystemy aplikacji IoT, powinny zostać skompilowane jako osobne usługi, które są niezależnie do wdrożenia i można skalować niezależnie.  Te atrybuty Włącz lepsze skalowanie i zapewnia większą elastyczność w aktualizacji poszczególnych podsystemów i zapewnia elastyczność wyboru odpowiedniej technologii na podstawie poszczególnych podsystemu.  Mikrousługi można zaimplementować przy użyciu wielu technologii. Na przykład przy użyciu technologii kontenerów, takich jak platforma Docker, za pomocą technologii bezserwerowej, takich jak Azure Functions, ani nie hostują mikrousług na usługach PaaS, takich jak usługi Azure App Services.

## <a name="core-subsystem-technology-choices"></a>Wybór technologi podsystemu Core

W tej sekcji przedstawiono Wybór technologii wprowadzonych w rozwiązaniu do zdalnego monitorowania dla poszczególnych podsystemów core.

![Core Diagram](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Brama chmury
Usługi Azure IoT Hub jest używany jako brama chmury rozwiązanie monitorowania zdalnego.  Usługi IoT Hub udostępnia bezpiecznej, dwukierunkowej komunikacji z urządzeniami. Dowiedz się więcej o usłudze IoT Hub [tutaj](https://azure.microsoft.com/services/iot-hub/). Łączność urządzeń IoT .NET Core i Java IoT Hub SDKs są używane.  Zestawy SDK oferują otok wokół interfejsu API REST Centrum IoT i obsługiwać scenariusze, takie jak ponownych prób.

### <a name="stream-processing"></a>Przetwarzanie strumienia
Dla strumienia przetwarzania rozwiązania do zdalnego monitorowania korzysta z usługi Azure Stream Analytics do przetwarzania reguł złożonych.  Dla klientów chcących reguły prostsze mamy też niestandardowe mikrousług z pomocą techniczną w celu przetwarzania proste zasady, chociaż tej konfiguracji nie jest częścią poza wdrożenia pole. Architektura referencyjna zaleca się użycie usługi Azure Functions dla usługi Azure Stream Analytics (ASA) i prosta reguła przetwarzania przetwarzanie złożonych reguł.  

### <a name="storage"></a>Magazyn
W przypadku usługi storage akceleratora rozwiązania monitorowania zdalnego używa usługi Azure Time Series Insights i Azure Cosmos DB. Usługa Azure Time Series Insights są przechowywane komunikaty przechodzącego przez usługę IoT Hub z połączonych urządzeń. Akcelerator rozwiązań używa usługi Azure Cosmos DB na potrzeby wszystkie magazyny takie jak zimnego magazynu, definicje zasad, alarmy i ustawień konfiguracji. Usługa Azure Cosmos DB jest rozwiązanie zalecane magazynu ciepłego ogólnego przeznaczenia dla aplikacji IoT, chociaż rozwiązań, takich jak Azure Time Series Insights i Azure Data Lake są odpowiednie dla wielu przypadków użycia. Za pomocą usługi Azure Time Series Insights można uzyskać lepszy wgląd w dane szeregów czasowych czujników, wykrywania trendów i anomalii, co pozwala na przeprowadzanie analiz głównych przyczyn i unikanie kosztownych przestojów. 

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze Azure (Chiny). Nowych wdrożeń akceleratora zdalne monitorowanie rozwiązań w chmurze Azure (Chiny) używać usługi Cosmos DB do przechowywania wszystkich.

### <a name="business-integration"></a>Integracja biznesowa
Integracja biznesowa w rozwiązaniu do zdalnego monitorowania jest ograniczona do generowania alarmy, które są umieszczane w magazynie bez wyłączania zasilania. Dodatkowo można wykonać integracji firmy dzięki integracji rozwiązania z usługą Azure Logic Apps.

### <a name="user-interface"></a>Interfejs użytkownika
Internetowy interfejs użytkownika jest tworzony przy użyciu języka JavaScript React.  React to platforma interfejsu użytkownika sieci web branży powszechnie używane i jest podobne do innych popularnych platform, takich jak Angular.  

### <a name="runtime-and-orchestration"></a>Środowisko uruchomieniowe i aranżacji
Środowisko uruchomieniowe aplikacji wybrane dla implementacji podsystemu w rozwiązaniu do zdalnego monitorowania jest kontenerów platformy Docker przy użyciu rozwiązania Kubernetes jako orkiestrator dla skalowania w poziomie.  Ta architektura umożliwia skalowanie poszczególnych definicji na podsystemie jednak wiąże się koszty operacji deweloperskich w aktualizowanie maszyny wirtualne i kontenery z punktu widzenia zabezpieczeń.  Alternatywy dla platformy Docker i Kubernetes obejmują hostingu mikrousług na usługach PaaS (na przykład usługi Azure App Service) lub za pomocą usługi Service Fabric, DCOS, Swarm itd. jako orkiestrator.

## <a name="next-steps"></a>Kolejne kroki
* Wdrażanie rozwiązania do zdalnego monitorowania [tutaj](https://www.azureiotsolutions.com/).
* Zapoznaj się z kodu GitHub w [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Dowiedz się więcej o architektura referencyjna IoT [tutaj](https://aka.ms/iotrefarchitecture).
