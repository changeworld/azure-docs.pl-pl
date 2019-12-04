---
title: 'Azure AD Connect agenta aprowizacji w chmurze: automatyczne uaktualnianie | Microsoft Docs'
description: W tym temacie opisano wbudowaną funkcję automatycznego uaktualniania dostępną w ramach agenta aprowizacji Azure AD Connect Cloud.
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794460"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect agenta aprowizacji w chmurze: automatyczne uaktualnianie

Upewnienie się, że Azure AD Connect Instalacja agenta aprowizacji w chmurze jest zawsze aktualna, a funkcja **automatycznego uaktualniania** nigdy nie była łatwiejsza. Ta funkcja jest domyślnie włączona i nie można jej wyłączyć.

Agent jest instalowany w tym miejscu: **"program FILES\AZURE AD Connecting Agent\AADConnectProvisioningAgent.exe"**

Aby sprawdzić wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz polecenie Właściwości, a następnie Szczegóły.

![Wersja pliku agenta](media/how-to-automatic-upgrade/agent1.png)

Agent Aktualizator jest zainstalowany w tym miejscu: **"program FILES\AZURE AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"**

Aby sprawdzić wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz polecenie Właściwości, a następnie Szczegóły.

![Wersja agenta Aktualizator](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Odinstalowywanie agenta
Aby usunąć agenta, przejdź do **Odinstaluj lub zmień program** i Odinstaluj następujące elementy:

- Microsoft Azure AD Aktualizator połączenia z agentem
- Microsoft Azure AD połączyć się z agentem aprowizacji
- Microsoft Azure AD Połącz pakiet agenta aprowizacji

![Usuwanie agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)

