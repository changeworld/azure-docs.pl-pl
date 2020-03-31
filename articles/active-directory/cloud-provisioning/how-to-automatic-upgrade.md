---
title: 'Agent inicjowania obsługi administracyjnej chmury usługi Azure AD Connect: automatyczne uaktualnianie | Dokumenty firmy Microsoft'
description: W tym artykule opisano wbudowaną funkcję automatycznego uaktualniania w agencie inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190317"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agent inicjowania obsługi administracyjnej w chmurze usługi Azure AD Connect: automatyczne uaktualnianie

Upewnienie się, że instalacja agenta inicjowania obsługi administracyjnej usługi Azure Active Directory (Azure AD) Connect jest zawsze aktualna, jest łatwa dzięki funkcji automatycznego uaktualniania.

Agent jest zainstalowany w tym miejscu: "Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Aby zweryfikować wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz właściwości, a następnie szczegóły.

![Wersja pliku agenta](media/how-to-automatic-upgrade/agent1.png)

Aktualizacja agenta jest zainstalowana w tym miejscu: "Pliki programu\Agent aprowizowania usługi Azure AD Connect Updater\AzureADConnectAgentUpdater.exe"

Aby zweryfikować wersję, kliknij prawym przyciskiem myszy plik wykonywalny i wybierz właściwości, a następnie szczegóły.

![Wersja programu aktualizacji agenta](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Odinstalowywanie agenta
Aby usunąć agenta, przejdź do **strony Odinstaluj lub zmień program** i odinstaluj następujące elementy:

- **Aktualizacja agenta usługi Microsoft Azure AD Connect**
- **Agent inicjowania obsługi administracyjnej usługi Microsoft Azure AD Connect**
- **Pakiet agenta aprowizacji usługi Microsoft Azure AD Connect**

![Usunięcie agenta](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)

