---
title: Informacje na temat Azure Security Center dla usługi IoT Security module dla IoT Edge Preview | Microsoft Docs
description: Zapoznaj się z architekturą i możliwościami Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596854"
---
# <a name="azure-iot-edge-security-module"></a>Moduł zabezpieczeń Azure IoT Edge

> [!IMPORTANT]
> Azure Security Center usługi IoT dla IoT Edge jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) zapewnia zaawansowane możliwości zarządzania i wykonywania służbowych przepływów pracy na krawędzi.
Kluczowa część, która IoT Edge odgrywa w środowiskach IoT, sprawia, że jest szczególnie atrakcyjna dla złośliwych aktorów.

Azure Security Center dla usługi IoT Security to kompleksowe rozwiązanie zabezpieczeń dla IoT Edge urządzeń.
Azure Security Center dla modułu IoT zbiera, agreguje i analizuje surowe dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.

Podobnie jak w przypadku Azure Security Center dla agentów zabezpieczeń IoT dla urządzeń IoT, Azure Security Center dla modułu IoT Edge jest wysoce dostosowywany za pomocą sznurka modułu.
Aby dowiedzieć się więcej, zobacz [Configure the Agent](how-to-agent-configuration.md) .

Azure Security Center dla usługi IoT Security module dla IoT Edge oferuje następujące funkcje:

- Zbiera nieprzetworzone zdarzenia zabezpieczeń z bazowego systemu operacyjnego (Linux) i IoT Edge systemów kontenerów.
  
  Aby dowiedzieć się więcej na temat dostępnych modułów zbierających dane zabezpieczeń, zobacz [Azure Security Center dla konfiguracji agenta IoT](how-to-agent-configuration.md) .

- Analiza manifestów wdrażania IoT Edge.

- Agreguje pierwotne zdarzenia zabezpieczeń do komunikatów wysyłanych za poorednictwem [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Usuń konfigurację za pomocą sznurka modułu zabezpieczeń.

  Aby dowiedzieć się więcej, zobacz [konfigurowanie Azure Security Center dla agenta IoT](how-to-agent-configuration.md) .

Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge jest uruchamiany w trybie uprzywilejowanym w obszarze IoT Edge.
Tryb uprzywilejowany jest wymagany, aby umożliwić modułowi monitorowanie systemu operacyjnego i innych modułów IoT Edge.

## <a name="module-supported-platforms"></a>Platformy obsługiwane przez moduły

Azure Security Center dla usługi IoT Security module dla IoT Edge jest obecnie dostępny tylko dla systemu Linux. 

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o architekturze i możliwościach Azure Security Center modułu zabezpieczeń IoT dla IoT Edge.

Aby kontynuować wprowadzenie do usługi Azure Security Center na potrzeby wdrażania IoT, użyj następujących artykułów:

- Wdróż [moduł zabezpieczeń dla IoT Edge](how-to-deploy-edge.md)
- Dowiedz się, jak [skonfigurować moduł zabezpieczeń](how-to-agent-configuration.md)
- Przejrzyj Azure Security Center [wymagań wstępnych usługi](service-prerequisites.md) IoT
- Dowiedz się, jak [włączyć usługę Azure Security Center dla usługi IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z [Azure Security Center na potrzeby usługi IoT — często zadawane pytania](resources-frequently-asked-questions.md)