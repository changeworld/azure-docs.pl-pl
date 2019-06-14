---
title: Łączniki w Interfejsie użytkownika Menedżera usługi synchronizacji usługi AD systemu Azure | Dokumentacja firmy Microsoft
description: Omówienie łączników kartę w Menedżerze usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60384190"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Za pomocą łączników za pomocą usługi Azure AD Connect synchronizacji Menedżera usług

![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Karta łączników jest używana do zarządzania wszystkimi systemami, aparat synchronizacji jest połączona z.

## <a name="connector-actions"></a>Akcje do łącznika
| Akcja | Komentarz |
| --- | --- |
| Przycisk Utwórz |Nie należy używać. Do łączenia się z dodatkowych lasów usługi AD, należy użyć Kreatora instalacji. |
| Właściwości |Używane do domeny i jednostki Organizacyjnej filtrowania. |
| [Usuwanie](#delete) |Używane do albo usunąć dane w przestrzeni łącznika lub usunąć połączenia do lasu. |
| [Konfigurowanie profilów uruchamiania](#configure-run-profiles) |Z wyjątkiem domeny filtrowanie, nic nie można skonfigurować w tym miejscu. Aby wyświetlić już skonfigurowane profile przebiegu, można użyć tej akcji. |
| Uruchom polecenie |Używane do uruchamiania jednorazowe wykonywania profilu. |
| Stop |Zatrzymuje aktualnie uruchomione profil łącznika. |
| Eksportowanie łączników |Nie należy używać. |
| Importowanie łącznika |Nie należy używać. |
| Zaktualizuj łącznik |Nie należy używać. |
| Odśwież schemat |Odświeża pamięci podręcznej schematu. Jest preferowana względem zamiast tego użyj opcji w Kreatorze instalacji również od, aktualizacje synchronizacji reguły. |
| [Wyszukiwania obszaru łącznika](#search-connector-space) |Używana do znajdowania obiektów i postępuj zgodnie z obiektu i jego danych za pośrednictwem systemu. |

### <a name="delete"></a>Usuwanie
Akcja usuwania jest używany do dwóch różnych rzeczy.  
![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Opcja **usunąć tylko obszaru łącznika** powoduje usunięcie wszystkich danych, ale zachować konfigurację.

Opcja **Usuń Connector i łącznika miejsca** usunięcie wszystkich danych i konfiguracji. Ta opcja jest używana, gdy użytkownik nie chce już połączenie z lasem.

Obie opcje synchronizować wszystkich obiektów i zaktualizować obiektów w magazynie metaverse. Ta akcja jest operacją wymagającą dużo czasu.

### <a name="configure-run-profiles"></a>Konfigurowanie profilów uruchamiania
Ta opcja umożliwia Zobacz profile przebiegu skonfigurowane dla łącznika.

![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Wyszukiwania obszaru łącznika
Akcja przestrzeni łącznika wyszukiwania jest przydatny do znajdowania obiektów i rozwiązywania problemów danych.

![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Rozpocznij, wybierając **zakres**. Możesz wyszukiwać na podstawie danych (RDN, nazwa Wyróżniająca, punktów kontrolnych, poddrzewie), lub stanu obiektu (wszystkie inne opcje).  
![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Jeśli na przykład wykonać wyszukiwanie poddrzewie, otrzymasz wszystkie obiekty w jednej jednostce Organizacyjnej.  
![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
W tym miejscu. Wybierz obiekt, wybierz **właściwości**, i [prześledzonych](tshoot-connect-object-not-syncing.md) z obszaru łącznika źródła, za pomocą obiektu metaverse i docelowej przestrzeni łącznika.

### <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Jeśli zmienisz hasło do konta, usługa synchronizacji nie będzie mógł importować/eksportować zmian do środowiska lokalnego usługi AD.   Mogą wystąpić następujące problemy:

- Krok importu/eksportu dla łącznika usługi AD kończy się niepowodzeniem z powodu błędu "nie-start poświadczenia".
- W obszarze podglądu zdarzeń Windows w dzienniku zdarzeń aplikacji zawiera błąd z 6000 identyfikator zdarzenia i komunikatem "agenta zarządzania"contoso.com"nie można uruchomić, ponieważ poświadczenia są nieprawidłowe."

Aby rozwiązać ten problem, zaktualizuj konto użytkownika usług AD DS przy użyciu następujących:


1. Uruchom Menedżera usługi synchronizacji (usługa synchronizacji → START).
</br>![Menedżera usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Przejdź do **łączników** kartę.
3. Wybierz łącznik usługi AD, który jest skonfigurowany do używania konta usługi AD DS.
4. W obszarze akcji wybierz **właściwości**.
5. W oknie podręcznym wybierz nawiązywanie połączenia z lasu usługi Active Directory:
6. Nazwa lasu wskazuje odpowiadającego lokalna Usługa AD.
7. Nazwa użytkownika wskazuje, że konto usług AD DS, używane na potrzeby synchronizacji.
8. Wprowadź nowe hasło konta usługi AD DS w pole tekstowe z hasłem ![platformy Azure AD Connect Sync szyfrowania klucza narzędzia](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kliknij przycisk OK, aby zapisać nowe hasło, a następnie uruchom ponownie usługi synchronizacji, aby usunąć stare hasło z pamięci podręcznej.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacji programu Azure AD Connect](how-to-connect-sync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
