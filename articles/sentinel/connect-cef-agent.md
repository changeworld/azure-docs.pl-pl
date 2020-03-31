---
title: Wdrażanie agenta w celu połączenia danych cef z usługą Azure Sentinel Preview| Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć agenta, aby połączyć dane CEF z usługą Azure Sentinel.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b0c9335357cb793ea76e1dbe68575f716a50372a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588471"
---
# <a name="step-1-deploy-the-agent"></a>Krok 1: Wdrażanie agenta


W tym kroku należy wybrać komputer z systemem Linux, który będzie działać jako serwer proxy między usługą Azure Sentinel i rozwiązania zabezpieczeń. Będziesz musiał uruchomić skrypt na komputerze proxy, który:
- Instaluje agenta usługi Log Analytics i konfiguruje go w razie potrzeby do nasłuchiwać wiadomości Syslog.
- Konfiguruje demona Syslog do nasłuchiwać wiadomości Syslog przy użyciu portu TCP 514, a następnie przesyła dalej tylko komunikaty CEF do agenta usługi Log Analytics przy użyciu portu TCP 25226.
- Ustawia agenta Syslog do zbierania danych i wysyłania ich bezpiecznie do usługi Azure Sentinel, gdzie jest analizowany i wzbogacony.
 
## <a name="deploy-the-agent"></a>Wdrażanie agenta
 
1. W portalu Azure Sentinel kliknij pozycję **Łączniki danych** i wybierz pozycję **Wspólny format zdarzeń (CEF),** a następnie **otwórz stronę łącznika**. 

1. W obszarze **Zainstaluj i skonfiguruj agenta Syslog**wybierz typ komputera, azure, inną chmurę lub lokalnie. 
   > [!NOTE]
   > Ponieważ skrypt w następnym kroku instaluje agenta usługi Log Analytics i łączy komputer z obszarem roboczym usługi Azure Sentinel, upewnij się, że ten komputer nie jest połączony z żadnym innym obszarem roboczym.
1. Musisz mieć podwyższone uprawnienia (sudo) na komputerze. Upewnij się, że masz Pythona na komputerze za pomocą następującego polecenia:`python –version`

1. Uruchom następujący skrypt na komputerze proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Gdy skrypt jest uruchomiony, sprawdź, czy nie otrzymasz żadnych komunikatów o błędzie ani ostrzeżeniach.

Przejdź do [kroku 2: Skonfiguruj rozwiązanie zabezpieczające do przesyłania dalej komunikatów CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć urządzenia CEF z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).

