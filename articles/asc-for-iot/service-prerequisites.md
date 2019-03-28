---
title: ASC IoT wstępnie wymaganych składników (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Szczegółowe informacje dotyczące wszystkich elementów potrzebnych do Rozpoczynanie pracy z usługą ASC wymagania wstępne usługi IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 213e6a95484b5f6953f8423474953125f8739015
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541830"
---
# <a name="asc-for-iot-prerequisites"></a>ASC IoT wstępnie wymaganych składników

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten artykuł zawiera omówienie różnych bloki konstrukcyjne usługi ASC IoT usłudze, co jest potrzebne do rozpoczęcia i podstawowe pojęcia, aby lepiej zrozumieć usługę. 

## <a name="minimum-requirements"></a>Minimalne wymagania

- W warstwie standardowa Centrum IoT
    - Rola RBAC **właściciela** poziomami uprawnień 
- [Obszar roboczy usługi log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Usługa Azure Security Center (zalecane)
    - Podczas korzystania z usługi Azure Security Center to jedynie zalecenia i nie wymóg, bez tego nie można wyświetlić innych zasobów platformy Azure w ramach usługi IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Praca z usługą ASC dla usługi IoT

ASC IoT szczegółowe informacje i raporty są dostępne przy użyciu usługi Azure IoT Hub i Azure Security Center. Do włączenia usługi ASC dla Internetu rzeczy na usłudze Azure IoT Hub, konto z **właściciela** poziomu uprawnień jest wymagane. Po włączeniu usługi ASC dla IoT w usłudze IoT Hub ASC, aby uzyskać informacje na temat technologii IoT są wyświetlane jako **zabezpieczeń** funkcji w usłudze Azure IoT Hub oraz jako **IoT** w usłudze Azure Security Center. 

## <a name="supported-service-regions"></a>Usługi obsługiwane regiony 

ASC IoT jest obecnie obsługiwana dla centrów IoT w następujących regionach platformy Azure:
  - Środkowe stany USA
  - Europa Północna
  - Azja Południowo-Wschodnia

## <a name="wheres-my-iot-hub"></a>Gdzie jest Mój usługi IoT Hub?

Sprawdź swoją lokalizację centrum IoT Hub, aby sprawdzić dostępność usługi, przed przystąpieniem do wykonywania. 

1. Otwórz Centrum IoT Hub. 
2. Kliknij pozycję **Przegląd**. 
3. Sprawdź lokalizację na liście odpowiada jednej z [obsługiwane regiony usługi](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Platformy obsługiwane przez agentów 

ASC agentów IoT obsługuje rosnącą listę urządzeń i platform. Zobacz [obsługiwane listy platform](select-deploy-agent.md) do sprawdzenia istniejących lub planowanych biblioteki urządzeń.  

## <a name="next-steps"></a>Kolejne kroki
- [Omówienie](overview.md)
- [Włącz usługę](quickstart-onboard-iot-hub.md)
- [ASC IoT — często zadawane pytania](resources-frequently-asked-questions.md)
- [Opis usługi ASC alertów IoT](concept-security-alerts.md)
