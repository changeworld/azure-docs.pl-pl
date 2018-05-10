---
title: Szybki Start — skonfiguruj dla aplikacji usługi MFA w chmurze przy użyciu dostępu warunkowego usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można powiązać wymagań uwierzytelniania do typu aplikacji w chmurze używanych przy użyciu dostępu warunkowego w usłudze Azure Active Directory (Azure AD).
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ac43817fb3f253c35cd69a8ecd8931afca50892b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-configure-per-cloud-app-mfa-with-azure-active-directory-conditional-access"></a>Szybki Start: Skonfiguruj dla aplikacji usługi MFA w chmurze przy użyciu dostępu warunkowego usługi Azure Active Directory 


Aby uprościć proces logowania użytkowników, można zezwolić im na Zaloguj się do aplikacji w chmurze przy użyciu nazwy użytkownika i hasła. Jednak w wielu środowiskach jest co najmniej kilka aplikacji, dla których zaleca się wymagają silniejszego formę weryfikacji konta, takich jak uwierzytelnianie wieloskładnikowe. Może to być na przykład wartość true, dostępu do systemu poczty e-mail w organizacji lub HR aplikacji.  

Ta opcja szybkiego startu pokazuje, jak wymusić uwierzytelnianie wieloskładnikowe tylko dla zestawu aplikacji w wybranej chmurze w środowisku za pomocą [zasady dostępu warunkowego usługi Azure AD](active-directory-conditional-access-azure-portal.md).

Aby ukończyć scenariusz, w tym szybkiego startu, możesz:


> [!div class="checklist"]
> * Utwórz zasady dostępu warunkowego
> * Oceń zasady dostępu warunkowego
> * Testowanie zasady dostępu warunkowego  


## <a name="scenario-description"></a>Opis scenariusza

Scenariusz, w tym artykule używa portalu Azure jako symbolu zastępczego dla aplikacji w chmurze, która wymaga usługi Multi-Factor authentication dla określonego użytkownika. Simona Britta jest użytkownikiem w organizacji. Gdy użytkownik zaloguje się do portalu Azure, ma swoje dodatkową weryfikację swoje konto przy użyciu uwierzytelniania wieloskładnikowego.

![Uwierzytelnianie wieloskładnikowe](./media/active-directory-conditional-access-app-based-mfa/01.png)



## <a name="before-you-begin"></a>Przed rozpoczęciem 

Aby ukończyć ten scenariusz, w tym szybkiego startu, należy:

- **Dostęp do usługi Azure AD — wersja Premium** -dostępu warunkowego dla usługi Azure AD jest możliwość usługi Azure AD Premium. Jeśli nie masz dostępu do usługi Azure AD — wersja Premium, możesz [tworzenia konta wersji próbnej](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Konto testu o nazwie Simona Britta** — Jeśli nie wiadomo, jak utworzyć testowe konto, przeczytaj [tych instrukcji](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>Utwórz zasady dostępu warunkowego 

W tej sekcji przedstawiono sposób tworzenia zasad dostępu warunkowego wymagany.  
W zasadach można ustawić następujące czynności:

|Ustawienie |Wartość|
|---     | --- |
|Użytkownicy i grupy | Simona Britta |
|Aplikacje w chmurze | Zarządzania Microsoft Azure |
|Udziel | Wymagaj uwierzytelniania wieloskładnikowego |
 

![Tworzenie zasad](./media/active-directory-conditional-access-app-based-mfa/12.png)




**Aby skonfigurować zasady dostępu warunkowego:**

1. Zaloguj się do Twojego [portalu Azure](https://portal.azure.com) jako administrator globalny.

2. W portalu Azure na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**. 

    ![Usługa Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. Na **usługi Azure Active Directory** strony w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. Na **dostępu warunkowego** na pasku narzędzi u góry, kliknij przycisk **Dodaj**.

    ![Add](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. Na **nowy** strony w **nazwa** pole tekstowe, typ **wymagają usługi MFA dla Britta**.

    ![Name (Nazwa)](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. W **przypisania** kliknij **użytkowników i grup**.

    ![Użytkownicy i grupy](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. Na **użytkowników i grup** wykonaj następujące czynności:

    ![Użytkownicy i grupy](./media/active-directory-conditional-access-app-based-mfa/07.png)

    a. Kliknij przycisk **Wybierz użytkowników i grupy**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** , wybierz użytkownika testowego, a następnie kliknij przycisk **wybierz**.

    d. Na **użytkowników i grup** kliknij przycisk **gotowe**.

8. Kliknij przycisk **aplikacji w chmurze**.

    ![Aplikacje w chmurze](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. Na **aplikacji w chmurze** wykonaj następujące czynności:

    ![Wybierz aplikacje w chmurze](./media/active-directory-conditional-access-app-based-mfa/09.png)

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


## <a name="evaluate-your-conditional-access-policy"></a>Oceń zasady dostępu warunkowego

Aby zrozumieć wpływ zasad dostępu warunkowego w środowisku, można użyć [warunkowego dostępu co, jeśli narzędzie zasad](active-directory-conditional-access-whatif.md). Za pomocą tego narzędzia, możesz obliczyć symulowane logowanie użytkownika.

Po skonfigurowaniu narzędzie z **Simona Britta** jako użytkownik i **Microsoft Azure Management** jako aplikacji w chmurze zawiera narzędzie **wymagają usługi MFA dla Britta** w obszarze  **Zasady dotyczące**.

![Co zrobić, jeśli narzędzie zasad](./media/active-directory-conditional-access-app-based-mfa/17.png)



**Aby ocenić zasady dostępu warunkowego:**

1. Na [dostępu warunkowego — zasady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) w menu u góry kliknij **co zrobić, jeśli**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Kliknij przycisk **użytkowników**, wybierz pozycję **Simona Britta**, a następnie kliknij przycisk **wybierz**.

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

Aby przetestować zasady, spróbuj zalogować się do sieci [portalu Azure](https://portal.azure.com) przy użyciu programu **Simona Britta** przetestować konto. Powinny zostać wyświetlone okno dialogowe, które należy ustawić konta do dodatkowej weryfikacji zabezpieczeń.

![Uwierzytelnianie wieloskładnikowe](./media/active-directory-conditional-access-app-based-mfa/01.png)


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

