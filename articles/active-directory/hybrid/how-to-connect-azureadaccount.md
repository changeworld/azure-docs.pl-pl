---
title: 'Synchronizacja programu Azure AD Connect: Jak zarządzać kontem usługi Azure AD | Dokumentacja firmy Microsoft'
description: W tym temacie opisano jak przywrócić konto usługi Azure AD.
services: active-directory
keywords: AADSTS70002, AADSTS50054, jak zresetować hasło do synchronizacji Azure AD Connect konta usługi łącznika
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 3ced4d7be9864f16b4fbd7a91ae916e1b4f65d18
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177920"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Synchronizacja programu Azure AD Connect: Jak zarządzać kontem usługi Azure AD
Konto usługi używane przez łącznik usługi Azure AD powinien być usługę bezpłatnie. Jeśli potrzebujesz zresetować swoje poświadczenia, w tym temacie jest dla Ciebie. Na przykład jeśli przez pomyłkę administratora globalnego resetowania haseł na koncie usługi przy użyciu programu PowerShell.

## <a name="reset-the-credentials"></a>Zresetuj poświadczenia
Jeśli konto usługi zdefiniowane w programie Azure AD Connector nie może skontaktować się z usługi Azure AD z powodu problemów dotyczących uwierzytelniania, można zresetować hasło.

1. Zaloguj się do serwera synchronizacji Azure AD Connect i uruchom program PowerShell.
2. Uruchom polecenie `Add-ADSyncAADServiceAccount`.  
   ![Addadsyncaadserviceaccount polecenia cmdlet programu PowerShell](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Podaj poświadczenia administratora globalnego usługi Azure AD.

To polecenie cmdlet resetuje hasło dla konta usługi i zaktualizować go zarówno w usłudze Azure AD, jak i w aparacie synchronizacji.

## <a name="known-issues-these-steps-can-solve"></a>Znane problemy te kroki można rozwiązać.
W tej sekcji znajduje się lista błędów zgłaszanych przez klientów, które zostały rozwiązane przez poświadczenia, zresetuj na konto usługi Azure AD.

- - -
Zdarzenie 6900  
Serwer napotkał nieoczekiwany błąd podczas przetwarzania powiadomienia o zmianie hasła:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Podczas uwierzytelniania użyto starego hasła.

- - -
Zdarzenie 659  
Wystąpił błąd podczas pobierania konfiguracji synchronizacji zasad haseł. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS50054: Podczas uwierzytelniania użyto starego hasła.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

