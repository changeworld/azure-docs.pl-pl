---
title: Łączenie danych sieci Palo Alto z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane sieci Palo Alto z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588131"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Łączenie sieci Palo Alto z usługą Azure Sentinel



W tym artykule wyjaśniono, jak połączyć urządzenie Palo Alto Networks z usługą Azure Sentinel. Łącznik danych Palo Alto Networks umożliwia łatwe łączenie dzienników sieci Palo Alto z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badań. Korzystanie z sieci Palo Alto networks na platformie Azure Sentinel zapewni Ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Forward Palo Alto Networks loguje się do agenta Syslog

Skonfiguruj sieci Palo Alto, aby przesyłać dalej wiadomości Syslog w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta Syslog:
1.  Przejdź do [przewodników konfiguracji Wspólnego Formatu zdarzeń (CEF)](https://docs.paloaltonetworks.com/resources/cef) i pobierz plik pdf dla swojego typu urządzenia. Postępuj zgodnie ze wszystkimi instrukcjami zawartymi w przewodniku, aby skonfigurować urządzenie Palo Alto Networks do zbierania zdarzeń CEF. 

1.  Przejdź do [konfigurowania monitorowania Syslog](https://aka.ms/asi-syslog-paloalto-forwarding) i wykonaj kroki 2 i 3, aby skonfigurować przekazywanie zdarzeń CEF z urządzenia Palo Alto Networks do usługi Azure Sentinel.

    1. Upewnij się, że **format serwera Syslog** jest ustawiony na **BSD**.

       > [!NOTE]
       > Operacje kopiowania/wklejania z pliku PDF mogą zmieniać tekst i wstawiać losowe znaki. Aby tego uniknąć, skopiuj tekst do edytora i usuń wszystkie znaki, które mogą spowodować przerwanie formatu dziennika przed wklejeniem go, jak widać w tym przykładzie.
 
        ![Problem z kopiowaniem tekstu w ramach cef](./media/connect-cef/paloalto-text-prob1.png)

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zdarzeń Palo Alto Networks, wyszukaj **pozycję CommonSecurityLog**.

1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).




## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia Palo Alto Networks z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


