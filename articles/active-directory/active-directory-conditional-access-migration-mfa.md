---
title: Migracja z klasycznego zasadę, która wymaga usługi Multi-Factor authentication w portalu Azure | Dokumentacja firmy Microsoft
description: W tym artykule pokazano, jak przeprowadzić migrację klasycznego zasadę, która wymaga usługi Multi-Factor authentication w portalu Azure.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 52d4297d2e86ad67672d38f1c527041b813f8d61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723691"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migracja z klasycznego zasadę, która wymaga usługi Multi-Factor authentication w portalu Azure 

W tym artykule przedstawiono sposób migracji klasycznego zasadę, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze. Chociaż nie jest to wymagane, zaleca się przeczytanie [migracji klasycznego zasad w portalu Azure](active-directory-conditional-access-migration.md) przed rozpoczęciem migracji zasad klasycznego.


 
## <a name="overview"></a>Przegląd 

Scenariusz, w tym artykule przedstawiono sposób migracji klasycznego zasadę, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze. 

![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Proces migracji składa się z następujących czynności:

1. [Otwórz klasyczny zasad](#open-a-classic-policy) można pobrać ustawień konfiguracji.
2. Utwórz nowe zasady dostępu warunkowego usługi Azure AD zastąpić klasycznego zasad. 
3. Wyłącz zasady klasycznego.



## <a name="open-a-classic-policy"></a>Otwórz zasadę klasycznego

1. W [portalu Azure](https://portal.azure.com), na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration-mfa/02.png)

3. W **Zarządzaj** kliknij **zasady klasyczny (wersja zapoznawcza)**.

    ![Zasady klasyczne](./media/active-directory-conditional-access-migration-mfa/12.png)

4. Na liście zasad w klasycznym kliknij zasady, które wymagają **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze.

    ![Zasady klasyczne](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Utwórz nowe zasady dostępu warunkowego


1. W [portalu Azure](https://portal.azure.com), na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**.

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/02.png)



3. Na **dostępu warunkowego** strony, aby otworzyć **nowy** na pasku narzędzi u góry, kliknij przycisk **Dodaj**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/03.png)

4. Na **nowy** strony w **nazwa** tekstowym, wpisz nazwę zasady.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/29.png)

5. W **przypisania** kliknij **użytkowników i grup**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/05.png)

    a. Jeśli wszyscy użytkownicy wybrane w zasadach klasycznym kliknij **wszyscy użytkownicy**. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/35.png)

    b. Jeśli wybrana w klasycznym zasad grupy, kliknij przycisk **Wybierz użytkowników i grupy**, a następnie wybierz wymaganych użytkowników i grup.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/36.png)

    c. Jeśli masz wykluczone grupy, kliknij przycisk **wykluczyć** , a następnie wybierz wymaganych użytkowników i grup. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/37.png)

6. Na **nowy** strony, aby otworzyć **aplikacji w chmurze** strony w **przypisania** kliknij **aplikacji w chmurze**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na **aplikacji w chmurze** wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/08.png)

    a. Kliknij przycisk **Wybierz aplikacje**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz aplikacji w chmurze, a następnie kliknij przycisk **wybierz**.

    d. Na **aplikacji w chmurze** kliknij przycisk **gotowe**.



9. Jeśli masz **wymusić uwierzytelnianie wieloskładnikowe** wybranych:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/26.png)

    a. W **dostęp do formantów** kliknij **Grant**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/27.png)

    b. Na **Grant** kliknij przycisk **udzielić dostępu**, a następnie kliknij przycisk **wymusić uwierzytelnianie wieloskładnikowe**.

    c. Kliknij pozycję **Wybierz**.


10. Kliknij przycisk **na** Aby włączyć zasady.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>Wyłącz zasady klasycznego

Aby wyłączyć klasycznego zasad, kliknij przycisk **wyłączyć** w **szczegóły** widoku.

![Zasady klasyczne](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji o migracji klasycznego zasad, zobacz [migracji klasycznego zasad w portalu Azure](active-directory-conditional-access-migration.md).


- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wprowadzenie dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Jeśli wszystko jest gotowe do skonfigurowania zasad dostępu warunkowego dla danego środowiska, zobacz [najlepszych rozwiązań dotyczących dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-best-practices.md). 
