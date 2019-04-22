---
title: Przewodnik Szybki Start — wymagają warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze, które są chronione przez dostęp warunkowy usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak można wymagać, że warunki użytkowania są akceptowane przed uzyskaniem dostępu do aplikacji w chmurze wybranych przez dostęp warunkowy usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c37a58cb9f9d1082d02854f43e511e5431d90c13
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894255"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Szybki start: Wymagane warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze

Przed uzyskaniem dostępu do niektórych aplikacji w chmurze w danym środowisku, można uzyskać zgodę od użytkowników w postaci zaakceptowania Twoich warunków użytkowania (ToU). Dostęp warunkowy usługi Azure Active Directory (Azure AD) zapewnia:

- Prosta metoda do skonfigurowania warunków użytkowania
- Możliwość wymagane zaakceptowanie warunków użytkowania za pośrednictwem zasad dostępu warunkowego  

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) wymagającym użytkowania, należy zaakceptować dla wybranej chmury aplikacji w danym środowisku.

![Tworzenie zasad](./media/require-tou/5555.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten scenariusz, w tym przewodniku Szybki Start, potrzebne są:

- **Dostęp do usługi Azure AD — wersja Premium** — dostęp warunkowy usługi Azure AD jest możliwość usługi Azure AD Premium.
- **Konto testowe o nazwie Isabella Simonsen** — Jeśli nie wiesz, jak tworzyć konta testowego, zobacz [Dodawanie użytkowników w chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest sobie pogląd logowania bez zasad dostępu warunkowego.

**Aby przetestować logowanie:**

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com/) jako Isabella Simonsen.
1. Wyloguj się.

## <a name="create-your-terms-of-use"></a>Utwórz warunki użytkowania

Ta sekcja zawiera kroki pozwalające utworzyć przykładowy warunków użytkowania. Podczas tworzenia warunków użytkowania, wybierz wartość dla **Wymuś za pomocą szablonów zasad dostępu warunkowego**. Wybieranie **zasady niestandardowe** otwiera okno dialogowe, aby utworzyć nowe zasady dostępu warunkowego, zaraz po utworzeniu swoje warunki użytkowania.

**Aby utworzyć warunki użytkowania:**

1. W programie Microsoft Word utwórz nowy dokument.

1. Typ **Moje warunki użytkowania**, a następnie zapisz go na komputerze jako **mytou.pdf**.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

1. W witrynie Azure portal, w lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/require-tou/02.png)

1. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

   ![Dostęp warunkowy](./media/require-tou/03.png)

1. W **Zarządzaj** kliknij **warunki użytkowania**.

   ![Warunki użytkowania](./media/require-tou/04.png)

1. W menu u góry kliknij **nowe warunki**.

   ![Warunki użytkowania](./media/require-tou/05.png)

1. Na **nowe warunki użytkowania** strony:

   ![Warunki użytkowania](./media/require-tou/112.png)

   1. W **nazwa** polu tekstowym wpisz **Moje warunków użytkowania**.

   1. W **nazwę wyświetlaną** polu tekstowym wpisz **Moje warunków użytkowania**.

   1. Przekaż swoje warunki Użyj pliku PDF.

   1. Jako **języka**, wybierz opcję **angielski**.

   1. Jako **Wymagaj od użytkowników rozwinięcia warunków użytkowania**, wybierz opcję **na**.

   1. Jako **Wymuś za pomocą szablonów zasad dostępu warunkowego**, wybierz opcję **zasady niestandardowe**.

   1. Kliknij pozycję **Utwórz**.

## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany. Scenariusz, w tym przewodniku Szybki Start używa:

- Witryny Azure portal jako symbol zastępczy dla aplikacji w chmurze, wymagającego swoje warunki użytkowania, należy zaakceptować. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

Ustaw w zasadach:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Isabella Simonsen |
| Aplikacje w chmurze | Microsoft Azure Management |
| Udzielanie dostępu | Moje warunków użytkowania |

![Tworzenie zasad](./media/require-tou/1234.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Na **nowy** stronie **nazwa** polu tekstowym wpisz **wymagają warunków użytkowania dla Isabella**.

   ![Name (Nazwa)](./media/require-tou/71.png)

1. W **przypisania** kliknij **użytkowników i grup**.

   ![Użytkownicy i grupy](./media/require-tou/06.png)

1. Na **użytkowników i grup** strony:

   ![Użytkownicy i grupy](./media/require-tou/24.png)

   1. Kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz pozycję **użytkowników i grup**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

   1. Na **użytkowników i grup** kliknij **gotowe**.

1. Kliknij przycisk **aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/require-tou/08.png)

1. Na **aplikacje w chmurze** strony:

   ![Wybierz aplikacje w chmurze](./media/require-tou/26.png)

   1. Kliknij przycisk **Wybierz aplikacje**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

   1. Na **aplikacje w chmurze** kliknij **gotowe**.

1. W **kontrole dostępu** kliknij **Grant**.

   ![Kontrole dostępu](./media/require-tou/10.png)

1. Na **Grant** strony:

   ![Udziel](./media/require-tou/111.png)

   1. Wybierz **udzielić dostępu**.

   1. Wybierz **Moje warunków użytkowania**.

   1. Kliknij pozycję **Wybierz**.

1. W **Włącz zasady** kliknij **na**.

   ![Włącz zasady](./media/require-tou/18.png)

1. Kliknij pozycję **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działają one zgodnie z oczekiwaniami. Pierwszym krokiem należy używać dostępu warunkowego, zasady narzędzie analizy warunkowej w celu symulowania logowania dla użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Można zainicjować co, jeśli narzędzie oceny zasad, ustaw:

- **Isabella Simonsen** jako użytkownik
- **Usługa Microsoft Azure Management** jako aplikacja w chmurze

Klikając **co zrobić, jeśli** tworzy raport symulacji, który pokazuje:

- **Wymagaj warunków użytkowania dla Isabella** w obszarze **zasady, które będą miały zastosowanie**
- **Moje warunków użytkowania** jako **Udziel kontroli**.

![Co zrobić, jeśli narzędzie zasad](./media/require-tou/79.png)

**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  

   ![What If](./media/require-tou/14.png)

1. Kliknij przycisk **użytkowników**, wybierz opcję **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

   ![Użytkownik](./media/require-tou/15.png)

1. Aby wybrać aplikację w chmurze:

   ![Aplikacje w chmurze](./media/require-tou/16.png)

   1. Kliknij przycisk **aplikacje w chmurze**.

   1. Na **stronę aplikacji w chmurze**, kliknij przycisk **Wybierz aplikacje**.

   1. Kliknij pozycję **Wybierz**.

   1. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

   1. Na stronie aplikacji w chmurze kliknij **gotowe**.

1. Kliknij przycisk **co zrobić, jeśli**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji mają pokazaliśmy, jak można obliczyć symulowane logowania. Oprócz symulacji należy przetestować również zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby sprawdzić zasady, spróbuj zalogować się do swojej [witryny Azure portal](https://portal.azure.com) przy użyciu usługi **Isabella Simonsen** przetestować konto. Powinieneś zobaczyć okno dialogowe, które wymaga zaakceptowania Twoich warunków użytkowania.

![Warunki użytkowania](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń użytkownika testowego i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [usunąć użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi Szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/require-tou/33.png)

- Aby usunąć warunki użytkowania, zaznacz go, a następnie kliknij **warunków usuwania** na pasku narzędzi u góry.

    ![Uwierzytelnianie wieloskładnikowe](./media/require-tou/29.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji](app-based-mfa.md)
> [zablokować dostęp po wykryciu zagrożenia sesji](app-sign-in-risk.md)
