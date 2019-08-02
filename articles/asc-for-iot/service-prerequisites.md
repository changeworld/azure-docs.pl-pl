---
title: Azure Security Center wymagań wstępnych usługi IoT | Microsoft Docs
description: Szczegóły dotyczące wszystkiego, czego potrzebujesz, aby rozpocząć pracę z Azure Security Centerami dotyczącymi wymagań wstępnych usługi IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596817"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center wymagań wstępnych usługi IoT

Ten artykuł zawiera objaśnienie różnych bloków konstrukcyjnych Azure Security Center (ASC) dla usługi IoT, co należy zacząć i objaśnia podstawowe koncepcje ułatwiające zrozumienie usługi. 

## <a name="minimum-requirements"></a>Minimalne wymagania

- Warstwa standardowa IoT Hub
    - Uprawnienia na poziomie **właściciela** roli RBAC 
- [Log Analytics obszar roboczy](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (zalecane)
    - Korzystanie z Azure Security Center jest zaleceniem, a nie wymaganiem. Bez Azure Security Center nie będzie można wyświetlać innych zasobów platformy Azure w ramach usługi IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Praca z usługą ASC dla usługi IoT

Usługa ASC dla usługi IoT Insights i raportowania jest dostępna przy użyciu usług Azure IoT Hub i Azure Security Center. Aby włączyć funkcję ASC dla IoT na platformie Azure IoT Hub, wymagane jest konto z uprawnieniami na poziomie **właściciela** . Po włączeniu funkcji ASC dla IoT w IoT Hub w usłudze Azure Insights dla usługi IoT są wyświetlane jako funkcja **zabezpieczeń** w ramach IoT Hub, a jako **IoT** w Azure Security Center. 

## <a name="supported-service-regions"></a>Obsługiwane regiony usługi 

Usługa ASC dla IoT jest obecnie obsługiwana w przypadku centrów IoT w następujących regionach platformy Azure:
  - Środkowe stany USA  
  - East US 
  - Wschodnie stany USA 2
  - Środkowo-zachodnie stany USA
  - Zachodnie stany USA
  - Zachodnie stany USA 2
  - Południowo-środkowe stany USA
  - Środkowo-północne stany USA
  - Kanada Środkowa
  - Kanada Wschodnia 
  - Europa Północna    
  - Brazylia Południowa
  - Francja Środkowa  
  - Zachodnie Zjednoczone Królestwo 
  - Południowe Zjednoczone Królestwo
  - Europa Zachodnia 
  - Europa Północna 
  - Japonia Zachodnia  
  - Japonia Wschodnia  
  - Australia Południowo-Wschodnia
  - Australia Wschodnia
  - Azja Wschodnia   
  - Azja Południowo-Wschodnia
  - Korea Środkowa
  - Korea Południowa 
  - Indie Środkowe
  - Indie Południowe
  
## <a name="wheres-my-iot-hub"></a>Gdzie mam IoT Hub?

Sprawdź lokalizację IoT Hub, aby zweryfikować dostępność usługi przed rozpoczęciem. 

1. Otwórz IoT Hub. 
2. Kliknij pozycję **Przegląd**. 
3. Sprawdź, czy podana lokalizacja odpowiada jednemu z [obsługiwanych regionów usługi](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Obsługiwane platformy dla agentów 

Usługi ASC dla agentów IoT obsługują rosnącą listę urządzeń i platform. Zapoznaj się z [listą obsługiwanych platform](how-to-deploy-agent.md) , aby sprawdzić istniejącą lub zaplanowaną bibliotekę urządzeń.  

## <a name="next-steps"></a>Następne kroki
- Przeczytaj [Omówienie](overview.md) zabezpieczeń usługi Azure IoT
- Dowiedz się [, jak włączyć usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj [Azure Security Center dla usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)
- Dowiedz się, jak [zrozumieć Azure Security Center alertów IoT](concept-security-alerts.md)
