---
title: Migrowanie zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak przeprowadzić migracji zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5e26f901b0170116a83663176a402b55d6d70b0
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522149"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrowanie zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal 

W tym artykule pokazano, jak przeprowadzić migracji zasad klasycznych, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze. Chociaż nie jest to warunek wstępny, zalecamy przeczytanie [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md) przed rozpoczęciem migracji zasad klasycznych.


 
## <a name="overview"></a>Przegląd 

Scenariusz, w tym artykule pokazano, jak przeprowadzić migracji zasad klasycznych, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze. 

![Usługa Azure Active Directory](./media/policy-migration/33.png)


Proces migracji składa się z następujących czynności:

1. [Otwórz zasady klasyczne](#open-a-classic-policy) można pobrać ustawień konfiguracji.
2. Utwórz nowe zasady dostępu warunkowego usługi Azure AD w celu zastąpienia zasad klasycznych. 
3. Wyłączanie zasad klasycznych.



## <a name="open-a-classic-policy"></a>Otwórz zasad klasycznych

1. W [witryny Azure portal](https://portal.azure.com), na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/policy-migration-mfa/01.png)

2. Na **usługi Azure Active Directory** stronie **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/policy-migration-mfa/02.png)

3. W **Zarządzaj** kliknij **zasady klasyczne (wersja zapoznawcza)**.

    ![Zasady klasyczne](./media/policy-migration-mfa/12.png)

4. Na liście zasady klasyczne, kliknij zasady, które wymagają **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze.

    ![Zasady klasyczne](./media/policy-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Utwórz nowe zasady dostępu warunkowego


1. W [witryny Azure portal](https://portal.azure.com), na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/policy-migration/01.png)

2. Na **usługi Azure Active Directory** stronie **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/policy-migration/02.png)



3. Na **dostępu warunkowego** strony, aby otworzyć **New** w pasku narzędzi u góry, kliknij **Dodaj**.

    ![Dostęp warunkowy](./media/policy-migration/03.png)

4. Na **New** stronie **nazwa** polu tekstowym wpisz nazwę zasady.

    ![Dostęp warunkowy](./media/policy-migration/29.png)

5. W **przypisania** kliknij **użytkowników i grup**.

    ![Dostęp warunkowy](./media/policy-migration/05.png)

    a. Jeśli masz wybranych w zasadach klasycznego wszystkich użytkowników, kliknij przycisk **wszyscy użytkownicy**. 

    ![Dostęp warunkowy](./media/policy-migration/35.png)

    b. Jeśli masz wybrane w klasycznym zasad grupy, kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz wymaganych użytkowników i grup.

    ![Dostęp warunkowy](./media/policy-migration/36.png)

    c. Jeśli masz wykluczone grupy, kliknij przycisk **wykluczyć** kartę, a następnie wybierz wymaganych użytkowników i grup. 

    ![Dostęp warunkowy](./media/policy-migration/37.png)

6. Na **New** strony, aby otworzyć **aplikacje w chmurze** strony w **przypisania** kliknij **aplikacje w chmurze**.

8. Na **aplikacje w chmurze** strony, wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/policy-migration/08.png)

    a. Kliknij przycisk **Wybierz aplikacje**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** stronie, wybierz swoją aplikację w chmurze, a następnie kliknij **wybierz**.

    d. Na **aplikacje w chmurze** kliknij **gotowe**.



9. Jeśli masz **Wymagaj uwierzytelniania wieloskładnikowego** wybrane:

    ![Dostęp warunkowy](./media/policy-migration/26.png)

    a. W **kontrole dostępu** kliknij **Grant**.

    ![Dostęp warunkowy](./media/policy-migration/27.png)

    b. Na **Grant** kliknij **udzielić dostępu**, a następnie kliknij przycisk **Wymagaj uwierzytelniania wieloskładnikowego**.

    c. Kliknij pozycję **Wybierz**.


10. Kliknij przycisk **na** Aby włączyć zasady.

    ![Dostęp warunkowy](./media/policy-migration/30.png)



## <a name="disable-the-classic-policy"></a>Wyłączanie zasad klasycznych

Aby wyłączyć zasady klasyczne, kliknij pozycję **wyłączyć** w **szczegóły** widoku.

![Zasady klasyczne](./media/policy-migration-mfa/14.png)



## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat migracji zasad klasycznych, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md).


- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).

- Jeśli chcesz skonfigurować zasady dostępu warunkowego w swoim środowisku, zobacz [Best practices for conditional access in Azure Active Directory (Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory)](best-practices.md). 
