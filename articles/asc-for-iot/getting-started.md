---
title: Wprowadzenie do korzystania z Azure Security Center dla usługi IoT | Microsoft Docs
description: Zacznij od zapoznania się z podstawowym przepływem pracy Azure Security Center dla funkcji i usług IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 86c6c0bac5caae0873d0067c6abcb5a8ac864c88
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596420"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>Wprowadzenie do usługi Azure Security Center dla IoT

Ten artykuł zawiera objaśnienie różnych bloków konstrukcyjnych Azure Security Center usługi IoT i wyjaśnia, jak rozpocząć pracę z usługą przy użyciu dwóch możliwych opcji wdrażania.  

## <a name="deployment-options"></a>Opcje wdrożenia

Wybierz scenariusz usługi, który najlepiej spełnia wymagania dotyczące urządzenia IoT i środowiska. 

### <a name="built-in-deployment"></a>Wbudowane wdrożenie
Korzystając z płynnej, wbudowanej opcji wdrażania, Azure Security Center dla usługi IoT można szybko zintegrować z IoT Hub i zapewniać analizę zabezpieczeń konfiguracji usługi IoT Hub, tożsamości i zarządzania urządzeniami oraz wzorców komunikacji urządzeń centralnych.

Rozpocznij [wdrożenie wbudowane](iot-hub-integration.md) , które ma IoT Hub monitorowanie i zalecenia. 
    <br>

### <a name="enhanced-deployment"></a>Ulepszone wdrożenie
W celu zapewnienia zwiększonych możliwości zabezpieczeń wdrożenie Azure Security Center dla agentów IoT Oprócz włączenia zabezpieczeń IoT Hub zapewnia oparte na agentach zbieranie zdarzeń, analizowanie i wykrywanie zagrożeń najważniejszych danych zabezpieczeń z urządzeń IoT oraz kompleksowe funkcje zarządzania stanmi zabezpieczeń.

Rozpocznij [wdrożenie rozszerzone](security-agents.md) , które zapewnia oparte na agencie kompleksowe rozwiązanie do zarządzania Stanami zagrożeń i zabezpieczeniami.
   

## <a name="next-steps"></a>Kolejne kroki

- Włącz [Azure Security Center IoT](quickstart-onboard-iot-hub.md)
- Skonfiguruj [rozwiązanie](quickstart-configure-your-solution.md)
- [Tworzenie modułów zabezpieczeń](quickstart-create-security-twin.md)
- Konfigurowanie [alertów niestandardowych](quickstart-create-custom-alerts.md)
- [Wdrażanie agenta zabezpieczeń](how-to-deploy-agent.md)
