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
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 36f1bd9c11c8932968a3501ef22fdb7153411256
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867565"
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

- Subskrypcji usługi Azure AD
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

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

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

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

    ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-tutorial/tutorial_general_300.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-tutorial/tutorial_general_301.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.
   
    ![Skonfigurować łącze rejestracji jednokrotnej](./media/salesforce-tutorial/tutorial_general_302.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![Domena usługi SalesForce i adresy URL pojedynczego logowania jednokrotnego informacji](./media/salesforce-tutorial/tutorial_salesforce_url.png)

    a. W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca:

    Konta przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następującego wzorca:

    Konta przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klient Salesforce](https://help.salesforce.com/support) do uzyskania tych wartości.

6. Na **certyfikat podpisywania SAML** sekcji, kliknij na **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![Link pobierania certyfikatu](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

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

12. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij **nowy z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Kliknij przycisk **wybierz plik** można przekazać pliku XML metadanych, który został pobrany z witryny Azure portal i kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/xmlchoose.png)

14. Na **SAML pojedynczego ustawień logowania jednokrotnego** stronie automatycznie wypełnić pola i kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/salesforcexml.png)

15. W okienku nawigacji po lewej stronie w usłudze Salesforce kliknij **ustawienia firmowe** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-my-domain.png)

16. Przewiń w dół do **konfiguracji uwierzytelniania** sekcji, a następnie kliknij przycisk **Edytuj** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-edit-auth-config.png)

17. W **konfiguracji uwierzytelniania** sekcji wyboru **AzureSSO** jako **na uwierzytelnianie** z konfiguracji logowania jednokrotnego SAML, a następnie kliknij **Zapisz** .

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Jeśli wybrano więcej niż jedna usługa uwierzytelniania, użytkownicy są monitowani o wybierz usługę uwierzytelniania, które one chcesz zarejestrować się za pomocą podczas inicjowania logowania jednokrotnego do środowiska usługi Salesforce. Jeśli nie chcesz, aby się zdarzyć, a następnie należy **zaznaczaj wszystkich innych usług uwierzytelniania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/salesforce-tutorial/create_aaduser_02.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

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

4. Kliknij przycisk **Dodaj użytkownika** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

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
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
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