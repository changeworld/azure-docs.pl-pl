---
title: Migrowanie zasad dostępu warunkowego — Azure Active Directory
description: W tym artykule przedstawiono sposób migracji zasad klasycznych, które wymagają uwierzytelniania wieloskładnikowego w Azure Portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: tutorial
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca00ae62ba114aecef48117fd8a54b7f2e962dfd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380330"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrowanie zasad klasycznych, które wymagają uwierzytelniania wieloskładnikowego w Azure Portal

Ten samouczek pokazuje, jak przeprowadzić migrację klasycznych zasad, które wymagają **uwierzytelniania wieloskładnikowego** dla aplikacji w chmurze. Chociaż nie jest to wymaganie wstępne, przed rozpoczęciem migrowania klasycznych zasad zaleca się odczytanie [zasad migracji klasycznych w Azure Portal](policy-migration.md) .

## <a name="overview"></a>Omówienie

W scenariuszu w tym artykule przedstawiono sposób migrowania zasad klasycznych, które wymagają **uwierzytelniania wieloskładnikowego** w przypadku aplikacji w chmurze.

![Usługa Azure Active Directory](./media/policy-migration/33.png)

Proces migracji obejmuje następujące kroki:

1. Aby uzyskać ustawienia konfiguracji, [Otwórz zasady klasyczne](#open-a-classic-policy) .
1. Utwórz nowe zasady dostępu warunkowego usługi Azure AD, aby zastąpić zasady klasyczne. 
1. Wyłącz zasady klasyczne.

## <a name="open-a-classic-policy"></a>Otwieranie zasad klasycznych

1. W [Azure Portal](https://portal.azure.com)na lewym pasku nawigacyjnym kliknij pozycję **Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/policy-migration-mfa/01.png)

1. Na stronie **Azure Active Directory** w sekcji **Zarządzanie** kliknij pozycję **dostęp warunkowy**.

   ![Dostęp warunkowy](./media/policy-migration-mfa/02.png)

1. W sekcji **Zarządzanie** kliknij pozycję **zasady klasyczne (wersja zapoznawcza)** .

   ![Zasady klasyczne](./media/policy-migration-mfa/12.png)

1. Na liście zasad klasycznych kliknij zasady, które wymagają **uwierzytelniania wieloskładnikowego** dla aplikacji w chmurze.

   ![Zasady klasyczne](./media/policy-migration-mfa/13.png)

## <a name="create-a-new-conditional-access-policy"></a>Tworzenie nowych zasad dostępu warunkowego

1. W [Azure Portal](https://portal.azure.com)na lewym pasku nawigacyjnym kliknij pozycję **Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/policy-migration/01.png)

1. Na stronie **Azure Active Directory** w sekcji **Zarządzanie** kliknij pozycję **dostęp warunkowy**.

   ![Dostęp warunkowy](./media/policy-migration/02.png)

1. Na stronie **dostęp warunkowy** , aby otworzyć **nową** stronę, na pasku narzędzi u góry kliknij przycisk **Dodaj**.

   ![Dostęp warunkowy](./media/policy-migration/03.png)

1. Na **nowej** stronie, w polu tekstowym **Nazwa** wpisz nazwę zasady.

   ![Dostęp warunkowy](./media/policy-migration/29.png)

1. W sekcji **przypisania** kliknij pozycję **Użytkownicy i grupy**.

   ![Dostęp warunkowy](./media/policy-migration/05.png)

   1. Jeśli masz wszystkich użytkowników wybranych w ramach zasad klasycznych, kliknij pozycję **Wszyscy użytkownicy**. 

      ![Dostęp warunkowy](./media/policy-migration/35.png)

   1. Jeśli w zasadach klasycznych zostały wybrane grupy, kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz wymaganych użytkowników i grupy.

      ![Dostęp warunkowy](./media/policy-migration/36.png)

   1. Jeśli masz wykluczone grupy, kliknij kartę **Wyklucz** , a następnie wybierz wymaganych użytkowników i grupy. 

      ![Dostęp warunkowy](./media/policy-migration/37.png)

1. Na stronie **Nowy** , aby otworzyć stronę **aplikacje w chmurze** , w sekcji **przypisanie** kliknij pozycję aplikacje w **chmurze**.
1. Na stronie **aplikacje w chmurze** wykonaj następujące czynności:
   1. Kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij pozycję **Wybierz**.
   1. Na stronie **Wybierz** wybierz aplikację w chmurze, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **aplikacje w chmurze** kliknij pozycję **gotowe**.
1. Jeśli wybrano **wymaganie uwierzytelniania wieloskładnikowego** :

   ![Dostęp warunkowy](./media/policy-migration/26.png)

   1. W sekcji **kontrole dostępu** kliknij pozycję **Udziel**.

      ![Dostęp warunkowy](./media/policy-migration/27.png)

   1. Na stronie **Grant** kliknij pozycję **Udziel dostępu**, a następnie kliknij pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij pozycję **Wybierz**.
1. Kliknij **pozycję Włącz,** aby włączyć zasady.

   ![Dostęp warunkowy](./media/policy-migration/30.png)

## <a name="disable-the-classic-policy"></a>Wyłącz zasady klasyczne

Aby wyłączyć zasady klasyczne, kliknij przycisk **Wyłącz** w widoku **szczegółów** .

![Zasady klasyczne](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat klasycznej migracji zasad, zobacz [Migrowanie zasad klasycznych w Azure Portal](policy-migration.md).
- Jeśli chcesz dowiedzieć się, jak skonfigurować zasady dostępu warunkowego, zobacz [Wymagaj uwierzytelniania wieloskładnikowego dla określonych aplikacji, Azure Active Directory dostępu warunkowego](app-based-mfa.md).
- Aby skonfigurować zasady dostępu warunkowego dla środowiska, zobacz [najlepsze rozwiązania dotyczące dostępu warunkowego w Azure Active Directory](best-practices.md).
