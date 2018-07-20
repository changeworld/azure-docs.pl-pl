---
title: 'Samouczek: Integracja usługi Azure Active Directory z usług Salesforce | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługą Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: jeedes
ms.openlocfilehash: a453e2d16edecda9753c2940a745b260a3a2b893
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160267"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Samouczek: Integracja usługi Azure Active Directory z usług Salesforce

W tym samouczku dowiesz się, jak zintegrować usługi Salesforce z usługą Azure Active Directory (Azure AD).

Integrowanie usługi Salesforce z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Salesforce.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowanych do usługi Salesforce (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi Salesforce, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Salesforce logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi Salesforce z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-salesforce-from-the-gallery"></a>Dodawanie usługi Salesforce z galerii
Aby skonfigurować integrację usług Salesforce do usługi Azure AD, należy dodać Salesforce za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi Salesforce z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Salesforce**, wybierz opcję **Salesforce** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SalesForce, na liście wyników](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Salesforce w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w usłudze Salesforce jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Salesforce musi zostać ustanowione.

W usłudze Salesforce, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego usługi Salesforce, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Salesforce](#create-a-salesforce-test-user)**  — aby odpowiednikiem Britta Simon w usłudze Salesforce, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Salesforce.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego usługi Salesforce, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Salesforce** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na **Salesforce domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a. W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca:

    Konta przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następującego wzorca:

    Konta przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klient Salesforce](https://help.salesforce.com/support) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/salesforce-tutorial/tutorial_general_400.png)

6. Na **Konfiguracja usługi Salesforce** , kliknij przycisk **skonfigurować Salesforce** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja usługi SalesForce](./media/salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Otwarcie nowej karty w przeglądarce i zaloguj się do konta administratora usługi Salesforce.

8. Kliknij pozycję **instalacji** w obszarze **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/configure1.png)

9. Przewiń w dół do **ustawienia** w okienku nawigacji kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso.png)

10. Na **ustawienia rejestracji jednokrotnej** kliknij **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Jeśli nie można włączyć ustawienia logowania jednokrotnego dla konta usługi Salesforce, może być konieczne skontaktowanie się z [zespołem pomocy technicznej klient Salesforce](https://help.salesforce.com/support). 

11. Wybierz **włączone SAML**, a następnie kliknij przycisk **Zapisz**.

      ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-enable-saml.png)
12. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **New**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Na **SAML pojedynczego logowania jednokrotnego ustawienie Edytuj** strony, wprowadź następujące ustawienia:

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-saml-config.png)

    a. Aby uzyskać **nazwa** wpisz przyjazną nazwę dla tej konfiguracji. Podanie wartości dla **nazwa** automatycznie wypełnić **Nazwa interfejsu API** pola tekstowego.

    b. W **wystawcy** pole, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z witryny Azure portal.

    c. W **polu tekstowym identyfikator jednostki**, wpisz nazwę domeny usługi Salesforce za pomocą następującego wzorca:

      * Konta przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`
      * Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    d. Aby przekazać **certyfikatu dostawcy tożsamości**, kliknij przycisk **wybierz plik** do przeglądania i wybierz plik certyfikatu, który został pobrany z witryny Azure portal.

    e. Jako **typu tożsamości SAML**, wybierz jedną z następujących opcji:

      * Wybierz **potwierdzenie zawiera Salesforce użytkownika**, jeśli Salesforce użytkownika jest przekazywany w potwierdzenie SAML

      * Wybierz **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**, jeśli identyfikator federacyjnej z obiektu użytkownika jest przekazywany w potwierdzenie SAML

      * Wybierz **potwierdzenie zawiera identyfikator korzystanie z obiektu użytkownika**, jeśli identyfikator użytkownika na podstawie obiektu użytkownika, który jest przekazywany w potwierdzenie SAML

    f. Dla **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentifier instrukcji podmiotu**.

    g. Dla **dostawcy inicjowane żądania powiązania usługi**, wybierz opcję **przekierowania HTTP**.

    h. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z witryny Azure portal

    i. Na koniec kliknij **Zapisz** Aby zastosować SAML pojedynczego logowania jednokrotnego ustawienia.

14. W okienku nawigacji po lewej stronie w usłudze Salesforce kliknij **ustawienia firmowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-my-domain.png)

15. Przewiń w dół do **konfiguracji uwierzytelniania** sekcji, a następnie kliknij przycisk **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-edit-auth-config.png)

16. W **konfiguracji uwierzytelniania** sekcji wyboru **AzureSSO** jako **na uwierzytelnianie** z konfiguracji logowania jednokrotnego SAML, a następnie kliknij **Zapisz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Jeśli wybrano więcej niż jedna usługa uwierzytelniania, użytkownicy są monitowani o wybierz usługę uwierzytelniania, które one chcesz zarejestrować się za pomocą podczas inicjowania logowania jednokrotnego do środowiska usługi Salesforce. Jeśli nie chcesz, aby się zdarzyć, a następnie należy **zaznaczaj wszystkich innych usług uwierzytelniania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/salesforce-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/salesforce-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/salesforce-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/salesforce-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-salesforce-test-user"></a>Tworzenie użytkownika testowego usługi Salesforce

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w usłudze Salesforce. SalesForce obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w usłudze Salesforce, jest tworzony nowy, przy próbie uzyskania dostępu do usługi Salesforce. SalesForce obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](salesforce-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Salesforce.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon, SalesForce, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Salesforce**.

    ![Link usługi Salesforce, na liście aplikacji](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Salesforce w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Salesforce.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png
