---
title: Szybki Start - wymusić uwierzytelnianie wieloskładnikowe (MFA) dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Z tego przewodnika Szybki Start dowiesz się, jak można powiązać wymagań uwierzytelniania do typu aplikacji w chmurze używanych przy użyciu dostępu warunkowego w usłudze Azure Active Directory (Azure AD).
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 475b0229b9e627a56b02d2299ee2e5400aa0ede1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Szybki Start: Wymagają usługi MFA dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory 

Aby uprościć proces logowania użytkowników, można zezwolić im na Zaloguj się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak w wielu środowiskach jest co najmniej kilka aplikacji, dla których zaleca się wymagają silniejszego formę weryfikacji konta, takich jak uwierzytelnianie wieloskładnikowe (MFA). Może to być na przykład wartość true, dostępu do systemu poczty e-mail w organizacji lub HR aplikacji. W usłudze Azure Active Directory (Azure AD) można wykonywać w tym celu za pomocą zasad dostępu warunkowego.    

Ta opcja szybkiego startu przedstawia sposób konfigurowania [zasady dostępu warunkowego usługi Azure AD](active-directory-conditional-access-azure-portal.md) wymagającego uwierzytelniania wieloskładnikowego dla wybranej chmury aplikacji w danym środowisku.

![Tworzenie zasad](./media/active-directory-conditional-access-app-based-mfa/32.png)


Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten scenariusz, w tym szybkiego startu, należy:

- **Dostęp do usługi Azure AD — wersja Premium** -dostępu warunkowego dla usługi Azure AD jest możliwość usługi Azure AD Premium. 

- **Konto testu o nazwie Isabella Simonsen** — Jeśli nie wiadomo, jak utworzyć testowe konto, zobacz [Dodaj użytkowników w chmurze](add-users-azure-active-directory.md#add-cloud-based-users).



## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego 

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany. Scenariusz, w tym szybkiego startu używa:

- Portalu Azure jako symbolu zastępczego dla aplikacji w chmurze, która wymaga usługi MFA. 
- Przykładowe użytkownika do testowania zasad dostępu warunkowego.  

Ustaw w zasadach:

|Ustawienie |Wartość|
|---     | --- |
|Użytkownicy i grupy | Isabella Simonsen |
|Aplikacje w chmurze | Zarządzania Microsoft Azure |
|Udzielanie dostępu | Wymagaj uwierzytelniania wieloskładnikowego |
 

![Tworzenie zasad](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do Twojego [portalu Azure](https://portal.azure.com) jako administrator globalny.

2. W portalu Azure na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**. 

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Na **dostępu warunkowego** na pasku narzędzi u góry, kliknij przycisk **Dodaj**.

    ![Add](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Na **nowy** strony w **nazwa** pole tekstowe, typ **wymagają usługi MFA dla dostępu do portalu Azure**.

    ![Name (Nazwa)](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. W **przypisania** kliknij **użytkowników i grup**.

    ![Użytkownicy i grupy](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Na **użytkowników i grup** wykonaj następujące czynności:

    ![Użytkownicy i grupy](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Kliknij przycisk **Wybierz użytkowników i grupy**, a następnie wybierz **użytkowników i grup**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

    d. Na **użytkowników i grup** kliknij przycisk **gotowe**.

8. Kliknij przycisk **aplikacji w chmurze**.

    ![Aplikacje w chmurze](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Na **aplikacji w chmurze** wykonaj następujące czynności:

    ![Wybierz aplikacje w chmurze](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. Kliknij przycisk **Wybierz aplikacje**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** wybierz **Microsoft Azure Management**, a następnie kliknij przycisk **wybierz**.

    d. Na **aplikacji w chmurze** kliknij przycisk **gotowe**.


10. W **dostęp do formantów** kliknij **Grant**.

    ![Kontrole dostępu](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. Na **Grant** wykonaj następujące czynności:

    ![Udziel](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Wybierz **udzielić dostępu**.

    a. Wybierz **wymusić uwierzytelnianie wieloskładnikowe**.

    b. Kliknij pozycję **Wybierz**.

12. W **Włącz zasady** kliknij **na**.

    ![Włącz zasady](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Kliknij przycisk **Utwórz**.


## <a name="evaluate-a-simulated-sign-in"></a>Ocena symulowane logowanie

Teraz, gdy skonfigurowano zasady dostępu warunkowego, prawdopodobnie chcesz wiedzieć, czy działa on zgodnie z oczekiwaniami. Pierwszym krokiem należy użyć dostępu warunkowego, co zrobić, jeśli zasady narzędzia, aby symulować logowanie dla użytkownika testowego. Symulacja szacuje wpływu tego logowania ma na zasad i generuje raport symulacji.  

Zainicjować co jeśli narzędzie oceny zasad, wartość:

- **Isabella Simonsen** jako użytkownik 
- **Microsoft Azure Management** jako aplikacji w chmurze

 Kliknięcie przycisku **co zrobić, jeśli** tworzy symulacji raportu, który zawiera:

- **Uwierzytelniania MFA można wymagać dla dostępu do portalu Azure** w obszarze **zasad, które będą miały zastosowania** 
- **Wymagaj uwierzytelniania wieloskładnikowego** jako **formanty Grant**.

![Co zrobić, jeśli narzędzie zasad](./media/active-directory-conditional-access-app-based-mfa/23.png)



**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Kliknij przycisk **użytkowników**, wybierz pozycję **Isabella Simonsen**, a następnie kliknij przycisk **wybierz**.

    ![Użytkownik](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Aby wybrać aplikacji w chmurze, wykonaj następujące czynności:

    ![Aplikacje w chmurze](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Kliknij przycisk **aplikacji w chmurze**.

    b. Na **strona aplikacje w chmurze**, kliknij przycisk **Wybierz aplikacje**.

    c. Kliknij pozycję **Wybierz**.

    d. Na **wybierz** wybierz Microsoft Azure Management **, a następnie kliknij przycisk **wybierz**.

    e. Na stronie aplikacji w chmurze kliknij **gotowe**.

3. Kliknij przycisk **co zrobić, jeśli**.


## <a name="test-your-conditional-access-policy"></a>Testowanie zasady dostępu warunkowego

W poprzedniej sekcji zapoznaniu do oceny symulowane logowanie. Oprócz symulację należy również przetestować zasady dostępu warunkowego, aby upewnić się, że działa zgodnie z oczekiwaniami. 

Aby przetestować zasady, spróbuj zalogować się do sieci [portalu Azure](https://portal.azure.com) przy użyciu programu **Isabella Simonsen** przetestować konto. Powinny zostać wyświetlone okno dialogowe, które należy ustawić konta do dodatkowej weryfikacji zabezpieczeń.

![Uwierzytelnianie wieloskładnikowe](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń użytkownika testowego i zasad dostępu warunkowego:

- Jeśli nie wiadomo, jak usunąć użytkownika usługi Azure AD, zobacz [usunąć użytkowników z usługi Azure AD](add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Aby usunąć zasady, wybierz zasady, a następnie kliknij **usunąć** na pasku narzędzi Szybki dostęp.

    ![Uwierzytelnianie wieloskładnikowe](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

