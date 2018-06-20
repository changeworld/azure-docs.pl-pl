---
title: 'Samouczek: Integracji Azure Active Directory z Intacct | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 1fb8a1ac4474e91671710ce1f84980f296ecd8df
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224267"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Samouczek: Integracji Azure Active Directory z Intacct

Z tego samouczka dowiesz się integrowanie Intacct z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Intacct zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Intacct
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Intacct (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Intacct, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Intacct logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Intacct z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-intacct-from-the-gallery"></a>Dodawanie Intacct z galerii
Aby skonfigurować integrację usługi Azure AD Intacct, należy dodać Intacct z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Intacct z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Intacct**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/tutorial_intacct_search.png)

5. W panelu wyników wybierz **Intacct**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/tutorial_intacct_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Intacct w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Intacct jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Intacct musi się.

W Intacct, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Intacct, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Intacct](#creating-an-intacct-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Intacct połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Intacct.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Intacct, wykonaj następujące czynności:**

1. W portalu Azure na **Intacct** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_intacct_samlbase.png)

3. Na **Intacct domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_intacct_url.png)

    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej Intacct](https://us.intacct.com/support) aby zyskać tę wartość.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_intacct_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Intacct** , kliknij przycisk **skonfigurować Intacct** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_intacct_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Intacct.

8. Kliknij przycisk **firmy** , a następnie kliknij pozycję **informacje o firmie**.

    ![Firmy](./media/intacct-tutorial/ic790037.png "firmy")

9. Kliknij przycisk **zabezpieczeń** , a następnie kliknij pozycję **Edytuj**.

    ![Zabezpieczenia](./media/intacct-tutorial/ic790038.png "zabezpieczeń")

10. W **logowania (SSO) z jednym** sekcji, wykonaj następujące czynności:

    ![Jednokrotne](./media/intacct-tutorial/ic790039.png "jednokrotne")

    a. Wybierz **włączenia funkcji logowania jednokrotnego w**.

    b. Jako **typ dostawcy tożsamości**, wybierz pozycję **SAML 2.0**.

    c. W **adres URL wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.
   
    d. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    e. Otwórz z **base-64** zakodowane certyfikatów w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu** pole.
   
    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/intacct-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-intacct-test-user"></a>Tworzenie użytkownika testowego Intacct

Aby skonfigurować użytkowników usługi Azure AD, aby móc zalogować się do Intacct, muszą mieć przydzielone do Intacct. Dla Intacct Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Intacct** dzierżawy.

2. Kliknij przycisk **firmy** , a następnie kliknij pozycję **użytkowników**.

    ![Użytkownicy](./media/intacct-tutorial/ic790041.png "użytkowników")
3. Kliknij przycisk **Dodaj** kartę.

    ![Dodaj](./media/intacct-tutorial/ic790042.png "Dodaj")
4. W **informacje o użytkowniku** sekcji, wykonaj następujące czynności:

    ![Informacje o użytkowniku](./media/intacct-tutorial/ic790043.png "informacje o użytkowniku")

    a. Wprowadź **identyfikator użytkownika**, **nazwisko**, **imię**, **adres E-mail**, **tytuł**i **Phone** konta usługi Azure AD, które chcesz udostępnić w **informacje o użytkowniku** sekcji.

    b. Wybierz **uprawnień administratora** konta usługi Azure AD, które chcesz udostępnić.
   
    c. Kliknij pozycję **Zapisz**. Właścicielem konta usługi Azure AD odbiera wiadomości e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

>[!NOTE]
>Aby udostępnić konta użytkownika usługi Azure AD, można użyć innych Intacct narzędzia do tworzenia konta użytkownika lub interfejsów API, które są dostarczane przez Intacct.
        
### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Intacct.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Intacct, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Intacct**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/intacct-tutorial/tutorial_intacct_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Intacct w panelu dostępu należy powinny być automatycznie zalogowany do aplikacji Intacct.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/intacct-tutorial/tutorial_general_01.png
[2]: ./media/intacct-tutorial/tutorial_general_02.png
[3]: ./media/intacct-tutorial/tutorial_general_03.png
[4]: ./media/intacct-tutorial/tutorial_general_04.png

[100]: ./media/intacct-tutorial/tutorial_general_100.png

[200]: ./media/intacct-tutorial/tutorial_general_200.png
[201]: ./media/intacct-tutorial/tutorial_general_201.png
[202]: ./media/intacct-tutorial/tutorial_general_202.png
[203]: ./media/intacct-tutorial/tutorial_general_203.png

