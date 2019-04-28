---
title: Omówienie usługi Azure Security Center dla architektury rozwiązania IoT (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Zapoznaj się z przepływem informacji w usłudze Azure Security Center dla usługi IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360387"
---
# <a name="azure-security-center-for-iot-architecture"></a>Usługa Azure Security Center dla architektury IoT

W tym artykule opisano architekturę działający system z usługi Azure Security Center (ASC) dla rozwiązania IoT. 

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług, a nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="asc-for-iot-components"></a>ASC składników IoT

ASC IoT składa się z następujących składników:
- Agenci urządzenia
- Wyślij komunikat zabezpieczeń zestawu SDK
- Integracja usługi IoT Hub
- Potok analizy
 
### <a name="asc-for-iot-workflow"></a>ASC IoT przepływu pracy

ASC agentów urządzenia IoT umożliwia łatwe zbieranie zdarzeń związanych z zabezpieczeniami pierwotnych z urządzeń. Zdarzenia zabezpieczeń Raw mogą obejmować połączeń IP, procesu tworzenia, identyfikatory logowania użytkownika i innych informacji związanych z zabezpieczeniami. ASC agentów urządzenia IoT również obsługiwać agregację zdarzeń w celu uniknięcia sieci o wysokiej przepływności. Agenci są w dużym stopniu dostosowywane, możliwość używania ich dla określonego zadania, takie jak wysyłanie tylko ważne informacje o najszybszym warunki umowy SLA lub agregowanie informacji o zabezpieczeniach rozbudowane i kontekst na większych segmenty, unikając wyższe koszty usług.
 
Agenci urządzenia i innych aplikacji użyj **Azure ASC wysłać wiadomość zabezpieczeń zestawu SDK** do wysyłania informacji o zabezpieczeniach w usłudze Azure IoT Hub. Usługa IoT Hub zbiera te informacje i przekazuje go do usługi ASC dla usługi IoT.

Po włączeniu ASC dla usługi IoT, oprócz przekazane dane IoT Hub wysyła również wszystkie wewnętrzne dane do analizy przez usługę ASC dla IoT. Te dane obejmują dzienniki operacji urządzenia do chmury, tożsamości urządzenia i konfigurację koncentratora. Wszystkie te informacje pomaga utworzyć ASC potoku analizy IoT.
 
ASC potoku analizy IoT Ponadto otrzymuje strumieni analizy dodatkowe zagrożenie z różnych źródeł firmy Microsoft i Microsoft partnerów. ASC IoT analytics całego potoku współpracuje z każdej konfiguracji klienta na usługi (takie jak niestandardowe alerty i korzystanie z zabezpieczeń wysyłanie wiadomości zestawu SDK).
 
Przy użyciu potoku analizy, ASC IoT łączy wszystkie strumienie informacje potrzebne do generowania informacje z możliwością działania zaleceń i alertów. Potok zawiera zarówno reguły niestandardowe, które zostały utworzone przez badaczy zabezpieczeń i ekspertów, a także wyszukiwanie odchylenie od standardowych urządzeniami zachowanie i analizy ryzyka modeli uczenia maszynowego.
 
ASC IoT zaleceń i alertów (dane wyjściowe potoku analitycznego) są zapisywane do obszaru roboczego usługi Log Analytics każdego klienta. Łącznie nieprzetworzonych zdarzeń w obszarze roboczym, a także alerty i zalecenia umożliwia szczegółowe badania i zapytań za pomocą szczegółowymi informacjami na temat podejrzanych działań wykrytych.  

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano podstawowe architekturę i przepływ pracy z usługą ASC dla rozwiązania IoT. Aby dowiedzieć się więcej o wymaganiach wstępnych, jak rozpocząć pracę i włączyć rozwiązania zabezpieczeń w usłudze IoT Hub, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Rozpoczęcie pracy](getting-started.md)
- [Konfiguruj rozwiązanie](quickstart-configure-your-solution.md)
- [Włącz zabezpieczenia w usłudze IoT Hub](quickstart-onboard-iot-hub.md)
- [ASC IoT — często zadawane pytania](resources-frequently-asked-questions.md)
- [ASC alerty zabezpieczeń IoT](concept-security-alerts.md)

