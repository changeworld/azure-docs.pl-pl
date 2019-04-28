---
title: Wybierz i wdrożyć usługę Azure Security Center dla agenta IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać około i ciągłe wdrażanie usługi Azure Security Center agentów zabezpieczeń IoT na urządzeniach IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358407"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Wybierz i wdrożyć agenta zabezpieczeń na urządzeniu IoT

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Security Center (ASC) dla IoT udostępnia architektur referencyjnych dla agentów zabezpieczeń, które monitorują i zbierają dane z urządzeń IoT.
Zobacz [architektury referencyjnej agenta zabezpieczeń](security-agent-architecture.md) Aby dowiedzieć się więcej.

Agenci są tworzone jako projektów typu open source i są dostępne w dwóch wersjach w odpowiedzi: <br> [C](https://aka.ms/iot-security-github-c), i [ C# ](https://aka.ms/iot-security-github-cs).

W tym artykule omówiono sposób wykonywania następujących zadań: 
> [!div class="checklist"]
> * Porównaj odmian agenta zabezpieczeń
> * Odnajdywanie agenta obsługiwanych platform
> * Wybierz wersję agenta odpowiednie dla Twojego rozwiązania

## <a name="understand-security-agent-options"></a>Informacje o opcjach agenta zabezpieczeń

Każdy ASC wersja agenta zabezpieczeń IoT oferuje ten sam zestaw funkcji i obsługuje podobne opcje konfiguracji. 

Agent zabezpieczenia oparty na C ma mniejsze obciążenie pamięci i jest idealnym wyborem dla urządzeń z mniejszą liczbą dostępnych zasobów. 

|     | Agent zabezpieczenia oparty na C | C#-agent zabezpieczenia oparty na |
| --- | ----------- | --------- |
| Oprogramowanie typu „open source” | Dostępne w obszarze [licencją MIT](https://en.wikipedia.org/wiki/MIT_License) w [Github](https://aka.ms/iot-security-github-cs) | Dostępne w obszarze [licencją MIT](https://en.wikipedia.org/wiki/MIT_License) w [Github](https://aka.ms/iot-security-github-c) |
| Języka programowania    | C | C# |
| Obsługiwane platformy Windows? | Nie | Yes |
| Wymagania wstępne Windows | --- | [USŁUGI WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Obsługiwane platformy Linux? | Tak, x64 i x86 | Tak, tylko x64 |
| Wymagania wstępne systemu Linux | libunwind8 libcurl3, środowisko uruchomieniowe identyfikatora uuid, wykorzystują, audispd wtyczek | libunwind8 libcurl3, środowisko uruchomieniowe uuid, wykorzystują, audispd wtyczek, "sudo", polecenia netstat, iptables |
| Przestrzeń dyskową | 10.5 MB | 90MB |
| Zużycie pamięci (średnio) | 5.5 MB | 33MB |
| [Uwierzytelnianie](concept-security-agent-authentication-methods.md) do usługi IoT Hub | Yes | Yes |
| Dane zabezpieczeń [kolekcji](how-to-agent-configuration.md#supported-security-events) | Yes | Yes |
| Zdarzenie agregacji | Yes | Yes |
| Konfigurowanie zdalnego za pomocą [bliźniaczą reprezentację modułu zabezpieczeń](concept-security-module.md) | Yes | Yes |


## <a name="choose-an-agent-flavor"></a>Wybierz wersję agenta 

Należy odpowiedzieć na następujące pytania dotyczące urządzenia IoT, aby wybrać prawidłowy agent:

- Czy używasz _systemu Windows Server_ lub _Windows IoT Core_? 

    [Wdrażanie C#— na podstawie agenta zabezpieczeń dla Windows](how-to-deploy-windows-cs.md).

- Czy używasz dystrybucji systemu Linux przy użyciu x86 architektura? 

    [Wdróż zabezpieczenia oparty na C agenta dla systemu Linux](how-to-deploy-linux-c.md).

- Czy używasz dystrybucji systemu Linux przy użyciu x64 architektura?

    Możesz użyć albo wersja agenta. <br>
    [Wdróż zabezpieczenia oparty na C agenta dla systemu Linux](how-to-deploy-linux-c.md) i/lub [Wdróż C#— na podstawie zabezpieczeń agenta dla systemu Linux](how-to-deploy-linux-cs.md).

Zarówno odmian agenta oferuje ten sam zestaw funkcji i obsługuje podobne opcje konfiguracji.
Zobacz [porównania agenta zabezpieczeń](how-to-deploy-agent.md#understand-security-agent-options) Aby dowiedzieć się więcej.

## <a name="supported-platforms"></a>Obsługiwane platformy

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

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do Przewodnik z instrukcjami dotyczącymi konfiguracji agenta. 
> [!div class="nextstepaction"]
> [Konfiguracja agenta jak przewodnik](./how-to-agent-configuration.md)
