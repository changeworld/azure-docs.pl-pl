---
title: Przewodnik Szybki Start — Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak powiązać Twoje wymagania dotyczące uwierzytelniania dla typu aplikacji w chmurze używanych przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: db191587f02fa8fa8934cac7a001ea31c233cbdb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112750"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Szybki start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu usługi Azure Active Directory dostępu warunkowego

Aby uprościć środowisko logowania użytkowników, można zezwolić im na Zaloguj się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak w wielu środowiskach ma co najmniej kilka aplikacji, dla których zaleca się wymagają silniejszego formularz weryfikacji konta, takie jak uwierzytelnianie wieloskładnikowe (MFA). Może to być na przykład wartość true, aby uzyskać dostęp do systemu poczty e-mail w organizacji lub aplikacji działu KADR. W usłudze Azure Active Directory (Azure AD) można osiągnąć ten cel, za pomocą zasad dostępu warunkowego.

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasady dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) wymagającym uwierzytelniania wieloskładnikowego dla wybranej chmury aplikacji w danym środowisku.

![Przykładowe zasady dostępu warunkowego w witrynie Azure portal](./media/app-based-mfa/32.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz, w tym przewodniku Szybki Start, potrzebne są:

- **Dostęp do usługi Azure AD — wersja Premium** -dostępu warunkowego usługi Azure AD jest możliwość usługi Azure AD Premium.

- **Konto testowe o nazwie Isabella Simonsen** — Jeśli nie wiesz, jak tworzyć konta testowego, zobacz [Dodawanie użytkowników w chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Scenariusz, w tym przewodniku Szybki Start wymaga dla poszczególnych użytkowników usługi MFA nie włączenia konta testu. Aby uzyskać więcej informacji, zobacz [jak, które wymuszają weryfikację dwuetapową dla użytkownika](../authentication/howto-mfa-userstates.md).

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest sobie pogląd logowania bez zasad dostępu warunkowego.

**Aby zainicjować środowisko:**

1. Zaloguj się do witryny Azure portal jako Isabella Simonsen.
1. Wyloguj się.

## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego

W tej sekcji przedstawiono sposób tworzenia wymaganych zasad dostępu warunkowego. Scenariusz, w tym przewodniku Szybki Start używa:

- Witryny Azure portal jako symbol zastępczy dla aplikacji w chmurze, która wymaga uwierzytelniania Wieloskładnikowego. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

Ustaw w zasadach:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Isabella Simonsen |
| Aplikacje w chmurze | Microsoft Azure Management |
| Udzielanie dostępu | Wymagaj uwierzytelniania wieloskładnikowego |

![Rozwinięty zasad dostępu warunkowego](./media/app-based-mfa/31.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com) jako administratora globalnego, administratora zabezpieczeń lub administrator dostępu warunkowego.

1. W witrynie Azure portal, w lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/app-based-mfa/02.png)

1. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

   ![Dostęp warunkowy](./media/app-based-mfa/03.png)

1. Na **dostępu warunkowego** w pasku narzędzi u góry, kliknij **nowe zasady**.

   ![Add](./media/app-based-mfa/04.png)

1. Na **New** strony w **nazwa** polu tekstowym wpisz **wymagają usługi MFA, aby uzyskać dostęp do portalu Azure**.

   ![Name (Nazwa)](./media/app-based-mfa/05.png)

1. W **przypisania** kliknij **użytkowników i grup**.

   ![Użytkownicy i grupy](./media/app-based-mfa/06.png)

1. Na **użytkowników i grup** strony, wykonaj następujące czynności:

   ![Użytkownicy i grupy](./media/app-based-mfa/24.png)

   1. Kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz pozycję **użytkowników i grup**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

   1. Na **użytkowników i grup** kliknij **gotowe**.

1. Kliknij przycisk **aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/app-based-mfa/08.png)

1. Na **aplikacje w chmurze** strony, wykonaj następujące czynności:

   ![Wybierz aplikacje w chmurze](./media/app-based-mfa/26.png)

   1. Kliknij przycisk **Wybierz aplikacje**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

   1. Na **aplikacje w chmurze** kliknij **gotowe**.

1. W **kontrole dostępu** kliknij **Grant**.

   ![Kontrola dostępu](./media/app-based-mfa/10.png)

1. Na **Grant** strony, wykonaj następujące czynności:

   ![Przydział](./media/app-based-mfa/11.png)

   1. Wybierz **udzielić dostępu**.

   1. Wybierz **Wymagaj uwierzytelniania wieloskładnikowego**.

   1. Kliknij pozycję **Wybierz**.

1. W **Włącz zasady** kliknij **na**.

   ![Włączanie zasad](./media/app-based-mfa/18.png)

1. Kliknij pozycję **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. Pierwszym krokiem należy używać dostępu warunkowego, zasady narzędzie analizy warunkowej w celu symulowania logowania dla użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Można zainicjować co, jeśli narzędzie oceny zasad, ustaw:

- **Isabella Simonsen** jako użytkownik
- **Usługa Microsoft Azure Management** jako aplikacja w chmurze

Klikając **co zrobić, jeśli** tworzy raport symulacji, który pokazuje:

- **Wymagać uwierzytelniania Wieloskładnikowego, aby uzyskać dostęp do portalu Azure** w obszarze **zasady, które będą miały zastosowanie**
- **Wymagaj uwierzytelniania wieloskładnikowego** jako **Udziel kontroli**.

![Co zrobić, jeśli narzędzie zasad](./media/app-based-mfa/23.png)

**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  

   ![A co jeżeli](./media/app-based-mfa/14.png)

1. Kliknij przycisk **użytkowników**, wybierz opcję **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

   ![Użytkownik](./media/app-based-mfa/15.png)

1. Aby wybrać aplikację w chmurze, wykonaj następujące czynności:

   ![Aplikacje w chmurze](./media/app-based-mfa/16.png)

   1. Kliknij przycisk **aplikacje w chmurze**.

   1. Na **stronę aplikacji w chmurze**, kliknij przycisk **Wybierz aplikacje**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

   1. Na stronie aplikacji w chmurze kliknij **gotowe**.

1. Kliknij przycisk **co zrobić, jeśli**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji mają pokazaliśmy, jak można obliczyć symulowane logowania. Oprócz symulacji należy przetestować również zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby sprawdzić zasady, spróbuj zalogować się do swojej [witryny Azure portal](https://portal.azure.com) przy użyciu usługi **Isabella Simonsen** przetestować konto. Powinieneś zobaczyć okno dialogowe, które wymaga ustawienia konta dla dodatkowej weryfikacji zabezpieczeń.

![Uwierzytelnianie wieloskładnikowe](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń użytkownika testowego i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usunąć użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wymagane warunki użytkowania, należy zaakceptować](require-tou.md)
> [zablokować dostęp po wykryciu zagrożenia sesji](app-sign-in-risk.md)
