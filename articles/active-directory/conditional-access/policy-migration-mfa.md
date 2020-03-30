---
title: Migrowanie zasad dostępu warunkowego — usługa Azure Active Directory
description: W tym artykule pokazano, jak przeprowadzić migrację zasad klasycznych, które wymagają uwierzytelniania wieloskładnikowego w witrynie Azure portal.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846038"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrowanie zasad klasycznych w witrynie Azure portal

W tym artykule pokazano, jak przeprowadzić migrację klasycznej zasady, która wymaga **uwierzytelniania wieloskładnikowego** dla aplikacji w chmurze. Chociaż nie jest to warunek wstępny, zaleca się [przeczytanie narzędzia Migruj klasyczne zasady w witrynie Azure portal](policy-migration.md) przed rozpoczęciem migracji klasycznych zasad.

![Klasyczne szczegóły zasad wymagające usługi MFA dla aplikacji Salesforce](./media/policy-migration/33.png)

Proces migracji składa się z następujących kroków:

1. [Otwórz zasadę klasyczną,](#open-a-classic-policy) aby uzyskać ustawienia konfiguracji.
1. Utwórz nową zasadę dostępu warunkowego usługi Azure AD, aby zastąpić klasyczne zasady. 
1. Wyłącz klasyczne zasady.

## <a name="open-a-classic-policy"></a>Otwieranie klasycznej polityki

1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz, **Zasady klasyczne**.

   ![Widok zasad klasycznych](./media/policy-migration-mfa/12.png)

1. Na liście klasycznych zasad wybierz zasady, które mają być migrowane. Udokumentować ustawienia konfiguracji, aby można było ponownie utworzyć za pomocą nowej zasady dostępu warunkowego.

## <a name="create-a-new-conditional-access-policy"></a>Tworzenie nowych zasad dostępu warunkowego

1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Aby utworzyć nową zasadę dostępu warunkowego, wybierz pozycję **Nowa zasada**.
1. Na stronie **Nowy** w polu tekstowym **Nazwa** wpisz nazwę zasad.
1. W sekcji **Przydziały** kliknij pozycję **Użytkownicy i grupy**.
   1. Jeśli w zasadach klasycznych wybrano wszystkich użytkowników, kliknij pozycję **Wszyscy użytkownicy**. 
   1. Jeśli w zasadach klasycznych wybrano grupy, kliknij pozycję **Wybierz użytkowników i grupy**, a następnie wybierz wymaganych użytkowników i grupy.
   1. Jeśli masz wykluczone grupy, kliknij kartę **Wyklucz,** a następnie wybierz wymaganych użytkowników i grupy. 
   1. Wybierz **gotowe**
1. W sekcji **Przydział** kliknij pozycję **Aplikacje lub akcje w chmurze**.
1. Na stronie **Aplikacje lub akcje w chmurze** wykonaj następujące czynności:
   1. Kliknij **pozycję Wybierz aplikacje**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Wybierz** wybierz aplikację w chmurze, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **Aplikacje w chmurze** kliknij pozycję **Gotowe**.
1. Jeśli wybrano **opcję Wymagaj uwierzytelniania wieloskładnikowego:**
   1. W sekcji **Formanty dostępu** kliknij pozycję **Przyznaj**.
   1. Na stronie **Grant (Udzielenie)** kliknij pozycję **Udzielij dostępu**, a następnie kliknij pozycję **Wymagaj uwierzytelniania wieloskładnikowego**.
   1. Kliknij **pozycję Wybierz**.
1. Kliknij **przycisk Włącz,** aby włączyć zasady, a następnie wybierz pozycję **Zapisz**.

   ![Tworzenie zasad dostępu warunkowego](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Wyłączanie zasad klasycznych

Aby wyłączyć klasyczne zasady, kliknij pozycję **Wyłącz** w widoku **Szczegóły.**

![Wyłączanie klasycznych zasad](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat migracji zasad klasycznych, zobacz [Migrowanie zasad klasycznych w witrynie Azure portal](policy-migration.md).
- [Użyj trybu tylko do raportu dla dostępu warunkowego, aby określić wpływ nowych decyzji dotyczących zasad.](concept-conditional-access-report-only.md)