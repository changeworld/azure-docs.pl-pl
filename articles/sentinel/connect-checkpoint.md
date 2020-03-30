---
title: Łączenie danych punktu kontrolnego z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane punktu kontrolnego z usługą Azure Sentinel.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588420"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Połącz punkt kontrolny z usługą Azure Sentinel



W tym artykule wyjaśniono, jak połączyć urządzenie check point z usługą Azure Sentinel. Łącznik danych punktu kontrolnego umożliwia łatwe łączenie dzienników punktów kontrolnych z usługą Azure Sentinel, wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i usprawnianie badania. Korzystanie z funkcji Check Point na platformie Azure Sentinel zapewni ci więcej informacji na temat korzystania z Internetu w organizacji i zwiększy jej możliwości obsługi zabezpieczeń. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Forward Check Point loguje się do agenta Syslog

Skonfiguruj urządzenie Check Point do przekazywania wiadomości Syslog w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta Syslog.

1. Przejdź do [punktu eksportu dziennika punktu kontrolnego](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Przewiń w dół do **wdrożenia podstawowego** i postępuj zgodnie z instrukcjami, aby skonfigurować połączenie, korzystając z następujących wskazówek:
   - Ustaw **port Syslog** na **514** lub port ustawiony na agencie.
     - Zastąp **nazwę** i **adres IP serwera docelowego** w wierszu polecenia nazwą agenta Syslog i adresem IP.
     - Ustaw format na **CEF**.
1. Jeśli używasz wersji R77.30 lub R80.10, przewiń w górę do **instalacji** i postępuj zgodnie z instrukcjami, aby zainstalować eksportera dziennika dla swojej wersji.
1. Przejdź do [kroku 3: Sprawdź poprawność łączności](connect-cef-verify.md).
 

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia punktu kontrolnego z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- [Sprawdź poprawność łączności](connect-cef-verify.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)


