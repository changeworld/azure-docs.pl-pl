---
title: 'Samouczek: Integracji Azure Active Directory z usług Salesforce | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: e5b9fb0e1696e83ca4dfc8936613631f4c84ca47
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590786"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Samouczek: Integracji Azure Active Directory z usług Salesforce

Z tego samouczka dowiesz się Integrowanie usługi Salesforce z usługą Azure Active Directory (Azure AD).

Integrowanie usługi Salesforce z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usług Salesforce.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usług Salesforce (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usług Salesforce, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Salesforce logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie usługi Salesforce z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-salesforce-from-the-gallery"></a>Dodawanie usługi Salesforce z galerii
Aby skonfigurować integrację usługi Salesforce z usługą Azure AD, należy dodać usługi Salesforce z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Salesforce z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Salesforce**, wybierz pozycję **Salesforce** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Usługi SalesForce z listy wyników](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usług Salesforce w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w usłudze Salesforce jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w usłudze Salesforce musi określone.

W usłudze Salesforce, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usług Salesforce, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Salesforce](#create-a-salesforce-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Salesforce połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Salesforce.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usług Salesforce, wykonaj następujące czynności:**

1. W portalu Azure na **Salesforce** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Na **domeny Salesforce i adres URL** sekcji, wykonaj następujące czynności:

    ![Domena usługi SalesForce i adresy URL pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. W **adres URL logowania** tekstowym, wpisz wartość, przy użyciu następującego wzorca:
    
    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konta dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. W **identyfikator** tekstowym, wpisz wartość, przy użyciu następującego wzorca:
    
    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konta dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta usług Salesforce](https://help.salesforce.com/support) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Salesforce** , kliknij przycisk **skonfigurować Salesforce** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja usług SalesForce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora usługi Salesforce.

8. Polecenie **Instalator** w obszarze **ikonę ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Przewiń w dół do **ustawienia** w okienku nawigacji kliknij **tożsamości** do rozwiń sekcję pokrewne. Następnie kliknij przycisk **ustawień rejestracji jednokrotnej**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. Na **ustawień rejestracji jednokrotnej** kliknij przycisk **Edytuj** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Jeśli nie można włączyć logowanie jednokrotne ustawień konta usług Salesforce, konieczne może być skontaktuj się z [zespołem pomocy technicznej klienta usług Salesforce](https://help.salesforce.com/support). 

11. Wybierz **włączone SAML**, a następnie kliknij przycisk **zapisać**.

      ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Aby skonfigurować SAML pojedynczego logowania jednokrotnego ustawienia, kliknij przycisk **nowy**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. Na **SAML pojedynczego logowania jednokrotnego ustawienie Edytuj** strony, sprawdź następujące konfiguracje:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Aby uzyskać **nazwa** wpisz przyjazną nazwę dla tej konfiguracji. Wartość dla **nazwa** automatycznie wypełnić **Nazwa interfejsu API** pola tekstowego.

    b. W **wystawcy** pól, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    c. W **textbox identyfikator jednostki**, wpisz nazwę domeny witryny Salesforce przy użyciu następującego wzorca:
      
      * Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`
      * Konta dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Aby przekazać **certyfikat dostawcy tożsamości**, kliknij przycisk **wybierz plik** Przeglądaj i wybierz plik certyfikatu, który został pobrany z portalu Azure.

    e. Jako **typ tożsamości SAML**, wybierz jedną z następujących opcji:
    
      * Wybierz **potwierdzenia zawiera Salesforce użytkownika**, jeśli Salesforce użytkownika jest przekazywany potwierdzenia języka SAML

      * Wybierz **potwierdzenia zawiera identyfikator federacji z obiektu użytkownika**, jeśli identyfikator federacyjnej z obiektu użytkownika jest przekazywany potwierdzenia języka SAML

      * Wybierz **potwierdzenia zawiera identyfikator używany z obiektu użytkownika**, jeśli identyfikator użytkownika z obiektu użytkownika jest przekazywany potwierdzenia języka SAML

    f. Dla **lokalizacji tożsamości SAML**, wybierz pozycję **jest tożsamość w elemencie NameIdentifier instrukcji podmiotu**.

    g. Dla **dostawcy zainicjował żądanie powiązania usługi**, wybierz pozycję **przekierowywanie HTTP**.

    h. W **adresu URL logowania do dostawcy tożsamości** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure
    
    i. Na koniec kliknij **zapisać** dotyczyć SAML pojedynczego logowania jednokrotnego ustawień.

14. W lewym okienku nawigacji w aplikacji Salesforce, kliknij polecenie **ustawienia firmy** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moje domeny**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Przewiń w dół do **konfiguracji uwierzytelniania** sekcji, a następnie kliknij polecenie **Edytuj** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. W **konfiguracji uwierzytelniania** sekcji wyboru **strony logowania** jako **uwierzytelniania na** z logowania jednokrotnego SAML konfiguracji, a następnie kliknij  **Zapisz**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Jeśli zostanie wybrana więcej niż jedna usługa uwierzytelniania, użytkownicy są monit o wybranie usługi uwierzytelniania, która ich, takich jak się zalogować podczas inicjowania rejestracji jednokrotnej w środowisku usług Salesforce. Jeśli nie chcesz, aby mieć miejsce, a następnie wykonaj następujące czynności **pozostawienie jej niezaznaczonej innych usług uwierzytelniania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-salesforce-test-user"></a>Tworzenie użytkownika testowego usług Salesforce

W tej sekcji użytkownika o nazwie Simona Britta jest tworzony w Salesforce. SalesForce obsługę w czasie, który jest domyślnie włączona. Nie ma elementu akcji można w tej sekcji. Jeśli użytkownik nie istnieje w usłudze Salesforce, nowy jest tworzony przy próbie uzyskania dostępu do usług Salesforce. SalesForce również obsługę użytkowników, można znaleźć więcej szczegółów [tutaj](active-directory-saas-salesforce-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do usług Salesforce.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta Salesforce, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **Salesforce**.

    ![Łącze Salesforce na liście aplikacji](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Salesforce w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Salesforce.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png
