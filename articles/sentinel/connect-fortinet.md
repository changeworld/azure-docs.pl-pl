---
title: Łączenie danych Fortinet z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane Fortinet z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588199"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Łączenie fortinet z usługą Azure Sentinel



W tym artykule wyjaśniono, jak połączyć urządzenie Fortinet z usługą Azure Sentinel. Łącznik danych Fortinet umożliwia łatwe łączenie dzienników Fortinet z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie niestandardowych alertów i usprawnianie badania. Korzystanie z fortinet na platformie Azure Sentinel zapewni Ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Forward Fortinet loguje się do agenta Syslog

Skonfiguruj fortinet do przekazywania wiadomości Syslog w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta Syslog.

1. Otwórz interfejsu wiersza polecenia na urządzeniu Fortinet i uruchom następujące polecenia:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Zastąp **adres IP** serwera adresem IP agenta.
    - Ustaw **port syslog** na **514** lub port ustawiony na agencie.
    - Aby włączyć format CEF we wczesnych wersjach fortiOS, może być konieczne uruchomienie zestawu poleceń **csv disable**.
 
   > [!NOTE] 
   > Aby uzyskać więcej informacji, przejdź do [biblioteki dokumentów Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Wybierz swoją wersję i użyj **podręcznika** i **odwołania do komunikatu dziennika**.

1. Aby użyć odpowiedniego schematu w usłudze Azure Monitor Log `CommonSecurityLog`Analytics dla zdarzeń Fortinet, wyszukaj .

1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).


## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak połączyć urządzenia Fortinet z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


