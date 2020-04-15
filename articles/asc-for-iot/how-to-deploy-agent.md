---
title: Wybieranie i wdrażanie agentów zabezpieczeń
description: Dowiedz się, jak wybierać i wdrażać usługi Azure Security Center dla agentów zabezpieczeń IoT na urządzeniach IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: c3b514c79be87cd136375b4853226426965f4185
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311215"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Wybieranie i wdrażanie agenta zabezpieczeń na urządzeniu IoT

Usługa Azure Security Center dla IoT udostępnia architektury referencyjne dla agentów zabezpieczeń, które monitorują i zbierają dane z urządzeń IoT.
Aby dowiedzieć się więcej, zobacz [Architektura referencyjna agenta zabezpieczeń](security-agent-architecture.md).

Agenci są opracowywane jako projekty open source i są dostępne w dwóch smakach: <br> [C](https://aka.ms/iot-security-github-c)i [C#](https://aka.ms/iot-security-github-cs).

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Porównaj smaki agentów zabezpieczeń
> * Odkryj obsługiwane platformy agentów
> * Wybierz odpowiedni smak środka dla swojego rozwiązania

## <a name="understand-security-agent-options"></a>Opis opcji agenta zabezpieczeń

Każdy format agenta zabezpieczeń usługi Azure Security Center for IoT oferuje ten sam zestaw funkcji i obsługuje podobne opcje konfiguracji.

Agent zabezpieczeń oparty na języku C ma mniejszą ilość pamięci i jest idealnym wyborem dla urządzeń z mniejszą ilością dostępnych zasobów.

|     | Agent zabezpieczeń oparty na języku C | Agent zabezpieczeń oparty na języku C# |
| --- | ----------- | --------- |
| Open source | Dostępne na [licencji MIT](https://en.wikipedia.org/wiki/MIT_License) w [GitHub](https://aka.ms/iot-security-github-cs) | Dostępne na [licencji MIT](https://en.wikipedia.org/wiki/MIT_License) w [GitHub](https://aka.ms/iot-security-github-c) |
| Języka programowania    | C | C# |
| Obsługiwane platformy Windows? | Nie | Tak |
| Wymagania wstępne systemu Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Obsługiwane platformy Linux? | Tak, x64 i x86 | Tak, tylko x64 |
| Wymagania wstępne systemu Linux | libunwind8, libcurl3, uuid-runtime, kontrolowane, audispd-pluginy | libunwind8, libcurl3, uuid-runtime, audytowane, audispd-pluginy, sudo, netstat, iptables |
| Ślad dysku | 10,5 MB | 90 MB |
| Ilość pamięci (średnio) | 5,5 MB | 33 MB |
| [Uwierzytelnianie](concept-security-agent-authentication-methods.md) w Centrum IoT | Tak | Tak |
| Gromadzenie danych [zabezpieczających](how-to-agent-configuration.md#supported-security-events) | Tak | Tak |
| Agregacja zdarzeń | Tak | Tak |
| Konfiguracja zdalna za pośrednictwem [bliźniaczej reprezentacji modułu zabezpieczeń](concept-security-module.md) | Tak | Tak |
|

## <a name="security-agent-installation-guidelines"></a>Wskazówki dotyczące instalacji agenta zabezpieczeń

W **systemie Windows**: Skrypt Install SecurityAgent.ps1 musi być wykonywany z okna programu PowerShell administratora.

Dla **Linuksa:** InstallSecurityAgent.sh musi być uruchamiany jako superużyt. Zalecamy prefiksowanie polecenia instalacji za pomocą "sudo".

## <a name="choose-an-agent-flavor"></a>Wybierz smak środka

Odpowiedz na następujące pytania dotyczące urządzeń IoT, aby wybrać odpowiedniego agenta:

- Czy używasz _systemu Windows Server_ lub Windows _IoT Core?_

    [Wdrażanie agenta zabezpieczeń opartego na języku C#dla systemu Windows](how-to-deploy-windows-cs.md).

- Czy korzystasz z dystrybucji Linuksa z architekturą x86?

    [Wdrażanie agenta zabezpieczeń opartego na języku C dla systemu Linux](how-to-deploy-linux-c.md).

- Czy korzystasz z dystrybucji Linuksa z architekturą x64?

    Można użyć obu aromatów agenta. <br>
    [Wdrażanie agenta zabezpieczeń opartego na języku C dla systemu Linux](how-to-deploy-linux-c.md) i/lub [wdrażanie agenta zabezpieczeń opartego na języku C dla systemu Linux](how-to-deploy-linux-cs.md).

Oba smaki agenta oferują ten sam zestaw funkcji i obsługują podobne opcje konfiguracji.
Zobacz [Porównanie agentów zabezpieczeń,](how-to-deploy-agent.md#understand-security-agent-options) aby dowiedzieć się więcej.

## <a name="supported-platforms"></a>Obsługiwane platformy

Poniższa lista zawiera wszystkie aktualnie obsługiwane platformy.

|Usługa Azure Security Center dla agenta IoT |System operacyjny |Architektura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, kompilacja 17763    |x64|
|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika infiguracyjnego dla konfiguracji agenta.
> [!div class="nextstepaction"]
> [Konfiguracja agenta, jak prowadzić](./how-to-agent-configuration.md)
