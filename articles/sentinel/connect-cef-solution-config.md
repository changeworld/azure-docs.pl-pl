---
title: Skonfiguruj rozwiązanie zabezpieczające, aby połączyć dane cef z usługą Azure Sentinel Preview| Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować rozwiązanie zabezpieczające do łączenia danych cef z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588454"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>KROK 2: Skonfiguruj rozwiązanie zabezpieczające do wysyłania wiadomości CEF

W tym kroku należy wykonać niezbędne zmiany konfiguracji na samym rozwiązaniu zabezpieczeń, aby wysłać dzienniki do agenta CEF.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurowanie rozwiązania za pomocą łącznika

Jeśli rozwiązanie zabezpieczające ma już istniejące złącze, należy użyć instrukcji specyficznych dla łącznika w następujący sposób:

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 (włas ie](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [Łączenie z rozwiązaniem One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Konfigurowanie innego rozwiązania
Jeśli łącznik nie istnieje dla określonego rozwiązania zabezpieczeń, należy użyć następujących ogólnych instrukcji przekazywania dzienników do agenta CEF.

1. Przejdź do określonego artykułu konfiguracji, aby uzyskać instrukcje dotyczące konfigurowania rozwiązania do wysyłania komunikatów cef. Jeśli rozwiązanie nie jest wymienione, na urządzeniu należy ustawić te wartości, tak aby urządzenie wysyła niezbędne dzienniki w formacie niezbędnym do agenta Syslog usługi Azure Sentinel, na podstawie agenta usługi Log Analytics. Można zmodyfikować te parametry w urządzeniu, tak długo, jak można również zmodyfikować je w demon syslogu na agenta wartowniczych platformy Azure.
    - Protokół = TCP
    - Port = 514
    - Format = CEF
    - Adres IP — upewnij się, że wysyłasz wiadomości CEF na adres IP maszyny wirtualnej, którą przeznaczyłeś w tym celu.

   > [!NOTE]
   > To rozwiązanie obsługuje Syslog RFC 3164 lub RFC 5424.


1. Aby użyć odpowiedniego schematu w usłudze Log Analytics `CommonSecurityLog`dla zdarzeń cef, wyszukaj .

1. Przejdź do kroku 3: [sprawdź poprawność łączności](connect-cef-verify.md).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).

