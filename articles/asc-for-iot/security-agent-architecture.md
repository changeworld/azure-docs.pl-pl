---
title: Opis usługi ASC dla architektury agenta zabezpieczeń IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Informacje na temat architektury agenta zabezpieczeń dla agentów używanych w ASC dla usługi IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: d81054e95b9f712262fe5fa46453749b54a7ac24
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651401"
---
# <a name="security-agent-reference-architecture"></a>Architektura referencyjna agenta zabezpieczeń

> [!IMPORTANT]
> ASC IoT jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


ASC IoT udostępnia architekturę referencyjną dla agentów zabezpieczeń, które logowania, przetwarzania, agregacji i wysłać danych zabezpieczeń za pomocą usługi IoT hub.

Agentów zabezpieczeń zostały zaprojektowane do pracy w środowisku IoT ograniczone i są wysoce dostosowywalne pod względem wartości, które zapewniają w porównaniu do zasoby, z których korzystają.

Zabezpieczenia agentów obsługuje następujące funkcje:

- Zbieranie zdarzeń zabezpieczeń pierwotne od zasadniczego systemu operacyjnego (Linux, Windows). Aby dowiedzieć się więcej na temat modułów zbierających dane zabezpieczeń dostępne, zobacz [ASC konfiguracji agenta IoT](how-to-agent-configuration.md).

- Agregowanie zdarzeń zabezpieczeń pierwotnych do komunikaty wysyłane za pośrednictwem usługi IoT hub.

- Uwierzytelnianie za pomocą istniejących tożsamości urządzenia lub tożsamości dedykowanych modułu. Zobacz [metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md) Aby dowiedzieć się więcej.

- Skonfiguruj zdalne za pośrednictwem **azureiotsecurity** bliźniaczą reprezentację modułu. Aby dowiedzieć się więcej, zobacz [skonfigurować ASC agenta IoT](how-to-agent-configuration.md).

ASC agentów zabezpieczeń IoT są tworzone jako projekty typu open source i są dostępne w witrynie GitHub: 

- [ASC agenta na podstawie IoT C](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [ASC IoT C#— na podstawie agenta](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>Agent obsługiwany platformy

ASC IoT oferuje różne Instalatora agentów dla 32-bitowe i 64-bitowej Windows oraz takie same dla 32-bitowe i 64-bitowy systemu Linux. Upewnij się, że masz Instalatora agenta poprawne dla każdego urządzenia, zgodnie z poniższą tabelą:

| 32 lub 64-bitowy | Linux | Windows |    Szczegóły|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32-bitowe  | C  | C#  ||
| 64-bitowy  | C#lub C           | C#      | Użyj agenta C w przypadku urządzeń z minimalnymi zasobami|

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano ASC architektury agenta zabezpieczeń IoT i dostępne pliki instalacyjne.

Aby kontynuować wprowadzenie do usługi ASC dla wdrożenia IoT, skorzystaj z poniższych artykułów:

- Zrozumienie [metody uwierzytelniania agenta zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybierz, a następnie wdrożyć [agent zabezpieczeń](how-to-deploy-agent.md)
- Zapoznaj się z usługą ASC dla IoT [usługi wymagań wstępnych](service-prerequisites.md)
- Dowiedz się, jak [ASC włączyć dla usługi IoT w usłudze IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [ASC IoT — często zadawane pytania](resources-frequently-asked-questions.md)
