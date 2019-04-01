---
title: Omówienie usługi Azure Security Center dla modułu zabezpieczeń IoT bliźniaczych reprezentacji (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat koncepcji bliźniaczych reprezentacjach modułów zabezpieczeń i jak są one używane w Centrum zabezpieczeń Azure dla IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 56bc93973bd2c080b13b97f1344cc165f742e757
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758404"
---
# <a name="security-module"></a>Moduł zabezpieczeń

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule wyjaśniono, jak usługi Azure Security Center (ASC) dla IoT korzysta z bliźniaczych reprezentacji urządzeń i moduły. 

## <a name="device-twins"></a>Bliźniacze reprezentacje urządzeń

Dla rozwiązań IoT, zbudowany na platformie Azure bliźniacze reprezentacje urządzeń odgrywają kluczową rolę zarówno w przypadku zarządzania urządzeniami, jak i automatyzację procesów.  

ASC IoT oferuje pełną integrację ze swojej istniejącej IoT platforma do zarządzania urządzeniami, dzięki któremu można zarządzać stanu zabezpieczeń urządzenia oraz upewnij korzystać z istniejących możliwości kontroli urządzenia. Integracja jest osiągane dzięki wykorzystaniu usługi IoT Hub bliźniaczej reprezentacji mechanizm.  

Dowiedz się więcej na temat koncepcji [bliźniaczych reprezentacji urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) w usłudze Azure IoT Hub. 

## <a name="security-module-twins"></a>Bliźniaczych reprezentacjach modułów zabezpieczeń

ASC IoT utrzymuje bliźniaczą reprezentację modułu zabezpieczeń dla każdego urządzenia w usłudze.
Bliźniacza reprezentacja modułu zabezpieczeń przechowuje wszystkie informacje dotyczą zabezpieczeń urządzeń dla każdego określonego urządzenia w rozwiązaniu.
Właściwości zabezpieczeń urządzenia są obsługiwane w dedykowanym zabezpieczeń bliźniaczą reprezentację modułu komunikacji bezpieczniejsze i włączania aktualizacji i obsługi, które wymagają mniejszej ilości zasobów.  

Zobacz [bliźniaczą reprezentację modułu zabezpieczeń Utwórz](quickstart-create-security-twin.md) i [Konfigurowanie agentów zabezpieczeń](how-to-agent-configuration.md) Dowiedz się, jak tworzenie, dostosowywanie i konfigurowanie bliźniaczej reprezentacji. Zobacz [bliźniaczych reprezentacjach modułów opis](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) Aby dowiedzieć się więcej na temat koncepcji bliźniaczych reprezentacjach modułów usługi IoT Hub. 
 

## <a name="see-also"></a>Zobacz także
- [ASC IoT (wersja zapoznawcza)](overview.md)
- [Wdrażanie agentów zabezpieczeń](how-to-deploy-agent.md)
- [Metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)