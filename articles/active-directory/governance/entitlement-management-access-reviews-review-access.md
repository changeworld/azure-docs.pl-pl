---
title: Przeglądanie dostępu do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD
description: Dowiedz się, jak ukończyć przegląd pakietów dostępu do zarządzania uprawnieniami w recenzjach dostępu usługi Azure Active Directory (Wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968740"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Przeglądanie dostępu do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Zarządzanie uprawnieniami usługi Azure AD upraszcza sposób zarządzania dostępem przedsiębiorstw do grup, aplikacji i witryn programu SharePoint. W tym artykule opisano sposób wykonywania przeglądów dostępu dla innych użytkowników, którzy są przypisani do pakietu dostępu jako wyznaczony recenzent.

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejrzeć przypisania pakietów aktywnego dostępu użytkowników, musisz spełnić wymagania wstępne, aby wykonać przegląd dostępu:
- Usługa Azure AD — warstwa Premium P2
- Administrator globalny
- Wyznaczony administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Aby znaleźć i otworzyć przegląd dostępu, należy wykonać następujące czynności:

1. Użytkownik może otrzymać wiadomość e-mail od firmy Microsoft z prośbą o sprawdzenie dostępu. Znajdź wiadomość e-mail, aby otworzyć przegląd dostępu. Oto przykładowy e-mail do przeglądu dostępu:
    
    ![Dostęp do wiadomości e-mail recenzenta dostępu](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. Kliknij łącze **Przejrzyj dostęp użytkownika,** aby otworzyć przegląd dostępu. 

1. Jeśli nie masz wiadomości e-mail, możesz znaleźć oczekujące recenzje https://myaccess.microsoft.comdostępu, przechodząc bezpośrednio do .  (Dla rządu STANÓW `https://myaccess.microsoft.us` Zjednoczonych użyj zamiast tego).

1. Kliknij **pozycję Przeglądy programu Access** na lewym pasku nawigacyjnym, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do Ciebie.
    
    ![Wybieranie recenzji dostępu w programie Mój dostęp](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Kliknij recenzję, którą chcesz rozpocząć.
    
    ![Wybierz recenzję dostępu](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>Wykonywanie przeglądu dostępu

Po otwarciu przeglądu dostępu zobaczysz nazwy użytkowników, dla których musisz przejrzeć. Istnieją dwa sposoby zatwierdzania lub odrzucania dostępu:
- Możesz ręcznie zatwierdzić lub odmówić dostępu jednemu lub większej liczbie użytkowników
- Można zaakceptować zalecenia systemowe

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Ręczne zatwierdzanie lub odmawianie dostępu jednemu lub większej liczbie użytkowników
1. Przejrzyj listę użytkowników i określ, którzy użytkownicy muszą nadal mieć dostęp.

    ![Lista użytkowników do przejrzenia](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. Aby zatwierdzić lub odmówić dostępu, wybierz przycisk opcji po lewej stronie nazwy użytkownika.

1. Wybierz **pozycję Zatwierdź** lub **Odmów** na pasku nad nazwami użytkowników.

    ![Wybierz użytkownika](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Jeśli nie masz pewności, możesz kliknąć przycisk **Nie wiem.**

    Jeśli dokonasz tego wyboru, użytkownik zachowa dostęp, a to zaznaczenie zostanie wybrane w dziennikach inspekcji. Dziennik pokazuje innych recenzentów, że nadal zakończone przeglądu.

1. Może być wymagane podanie przyczyny swojej decyzji. Wpisz przyczynę i kliknij przycisk **Prześlij**.

    ![Zatwierdzanie lub odmawianie dostępu](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. Decyzję można zmienić w dowolnym momencie przed zakończeniem przeglądu. Aby to zrobić, wybierz użytkownika z listy i zmień decyzję. Na przykład można zatwierdzić dostęp dla użytkownika, którego wcześniej odmówiono.

Jeśli istnieje wiele recenzentów, ostatnia przesłana odpowiedź jest rejestrowana. Rozważmy przykład, w którym administrator wyznacza dwóch recenzentów — Alicji i Roberta. Alicja najpierw otwiera recenzję i zatwierdza dostęp. Przed zakończeniem przeglądu, Bob otwiera przegląd i odmawia dostępu. W takim przypadku zostanie zarejestrowana ostatnia decyzja o dostępie odmowy.

>[!NOTE]
>Jeśli użytkownikowi odmówiono dostępu, nie są one natychmiast usuwane z pakietu dostępu. Użytkownik zostanie usunięty z pakietu dostępu po zakończeniu przeglądu lub administrator zakończy przegląd.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Zatwierdzanie lub odmawianie dostępu przy użyciu zaleceń generowanych przez system

Aby szybciej przejrzeć dostęp dla wielu użytkowników, można użyć zaleceń generowanych przez system, akceptując zalecenia za pomocą jednego kliknięcia. Zalecenia są generowane na podstawie działania logowania użytkownika.

1.  Na pasku u góry strony kliknij pozycję **Zaakceptuj rekomendacje**.
    
    ![Wybierz pozycję Zaakceptuj rekomendacje](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Zobaczysz podsumowanie zalecanych działań.

1.  Kliknij **przycisk Prześlij,** aby zaakceptować zalecenia.

## <a name="next-steps"></a>Następne kroki

- [Własny przegląd pakietów dostępu](entitlement-management-access-reviews-self-review.md)