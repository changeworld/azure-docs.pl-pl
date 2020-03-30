---
title: Wymagania wstępne usługi Azure Security Center dla IoT| Dokumenty firmy Microsoft
description: Szczegółowe informacje o wszystkim, co potrzebne do rozpoczęcia pracy z usługą Azure Security Center dla wymagań wstępnych usługi IoT.
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
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71299479"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Wymagania wstępne usługi Azure Security Center dla IoT

Ten artykuł zawiera wyjaśnienie różnych bloków konstrukcyjnych usługi Azure Security Center dla IoT, co należy rozpocząć, i wyjaśnia podstawowe pojęcia, aby ułatwić zrozumienie usługi. 

## <a name="minimum-requirements"></a>Minimalne wymagania

- Warstwa standardowa usługi IoT Hub
    - Uprawnienia poziomu **właściciela** roli RBAC 
- [Obszar roboczy analizy dzienników](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Usługa Azure Security Center (zalecane)
    - Korzystanie z usługi Azure Security Center jest zaleceniem, a nie wymaganiem. Bez usługi Azure Security Center nie będzie można wyświetlić innych zasobów platformy Azure w centrum IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Praca z usługą Azure Security Center dla usługi IoT

Usługa Azure Security Center dla szczegółowych informacji i raportowania IoT jest dostępna przy użyciu usługi Azure IoT Hub i Usługi Azure Security Center. Aby włączyć usługę Azure Security Center dla IoT w centrum Usługi Azure IoT, wymagane jest konto z uprawnieniami na poziomie **właściciela.** Po włączeniu asc dla IoT w centrum IoT Hub, Usługa Azure Security Center dla szczegółowych usług IoT są wyświetlane jako funkcja **zabezpieczeń** w usłudze Azure IoT Hub i jako **IoT** w Usłudze Azure Security Center. 

## <a name="supported-service-regions"></a>Obsługiwane regiony usług 

Usługa Azure Security Center dla IoT jest obecnie obsługiwana dla centrów IoT w następujących regionach platformy Azure:
  - Środkowe stany USA  
  - Wschodnie stany USA 
  - Wschodnie stany USA 2
  - Zachodnio-środkowe stany USA
  - Zachodnie stany USA
  - Zachodnie stany USA 2
  - Środkowe Stany Zjednoczone Południowe
  - Północno-środkowe stany USA
  - Kanada Środkowa
  - Kanada Wschodnia 
  - Europa Północna    
  - Brazylia Południowa
  - Francja Środkowa  
  - Zachodnie Zjednoczone Królestwo 
  - Południowe Zjednoczone Królestwo
  - Europa Zachodnia 
  - Europa północna 
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

Usługa Azure Security Center for IoT kieruje cały ruch ze wszystkich regionów europejskich do regionalnego centrum danych w Europie Zachodniej i wszystkich pozostałych regionów do regionalnego centrum danych w stanach Centralnych Stanów Zjednoczonych.  
  
## <a name="wheres-my-iot-hub"></a>Gdzie jest moje Centrum IoT?

Sprawdź lokalizację usługi IoT Hub, aby zweryfikować dostępność usługi przed rozpoczęciem. 

1. Otwórz centrum IoT Hub. 
2. Kliknij pozycję **Przegląd**. 
3. Sprawdź, czy wyświetlana lokalizacja jest zgodna z jednym z [obsługiwanych regionów serwisu.](#supported-service-regions) 


## <a name="supported-platforms-for-agents"></a>Obsługiwane platformy dla agentów 

Usługa Azure Security Center dla agentów IoT obsługuje rosnącą listę urządzeń i platform. Zobacz [listę obsługiwanych platform,](how-to-deploy-agent.md) aby sprawdzić istniejącą lub planowaną bibliotekę urządzeń.  

## <a name="next-steps"></a>Następne kroki
- Przeczytaj [omówienie](overview.md) zabezpieczeń usługi Azure IoT
- Dowiedz się, jak [włączyć usługę](quickstart-onboard-iot-hub.md)
- Przeczytaj często zadawane pytania dotyczące [Centrum zabezpieczeń platformy Azure dla IoT](resources-frequently-asked-questions.md)
- Dowiedz się, jak [poznać usługi Azure Security Center dla alertów IoT](concept-security-alerts.md)
