---
title: Wdróż agenta, aby połączyć dane CEF z platformą Azure wskaźnikiem wersji zapoznawczej | Microsoft Docs
description: Dowiedz się, jak wdrożyć agenta, aby połączyć dane CEF z platformą Azure.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588471"
---
# <a name="step-1-deploy-the-agent"></a>Krok 1. wdrażanie agenta


W tym kroku należy wybrać maszynę z systemem Linux, która będzie pełnić rolę serwera proxy między wskaźnikiem danych platformy Azure a rozwiązaniem zabezpieczeń. Na komputerze proxy należy uruchomić skrypt, który:
- Instaluje agenta Log Analytics i konfiguruje go w razie potrzeby w celu nasłuchiwania komunikatów dziennika systemowego.
- Konfiguruje demona dziennika systemowego do nasłuchiwania komunikatów dziennika systemu przy użyciu portu TCP 514, a następnie przekazuje tylko komunikaty CEF do agenta Log Analytics przy użyciu portu TCP 25226.
- Ustawia agenta dziennika systemowego do zbierania danych i bezpiecznego wysyłania go do usługi Azure wskaźnikowej, w której jest analizowany i wzbogacony.
 
## <a name="deploy-the-agent"></a>Wdrażanie agenta
 
1. W portalu Azure wskaźnikowym kliknij pozycję **Łączniki danych** i wybierz pozycję **Common Event format (CEF)** , a następnie **Otwórz stronę łącznik**. 

1. W obszarze **Instalowanie i Konfigurowanie agenta dziennika**systemu wybierz typ maszyny, platformę Azure, inną chmurę lub lokalnie. 
   > [!NOTE]
   > Ponieważ skrypt w następnym kroku instaluje agenta Log Analytics i łączy maszynę z obszarem roboczym wskaźnikowego platformy Azure, upewnij się, że ta maszyna nie jest połączona z żadnym innym obszarem roboczym.
1. Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. Upewnij się, że na maszynie jest zainstalowany program Python, przy użyciu następującego polecenia: `python –version`

1. Uruchom na komputerze proxy następujący skrypt.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Gdy skrypt jest uruchomiony, upewnij się, że nie są wyświetlane żadne komunikaty o błędach lub ostrzeżeniach.

Przejdź do [kroku 2: Skonfiguruj rozwiązanie zabezpieczeń do przesyłania dalej komunikatów CEF](connect-cef-solution-config.md) .


## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia urządzeń CEF z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).

