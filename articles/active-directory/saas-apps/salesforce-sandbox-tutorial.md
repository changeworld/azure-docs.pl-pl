---
title: 'Samouczek: Integracja usługi Azure Active Directory z Piaskownicą Salesforce | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i piaskownicy usługi Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904280"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Samouczek: Integracja usługi Azure Active Directory z Piaskownicą Salesforce

W tym samouczku dowiesz się, jak zintegrować piaskownicy usługi Salesforce z usługą Azure Active Directory (Azure AD).

Piaskownice zapewniają możliwość tworzenia wielu kopii organizacji w osobnych środowisk dla różnych celów, takich jak rozwój, testowanie i szkolenia bez narażania danych i aplikacji w środowisku produkcyjnym usługi Salesforce organizacja.
Aby uzyskać więcej informacji, zobacz [Przegląd piaskownicy](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrowanie piaskownicy usługi Salesforce z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Salesforce piaskownicy.
* Użytkownikom można automatycznie zalogowany do piaskownicy usługi Salesforce (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Piaskownicą Salesforce, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Piaskownica usługi SalesForce logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje piaskownicy usługi SalesForce **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez
* Obsługuje piaskownicy usługi SalesForce **Just In Time** aprowizacji użytkowników
* Obsługuje piaskownicy usługi SalesForce [ **automatyczne** aprowizacji użytkowników](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Dodawanie piaskownicy usługi Salesforce z galerii

Aby skonfigurować integrację z piaskownicy usługi Salesforce do usługi Azure AD, należy dodać piaskownicy usługi Salesforce z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać piaskownicy usługi Salesforce z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **piaskownicy usługi Salesforce**, wybierz opcję **piaskownicy usługi Salesforce** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Piaskownica usługi SalesForce, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce w oparciu o nazwie użytkownika testowego **Britta Simon**.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w piaskownicy usługi Salesforce dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w piaskownicy usługi Salesforce musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Piaskownicą Salesforce, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie usługi Salesforce piaskownicy logowania jednokrotnego](#configure-salesforce-sandbox-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego w piaskownicy usługi Salesforce](#create-salesforce-sandbox-test-user)**  — aby odpowiednikiem Britta Simon w piaskownicy usługi Salesforce, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **piaskownicy usługi Salesforce** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli masz **plik metadanych usługodawcy** i chcesz skonfigurować w **dostawcy tożsamości** zainicjowano tryb wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    > [!NOTE]
    > W portalu administracyjnym piaskownicy usługi Salesforce, które zostało wyjaśnione w dalszej części tego samouczka zostanie wyświetlony plik metadanych dostawcy usług.

    c. Po pomyślnym przekazaniu pliku metadanych **adres URL odpowiedzi** wartość otrzymają automatycznie wypełnione w **adres URL odpowiedzi** pola tekstowego.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Jeśli **adres URL odpowiedzi** wartości nie są automatycznie polulated, a następnie wprowadź wartość ręcznie zgodnie z wymaganiami.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **konfigurowania piaskownicy usługi Salesforce** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Konfigurowanie usługi Salesforce piaskownicy logowania jednokrotnego

1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

2. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

3. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

5. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kliknij pozycję **Wybierz plik**, aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na stronie **Ustawienia logowania jednokrotnego SAML** pola zostaną wypełnione automatycznie. Kliknij pozycję Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na **ustawienia rejestracji jednokrotnej** kliknij **Pobieranie metadanych** przycisk, aby pobrać plik metadanych dostawcy usługi. Użyj tego pliku w **podstawową konfigurację protokołu SAML** sekcji w portalu Azure dotyczące konfigurowania wymaganych adresów URL, jak wyjaśniono powyżej.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure4.png)

10. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w następujące są wymagania wstępne w tym:

    a. Należy mieć zweryfikowanej domeny.

    b. Musisz skonfigurować i włączyć domenę w piaskownicy usługi Salesforce, kroki te zostały omówione w dalszej części tego samouczka.

    c. W witrynie Azure portal na **podstawową konfigurację protokołu SAML** kliknij **Ustaw dodatkowe adresy URL** i wykonaj następujący krok:
  
    ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](common/both-signonurl.png)

    W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość powinny zostać skopiowane z portalu piaskownicy usługi Salesforce, po włączeniu domeny.

11. Na **certyfikat podpisywania SAML** kliknij **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

12. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

13. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

14. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

16. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Kliknij przycisk **wybierz plik** do przekazania pliku XML metadanych, a następnie kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na **SAML pojedynczego ustawień logowania jednokrotnego** stronie pola automatycznie wypełnić, wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test*) w polu **nazwa** polu tekstowym i kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Aby włączyć domenę w piaskownicy usługi Salesforce, wykonaj następujące czynności:

    > [!NOTE]
    > Przed włączeniem domeny, musisz utworzyć takie same w piaskownicy usługi Salesforce. Aby uzyskać więcej informacji, zobacz [Definiowanie Twojej nazwy domeny](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po utworzeniu domeny, upewnij się, że został on poprawnie skonfigurowany.

21. W okienku nawigacji po lewej stronie w piaskownicy usługi Salesforce kliknij **ustawienia firmowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. W **konfiguracji uwierzytelniania** kliknij **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. W **konfiguracji uwierzytelniania** sekcji jako **usługi uwierzytelniania**, wybierz nazwę SAML pojedynczego logowania jednokrotnego ustawienia określające podczas konfigurowania logowania jednokrotnego w piaskownicy usługi Salesforce i Kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do piaskownicy usługi Salesforce.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **piaskownicy usługi Salesforce**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **piaskownicy usługi Salesforce**.

    ![Link piaskownicy usługi Salesforce, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-salesforce-sandbox-test-user"></a>Tworzenie użytkownika testowego w piaskownicy usługi Salesforce

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w piaskownicy usługi Salesforce, jest tworzony nowy, przy próbie uzyskania dostępu do piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](salesforce-sandbox-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka piaskownicy usługi Salesforce w panelu dostępu, możesz powinny być automatycznie zalogowany do piaskownicy usługi Salesforce, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurowanie aprowizacji użytkowników](salesforce-sandbox-provisioning-tutorial.md)
