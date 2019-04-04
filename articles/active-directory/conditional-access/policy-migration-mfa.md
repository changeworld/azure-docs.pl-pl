---
title: Migrowanie zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal
description: W tym artykule pokazano, jak przeprowadzić migracji zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 06/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6661cee8ba6176bd706d31a10a8f20549e29e4d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894427"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrowanie zasad klasycznych, który wymaga uwierzytelniania wieloskładnikowego w witrynie Azure portal

W tym samouczku pokazano, jak przeprowadzić migracji zasad klasycznych, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze. Chociaż nie jest to warunek wstępny, zalecamy przeczytanie [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md) przed rozpoczęciem migracji zasad klasycznych.

## <a name="overview"></a>Przegląd

Scenariusz, w tym artykule pokazano, jak przeprowadzić migracji zasad klasycznych, która wymaga **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze.

![Usługa Azure Active Directory](./media/policy-migration/33.png)

Proces migracji składa się z następujących czynności:

1. [Otwórz zasady klasyczne](#open-a-classic-policy) można pobrać ustawień konfiguracji.
1. Utwórz nowe zasady dostępu warunkowego usługi Azure AD w celu zastąpienia zasad klasycznych. 
1. Wyłączanie zasad klasycznych.

## <a name="open-a-classic-policy"></a>Otwórz zasad klasycznych

1. W [witryny Azure portal](https://portal.azure.com), na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na **usługi Azure Active Directory** stronie **Zarządzaj** kliknij **dostępu warunkowego**.

   ![Dostęp warunkowy](./media/policy-migration-mfa/02.png)

1. W **Zarządzaj** kliknij **zasady klasyczne (wersja zapoznawcza)**.

   ![Zasady klasyczne](./media/policy-migration-mfa/12.png)

1. Na liście zasady klasyczne, kliknij zasady, które wymagają **uwierzytelnianie wieloskładnikowe** dla aplikacji w chmurze.

   ![Zasady klasyczne](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Utwórz nowe zasady dostępu warunkowego

1. W [witryny Azure portal](https://portal.azure.com), na lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/policy-migration/01.png)

1. Na **usługi Azure Active Directory** stronie **Zarządzaj** kliknij **dostępu warunkowego**.

   ![Dostęp warunkowy](./media/policy-migration/02.png)

1. Na **dostępu warunkowego** strony, aby otworzyć **New** w pasku narzędzi u góry, kliknij **Dodaj**.

   ![Dostęp warunkowy](./media/policy-migration/03.png)

1. Na **New** stronie **nazwa** polu tekstowym wpisz nazwę zasady.

   ![Dostęp warunkowy](./media/policy-migration/29.png)

1. W **przypisania** kliknij **użytkowników i grup**.

   ![Dostęp warunkowy](./media/policy-migration/05.png)

   1. Jeśli masz wybranych w zasadach klasycznego wszystkich użytkowników, kliknij przycisk **wszyscy użytkownicy**. 

   ![Dostęp warunkowy](./media/policy-migration/35.png)

   1. Jeśli masz wybrane w klasycznym zasad grupy, kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz wymaganych użytkowników i grup.

   ![Dostęp warunkowy](./media/policy-migration/36.png)

   1. Jeśli masz wykluczone grupy, kliknij przycisk **wykluczyć** kartę, a następnie wybierz wymaganych użytkowników i grup. 

   ![Dostęp warunkowy](./media/policy-migration/37.png)

1. Na **New** strony, aby otworzyć **aplikacje w chmurze** strony w **przypisania** kliknij **aplikacje w chmurze**.

1. Na **aplikacje w chmurze** strony, wykonaj następujące czynności:

   ![Dostęp warunkowy](./media/policy-migration/08.png)

   1. Kliknij przycisk **Wybierz aplikacje**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** stronie, wybierz swoją aplikację w chmurze, a następnie kliknij **wybierz**.

   1. Na **aplikacje w chmurze** kliknij **gotowe**.

1. Jeśli masz **Wymagaj uwierzytelniania wieloskładnikowego** wybrane:

   ![Dostęp warunkowy](./media/policy-migration/26.png)

   1. W **kontrole dostępu** kliknij **Grant**.

   ![Dostęp warunkowy](./media/policy-migration/27.png)

   1. Na **Grant** kliknij **udzielić dostępu**, a następnie kliknij przycisk **Wymagaj uwierzytelniania wieloskładnikowego**.

   1. Kliknij pozycję **Wybierz**.

1. Kliknij przycisk **na** Aby włączyć zasady.

   ![Dostęp warunkowy](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Wyłączanie zasad klasycznych

Aby wyłączyć zasady klasyczne, kliknij pozycję **wyłączyć** w **szczegóły** widoku.

![Zasady klasyczne](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat migracji zasad klasycznych, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md).
- Jeśli chcesz wiedzieć, jak skonfigurować zasady dostępu warunkowego, zobacz [wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](app-based-mfa.md).
- Jeśli chcesz skonfigurować zasady dostępu warunkowego w swoim środowisku, zobacz [Best practices for conditional access in Azure Active Directory (Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory)](best-practices.md).
