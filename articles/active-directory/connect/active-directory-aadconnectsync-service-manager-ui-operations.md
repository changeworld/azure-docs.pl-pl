---
title: Operacje Menedżera usługi synchronizacji programu Azure AD Connect | Dokumentacja firmy Microsoft
description: Dowiedz się na karcie operacje Menedżera usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d6a8e10c5b78317ffda0963a13907c4552892e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593473"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Na karcie operacje Menedżera usługi synchronizacji

![Menedżera usługi synchronizacji](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

Na karcie operacje pokazuje wyniki od ostatniej operacji. Na tej karcie jest kluczem do zrozumienia i rozwiązywania problemów.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Zrozumienie informacji widocznych na karcie operacje
Górnej połowie zawiera wszystkie elementy w kolejności chronologicznej. Domyślnie operacje logowania zachowuje informacje dotyczące ostatnich siedmiu dni, ale ustawienie to można zmienić z [harmonogramu](active-directory-aadconnectsync-feature-scheduler.md). Chcesz znaleźć dla dowolnego przebiegu pokazywać stanu Powodzenie. Można zmienić sortowania, klikając nagłówki.

**Stan** jest najważniejsze informacje i przedstawia najbardziej poważny problem dla przebiegu. Oto krótkie podsumowanie stanów najczęściej w kolejności priorytetu do sprawdzania, czy (gdzie * wskazać ciągi możliwy błąd kilka).

| Stan | Komentarz |
| --- | --- |
| Zatrzymano-\* |Nie można wykonać przebiegu. Na przykład, jeśli nie działa i nie można skontaktować się z systemu zdalnego. |
| stopped-error-limit |Wystąpiły błędy więcej niż 5000. Uruchom automatycznie zostało zatrzymane z powodu dużej liczby błędów. |
| Ukończono -\*— błędy |Przy uruchomieniu ukończone, ale wystąpiły błędy (mniej niż 5000), które należy zbadać. |
| Ukończono -\*— ostrzeżenia |Przy uruchomieniu ukończone, ale niektóre dane nie jest w oczekiwanym stanem. Jeśli masz błędy, następnie ten komunikat jest zwykle tylko objawem. Dopóki usunąć błędy, nie powinien być sprawdzony ostrzeżenia. |
| powodzenie |Brak problemów. |

Po zaznaczeniu wiersza dolnej aktualizuje Pokaż szczegóły uruchomienia. Na lewo od dołu, może mieć listy informacją o tym **krok nr**. Ta lista jest wyświetlana tylko, jeśli masz wiele domen w lesie gdzie każdej domeny jest reprezentowany przez krok. Nazwa domeny znajduje się w pozycji **partycji**. W obszarze **statystyki synchronizacji**, można znaleźć więcej informacji na temat liczby zmian, które zostały przetworzone. Kliknięcie łącza spowoduje wyświetlenie listy zmienionych obiektów. Jeśli masz obiektów z błędami, te błędy są wyświetlane w oknie **błędy synchronizacji**.

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z obiektu, który nie jest synchronizowanie](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfiguracji.

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
