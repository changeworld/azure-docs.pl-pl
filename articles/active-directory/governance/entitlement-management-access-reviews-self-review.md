---
title: Własny przegląd pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD
description: Dowiedz się, jak przejrzeć dostęp użytkowników do pakietów dostępu do zarządzania uprawnieniami w recenzjach dostępu usługi Azure Active Directory (Wersja zapoznawcza).
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
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967772"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Własny przegląd pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Zarządzanie uprawnieniami usługi Azure AD upraszcza sposób zarządzania dostępem przedsiębiorstw do grup, aplikacji i witryn programu SharePoint. W tym artykule opisano, jak użytkownik wykonuje własny przegląd przypisanych pakietów dostępu.

## <a name="open-the-access-review"></a>Otwórz przegląd dostępu

Aby wykonać przegląd dostępu, należy najpierw otworzyć przegląd dostępu. Aby znaleźć i otworzyć przegląd dostępu, należy skorzystać z poniższej procedury:

1. Użytkownik może otrzymać wiadomość e-mail od firmy Microsoft z prośbą o sprawdzenie dostępu. Znajdź wiadomość e-mail, aby otworzyć przegląd dostępu. Oto przykład wiadomości e-mail z prośbą o sprawdzenie dostępu: 
    
    ![Dostęp do wiadomości e-mail recenzenta](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Kliknij łącze **Przejrzyj dostęp.**

1. Możesz również przejść bezpośrednio https://myaccess.microsoft.com do, aby znaleźć oczekujące opinie o dostępie, jeśli nie otrzymasz wiadomości e-mail.  (Dla rządu STANÓW `https://myaccess.microsoft.us` Zjednoczonych użyj zamiast tego).

1. Kliknij **pozycję Przeglądy programu Access** na lewym pasku nawigacyjnym, aby wyświetlić listę oczekujących przeglądów dostępu przypisanych do Ciebie.


1.  Kliknij recenzję, którą chcesz rozpocząć.

## <a name="perform-the-access-review"></a>Wykonywanie przeglądu dostępu

Po otwarciu przeglądu dostępu możesz zobaczyć swój dostęp. Aby dokonać przeglądu dostępu, wykonaj następującą procedurę:

1.  Zdecyduj, czy nadal potrzebujesz dostępu do pakietu dostępu. Na przykład projekt, nad który pracujesz, nie został ukończony, więc nadal potrzebujesz dostępu, aby kontynuować pracę nad projektem.

1.  Kliknij **przycisk Tak,** aby zachować dostęp, lub kliknij przycisk **Nie,** aby usunąć dostęp.
    >[!NOTE]
    >Jeśli stwierdzono, że nie potrzebujesz już dostępu, nie są natychmiast usuwane z pakietu dostępu. Zostaniesz usunięty z pakietu dostępu po zakończeniu przeglądu lub jeśli administrator zatrzyma przegląd.

1.  Jeśli klikniesz **przycisk Tak,** może być konieczne dołączenie instrukcji justowania w polu **Przyczyna.**

1.  Kliknij **przycisk Prześlij**.

Możesz wrócić do recenzji, jeśli zmienisz zdanie i zdecydujesz się zmienić odpowiedź przed zakończeniem przeglądu.

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do pakietów dostępu](entitlement-management-access-reviews-review-access.md) 
