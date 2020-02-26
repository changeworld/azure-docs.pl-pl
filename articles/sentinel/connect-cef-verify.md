---
title: Sprawdź poprawność łączności z platformą Azure — wskaźnikiem | Microsoft Docs
description: Sprawdź poprawność łączności rozwiązania zabezpieczeń, aby upewnić się, że komunikaty CEF są przekazywane do usługi Azure wskaźnikowej.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588437"
---
# <a name="step-3-validate-connectivity"></a>Krok 3. Weryfikowanie łączności



Po wdrożeniu agenta i skonfigurowaniu rozwiązania zabezpieczającego do przesyłania dalej wiadomości CEF należy skorzystać z tego artykułu, aby dowiedzieć się, jak sprawdzić łączność między wskaźnikiem i rozwiązaniem bezpieczeństwa platformy Azure. 

## <a name="how-to-validate-connectivity"></a>Sprawdzanie poprawności łączności

1. Otwórz Log Analytics, aby upewnić się, że dzienniki są odbierane przy użyciu schematu CommonSecurityLog.<br> Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

1. Przed uruchomieniem skryptu zalecamy wysłanie komunikatów z rozwiązania zabezpieczeń, aby upewnić się, że są one przekazywane do skonfigurowanego komputera proxy dziennika systemu. 
1. Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. Upewnij się, że na maszynie jest zainstalowany program Python, przy użyciu następującego polecenia: `python –version`
1. Uruchom następujący skrypt, aby sprawdzić łączność między agentem, wskaźnikiem kontroli platformy Azure i rozwiązaniem zabezpieczeń. Sprawdza, czy przekazanie demona została prawidłowo skonfigurowana, nasłuchuje na prawidłowych portach i nie blokuje komunikacji między demonem a agentem Log Analytics. Skrypt wysyła również komunikat "TestCommonEventFormat" służący do przetestowania kompleksowej łączności. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.

