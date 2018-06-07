---
title: Łączniki w interfejsie użytkownika programu Azure AD Synchronization Service Manager | Dokumentacja firmy Microsoft
description: Dowiedz się na karcie łączniki Menedżera usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3c8925b213543d33f81c113b08e9601f2780bde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593156"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Używanie łączników przy użyciu usługi Azure AD Connect synchronizacji Menedżera usług

![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

Karta łączników jest używana do zarządzania wszystkimi systemami aparat synchronizacji jest połączona z.

## <a name="connector-actions"></a>Akcje łącznika
| Akcja | Komentarz |
| --- | --- |
| Przycisk Utwórz |Nie używaj. Do połączenia dodatkowych lasów usługi AD, należy użyć Kreatora instalacji. |
| Właściwości |Używany do domeny i jednostki Organizacyjnej filtrowania. |
| [Usuwanie](#delete) |Używane do albo usunąć dane w przestrzeni łącznika lub usunąć połączenia do lasu. |
| [Konfigurowanie profilów uruchamiania](#configure-run-profiles) |Z wyjątkiem domeny filtrowania, nic nie można skonfigurować w tym miejscu. Ta akcja umożliwia Zobacz już skonfigurowane profile uruchamiania. |
| Uruchom polecenie |Używane do uruchamiania jednorazowe Uruchom profilu. |
| Stop |Zatrzymuje łącznika aktualnie uruchomione profilu. |
| Łącznik eksportu |Nie używaj. |
| Importuj łącznika |Nie używaj. |
| Łącznik aktualizacji |Nie używaj. |
| Odśwież schemat |Odświeża pamięci podręcznej schematu. Jest preferowany, aby zamiast tego należy użyć opcji w Kreatorze instalacji również od który aktualizacje synchronizacji reguły. |
| [Obszar łączników wyszukiwania](#search-connector-space) |Używana do znajdowania obiektów i [wykonaj obiekt i jego danych za pośrednictwem systemu](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Usuwanie
Akcja usuwania, która jest używana do dwóch różnych rzeczy.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Opcja **usunąć tylko przestrzeni łącznika** powoduje usunięcie wszystkich danych, ale zachować konfigurację.

Opcja **usunąć Connector i łącznika miejsca** powoduje usunięcie danych i konfiguracji. Ta opcja jest używana, gdy użytkownik nie chce już połączenie z lasem.

Obie te opcje synchronizować wszystkie obiekty i zaktualizować obiektów metaverse. Ta akcja jest operacją wymagającą dużo czasu.

### <a name="configure-run-profiles"></a>Konfigurowanie profilów uruchamiania
Ta opcja umożliwia Zobacz profile uruchamiania skonfigurowane dla łącznika.

![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Obszar łączników wyszukiwania
Akcja wyszukiwania łącznika miejsca jest przydatne do znalezienia obiektów i rozwiązywania problemów danych.

![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Najpierw wybrać **zakres**. Można wyszukiwać na podstawie danych (RDN, nazwa Wyróżniająca, zakotwiczenia, poddrzewa), lub stanu obiektu (wszystkie inne opcje).  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Jeśli na przykład wykonać wyszukiwanie poddrzewa, możesz uzyskać wszystkie obiekty w jednej jednostce Organizacyjnej.  
![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
W tym miejscu. Wybierz obiekt, wybierz **właściwości**, i [po nim](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) z obszaru łącznika źródło, za pomocą środowiska metaverse, a do docelowej przestrzeni łącznika.

### <a name="changing-the-ad-ds-account-password"></a>Zmiana hasła do konta usług AD DS
Jeśli zmienisz hasło konta usługi synchronizacji nie będzie mógł importu/eksportu zmiany w lokalnej usługi AD.   Użytkownik może wystąpić następujący błąd:

- Krok importu/eksportu dla łącznika usługi AD nie powiedzie się z powodu błędu "nie-start — poświadczenia".
- W Podglądzie zdarzeń systemu Windows w dzienniku zdarzeń aplikacji zawiera błąd z 6000 identyfikator zdarzenia i komunikatem "agenta zarządzania"contoso.com"nie można uruchomić, ponieważ poświadczenia były nieprawidłowe."

Aby rozwiązać ten problem, zaktualizuj konto użytkownika usług AD DS za pomocą następujących czynności:


1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)
2. Przejdź do **łączniki** kartę.
3. Wybierz łącznik AD, która jest skonfigurowana pod kątem używania konta usług AD DS.
4. W obszarze akcje, wybierz **właściwości**.
5. W oknie podręcznym wybierz opcję Połącz do lasu usługi Active Directory:
6. Nazwa lasu wskazuje odpowiedniej lokalnej usługi AD.
7. Nazwa użytkownika wskazuje, że konto usługi AD DS, używane do synchronizacji.
8. Wprowadź nowe hasło do konta usług AD DS w pole tekstowe hasła ![Azure AD Connect synchronizacji szyfrowania klucz narzędzia](media/active-directory-aadconnectsync-encryption-key/key6.png)
9. Kliknij przycisk OK, aby zapisać nowe hasło i uruchomić ponownie usługi synchronizacji, aby usunąć stare hasło z pamięci podręcznej.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
