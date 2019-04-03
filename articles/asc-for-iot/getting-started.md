---
title: Rozpoczęcie korzystania z usługi Azure Security Center (ASC) dla IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Wprowadzenie do zrozumienia podstawowy przepływ pracy usługi Azure Security Center dla funkcji IoT i usługi.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: aac15d766439a725f593ca421cbdc6da496f29f4
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862696"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Rozpocznij pracę z Centrum zabezpieczeń Azure dla IoT 

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera wyjaśnienie różne bloki konstrukcyjne usługi Azure Security Center (ASC) dla usługi IoT i wyjaśniono, jak rozpocząć pracę z [Włączanie usługi](quickstart-onboard-iot-hub.md). 

ASC IoT można bezproblemowo zintegrować usługi IoT Hub, aby przeprowadzać analizę zabezpieczeń konfiguracji centrum IoT, tożsamości urządzenia i urządzenie koncentratora wzorców komunikacyjnych.
W przypadku możliwości zwiększone zabezpieczenia ASC IoT zapewnia opartej o agentów zbierania danych zabezpieczeń z urządzeń IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC IoT bezproblemową integrację usługi IoT Hub

Podczas próby ochrony pojedynczych urządzeń IoT, umożliwia zbieranie danych bezpośrednio z urządzenia lub sieci jest wymagana. Aby obsługiwać ten nakład pracy, ASC IoT oferuje Arsenał agentów niskiego poziomu zabezpieczeń, aby zapewnić monitorowanie urządzenia i Ograniczanie funkcjonalności.

W ASC IoT (wersja zapoznawcza), architektura referencyjna dla systemów Linux i Windows agentów zabezpieczeń, zarówno w C# i są udostępniane przez C.
Agenci obsługi kolekcji nieprzetworzonych zdarzeń z systemu operacyjnego urządzenia, zdarzenie agregacji obniża koszty i konfiguracji za pomocą modułu bliźniaczej reprezentacji urządzenia.
Zabezpieczenia wiadomości są wysyłane za pośrednictwem usługi IoT Hub do usługi ASC IoT analytics usług.

## <a name="asc-for-iot-basics"></a>ASC, aby uzyskać podstawowe informacje dotyczące IoT

Wybierz scenariusz przepływu pracy, który najlepiej odpowiada wymaganiom urządzenia i środowiska IoT:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Rozpoczynanie pracy z usługą ASC dla IoT bezproblemową integrację usługi IoT Hub 

>[!Note]
>Ten przepływ pracy można korzystać z niej bez użycia usługi ASC dla agentów zabezpieczeń IoT. 

Aby włączyć monitorowanie urządzenia zarządzania tożsamościami oraz urządzenia do chmury i w chmurze i wzorców komunikacyjnych urządzenia, należy użyć następującego podstawowy przepływ pracy do testowania i uruchomić usługi: 

1. [Włącz ASC dla usługi IoT w usłudze IoT Hub](quickstart-onboard-iot-hub.md)
1. Jeśli Centrum IoT Hub nie ma żadnych zarejestrowanych urządzeń [zarejestrować nowe urządzenie](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Utwórz moduł azureiotsecurity zabezpieczeń dla urządzeń z](quickstart-create-security-twin.md) dla urządzeń. 
1. Zdefiniuj normalnego zachowania urządzenia i systemu za pośrednictwem [niestandardowe alerty](quickstart-create-custom-alerts.md). 
1. Wykonaj test, aby sprawdzić stan usługi i urządzenia systemu. 
1. Zapoznaj się z [alerty](concept-security-alerts.md), [zalecenia](concept-recommendations.md), i [szczegółowe dane przy użyciu usługi Log Analytics](how-to-security-data-access.md) za pomocą usługi IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Rozpoczynanie pracy z usługą ASC agentów zabezpieczeń IoT

Korzystaj z usługi ASC dla IoT, ulepszone funkcje zabezpieczeń, takich jak monitorowanie zdalne połączenia, aktywne aplikacje, zdarzenia logowania i najlepszymi rozwiązaniami konfiguracyjnymi systemu operacyjnego za pomocą następujących podstawowy przepływ pracy do testowania i włączyć usługę: 

1. [Włączanie usługi ASC do Centrum IoT Hub przy użyciu usługi IoT](quickstart-onboard-iot-hub.md)
1. Jeśli Centrum IoT Hub nie ma żadnych zarejestrowanych urządzeń [zarejestrować nowe urządzenie](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Tworzenie modułu zabezpieczeń usługi azureiotsecurity](quickstart-create-security-twin.md) dla urządzeń.
1. Aby zainstalować agenta na Azure symulowanego urządzenia zamiast instalować ją na rzeczywistych urządzeniach [pokrętła się nowej maszyny wirtualnej (maszyny Wirtualnej platformy Azure)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) w strefie dostępności. 
1. [Wdrażanie usługi ASC agenta zabezpieczeń IoT](how-to-deploy-linux-cs.md) na urządzeniu IoT, lub Nowa maszyna wirtualna.
1. Postępuj zgodnie z instrukcjami dotyczącymi [trigger_events](https://aka.ms/iot-security-github-trigger-events) uruchamianie symulacji ataku nieszkodliwe.
1. Sprawdź ASC IoT alerty w odpowiedzi na atak symulowane w poprzednim kroku. Rozpocznij weryfikację pięć minut po uruchomieniu skryptu.
1. Zapoznaj się z [alerty](concept-security-alerts.md), [zalecenia](concept-recommendations.md), i [szczegółowe dane przy użyciu usługi Log Analytics](how-to-security-data-access.md) za pomocą usługi IoT Hub. 

## <a name="next-steps"></a>Kolejne kroki

- Włącz [ASC dla IoT](quickstart-onboard-iot-hub.md)
- Konfigurowanie usługi [rozwiązania](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alerty niestandardowe](quickstart-create-custom-alerts.md)
- [Wdróż agenta zabezpieczeń](how-to-deploy-agent.md)
