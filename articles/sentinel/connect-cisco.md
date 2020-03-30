---
title: Łączenie danych Cisco z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane Cisco z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588403"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Łączenie usługi Cisco ASA z usługą Azure Sentinel



W tym artykule wyjaśniono, jak połączyć urządzenie Cisco ASA z usługą Azure Sentinel. Łącznik danych Cisco ASA umożliwia łatwe łączenie dzienników cisco ASA z usługą Azure Sentinel w celu wyświetlania pulpitów nawigacyjnych, tworzenia niestandardowych alertów i usprawniania badań. Korzystanie z usługi Cisco ASA na platformie Azure Sentinel zapewni Ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Przesyłanie dalej cisco ASA loguje się do agenta Syslog

Cisco ASA nie obsługuje CEF, więc dzienniki są wysyłane jako Syslog i agenta usługi Azure Sentinel wie, jak je przeanalizować tak, jakby były dzienniki CEF. Skonfiguruj usługę Cisco ASA do przekazywania wiadomości Syslog do obszaru roboczego platformy Azure za pośrednictwem agenta Syslog:

1. Przejdź do [wysyłania wiadomości Syslog do zewnętrznego serwera Syslog](https://aka.ms/asi-syslog-cisco-forwarding)i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie. Użyj tych parametrów po wyświetleniu monitu:
    - Ustaw **port** na 514 lub port ustawiony w agencie.
    - Ustaw **syslog_ip** na adres IP agenta.

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics `CommonSecurityLog`dla zdarzeń Cisco, wyszukaj .

1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).




## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia Cisco ASA z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


