---
title: 'Synchronizacja programu Azure AD Connect: Zmienianie hasła do konta usług AD DS | Dokumentacja firmy Microsoft'
description: Dokument ten temat opisuje sposób aktualizacji Azure AD Connect, po zmianie hasła konta usług AD DS.
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
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a4d0d062b28b03de7f1e606202dddae28bf6a2f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592463"
---
# <a name="changing-the-ad-ds-account-password"></a>Zmiana hasła do konta usług AD DS
Konta usług AD DS odnosi się do konta użytkownika, używany przez program Azure AD Connect do komunikowania się z lokalną usługą Active Directory. Jeśli zmienisz hasło do konta usług AD DS, należy zaktualizować Azure AD Connect usługi synchronizacji przy użyciu nowego hasła. W przeciwnym razie synchronizacja nie będzie można poprawnie synchronizowane z lokalnej usługi Active Directory i można napotkać następujące błędy:

* W operacji Synchronization Service Manager, importu lub eksportu z lokalnej usługi AD kończy się niepowodzeniem z **logowanie bez poświadczeń na początku** błędu.

* W Podglądzie zdarzeń systemu Windows, w dzienniku zdarzeń aplikacji zawiera błąd **6000 identyfikator zdarzenia** i komunikat **"agenta zarządzania"contoso.com"nie można uruchomić, ponieważ poświadczenia były nieprawidłowe"**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak zaktualizować usługę synchronizacji z nowe hasło dla konta usług AD DS
Aby zaktualizować usługę synchronizacji przy użyciu nowego hasła:

1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  

2. Przejdź do **łączniki** kartę.

3. Wybierz **łącznika AD** odnosi się do konta usług AD DS, dla którego jego hasło zostało zmienione.

4. W obszarze **akcje**, wybierz pozycję **właściwości**.

5. W oknie podręcznym wybierz **Połącz z lasu usługi Active Directory**:

6. Wprowadź nowe hasło do konta usług AD DS w **hasło** pola tekstowego.

7. Kliknij przycisk **OK** Aby zapisać nowe hasło i zamknąć okno podręczne.

8. Ponowne uruchomienie programu Azure AD Connect usługi synchronizacji w obszarze Menedżer sterowania usługami systemu Windows. To, aby upewnić się, że wszystkie odwołania do stare hasło zostanie usunięty z pamięci podręcznej.

## <a name="next-steps"></a>Kolejne kroki
**Tematy poglądowe**

* [Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](active-directory-aadconnectsync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
