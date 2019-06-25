---
title: Zmień hasło konta łącznika usługi Azure AD | Dokumentacja firmy Microsoft
description: Ten temat dokumenty, jak przywrócić konta łącznika usługi Azure AD.
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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204529"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Zmienianie hasła do konta łącznika usługi Azure AD
Konta łącznika usługi Azure AD powinien być usługę bezpłatnie. Jeśli potrzebujesz zresetować swoje poświadczenia, w tym temacie jest dla Ciebie. Na przykład jeśli przez pomyłkę administratora globalnego zresetować hasło konta, przy użyciu programu PowerShell.

## <a name="reset-the-credentials"></a>Zresetuj poświadczenia
Jeśli konto usługi Azure AD Connector nie może skontaktować się z usługi Azure AD, ze względu na problemy z uwierzytelnianiem, można zresetować hasło.

1. Zaloguj się do serwera synchronizacji Azure AD Connect i uruchom program PowerShell.
2. Uruchom polecenie `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount polecenia cmdlet programu PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Podaj poświadczenia administratora globalnego usługi Azure AD.

To polecenie cmdlet resetuje hasło dla konta usługi i zaktualizować go zarówno w usłudze Azure AD, jak i w aparacie synchronizacji.

## <a name="known-issues-these-steps-can-solve"></a>Znane problemy te kroki można rozwiązać.
W tej sekcji znajduje się lista błędów zgłaszanych przez klientów, które zostały rozwiązane przez poświadczenia, resetowania konta łącznika usługi Azure AD.

---
Zdarzenie 6900  
Serwer napotkał nieoczekiwany błąd podczas przetwarzania powiadomienia o zmianie hasła:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Podczas uwierzytelniania użyto starego hasła.

---
Zdarzenie 659  
Wystąpił błąd podczas pobierania konfiguracji synchronizacji zasad haseł. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Podczas uwierzytelniania użyto starego hasła.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

