---
title: 'Samouczek: Integracji Azure Active Directory z Cisco Webex | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 76980214daf6e7dd019c56db089095ae121b853f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215355"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Samouczek: Integracji Azure Active Directory z Cisco Webex

W tym samouczku Dowiedz się jak zintegrować Cisco Webex w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Cisco Webex zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Cisco Webex.
- Można umożliwić użytkownikom automatycznie pobrać zalogowany do Cisco Webex z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Cisco Webex, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Cisco Webex pojedynczy znak z włączoną subskrypcji

> [!NOTE]
> Nie zaleca się używanie środowiska produkcyjnego do testowania czynności w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [Pobierz bezpłatną wersję próbną miesięcznego](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Cisco Webex z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-cisco-webex-from-the-gallery"></a>Dodaj Cisco Webex z galerii
Aby skonfigurować integrację usługi Azure AD Cisco Webex, należy dodać Cisco Webex z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Cisco Webex z galerii, należy wykonać następujące czynności:**

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Cisco Webex**. 

5. Wybierz **Cisco Webex** z panelu wyników. Następnie wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Cisco Webex na liście wyników](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Webex Cisco oparte na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, komu użytkownika odpowiednika w Cisco Webex jest użytkownikiem w usłudze Azure AD. Innymi słowy należy ustanowić powiązanie użytkownika usługi Azure AD i powiązanych użytkowników w Cisco Webex.

W Cisco Webex, należy podać wartość **Username** tę samą wartość jak **nazwy użytkownika** w usłudze Azure AD. Teraz ustanowieniu połączenia między dwóch użytkowników. 

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Cisco Webex, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego Cisco Webex](#create-a-cisco-webex-test-user) w celu zapewnienia odpowiednikiem Simona Britta Webex Cisco, połączonej z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#test-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Cisco Webex.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Cisco Webex, należy wykonać następujące czynności:**

1. W portalu Azure na **Cisco Webex** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okna dialogowego, **tryb** listy rozwijanej wybierz **na języku SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. W **Cisco Webex domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Webex Cisco pojedynczy informacje logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującego wzorca: `https://<subdomain>.webex.com`

    b. W **identyfikator** wpisz adres URL `http://www.webex.com`.

    c. W **adres URL odpowiedzi** wpisz adres URL z następującego wzorca: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z adresem URL odpowiedzi rzeczywiste i logowania jednokrotnego adresu URL. Skontaktuj się z [zespołem pomocy technicznej klienta Webex Cisco](https://www.webex.co.in/support/support-overview.html) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** wybierz opcję **XML metadanych**, a następnie zapisz plik metadanych na komputerze.

    ![Łącze pobierania certyfikatu](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej przycisk zapisywania](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
6. W **konfiguracji Webex Cisco** zaznacz **skonfigurować Webex Cisco** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL**, **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** z **krótkimi opisami** sekcji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Cisco Webex.

8. Wybierz z menu u góry **administrowania lokacją**.

    ![Administrowanie lokacją](./media/cisco-webex-tutorial/ic777621.png "lokacji administracyjnej")

9. W **zarządzania witryną** zaznacz **konfiguracji logowania jednokrotnego**.
   
    ![Konfiguracja rejestracji Jednokrotnej](./media/cisco-webex-tutorial/ic777622.png "konfiguracji logowania jednokrotnego")

10. W **federacyjnych konfiguracji Usługa rejestracji Jednokrotnej w sieci Web** sekcji, wykonaj następujące czynności:
   
    ![Federacyjna usługa rejestracji Jednokrotnej w konfiguracji](./media/cisco-webex-tutorial/ic777623.png "Federacyjna usługa rejestracji Jednokrotnej w konfiguracji")  

    a. W **protokołu federacyjnego** listy, wybierz **SAML 2.0**.

    b. Dla **profilu rejestracji Jednokrotnej**, wybierz pozycję **inicjowane SP**.

    c. Otwórz w Notatniku pobranego certyfikatu, a następnie skopiuj zawartość.

    d. Wybierz **Importowanie metadanych SAML**, a następnie wkleić skopiowanej zawartości certyfikatu.

    e. W **wystawca SAML (identyfikator IdP)** Wklej wartość **identyfikator jednostki SAML** skopiowany z portalu Azure.

    f. W **adres URL dla klientów logowania jednokrotnego usługi logowania** Wklej **SAML pojedynczy znak na adres URL usługi**, który został skopiowany z portalu Azure.

    g. Z **NameID Format** listy, wybierz **adres E-mail**.

    h. W **AuthnContextClassRef** wpisz **urn: oasis: nazwy: tc: SAML:2.0:ac:classes:Password**.

    i. W **adres URL wylogowania usługi logowania jednokrotnego dla klientów** Wklej **Sign-Out URL**, który został skopiowany z portalu Azure.
   
    j. Wybierz **aktualizacji**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i uzyskuje dostęp do osadzonego Dokumentacja za pośrednictwem **konfiguracji** sekcji u dołu. Możesz przeczytać dodatkowe informacje o funkcji dokumentacji osadzony w [usługi Azure AD osadzonych dokumentacji](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-cisco-webex-test-user"></a>Tworzenie użytkownika testowego Cisco Webex

Aby umożliwić użytkownikom usługi Azure AD do logowania się na Cisco Webex, muszą mieć przydzielone w Cisco Webex. W przypadku Cisco Webex Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, należy wykonać następujące czynności:**

1. Zaloguj się do Twojego **Cisco Webex** dzierżawy.

2. Przejdź do **Zarządzanie użytkownikami** > **dodać użytkownika**.
   
    ![Dodawanie użytkowników](./media/cisco-webex-tutorial/ic777625.png "Dodawanie użytkowników")

3. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Dodaj użytkownika](./media/cisco-webex-tutorial/ic777626.png "Dodaj użytkownika")   

    a. Aby uzyskać **typ konta**, wybierz pozycję **hosta**.

    b. W **imię** wpisz imię użytkownika (w tym przypadku **Britta**).

    c. W **nazwisko** wpisz nazwisko użytkownika (w tym przypadku **Simona**).

    d. W **Username** wpisz adres e-mail użytkownika (w tym przypadku **Brittasimon@contoso.com**).

    e. W **E-mail** wpisz adres e-mail użytkownika (w tym przypadku **Brittasimon@contoso.com**).

    f. W **hasło** wpisz hasło użytkownika.

    g. W **Potwierdź** hasło polu, wprowadź ponownie hasło użytkownika.

    h. Wybierz pozycję **Dodaj**.

>[!NOTE]
>Można użyć innych narzędzi tworzenia konta użytkownika Cisco Webex lub interfejsów API, które są dostarczane przez firmy Cisco Webex do udostępnienia konta użytkownika usługi Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do przyznania im dostęp do Cisco Webex za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Cisco Webex, należy wykonać następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji. Następny, przejdź do widoku katalogu, a następnie do **aplikacje dla przedsiębiorstw**.  

2. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

3. Na liście aplikacji zaznacz **Cisco Webex**.

    ![Łącze Cisco Webex na liście aplikacji](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** w **użytkowników** listy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. Wybierz **przypisać** przycisk **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu kafelka Cisco Webex w panelu dostępu, można automatycznie pobrać zalogowany do aplikacji Cisco Webex.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

