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
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 6feafba41cf65a752dd5bf0819b0b93bacff0aff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054676"
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

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Na **Salesforce piaskownicy domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

   ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

   W **adres URL odpowiedzi** polu tekstowym wpisz konkretnej organizacji **adres URL odpowiedzi**.

   > [!NOTE]
   > Zaktualizuj wartość adresu URL odpowiedzi przy użyciu rzeczywistego adresu URL odpowiedzi, które zostało wyjaśnione w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(RAW)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. Na **konfiguracji piaskownicy usługi Salesforce** kliknij **konfigurowania piaskownicy usługi Salesforce** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

7. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

8. Kliknij pozycję **instalacji** w obszarze **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

9. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Na **ustawienia rejestracji jednokrotnej** kliknij **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

11. Wybierz **włączone SAML**, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **New**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Na **ustawienia rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config1.png)

    a. Wybierz **włączone SAML** pola wyboru.

    b. W **wystawcy** pole, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z witryny Azure portal.

    c. Aby przekazać **certyfikatu dostawcy tożsamości**, kliknij przycisk **Przeglądaj** do przeglądania i wybierz plik certyfikatu, który został pobrany z witryny Azure portal.

    d. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z witryny Azure portal.

    e. Jako **typu tożsamości SAML**, wybierz jedną z następujących opcji:

      * Wybierz **potwierdzenie zawiera Salesforce użytkownika**, jeśli Salesforce użytkownika jest przekazywany w potwierdzenie SAML

      * Wybierz **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**, jeśli identyfikator federacyjnej z obiektu użytkownika jest przekazywany w potwierdzenie SAML
  
    f. Jako **lokalizacji tożsamości SAML**, wybierz opcję **tożsamości jest elementem atrybutu**.

    g. SFDC nie obsługuje wylogowania protokołu SAML.  Jako obejście, Wklej `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` do **adres URL wylogowania niestandardowe** pola tekstowego.

    h. Kliknij pozycję **Zapisz**.

14. Na **ustawienia rejestracji jednokrotnej** kliknij **Pobieranie metadanych** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure4.png)

15. Otwórz pobrany metadanych w innej przeglądarce lub skopiować **lokalizacji** wartość i wklej go w **adres URL odpowiedzi** tekstowe na **Salesforce piaskownicy domena i adresy URL**sekcji w witrynie Azure portal.  

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure5.png)

16. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w następujące są wymagania wstępne w tym:

    a. Należy mieć zweryfikowanej domeny.

    b. Musisz skonfigurować i włączyć domenę w piaskownicy usługi Salesforce, kroki te zostały omówione w dalszej części tego samouczka.

    c. W witrynie Azure portal na **Salesforce piaskownicy domena i adresy URL** sekcji wyboru **Pokaż zaawansowane ustawienia adresu URL** i wykonaj następujące kroki:
  
    ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość powinny zostać skopiowane z portalu piaskownicy usługi Salesforce, po włączeniu domeny.

17. Na **certyfikat podpisywania SAML** kliknij **Certificate(RAW)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

19. Na **konfiguracji piaskownicy usługi Salesforce** kliknij **konfigurowania piaskownicy usługi Salesforce** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

20. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

21. Kliknij pozycję **instalacji** w obszarze **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

22. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

23. Na **ustawienia rejestracji jednokrotnej** kliknij **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

24. Wybierz **włączone SAML**, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

25. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **New**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

26. W przypadku dodawania drugiego wystąpienia, należy włączyć do domeny, jak wspomniano powyżej (SP inicjowane przypadek). W sekcji Ustawienia języka SAML pojedynczego logowania jednokrotnego wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. W **nazwa** polu tekstowym wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test*).

    b. W **wystawcy** pole, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z witryny Azure portal.

    c. W **identyfikator jednostki** pola tekstowego, użyj `https://test.salesforce.com` wartość dla pierwszego wystąpienia i z drugiego wystąpienia aplikacji, możesz użyć określonej wartości identyfikatora dzierżawy.

    d. Aby przekazać **certyfikatu dostawcy tożsamości**, kliknij przycisk **wybierz plik** do przeglądania i wybierz plik certyfikatu, który został pobrany z witryny Azure portal.

    e. Jako **typu tożsamości SAML**, wybierz jedną z następujących opcji:

      * Wybierz **potwierdzenie zawiera Salesforce użytkownika**, jeśli Salesforce użytkownika jest przekazywany w potwierdzenie SAML

      * Wybierz **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**, jeśli identyfikator federacyjnej z obiektu użytkownika jest przekazywany w potwierdzenie SAML

      * Wybierz **potwierdzenie zawiera identyfikator korzystanie z obiektu użytkownika**, jeśli identyfikator użytkownika na podstawie obiektu użytkownika, który jest przekazywany w potwierdzenie SAML

    f. Jako **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentifier instrukcji podmiotu**.

    g. Jako **dostawcy inicjowane żądania powiązania usługi**, wybierz opcję **żądania HTTP POST**.

    h. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z witryny Azure portal.

    i. SFDC nie obsługuje wylogowania protokołu SAML.  Jako obejście, Wklej `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` go do **adres URL wylogowania niestandardowe** pola tekstowego.

    j. Kliknij pozycję **Zapisz**.

27. Aby włączyć domenę w piaskownicy usługi Salesforce, wykonaj następujące czynności:

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

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

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

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

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