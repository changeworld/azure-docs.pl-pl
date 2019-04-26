---
title: 'Synchronizacja programu Azure AD Connect:  Zmienianie hasła konta usług AD DS | Dokumentacja firmy Microsoft'
description: W tym dokumencie tematu opisano, jak zaktualizować program Azure AD Connect po zmianie hasła konta usług AD DS.
services: active-directory
keywords: Usługi AD DS konta, konto usługi Active Directory, hasło
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/09/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60241588"
---
# <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Konto usług AD DS odnosi się do konta użytkownika, używany przez program Azure AD Connect do komunikowania się z usługą Active Directory w środowisku lokalnym. Jeśli zmienisz hasło do konta usług AD DS, należy zaktualizować usługi Azure AD Connect synchronizacji przy użyciu nowego hasła. W przeciwnym razie synchronizacji może już nie są poprawnie synchronizowane z usługą Active Directory w środowisku lokalnym i można napotkać następujące błędy:

- W operacji Synchronization Service Manager, wszelkie importu lub eksportu przy użyciu lokalnej usługi AD zakończy się niepowodzeniem z **nie-start-credentials** błędu.

- W obszarze Windows podglądu zdarzeń, w dzienniku zdarzeń aplikacji zawiera błąd przy użyciu **6000 identyfikator zdarzenia** i komunikat **"agenta zarządzania"contoso.com"nie można uruchomić, ponieważ poświadczenia są nieprawidłowe"**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak zaktualizować usługę synchronizacji przy użyciu nowego hasła dla konta usługi AD DS
Aby zaktualizować usługę synchronizacji przy użyciu nowego hasła:

1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Przejdź do **łączników** kartę.

3. Wybierz **łącznika AD** odnosi się do konta usługi AD DS, dla których zmieniono jego hasło.

4. W obszarze **akcje**, wybierz opcję **właściwości**.

5. W oknie podręcznym wybierz **nawiązywanie połączenia z lasu usługi Active Directory**:

6. Wprowadź nowe hasło konta usługi AD DS w **hasło** pola tekstowego.

7. Kliknij przycisk **OK** Aby zapisać nowe hasło i zamknąć okno podręczne.

8. Ponowne uruchomienie programu Azure AD Connect usługi synchronizacji w obszarze Menedżer sterowania usługami Windows. To, aby upewnić się, że wszystkie odwołania do stare hasło zostanie usunięty z pamięci podręcznej.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

- [Synchronizacja w programie Azure AD Connect: Zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

