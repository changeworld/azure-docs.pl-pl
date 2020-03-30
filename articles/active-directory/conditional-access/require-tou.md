---
title: Dostęp warunkowy wymaga warunków użytkowania — usługa Azure Active Directory
description: W tym przewodniku Szybki start dowiesz się, jak wymagać, aby warunki użytkowania były akceptowane, zanim dostęp do wybranych aplikacji w chmurze zostanie przyznany przez dostęp warunkowy usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74380100"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Szybki start: wymagaj akceptowania warunków użytkowania przed uzyskiwaniem dostępu do aplikacji w chmurze

Przed uzyskaniem dostępu do niektórych aplikacji w chmurze w danym środowisku możesz chcieć uzyskać zgodę od użytkowników w formie akceptowania warunków użytkowania (ToU). Dostęp warunkowy usługi Azure Active Directory (Azure AD) zapewnia:

- Prosta metoda konfigurowania ToU
- Opcja wymagania zaakceptowania warunków użytkowania za pośrednictwem zasad dostępu warunkowego  

Ten przewodnik Szybki start pokazuje, jak skonfigurować [zasady dostępu warunkowego usługi Azure AD,](../active-directory-conditional-access-azure-portal.md) która wymaga zaakceptowania wyliczenie witryn w chmurze dla wybranej aplikacji w chmurze w twoim środowisku.

![Tworzenie zasad](./media/require-tou/5555.png)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć scenariusz w tym przewodniku Szybki start, potrzebujesz:

- **Dostęp do usługi Azure AD Premium —** usługa Azure AD Conditional Access to usługa Azure AD Premium.
- **Konto testowe o nazwie Isabella Simonsen** - Jeśli nie wiesz, jak utworzyć konto testowe, zobacz [Dodawanie użytkowników w chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest uzyskanie wrażenia logowania bez zasad dostępu warunkowego.

**Aby przetestować logowanie:**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako Isabella Simonsen.
1. Wyloguj się.

## <a name="create-your-terms-of-use"></a>Tworzenie warunków użytkowania

W tej sekcji przedstawiono kroki tworzenia przykładowego ToU. Podczas tworzenia wyliczenie zasad wybrano wartość **wymuszania za pomocą szablonów zasad dostępu warunkowego**. Wybranie **opcji Zasady niestandardowe** powoduje otwarcie okna dialogowego w celu utworzenia nowej zasady dostępu warunkowego zaraz po utworzeniu WY.

**Aby utworzyć warunki użytkowania:**

1. W programie Microsoft Word utwórz nowy dokument.
1. Wpisz **Moje warunki użytkowania**, a następnie zapisz dokument na komputerze jako **mytou.pdf**.
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. W witrynie Azure portal na lewym pasku nawigacyjnym kliknij pozycję **Usługa Azure Active Directory**.

   ![Usługa Azure Active Directory](./media/require-tou/02.png)

1. Na stronie **Usługi Azure Active Directory** w sekcji **Zabezpieczenia** kliknij pozycję **Dostęp warunkowy**.

   ![Dostęp warunkowy](./media/require-tou/03.png)

1. W sekcji **Zarządzanie** kliknij pozycję **Warunki użytkowania**.

   ![Warunki użytkowania](./media/require-tou/04.png)

1. W menu u góry kliknij pozycję **Nowe terminy**.

   ![Warunki użytkowania](./media/require-tou/05.png)

1. Na stronie **Nowe warunki użytkowania:**

   ![Warunki użytkowania](./media/require-tou/112.png)

   1. W polach tekstowych **Nazwa** wpisz **moje wyplomicie**.
   1. W polach tekstowych **Nazwa wyświetlana** wpisz **moje wylinie .**
   1. Prześlij swoje warunki użytkowania pliku PDF.
   1. Jako **język**, wybierz **angielski**.
   1. Ponieważ **wymagasz od użytkowników, aby rozwinąć warunki użytkowania**, wybierz **wł**.
   1. Jako **Wymuszaj za pomocą szablonów zasad dostępu warunkowego**wybierz pozycję **Zasady niestandardowe**.
   1. Kliknij przycisk **Utwórz**.

## <a name="create-your-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

W tej sekcji pokazano, jak utworzyć wymagane zasady dostępu warunkowego. Scenariusz w tym przewodniku Szybki start używa:

- Witryna Azure portal jako symbol zastępczy dla aplikacji w chmurze, która wymaga zaakceptowania wyliczonej przez użytkownika. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

W zasadach ustaw:

| Ustawienie | Wartość |
| --- | --- |
| Użytkownicy i grupy | Izabela Simonsen |
| Aplikacje w chmurze | Microsoft Azure Management |
| Udzielanie dostępu | Mój TOU |

![Tworzenie zasad](./media/require-tou/1234.png)

**Aby skonfigurować zasady dostępu warunkowego:**

1. Na stronie **Nowy** w polu tekstowym **Nazwa** wpisz **Polecenie Wymagaj wylicznika dla Izabeli**.

   ![Nazwa](./media/require-tou/71.png)

1. W sekcji **Przydział kliknij** pozycję **Użytkownicy i grupy**.

   ![Użytkownicy i grupy](./media/require-tou/06.png)

1. Na stronie **Użytkownicy i grupy:**

   ![Użytkownicy i grupy](./media/require-tou/24.png)

   1. Kliknij **pozycję Wybierz użytkowników i grupy**, a następnie wybierz pozycję **Użytkownicy i grupy**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Isabella Simonsen**, a następnie kliknij przycisk **Zaznacz**.
   1. Na stronie **Użytkownicy i grupy** kliknij pozycję **Gotowe**.
1. Kliknij pozycję **Aplikacje w chmurze**.

   ![Aplikacje w chmurze](./media/require-tou/08.png)

1. Na stronie **Aplikacje w chmurze:**

   ![Wybieranie aplikacji w chmurze](./media/require-tou/26.png)

   1. Kliknij **pozycję Wybierz aplikacje**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Microsoft Azure Management**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie **Aplikacje w chmurze** kliknij pozycję **Gotowe**.
1. W sekcji **Formanty dostępu** kliknij pozycję **Przyznaj**.

   ![Kontrole dostępu](./media/require-tou/10.png)

1. Na stronie **Grant:**

   ![Uprawnienie](./media/require-tou/111.png)

   1. Wybierz **pozycję Przyznaj dostęp**.
   1. Wybierz **pozycję Mój TOU**.
   1. Kliknij **pozycję Wybierz**.
1. W sekcji **Włącz zasady** kliknij pozycję **Włączone**.

   ![Włączanie zasad](./media/require-tou/18.png)

1. Kliknij przycisk **Utwórz**.

## <a name="evaluate-a-simulated-sign-in"></a>Ocena symulowanego logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa zgodnie z oczekiwaniami. W pierwszym kroku użyj narzędzia Dostępu warunkowego, jeśli narzędzie zasad do symulowania logowania użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Aby zainicjować narzędzie do oceny zasad **Co jeśli,** ustaw:

- **Isabella Simonsen** jako użytkownik
- **Usługa Microsoft Azure Management** jako aplikacja w chmurze

Kliknięcie przycisku **Co jeśli** powoduje utworzenie raportu symulacji, który pokazuje:

- **Wymagaj WU dla Izabeli** w obszarze **Zasady, które będą miały zastosowanie**
- **Moje WU** jako **Grant Controls**.

![Co zrobić, jeśli narzędzie zasad](./media/require-tou/79.png)

**Aby ocenić zasady dostępu warunkowego:**

1. Na stronie [Dostęp warunkowy — Zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij pozycję Co **jeśli**.  

   ![What If](./media/require-tou/14.png)

1. Kliknij **pozycję Użytkownicy**, wybierz pozycję **Isabella Simonsen**, a następnie kliknij przycisk **Wybierz**.

   ![Użytkownik](./media/require-tou/15.png)

1. Aby wybrać aplikację w chmurze:

   ![Aplikacje w chmurze](./media/require-tou/16.png)

   1. Kliknij pozycję **Aplikacje w chmurze**.
   1. Na **stronie Aplikacje**w chmurze kliknij pozycję **Wybierz aplikacje**.
   1. Kliknij **pozycję Wybierz**.
   1. Na stronie **Wybierz** wybierz pozycję **Microsoft Azure Management**, a następnie kliknij przycisk **Wybierz**.
   1. Na stronie aplikacje w chmurze kliknij pozycję **Gotowe**.
1. Kliknij **przycisk Co jeśli**.

## <a name="test-your-conditional-access-policy"></a>Testowanie zasad dostępu warunkowego

W poprzedniej sekcji dowiesz się, jak ocenić symulowane logowanie. Oprócz symulacji należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami.

Aby przetestować zasady, spróbuj zalogować się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta testowego **Isabella Simonsen.** Powinno zostać wyświetlone okno dialogowe, które wymaga zaakceptowania warunków użytkowania.

![Warunki użytkowania](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, usuń użytkownika testowego i zasady dostępu warunkowego:

- Jeśli nie wiesz, jak usunąć użytkownika usługi Azure AD, zobacz [Usuwanie użytkowników z usługi Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Aby usunąć zasady, wybierz zasady, a następnie kliknij przycisk **Usuń** na pasku narzędzi szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/require-tou/33.png)

- Aby usunąć warunki użytkowania, zaznacz je, a następnie kliknij pozycję **Usuń terminy** na pasku narzędzi u góry.

    ![Uwierzytelnianie wieloskładnikowe](./media/require-tou/29.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wymagaj usługi MFA dla określonych aplikacji](app-based-mfa.md)
> [Blokuj dostęp po wykryciu ryzyka sesji](app-sign-in-risk.md)
