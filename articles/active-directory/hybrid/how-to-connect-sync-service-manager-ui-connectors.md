---
title: Łączniki w interfejsie użytkownika usługi Azure SYNCHRONIZATION Service Manager | Dokumenty firmy Microsoft
description: Poznaj kartę Łączniki w menedżerze usługi Synchronizacja usługi Azure AD Connect.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261049"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Korzystanie z łączników z menedżerem usługi Azure AD Connect Sync

![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Karta Łączniki służy do zarządzania wszystkimi systemami, z którymi jest podłączony aparat synchronizacji.

## <a name="connector-actions"></a>Akcje łącznika
| Akcja | Komentarz |
| --- | --- |
| Utwórz |Nie używaj. Aby połączyć się z dodatkowymi lasami usługi AD, użyj kreatora instalacji. |
| Właściwości |Służy do filtrowania domeny i usługi organizacyjnej. |
| [Usuwanie](#delete) |Służy do usuwania danych w przestrzeni łącznika lub do usuwania połączenia z lasem. |
| [Konfigurowanie profilów uruchamiania](#configure-run-profiles) |Z wyjątkiem filtrowania domen, nic do skonfigurowania tutaj. Za pomocą tej akcji można wyświetlić już skonfigurowane profile uruchamiania. |
| Run |Służy do uruchamiania jednorazowego uruchomienia profilu. |
| Stop |Zatrzymuje łącznik aktualnie uruchomiony profil. |
| Złącze eksportu |Nie używaj. |
| Zaimportuj łącznik |Nie używaj. |
| Łącznik aktualizacji |Nie używaj. |
| Odśwież schemat |Odświeża buforowany schemat. Zaleca się użycie tej opcji w kreatorze instalacji, ponieważ powoduje to również aktualizację reguł synchronizacji. |
| [Miejsce łącznika wyszukiwania](#search-connector-space) |Służy do znajdowania obiektów i śledzenia obiektu i jego danych za pośrednictwem systemu. |

### <a name="delete"></a>Usuń
Akcja usuwania jest używana dla dwóch różnych rzeczy.  
![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Opcja **Usuń miejsce łącznika** usuwa tylko wszystkie dane, ale zachowaj konfigurację.

Opcja **Usuń łącznik i miejsce łącznika** usuwa dane i konfigurację. Ta opcja jest używana, gdy nie chcesz już łączyć się z lasem.

Obie opcje synchronizują wszystkie obiekty i aktualizują obiekty metaverse. Ta akcja jest długotrwałą operacją.

### <a name="configure-run-profiles"></a>Konfigurowanie profilów uruchamiania
Ta opcja umożliwia wyświetlenie profilów uruchamiania skonfigurowanych dla łącznika.

![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Miejsce łącznika wyszukiwania
Akcja obszaru łącznika wyszukiwania jest przydatna do znajdowania obiektów i rozwiązywania problemów z danymi.

![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Zacznij od wybrania **zakresu**. Można wyszukiwać na podstawie danych (RDN, DN, Anchor, Sub-Tree) lub stanu obiektu (wszystkie inne opcje).  
![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Jeśli na przykład zrobić pod drzewo wyszukiwania, można uzyskać wszystkie obiekty w jednej ou.  
![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Z tej siatki można wybrać obiekt, wybrać **właściwości**i [podążać za nim](tshoot-connect-object-not-syncing.md) z przestrzeni łącznika źródłowego, przez metaverse i do docelowego obszaru łącznika.

### <a name="changing-the-ad-ds-account-password"></a>Zmienianie hasła konta usług AD DS
Jeśli zmienisz hasło konta, usługa synchronizacji nie będzie już mogła importować/eksportować zmian do lokalnej usługi AD.   Mogą wystąpić następujące problemy:

- Krok importu/eksportu łącznika usługi AD kończy się niepowodzeniem z błędem "no-start-credentials".
- W obszarze Podgląd zdarzeń systemu Windows dziennik zdarzeń aplikacji zawiera błąd o identyfikatorze zdarzenia 6000 i nie można uruchomić komunikatu "Agent zarządzania "contoso.com", ponieważ poświadczenia były nieprawidłowe."

Aby rozwiązać ten problem, zaktualizuj konto użytkownika usług AD DS, wykonując następujące czynności:


1. Uruchom Menedżera usług synchronizacji (START → Usługa synchronizacji).
</br>![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Przejdź do karty **Łączniki.**
3. Wybierz łącznik usługi AD skonfigurowany do używania konta usług AD DS.
4. W obszarze Akcje wybierz pozycję **Właściwości**.
5. W wyskakującym oknie dialogowym wybierz pozycję Połącz z lasem usługi Active Directory:
6. Nazwa lasu wskazuje odpowiednie w lokalnej ad.
7. Nazwa użytkownika wskazuje konto usług AD DS używane do synchronizacji.
8. Wprowadź nowe hasło konta usług AD DS ![w narzędziu klucza szyfrowania synchronizacji usługi Azure AD Connect](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Kliknij przycisk OK, aby zapisać nowe hasło i ponownie uruchomić usługę synchronizacji, aby usunąć stare hasło z pamięci podręcznej.



## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
