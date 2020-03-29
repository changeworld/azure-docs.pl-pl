---
title: 'Synchronizacja usługi Azure AD Connect: zmiana hasła do konta usług AD DS | Dokumenty firmy Microsoft'
description: W tym dokumencie tematu opisano sposób aktualizowania usługi Azure AD Connect po zmianie hasła do konta usług AD DS.
services: active-directory
keywords: Konto usług AD DS, konto usługi Active Directory, hasło
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241588"
---
# <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Konto usług AD DS odnosi się do konta użytkownika używanego przez usługę Azure AD Connect do komunikowania się z lokalną usługą Active Directory. Jeśli zmienisz hasło konta usług AD DS, musisz zaktualizować usługę synchronizacji usługi Azure AD Connect za pomocą nowego hasła. W przeciwnym razie synchronizacja nie może już poprawnie synchronizować się z lokalną usługą Active Directory i wystąpią następujące błędy:

* W Menedżerze usług synchronizacji każda operacja importowania lub eksportowania z lokalną usługą AD kończy się niepowodzeniem z błędem **poświadczeń bez uruchomienia.**

* W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń aplikacji zawiera błąd o **identyfikatorze zdarzenia 6000** i komunikat **"Agent zarządzania "contoso.com" nie można uruchomić, ponieważ poświadczenia były nieprawidłowe.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Jak zaktualizować usługę synchronizacji za pomocą nowego hasła dla konta USŁUG AD DS
Aby zaktualizować usługę synchronizacji za pomocą nowego hasła:

1. Uruchom Menedżera usług synchronizacji (START → Usługa synchronizacji).
</br>![Menedżer usługi synchronizacji](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Przejdź do karty **Łączniki.**

3. Wybierz **łącznik usługi AD** odpowiadający kontu usług AD DS, dla którego jego hasło zostało zmienione.

4. W obszarze **Akcje**wybierz pozycję **Właściwości**.

5. W wyskakującym oknie dialogowym wybierz pozycję **Połącz z lasem usługi Active Directory:**

6. Wprowadź nowe hasło konta usług AD DS w polach tekstowych **Hasło.**

7. Kliknij **przycisk OK,** aby zapisać nowe hasło i zamknąć okno podręczne.

8. Uruchom ponownie usługę synchronizacji usługi Azure AD Connect w menedżerze sterowania usługami systemu Windows. Ma to na celu zapewnienie, że wszelkie odwołania do starego hasła są usuwane z pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)

* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
