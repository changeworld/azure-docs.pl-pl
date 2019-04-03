---
title: Omówienie usługi Azure Security Center dla modułu zabezpieczeń IoT, IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, architekturę i możliwości usługi Azure Security Center dla modułu zabezpieczeń IoT dla usługi IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: c6ac3d9dbbb16caed51243fea852adea541b9f04
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862237"
---
# <a name="azure-iot-edge-security-module"></a>Moduł zabezpieczeń w usłudze Azure IoT Edge

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecana dla produkcyjnego worklo§1ads. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Usługa Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/) zapewnia zaawansowane możliwości zarządzania i wykonać biznesowe przepływy pracy na urządzeniach brzegowych.
Kluczowym elementem, IoT Edge jest odtwarzany w środowiskach IoT ułatwiają szczególnie atrakcyjne dla uczestników złośliwych działań.

Usługa Azure Security Center (ASC) dla modułu zabezpieczeń IoT zapewnia rozwiązanie kompleksowych funkcji zabezpieczeń dla usługi IoT Edge urządzenia.
ASC IoT moduł służy do zbierania, agreguje i analizuje dane dotyczące zabezpieczeń pierwotnych z systemu operacyjnego i systemem kontenerów do zaleceń dotyczących zabezpieczeń z możliwością działania i alerty.

Podobnie jak ASC agentów zabezpieczeń IoT dla urządzenia IoT, ASC dla modułu usługi IoT Edge jest dużym stopniu dostosowywane za pośrednictwem jego bliźniaczą reprezentację modułu.
Zobacz [skonfigurować agenta](how-to-agent-configuration.md) Aby dowiedzieć się więcej.

ASC modułu zabezpieczeń IoT, IoT Edge zapewnia następujące funkcje:

- Zbiera zdarzenia zabezpieczeń pierwotne od zasadniczego systemu operacyjnego (Linux) i systemy IoT Edge kontenera.
  
  Zobacz [ASC konfiguracji agenta IoT](how-to-agent-configuration.md) dowiedzieć się więcej o moduły zbierające dane dostępnych zabezpieczeń.

- Analiza manifesty wdrożenia usługi IoT Edge.

- Zdarzenia zabezpieczeń pierwotne są agregowane we wiadomości wysłane za pośrednictwem [usługi IoT Edge Hub](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Usuń konfigurację za pośrednictwem bliźniaczą reprezentację modułu zabezpieczeń.

  Zobacz [skonfigurować ASC agenta IoT](how-to-agent-configuration.md) Aby dowiedzieć się więcej.

ASC modułu zabezpieczeń IoT, IoT Edge jest uruchamiany w trybie uprzywilejowanym w ramach usługi IoT Edge.
Tryb uprzywilejowany jest wymagany w celu umożliwienia modułu do monitorowania systemu operacyjnego, a inne moduły usługi IoT Edge.

## <a name="agent-supported-platforms"></a>Agent obsługiwany platformy

ASC modułu zabezpieczeń IoT, IoT Edge jest obecnie dostępna tylko dla systemu Linux.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano architekturę i możliwości usługi ASC modułu zabezpieczeń IoT dla usługi IoT Edge.

Aby kontynuować wprowadzenie do usługi ASC dla wdrożenia IoT, skorzystaj z poniższych artykułów:

- Wdrażanie [modułu zabezpieczeń dla usługi IoT Edge](how-to-deploy-edge.md)
- Dowiedz się, jak [konfigurację modułu zabezpieczeń](how-to-agent-configuration.md)
- Zapoznaj się z usługą ASC dla IoT [usługi wymagań wstępnych](service-prerequisites.md)
- Dowiedz się, jak [ASC włączyć dla usługi IoT w usłudze IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [ASC IoT — często zadawane pytania](resources-frequently-asked-questions.md)