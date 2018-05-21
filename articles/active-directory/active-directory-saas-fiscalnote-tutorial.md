---
title: 'Samouczek: Integracja Azure usługi Active Directory z FiscalNote | Dokumenty Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej usługi Active Directory Azure i FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: a1c0175d0bb19e372730b850aa1f49a2c247b032
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Samouczek: Integracja Azure usługi Active Directory z FiscalNote

W tym samouczku omówiono sposób integracji z usługą Active Directory (Azure AD) FiscalNote.

Integracja z usługą Active Directory Azure FiscalNote zapewnia następujące korzyści:

- Można kontrolować w Azure AD, który ma dostęp do FiscalNote.
- Można włączyć użytkowników, aby automatycznie uzyskać podpisany, na FiscalNote (Single Sign-On) z ich rachunków Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować Integracja usługi Azure AD z FiscalNote, są potrzebne następujące elementy:

- Subskrypcję usługi Azure AD
- FiscalNote rejestracji jednokrotnej włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie FiscalNote do galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-fiscalnote-from-the-gallery"></a>Dodawanie FiscalNote do galerii
Aby skonfigurować integrację Azure AD FiscalNote, należy dodać FiscalNote z galerii do listy zarządzanych aplikacji władz akredytacji bezpieczeństwa.

**Aby dodać FiscalNote z galerii, należy wykonać następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **FiscalNote**, wybierz opcję **FiscalNote** z panelu wyników kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![FiscalNote na liście wyników](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz konfigurowania i testowania Azure AD rejestracji jednokrotnej z FiscalNote w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy Azure AD musi znać użytkownik odpowiednika w FiscalNote jest do użytkownika, w Azure AD. Innymi słowy konieczne jest ustanowienie relacji łącze między użytkownika Azure AD i danemu użytkownikowi w FiscalNote.

Do konfigurowania i testowania Azure AD rejestracji jednokrotnej z FiscalNote, musisz ukończyć następujących elementów konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Można utworzyć użytkownika testowego FiscalNote](#create-a-fiscalnote-test-user)**  - mają odpowiednik Britta Simon w FiscalNote, połączonej z reprezentacją Azure AD użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji zostanie włączone Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować rejestrację jednokrotną w aplikacji FiscalNote.

**Aby skonfigurować Azure AD rejestracji jednokrotnej z FiscalNote, należy wykonać następujące czynności:**

1. W portalu Azure na **FiscalNote** strona integracji aplikacji, kliknij przycisk **jednokrotne logowanie**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. Na **FiscalNote domeny i adresy URL** sekcji, należy wykonać następujące czynności:

    ![FiscalNote domeny i adresy URL pojedynczego logowania informacji](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Kontakt [zespół obsługi klienta FiscalNote](mailto:support@fiscalnote.com) można pobrać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. Aplikacja FiscalNote oczekuje potwierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu SAML. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:
           
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| ----------------|
    | name | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | wyślij wiadomość e-mail| User.mail|
    
    a. Usuń istniejące atrybuty i dodać nowe atrybuty. Kliknij na **Dodaj atrybut** otworzyć **dodać atrybut** okno dialogowe.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw puste przestrzeni nazw.
    
    e. Kliknij przycisk **OK**.

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_400.png)

8. Na **konfiguracji FiscalNote** sekcji, kliknij **skonfigurować FiscalNote** otworzyć **konfigurowania rejestracji** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Aby skonfigurować rejestrację jednokrotną na **FiscalNote** po stronie, musisz wysłać pobrane **Certificate(Raw), Sign-Out URL, identyfikator jednostki SAML i SAML pojedynczego logowania usługi** do [FiscalNote zespół pomocy technicznej](mailto:support@fiscalnote.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-fiscalnote-test-user"></a>Można utworzyć użytkownika testowego FiscalNote

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w FiscalNote. FiscalNote obsługuje w czasie inicjowania obsługi administracyjnej, która jest domyślnie włączona. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu do FiscalNote, jeśli go jeszcze nie istnieje.
>[!Note]
>Aby ręcznie utworzyć użytkownika, należy skontaktować się z [FiscalNote zespołu pomocy technicznej](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do używania Azure rejestracji jednokrotnej przez przyznanie dostępu do FiscalNote.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon FiscalNote, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **FiscalNote**.

    ![Łącze FiscalNote na liście aplikacji](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu dachówka FiscalNote w panelu dostępu, użytkownik powinien uzyskać automatycznie podpisany, na aplikacji FiscalNote.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_203.png

