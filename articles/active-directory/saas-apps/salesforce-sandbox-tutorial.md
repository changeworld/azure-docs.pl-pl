---
title: 'Samouczek: Integracja usługi Azure Active Directory z piaskownicy usługi Salesforce | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i piaskownicy usługi Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: 48dcb4a3b1e06ab62905092055ba1b48bd0dddb7
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114500"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Samouczek: Integracja usługi Azure Active Directory z piaskownicy usługi Salesforce

W tym samouczku dowiesz się, jak zintegrować piaskownicy usługi Salesforce z usługą Azure Active Directory (Azure AD).

Piaskownice zapewniają możliwość tworzenia wielu kopii organizacji w osobnych środowisk dla różnych celów, takich jak rozwój, testowanie i szkolenia bez narażania danych i aplikacji w środowisku produkcyjnym usługi Salesforce organizacja.
Aby uzyskać więcej informacji, zobacz [Przegląd piaskownicy](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrowanie piaskownicy usługi Salesforce z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Salesforce piaskownicy.
- Użytkowników, aby automatycznie uzyskać zalogowanych do piaskownicy usługi Salesforce (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Piaskownicą Salesforce, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Piaskownica usługi Salesforce logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie piaskownicy usługi Salesforce z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Dodawanie piaskownicy usługi Salesforce z galerii

Aby skonfigurować integrację z piaskownicy usługi Salesforce do usługi Azure AD, należy dodać piaskownicy usługi Salesforce z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać piaskownicy usługi Salesforce z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **piaskownicy usługi Salesforce**, wybierz opcję **piaskownicy usługi Salesforce** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Piaskownica usługi SalesForce, na liście wyników](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w piaskownicy usługi Salesforce dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w piaskownicy usługi Salesforce musi zostać ustanowione.

W piaskownicy usługi Salesforce, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Piaskownicą Salesforce, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego piaskownicy usługi Salesforce](#create-a-salesforce-sandbox-test-user)**  — aby odpowiednikiem Britta Simon w piaskownicy usługi Salesforce, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Salesforce piaskownicy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce, wykonaj następujące czynności:**

1. W witrynie Azure portal na **piaskownicy usługi Salesforce** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.
   
    ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    ![Przekaż plik metadanych](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    ![Wybierz plik metadanych](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > W portalu administracyjnym piaskownicy usługi Salesforce, które zostało wyjaśnione w dalszej części tego samouczka zostanie wyświetlony plik metadanych dostawcy usług.

    c. Po pomyślnym przekazaniu pliku metadanych **adres URL odpowiedzi** wartość otrzymają automatycznie wypełnione w **adres URL odpowiedzi** pola tekstowego.

    ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Na **certyfikat podpisywania SAML** sekcji, kliknij na **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

8. Kliknij pozycję **instalacji** w obszarze **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

9. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Na **ustawienia rejestracji jednokrotnej** kliknij **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

11. Wybierz **włączone SAML**, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **nowy z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Kliknij przycisk **wybierz plik** można przekazać pliku XML metadanych, który został pobrany z witryny Azure portal i kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Na **SAML pojedynczego ustawień logowania jednokrotnego** stronie automatycznie wypełnić pola i kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/salesforcexml.png)

15. Na **ustawienia rejestracji jednokrotnej** kliknij **Pobieranie metadanych** przycisk, aby pobrać plik metadanych dostawcy usługi. Użyj tego pliku w **podstawową konfigurację protokołu SAML** sekcji w portalu Azure dotyczące konfigurowania wymaganych adresów URL, jak wyjaśniono powyżej.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure4.png)

16. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w następujące są wymagania wstępne w tym:

    a. Należy mieć zweryfikowanej domeny.

    b. Musisz skonfigurować i włączyć domenę w piaskownicy usługi Salesforce, kroki te zostały omówione w dalszej części tego samouczka.

    c. W witrynie Azure portal na **podstawową konfigurację protokołu SAML** kliknij **Ustaw dodatkowe adresy URL** i wykonaj następujący krok:
  
    ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość powinny zostać skopiowane z portalu piaskownicy usługi Salesforce, po włączeniu domeny.

17. Na **certyfikat podpisywania SAML** kliknij **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

19. Kliknij pozycję **instalacji** w obszarze **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

20. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Na **ustawienia rejestracji jednokrotnej** kliknij **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

22. Wybierz **włączone SAML**, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **nowy z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Kliknij przycisk **wybierz plik** do przekazania pliku XML metadanych, a następnie kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Na **SAML pojedynczego ustawień logowania jednokrotnego** stronie pola automatycznie wypełnić, wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test*) w **nazwa** pole tekstowe i Kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

26. Aby włączyć domenę w piaskownicy usługi Salesforce, wykonaj następujące czynności:

    > [!NOTE]
    > Przed włączeniem domeny, musisz utworzyć takie same w piaskownicy usługi Salesforce. Aby uzyskać więcej informacji, zobacz [Definiowanie Twojej nazwy domeny](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po utworzeniu domeny, upewnij się, że został on poprawnie skonfigurowany.

    * W okienku nawigacji po lewej stronie w piaskownicy usługi Salesforce kliknij **ustawienia firmowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena**.

         ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * W **konfiguracji uwierzytelniania** kliknij **Edytuj**.

        ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * W **konfiguracji uwierzytelniania** sekcji jako **usługi uwierzytelniania**, wybierz nazwę SAML pojedynczego logowania jednokrotnego ustawienia określające podczas konfigurowania logowania jednokrotnego w piaskownicy usługi Salesforce i Kliknij przycisk **Zapisz**.

        ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Tworzenie użytkownika testowego piaskownicy usługi Salesforce

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w piaskownicy usługi Salesforce, jest tworzony nowy, przy próbie uzyskania dostępu do piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](salesforce-sandbox-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do piaskownicy usługi Salesforce.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon piaskownicy usługi Salesforce, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **piaskownicy usługi Salesforce**.

    ![Link piaskownicy usługi Salesforce, na liście aplikacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj użytkownika** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka piaskownicy usługi Salesforce w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji piaskownicy usługi Salesforce.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](salesforce-sandbox-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png