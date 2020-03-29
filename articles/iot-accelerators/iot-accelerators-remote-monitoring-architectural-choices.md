---
title: Opcje architektury rozwiązań do zdalnego monitorowania — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano wybory architektoniczne i techniczne dokonane w zakresie zdalnego monitorowania
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447191"
---
# <a name="remote-monitoring-architectural-choices"></a>Wybory dotyczące architektury związane z monitorowaniem zdalnym

Akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT to akcelerator rozwiązań typu open source licencjonowany przez MIT. Aby przyspieszyć proces tworzenia IoT, przedstawiono typowe scenariusze IoT, takie jak:

- Łączność urządzeń
- Zarządzanie urządzeniami
- Przetwarzanie strumienia

Rozwiązanie do zdalnego monitorowania jest zgodne z zalecaną [architekturą referencyjną usługi Azure IoT.](https://aka.ms/iotrefarchitecture)

W tym artykule opisano kluczowe wybory architektoniczne i techniczne dokonane w każdym z podsystemów zdalnego monitorowania. Jednak wybory techniczne dokonane przez firmę Microsoft w rozwiązaniu do zdalnego monitorowania nie są jedynym sposobem zaimplementowania rozwiązania IoT zdalnego monitorowania. Implementacja techniczna należy traktować jako punkt odniesienia dla tworzenia pomyślnej aplikacji i należy ją zmodyfikować w następujący sposób:

- Dopasuj dostępne umiejętności i doświadczenie w swojej organizacji.
- Spełnij twoje pionowe potrzeby aplikacji.

## <a name="architectural-choices"></a>Wybory dotyczące architektury

Architektura zalecana przez firmę Microsoft dla aplikacji IoT jest natywna dla chmury, mikrousługi i bezserwerowe. Należy utworzyć różne podsystemy aplikacji IoT jako dyskretne usługi, które można wdrożyć i skalować niezależnie. Te atrybuty umożliwiają większą skalę, większą elastyczność w aktualizowaniu poszczególnych podsystemów i zapewniają elastyczność wyboru odpowiedniej technologii dla każdego podsystemu.

Mikrousług można zaimplementować przy użyciu więcej niż jednej technologii. Na przykład można wybrać jedną z następujących opcji do zaimplementowania mikrousługi:

- Użyj technologii kontenera, takich jak Docker z technologii bezserwerowej, takich jak usługi Azure Functions.
- Hostuj swoje mikrousługi w usługach PaaS, takich jak usługi Azure App Services.

## <a name="technology-choices"></a>Wybór technologi

W tej sekcji opisano wybory technologiczne dokonane w rozwiązaniu do zdalnego monitorowania dla każdego z podstawowych podsystemów.

![Diagram rdzenia](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Brama w chmurze

Usługa Azure IoT Hub jest używana jako brama chmury rozwiązania do zdalnego monitorowania. [IoT Hub](https://azure.microsoft.com/services/iot-hub/) oferuje bezpieczną, dwukierunkową komunikację z urządzeniami.

W przypadku łączności z urządzeniem IoT można użyć:

- [SDK urządzenia usługi IoT Hub](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) do zaimplementowania natywnej aplikacji klienckiej dla urządzenia. Zestawy SDK oferują otoki wokół interfejsu API REST usługi IoT Hub i obsługują scenariusze, takie jak ponownych prób.
- Integracja z usługą Azure IoT Edge w celu wdrażania modułów niestandardowych działających w kontenerach na urządzeniach i zarządzania nimi.
- Integracja z automatycznym zarządzaniem urządzeniami w Uikw.

### <a name="stream-processing"></a>Przetwarzanie strumienia

Do przetwarzania strumienia rozwiązanie do zdalnego monitorowania używa usługi Azure Stream Analytics do przetwarzania reguł złożonych. Jeśli chcesz użyć prostszych reguł, istnieje niestandardowa mikrousługa z obsługą prostego przetwarzania reguł, chociaż ta konfiguracja nie jest częścią wdrożenia out-of-the-box. Architektura referencyjna zaleca usługi Azure Functions do prostego przetwarzania reguł i usługi Azure Stream Analytics do przetwarzania reguł złożonych.

### <a name="storage"></a>Magazyn

W przypadku magazynu akcelerator rozwiązań do zdalnego monitorowania używa zarówno usługi Azure Time Series Insights, jak i usługi Azure Cosmos DB. Usługa Azure Time Series Insights przechowuje komunikaty przychodzące za pośrednictwem usługi IoT Hub z podłączonych urządzeń. Akcelerator rozwiązań używa usługi Azure Cosmos DB dla wszystkich innych magazynów, takich jak magazyn w chłodni, definicje reguł, alerty i ustawienia konfiguracji.

Usługa Azure Cosmos DB to zalecane rozwiązanie ogólnego przeznaczenia do przechowywania danych dla aplikacji IoT. Jednak rozwiązania, takie jak usługa Azure Time Series Insights i usługa Azure Data Lake są odpowiednie dla wielu przypadków użycia. Dzięki usłudze Azure Time Series Insights możesz uzyskać lepszy wgląd w dane czujników szeregów czasowych, wykrywając trendy i anomalie. Ta funkcja umożliwia przeprowadzanie analiz przyczyn źródłowych i uniknięcie kosztownych przestojów.

> [!NOTE]
> Usługa Time Series Insights nie jest obecnie dostępna w chmurze platformy Azure w Chinach. Nowe wdrożenia akceleratora rozwiązań do zdalnego monitorowania w chmurze azure chiny używać usługi Cosmos DB dla wszystkich magazynu.

### <a name="business-integration"></a>Integracja biznesowa

Integracja biznesowa z rozwiązaniem do zdalnego monitorowania jest ograniczona do generowania alertów, które są umieszczane w ciepłej pamięci masowej. Połącz rozwiązanie z usługą Azure Logic Apps w celu zaimplementowania głębszych scenariuszy integracji biznesowej.

### <a name="user-interface"></a>Interfejs użytkownika

Interfejs użytkownika sieci Web jest zbudowany z JavaScript React. React oferuje powszechnie używane branżowe struktury interfejsu użytkownika sieci web i jest podobny do innych popularnych struktur, takich jak Angular.

### <a name="runtime-and-orchestration"></a>Środowisko wykonawcze i aranżacja

Rozwiązanie do zdalnego monitorowania używa kontenerów platformy Docker do uruchamiania podsystemów z umięsienia jako koordynatora skalowania poziomego. Ta architektura umożliwia indywidualne definicje skali dla każdego podsystemu. Jednak ta architektura ponosi koszty DevOps, aby zachować maszyny wirtualne i kontenery aktualne i bezpieczne.

Alternatywy dla platformy Docker obejmują hostowanie mikrousług w usługach PaaS, takich jak usługa Azure App Service. Alternatywy dla kubernetes obejmują orchestrators takich jak sieci szkieletowej usług, DC/OS lub Swarm.

## <a name="next-steps"></a>Następne kroki

* W tym [miejscu](https://www.azureiotsolutions.com/)należy wdrożyć rozwiązanie do zdalnego monitorowania .
* Poznaj kod GitHub w [języku C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) i [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Dowiedz się więcej o architekturze referencyjnej IoT [tutaj](https://aka.ms/iotrefarchitecture).
