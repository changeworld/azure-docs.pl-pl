---
title: Przewodnik Szybki Start — Wymagaj uwierzytelniania wieloskładnikowego (MFA) dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start dowiesz się, jak powiązać Twoje wymagania dotyczące uwierzytelniania dla typu aplikacji w chmurze używanych przy użyciu dostępu warunkowego usługi Azure Active Directory (Azure AD).
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy w usłudze Azure AD, zabezpieczenia dostępu do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/30/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbcb0271a1bd80f0f7155c379de7b5149c76fcca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520432"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Szybki start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory 

Aby uprościć środowisko logowania użytkowników, można zezwolić im na Zaloguj się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak w wielu środowiskach ma co najmniej kilka aplikacji, dla których zaleca się wymagają silniejszego formularz weryfikacji konta, takie jak uwierzytelnianie wieloskładnikowe (MFA). Może to być na przykład wartość true, aby uzyskać dostęp do systemu poczty e-mail w organizacji lub aplikacji działu KADR. W usłudze Azure Active Directory (Azure AD) można osiągnąć ten cel, za pomocą zasad dostępu warunkowego.    

Ten przewodnik Szybki Start przedstawia sposób konfigurowania [zasad dostępu warunkowego usługi Azure AD](../active-directory-conditional-access-azure-portal.md) wymagającym uwierzytelniania wieloskładnikowego dla wybranej chmury aplikacji w danym środowisku.

![Tworzenie zasad](./media/app-based-mfa/32.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten scenariusz, w tym przewodniku Szybki Start, potrzebne są:

- **Dostęp do usługi Azure AD — wersja Premium** — dostęp warunkowy usługi Azure AD jest możliwość usługi Azure AD Premium. 

- **Konto testowe o nazwie Isabella Simonsen** — Jeśli nie wiesz, jak tworzyć konta testowego, zobacz [Dodawanie użytkowników w chmurze](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


Scenariusz, w tym przewodniku Szybki Start wymaga dla poszczególnych użytkowników usługi MFA nie włączenia konta testu. Aby uzyskać więcej informacji, zobacz [jak, które wymuszają weryfikację dwuetapową dla użytkownika](../authentication/howto-mfa-userstates.md).


## <a name="test-your-sign-in"></a>Testowanie logowania

Celem tego kroku jest sobie pogląd logowania bez zasad dostępu warunkowego.

**Aby zainicjować środowisko:**

1. Zaloguj się do witryny Azure portal jako Isabella Simonsen.

2. Wyloguj się.


## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego 

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany. Scenariusz, w tym przewodniku Szybki Start używa:

- Witryny Azure portal jako symbol zastępczy dla aplikacji w chmurze, która wymaga uwierzytelniania Wieloskładnikowego. 
- Przykładowy użytkownik do testowania zasad dostępu warunkowego.  

Ustaw w zasadach:

|Ustawienie |Wartość|
|---     | --- |
|Użytkownicy i grupy | Isabella Simonsen |
|Aplikacje w chmurze | Microsoft Azure Management |
|Udzielanie dostępu | Wymagaj uwierzytelniania wieloskładnikowego |
 

![Tworzenie zasad](./media/app-based-mfa/31.png)

 


**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

2. W witrynie Azure portal, w lewym pasku nawigacyjnym kliknij **usługi Azure Active Directory**. 

    ![Usługa Azure Active Directory](./media/app-based-mfa/02.png)

3. Na **usługi Azure Active Directory** stronie **zabezpieczeń** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/app-based-mfa/03.png)
 
4. Na **dostępu warunkowego** w pasku narzędzi u góry, kliknij **nowe zasady**.

    ![Add](./media/app-based-mfa/04.png)

5. Na **New** strony w **nazwa** polu tekstowym wpisz **wymagają usługi MFA, aby uzyskać dostęp do portalu Azure**.

    ![Name (Nazwa)](./media/app-based-mfa/05.png)

6. W **przypisania** kliknij **użytkowników i grup**.

    ![Użytkownicy i grupy](./media/app-based-mfa/06.png)

7. Na **użytkowników i grup** strony, wykonaj następujące czynności:

    ![Użytkownicy i grupy](./media/app-based-mfa/24.png)

    a. Kliknij przycisk **Wybieranie użytkowników i grup**, a następnie wybierz pozycję **użytkowników i grup**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

    d. Na **użytkowników i grup** kliknij **gotowe**.

8. Kliknij przycisk **aplikacje w chmurze**.

    ![Aplikacje w chmurze](./media/app-based-mfa/08.png)

9. Na **aplikacje w chmurze** strony, wykonaj następujące czynności:

    ![Wybierz aplikacje w chmurze](./media/app-based-mfa/26.png)

    a. Kliknij przycisk **Wybierz aplikacje**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

    d. Na **aplikacje w chmurze** kliknij **gotowe**.


10. W **kontrole dostępu** kliknij **Grant**.

    ![Kontrole dostępu](./media/app-based-mfa/10.png)

11. Na **Grant** strony, wykonaj następujące czynności:

    ![Udziel](./media/app-based-mfa/11.png)

    a. Wybierz **udzielić dostępu**.

    a. Wybierz **Wymagaj uwierzytelniania wieloskładnikowego**.

    b. Kliknij pozycję **Wybierz**.

12. W **Włącz zasady** kliknij **na**.

    ![Włącz zasady](./media/app-based-mfa/18.png)

13. Kliknij pozycję **Utwórz**.


## <a name="evaluate-a-simulated-sign-in"></a>Oceń symulowane logowania

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działają one zgodnie z oczekiwaniami. Pierwszym krokiem należy używać dostępu warunkowego, zasady narzędzie analizy warunkowej w celu symulowania logowania dla użytkownika testowego. Symulacja szacuje wpływ tego logowania na zasady i generuje raport symulacji.  

Można zainicjować co, jeśli narzędzie oceny zasad, ustaw:

- **Isabella Simonsen** jako użytkownik 
- **Usługa Microsoft Azure Management** jako aplikacja w chmurze

  Klikając **co zrobić, jeśli** tworzy raport symulacji, który pokazuje:

- **Wymagać uwierzytelniania Wieloskładnikowego, aby uzyskać dostęp do portalu Azure** w obszarze **zasady, które będą miały zastosowanie** 
- **Wymagaj uwierzytelniania wieloskładnikowego** jako **Udziel kontroli**.

![Co zrobić, jeśli narzędzie zasad](./media/app-based-mfa/23.png)



**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  
 
    ![What If](./media/app-based-mfa/14.png)

2. Kliknij przycisk **użytkowników**, wybierz opcję **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

    ![Użytkownik](./media/app-based-mfa/15.png)

2. Aby wybrać aplikację w chmurze, wykonaj następujące czynności:

    ![Aplikacje w chmurze](./media/app-based-mfa/16.png)

    a. Kliknij przycisk **aplikacje w chmurze**.

    b. Na **stronę aplikacji w chmurze**, kliknij przycisk **Wybierz aplikacje**.

    c. Kliknij pozycję **Wybierz**.

    d. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

    e. Na stronie aplikacji w chmurze kliknij **gotowe**.

3. Kliknij przycisk **co zrobić, jeśli**.


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
