---
title: Opis architektury rozwiązania Usługi Azure Security Center dla rozwiązań IoT| Dokumenty firmy Microsoft
description: Dowiedz się więcej o przepływie informacji w usłudze Azure Security Center dla IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75922162"
---
# <a name="azure-security-center-for-iot-architecture"></a>Architektura usługi Azure Security Center dla IoT

W tym artykule opisano architekturę systemu funkcjonalnego usługi Azure Security Center dla rozwiązania IoT. 

## <a name="azure-security-center-for-iot-components"></a>Usługa Azure Security Center dla składników IoT

Usługa Azure Security Center for IoT składa się z następujących składników:
- Integracja z centrum IoT
- Agenci urządzeń (opcjonalnie)
- Wyślij sdk wiadomości zabezpieczających
- Potok analizy
 
### <a name="azure-security-center-for-iot-workflows"></a>Usługi Azure Security Center dla przepływów pracy IoT

Usługa Azure Security Center dla IoT działa w jednym z dwóch przepływów pracy funkcji: wbudowanym i rozszerzonym  

### <a name="built-in"></a>Wbudowane
W trybie **wbudowanym** usługa Azure Security Center dla IoT jest włączona po wybraniu opcji **zabezpieczeń** w centrum IoT Hub. Oferując monitorowanie w czasie rzeczywistym, rekomendacje i alerty, tryb wbudowany oferuje widoczność urządzenia jednoetapowego i niezrównane zabezpieczenia. Tryb wbudowany nie wymaga instalacji agenta na żadnych urządzeniach i wykorzystuje zaawansowaną analizę zarejestrowanych działań do analizowania i ochrony urządzenia terenowego. 

### <a name="enhanced"></a>Rozszerzone 
W trybie **rozszerzonym** po włączeniu opcji **Zabezpieczenia** w centrum IoT hub i zainstalowaniu usługi Azure Security Center dla agentów urządzeń IoT na urządzeniach agenci zbierają, agregują i analizują nieprzetworzone zdarzenia zabezpieczeń z urządzeń. Surowe zdarzenia zabezpieczeń mogą obejmować połączenia IP, tworzenie procesów, loginy użytkowników i inne informacje istotne dla zabezpieczeń. Usługa Azure Security Center dla agentów urządzeń IoT obsługuje również agregację zdarzeń, aby uniknąć wysokiej przepływności sieci. Agenci są wysoce konfigurowalny, co pozwala na korzystanie z nich do określonych zadań, takich jak wysyłanie tylko ważnych informacji w najszybszej umowy SLA lub do agregowania obszernych informacji o zabezpieczeniach i kontekstu w większych segmentach, unikając wyższych kosztów usług.

![Architektura usługi Azure Security Center dla IoT](./media/architecture/azure-iot-security-architecture.png)
 
Agenci urządzeń i inne aplikacje używają **sdk wiadomości zabezpieczeń platformy Azure** do wysyłania informacji o zabezpieczeniach do usługi Azure IoT Hub. Usługa IoT Hub odbiera te informacje i przekazuje ją do usługi Azure Security Center for IoT.

Po włączeniu usługi Azure Security Center dla IoT, oprócz przesyłanych dalej danych, Usługa IoT Hub wysyła również wszystkie swoje wewnętrzne dane do analizy przez usługę Azure Security Center dla IoT. Dane te obejmują dzienniki operacji w chmurze urządzenia, tożsamości urządzeń i konfigurację koncentratora. Wszystkie te informacje pomaga utworzyć potok analizy Usługi Azure Security Center dla IoT.
 
Potok analizy usługi Azure Security Center for IoT otrzymuje również dodatkowe strumienie analizy zagrożeń z różnych źródeł w firmach Microsoft i partnerów firmy Microsoft. Usługa Azure Security Center for IoT cały potok analizy działa z każdą konfiguracją klienta wykonaną w usłudze (na przykład alertami niestandardowymi i użyciem sdk wiadomości zabezpieczeń wysyłania).
 
Za pomocą potoku analizy usługa Azure Security Center dla IoT łączy wszystkie strumienie informacji w celu wygenerowania zaleceń i alertów podlegania działaniu. Potok zawiera zarówno niestandardowe reguły utworzone przez badaczy zabezpieczeń i ekspertów, jak i modele uczenia maszynowego wyszukujące odchylenie od standardowego zachowania urządzenia i analizy ryzyka.
 
Usługa Azure Security Center dla zaleceń i alertów IoT (dane wyjściowe potoku analizy) jest zapisywany w obszarze roboczym usługi Log Analytics każdego klienta. Uwzględnienie nieprzetworzonych zdarzeń w obszarze roboczym, a także alertów i zaleceń umożliwia szczegółowe badania i zapytania przy użyciu dokładnych szczegółów wykrytych podejrzanych działań.  

## <a name="next-steps"></a>Następne kroki

W tym artykule poznaliście podstawową architekturę i przepływ pracy rozwiązania Usługi Azure Security Center dla IoT. Aby dowiedzieć się więcej na temat wymagań wstępnych, jak rozpocząć i włączyć rozwiązanie zabezpieczeń w Centrum IoT, zobacz następujące artykuły:

- [Wymagania wstępne usługi](service-prerequisites.md)
- [Wprowadzenie](getting-started.md)
- [Konfigurowanie rozwiązania](quickstart-configure-your-solution.md)
- [Włączanie zabezpieczeń w Centrum IoT](quickstart-onboard-iot-hub.md)
- [Usługa Azure Security Center for IoT – często zadawane pytania](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT security alerts (Alerty zabezpieczeń usługi Azure Security Center dla IoT)](concept-security-alerts.md)
