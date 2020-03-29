---
title: Zmienianie hasła do konta usługi Azure AD Connector | Dokumenty firmy Microsoft
description: W tym temacie opisano sposób przywracania konta usługi Azure AD Łącznik.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204529"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Zmienianie hasła do konta łącznika usługi Azure AD
Konto usługi Azure AD Connector ma być bezpłatne usługi. Jeśli chcesz zresetować jego poświadczenia, to ten temat jest dla Ciebie. Na przykład jeśli administrator globalny przez pomyłkę zresetował hasło do konta przy użyciu programu PowerShell.

## <a name="reset-the-credentials"></a>Resetowanie poświadczeń
Jeśli konto usługi Azure AD Connector nie może skontaktować się z usługą Azure AD z powodu problemów z uwierzytelnianiem, hasło można zresetować.

1. Zaloguj się do serwera synchronizacji usługi Azure AD Connect i uruchom program PowerShell.
2. Uruchom polecenie `Add-ADSyncAADServiceAccount`.  
   ![Konto dodatkowe poleceń cmdlet programu PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Podaj poświadczenia administratora usługi Azure AD Global.

To polecenie cmdlet resetuje hasło do konta usługi i aktualizuje je zarówno w usłudze Azure AD, jak i w silniku synchronizacji.

## <a name="known-issues-these-steps-can-solve"></a>Znane problemy, które te kroki mogą rozwiązać
Ta sekcja jest lista błędów zgłoszonych przez klientów, które zostały naprawione przez poświadczenia zresetować na koncie usługi Azure AD Łącznika.

---
Zdarzenie 6900  
Serwer napotkał nieoczekiwany błąd podczas przetwarzania powiadomienia o zmianie hasła:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Stare hasło jest używane do uwierzytelniania.

---
Zdarzenie 659  
Błąd podczas pobierania konfiguracji synchronizacji zasad haseł. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Stare hasło jest używane do uwierzytelniania.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

