---
title: Wybierz i wdrażanie usługi ASC agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać około i ciągłe wdrażanie usługi ASC agentów zabezpieczeń IoT na urządzeniach IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541965"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Wybierz i wdrożyć agenta zabezpieczeń na urządzeniu IoT

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC IoT udostępnia architektur referencyjnych dla agentów zabezpieczeń, które monitorują i zbierają dane z urządzeń IoT.

Agentów zabezpieczeń są tworzone jako projektów typu open source i są dostępne w dwóch wersjach: <br> [C](https://aka.ms/iot-security-github-c), i [ C# ](https://aka.ms/iot-security-github-cs).

## <a name="supported-platforms-for-asc-for-iot-agents"></a>Platformy obsługiwane przez usługę ASC agentów IoT

Poniższa lista zawiera wszystkie aktualnie obsługiwanych platform.  

|ASC agenta IoT |System operacyjny |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core build 17763 |x64|


## <a name="which-flavor-should-i-use"></a>Która wersja należy używać?

Należy wziąć pod uwagę następujące pytania w odniesieniu do urządzeń IoT:

- Czy używasz _systemu Windows Server_ lub _Windows IoT Core_? 

    Użyj [ASC dla Windows przy użyciu instalacji IoT C#-agent zabezpieczenia oparty na](tutorial-deploy-windows-cs.md).

- Czy używasz dystrybucji systemu Linux przy użyciu x86 architektura? 

    Użyj [ASC instalacji IoT dla systemu Linux z agentem zabezpieczenia oparty na C](tutorial-deploy-linux-c.md).
- Czy używasz dystrybucji systemu Linux przy użyciu x64 architektura?

    Można użyć obu odmian. <br>
    [ASC instalacji IoT dla systemu Linux z agentem zabezpieczenia oparty na C](tutorial-deploy-linux-c.md) i/lub [ASC IoT instalacji dla systemu Linux przy użyciu C#-agent zabezpieczenia oparty na](tutorial-deploy-linux-cs.md).

Zarówno odmian mają ten sam zestaw funkcji i obsługują podobne opcje konfiguracji. Agent zabezpieczenia oparty na C ma mniejsze obciążenie pamięci.


## <a name="next-steps"></a>Kolejne kroki
- [Omówienie](overview.md)
- [Zabezpieczenia agentów](security-agent-architecture.md)
- [Metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
- [ASC IoT — często zadawane pytania](resources-frequently-asked-questions.md)