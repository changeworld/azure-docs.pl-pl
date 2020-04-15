---
title: Moduł bezpieczeństwa i bliźniacze urządzenia
description: Dowiedz się więcej o koncepcji bliźniaczych modułów zabezpieczeń i o tym, jak są one używane w usłudze Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311444"
---
# <a name="security-module"></a>Moduł zabezpieczeń

W tym artykule wyjaśniono, jak usługa Azure Security Center for IoT używa bliźniaczych urządzeń i modułów.

## <a name="device-twins"></a>Bliźnięta urządzeń

W przypadku rozwiązań IoT wbudowanych w platformę Azure bliźniacze urządzenia odgrywają kluczową rolę zarówno w zarządzaniu urządzeniami, jak i automatyzacji procesów.

Usługa Azure Security Center for IoT oferuje pełną integrację z istniejącą platformą zarządzania urządzeniami IoT, umożliwiając zarządzanie stanem zabezpieczeń urządzenia, a także korzystanie z istniejących funkcji sterowania urządzeniami. Integrację uzyskuje się za pomocą mechanizmu bliźniaczego usługi IoT Hub.

Dowiedz się więcej o koncepcji [bliźniaczych urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) w usłudze Azure IoT Hub.

## <a name="security-module-twins"></a>Bliźniacze moduły bezpieczeństwa

Usługa Azure Security Center for IoT przechowuje bliźniaczą reprezentację modułu zabezpieczeń dla każdego urządzenia w usłudze.
Moduł bliźniaczej reprezentacji modułu zabezpieczeń zawiera wszystkie informacje istotne dla zabezpieczeń urządzenia dla każdego konkretnego urządzenia w rozwiązaniu.
Właściwości zabezpieczeń urządzenia są obsługiwane w dedykowanej bliźniaczej reprezentacji modułu zabezpieczeń w celu bezpieczniejszej komunikacji i umożliwienia aktualizacji i konserwacji, która wymaga mniej zasobów.

Zobacz [Tworzenie bliźniaczej reprezentacji modułu zabezpieczeń](quickstart-create-security-twin.md) i [Konfigurowanie agentów zabezpieczeń,](how-to-agent-configuration.md) aby dowiedzieć się, jak tworzyć, dostosowywać i konfigurować bliźniaczej reprezentacji. Zobacz [Opis bliźniaczych modułów,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) aby dowiedzieć się więcej o koncepcji bliźniaczych modułów w centrum IoT Hub.

## <a name="see-also"></a>Zobacz też

- [Usługa Azure Security Center dla IoT — omówienie](overview.md)
- [Wdrażanie agentów zabezpieczeń](how-to-deploy-agent.md)
- [Metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
