---
title: Sprawdzanie poprawności łączności z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Sprawdź poprawność łączności rozwiązania zabezpieczeń, aby upewnić się, że wiadomości CEF są przekazywane do usługi Azure Sentinel.
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
ms.openlocfilehash: e224f6d5cfd82dfc6cb1ce107d111ee0e031247b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588437"
---
# <a name="step-3-validate-connectivity"></a>KROK 3: Sprawdzanie poprawności łączności



Po wdrożeniu agenta i skonfigurowaniu rozwiązania zabezpieczeń do przekazywania wiadomości cef, użyj tego artykułu, aby dowiedzieć się, jak zweryfikować łączność między usługą Azure Sentinel a rozwiązaniem zabezpieczającym. 

## <a name="how-to-validate-connectivity"></a>Jak sprawdzić poprawność łączności

1. Otwórz usługi Log Analytics, aby upewnić się, że dzienniki są odbierane przy użyciu schematu CommonSecurityLog.<br> Może upłynąć ponad 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 

1. Przed uruchomieniem skryptu zaleca się wysyłanie wiadomości z rozwiązania zabezpieczeń, aby upewnić się, że są one przekazywane do skonfigurowanego komputera proxy Syslog. 
1. Musisz mieć podwyższone uprawnienia (sudo) na komputerze. Upewnij się, że masz Pythona na komputerze za pomocą następującego polecenia:`python –version`
1. Uruchom następujący skrypt, aby sprawdzić łączność między agentem, usługa Azure Sentinel i rozwiązaniem zabezpieczającym. Sprawdza, czy przekierowanie demona jest poprawnie skonfigurowane, nasłuchuje na odpowiednich portach i że nic nie blokuje komunikacji między demonem a agentem usługi Log Analytics. Skrypt wysyła również makiety wiadomości "TestCommonEventFormat", aby sprawdzić łączność end-to-end. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

