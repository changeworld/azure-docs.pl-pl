---
title: Operacje programu Azure AD Connect z usługą Service Service Manager | Dokumenty firmy Microsoft
description: Poznaj kartę Operacje w menedżerze usługi Synchronizacja usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
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
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381430"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Korzystanie z karty Operacje menedżera usług synchronizacji

![Menedżer usługi synchronizacji](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Karta operacje pokazuje wyniki z najnowszych operacji. Ta karta jest kluczem do zrozumienia i rozwiązywania problemów.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Opis informacji widocznych na karcie operacje
Górna połowa pokazuje wszystkie biegi w porządku chronologicznym. Domyślnie dziennik operacji przechowuje informacje o ostatnich siedmiu dniach, ale to ustawienie można zmienić za pomocą [harmonogramu](how-to-connect-sync-feature-scheduler.md). Chcesz wyszukać dowolny przebieg, który nie pokazuje stanu sukcesu. Sortowanie można zmienić, klikając nagłówki.

**Kolumna Stan** jest najważniejszą informacją i pokazuje najpoważniejszy problem dla przebiegu. Oto krótkie podsumowanie najczęstszych stanów w kolejności priorytetu do zbadania (gdzie * wskazać kilka możliwych ciągów błędów).

| Stan | Komentarz |
| --- | --- |
| zatrzymana-\* |Nie można ukończyć biegu. Na przykład, jeśli system zdalny jest w dół i nie można się z nim skontaktować. |
| limit zatrzymanego błędu |Istnieje ponad 5000 błędów. Przebieg został automatycznie wstrzymany z powodu dużej liczby błędów. |
| zakończone-\*-błędy |Przebieg zakończony, ale istnieją błędy (mniej niż 5000), które powinny być badane. |
| zakończone-\*-ostrzeżenia |Przebieg został zakończony, ale niektóre dane nie są w oczekiwanym stanie. Jeśli masz błędy, ten komunikat jest zwykle tylko objawem. Dopóki nie zostaną uwzględnione błędy, nie należy badać ostrzeżenia. |
| powodzenie |Żadnych problemów. |

Po wybraniu wiersza, dolnej aktualizacji, aby wyświetlić szczegóły tego uruchomienia. Po lewej stronie dołu może być lista z napisem **Krok #**. Ta lista jest wyświetlana tylko wtedy, gdy w lesie znajduje się wiele domen, w których każda domena jest reprezentowana przez krok. Nazwę domeny można znaleźć pod nagłówkiem **Partycja**. W obszarze **Statystyki synchronizacji**można znaleźć więcej informacji na temat liczby przetworzonych zmian. Możesz kliknąć łącza, aby uzyskać listę zmienionych obiektów. Jeśli masz obiekty z błędami, te błędy są wyświetlane w obszarze **Błędy synchronizacji**.

Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów z obiektem, który nie jest synchronizowany](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o konfiguracji [synchronizacji usługi Azure AD Connect.](how-to-connect-sync-whatis.md)

Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
