---
title: 'Azure AD Connect agenta aprowizacji w chmurze: automatyczne uaktualnianie | Microsoft Docs'
description: W tym artykule opisano wbudowaną funkcję automatycznego uaktualniania w ramach agenta aprowizacji Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190317"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agenta aprowizacji w chmurze: automatyczne uaktualnianie

Upewnienie się, że Azure Active Directory (Azure AD) łączenie z agentem aprowizacji usług w chmurze jest zawsze aktualne, dzięki funkcji automatycznego uaktualniania.

Agent jest instalowany w tym miejscu: "program files\Azure AD Connecting Agent\AADConnectProvisioningAgent.exe"

Aby sprawdzić wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz polecenie Właściwości, a następnie Szczegóły.

![Wersja pliku agenta](media/how-to-automatic-upgrade/agent1.png)

Agent Aktualizator jest zainstalowany w tym miejscu: "program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Aby sprawdzić wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz polecenie Właściwości, a następnie Szczegóły.

![Wersja agenta Aktualizator](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Odinstalowywanie agenta
Aby usunąć agenta, przejdź do pozycji **Odinstaluj lub zmień program** i Odinstaluj następujące elementy:

- **Microsoft Azure AD Aktualizator połączenia z agentem**
- **Microsoft Azure AD połączyć się z agentem aprowizacji**
- **Microsoft Azure AD Połącz pakiet agenta aprowizacji**

![Usuwanie agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

