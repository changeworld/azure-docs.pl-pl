---
title: 'Samouczek: Integracja usługi Azure Active Directory z Piaskownicą Salesforce | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i piaskownicy usługi Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6794d7eaccb488bb345227161f0bca02f14bc518
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104967"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Samouczek: Integracja usługi Azure Active Directory z Piaskownicą Salesforce

W tym samouczku dowiesz się, jak zintegrować piaskownicy usługi Salesforce z usługą Azure Active Directory (Azure AD).

Piaskownice zapewniają możliwość tworzenia wielu kopii organizacji w osobnych środowisk dla różnych celów, takich jak rozwój, testowanie i szkolenia bez narażania danych i aplikacji w środowisku produkcyjnym usługi Salesforce organizacja.
Aby uzyskać więcej informacji, zobacz [Przegląd piaskownicy](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrowanie piaskownicy usługi Salesforce z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Salesforce piaskownicy.
- Użytkowników, aby automatycznie uzyskać zalogowanych do piaskownicy usługi Salesforce (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Piaskownicą Salesforce, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Piaskownica usługi Salesforce logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
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

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **piaskownicy usługi Salesforce**, wybierz opcję **piaskownicy usługi Salesforce** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Piaskownica usługi SalesForce, na liście wyników](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w piaskownicy usługi Salesforce dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w piaskownicy usługi Salesforce musi zostać ustanowione.

W piaskownicy usługi Salesforce, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Piaskownicą Salesforce, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego piaskownicy usługi Salesforce](#create-a-salesforce-sandbox-test-user)**  — aby odpowiednikiem Britta Simon w piaskownicy usługi Salesforce, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Salesforce piaskownicy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z piaskownicy usługi Salesforce, wykonaj następujące czynności:**

1. W witrynie Azure portal na **piaskownicy usługi Salesforce** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![Link do konfigurowania logowania jednokrotnego](./media/salesforce-sandbox-tutorial/tutorial_general_300.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Link do konfigurowania logowania jednokrotnego](./media/salesforce-sandbox-tutorial/tutorial_general_301.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.
   
    ![Link do konfigurowania logowania jednokrotnego](./media/salesforce-sandbox-tutorial/tutorial_general_302.png)

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](./media/salesforce-sandbox-tutorial/upload_metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](./media/salesforce-sandbox-tutorial/browse_upload_metadata.png)

    > [!NOTE]
    > W portalu administracyjnym piaskownicy usługi Salesforce, które zostało wyjaśnione w dalszej części tego samouczka zostanie wyświetlony plik metadanych dostawcy usług.

    c. Po pomyślnym przekazaniu pliku metadanych **adres URL odpowiedzi** wartość otrzymają automatycznie wypełnione w **adres URL odpowiedzi** pola tekstowego.

    ![Domena piaskownicy usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

6. Na **certyfikat podpisywania SAML** sekcji, kliknij na **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![Link do pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

7. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

8. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

9. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

11. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. Kliknij pozycję **Wybierz plik**, aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

14. Na stronie **Ustawienia logowania jednokrotnego SAML** pola zostaną wypełnione automatycznie. Kliknij pozycję Zapisz.

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

    ![Link do pobierania certyfikatu](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora w piaskownicy usługi Salesforce.

19. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

20. Przewiń w dół do **ustawienia** w okienku nawigacji po lewej stronie kliknij **tożsamości** aby rozwinąć sekcję powiązane. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

21. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

22. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

23. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

24. Kliknij przycisk **wybierz plik** do przekazania pliku XML metadanych, a następnie kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

25. Na **SAML pojedynczego ustawień logowania jednokrotnego** stronie pola automatycznie wypełnić, wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test*) w **nazwa** polu tekstowym i kliknij przycisk Zapisz.

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

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Tworzenie użytkownika testowego piaskownicy usługi Salesforce

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w piaskownicy usługi Salesforce, jest tworzony nowy, przy próbie uzyskania dostępu do piaskownicy usługi Salesforce. Piaskownica usługi SalesForce obsługuje również automatyczna aprowizacja użytkowników, więcej szczegółów można znaleźć [tutaj](salesforce-sandbox-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do piaskownicy usługi Salesforce.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon piaskownicy usługi Salesforce, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **piaskownicy usługi Salesforce**.

    ![Link piaskownicy usługi Salesforce, na liście aplikacji](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link „Użytkownicy i grupy”][202]

4. Kliknij przycisk **Dodaj użytkownika** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka piaskownicy usługi Salesforce w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji piaskownicy usługi Salesforce.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie aprowizacji użytkowników](salesforce-sandbox-provisioning-tutorial.md)

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
