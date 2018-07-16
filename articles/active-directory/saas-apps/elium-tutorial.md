---
title: 'Samouczek: Integracja usługi Azure Active Directory z Elium | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 2957fffecbf448fa456d80200aba9752569b5f69
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042728"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Samouczek: Integracja usługi Azure Active Directory z Elium

W tym samouczku dowiesz się, jak zintegrować Elium w usłudze Azure Active Directory (Azure AD).

Integrowanie Elium z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Elium.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Elium (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Elium, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Elium logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Elium z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-elium-from-the-gallery"></a>Dodawanie Elium z galerii
Aby skonfigurować integrację Elium w usłudze Azure AD, należy dodać Elium z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Elium z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Elium**, wybierz opcję **Elium** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Elium na liście wyników](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Elium w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Elium do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Elium musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Elium, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Elium](#create-an-elium-test-user)**  — aby odpowiednikiem Britta Simon w Elium połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Elium.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Elium, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Elium** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/elium-tutorial/tutorial_elium_samlbase.png)

3. Na **Elium domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Elium domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/elium-tutorial/tutorial_elium_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Elium domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/elium-tutorial/tutorial_elium_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Otrzymasz te wartości z **plik metadanych SP** można pobrać `https://<platform-domain>.elium.com/login/saml2/metadata`, co jest opisane w dalszej części tego samouczka.

5. Aplikacja Elium oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/tutorial_attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
           
    | Nazwa atrybutu | Wartość atrybutu |   
    | ---------------| ----------------|
    | wyślij wiadomość e-mail   |User.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | Firmy| user.companyname|
    
    > [!NOTE]
    > Są to domyślne oświadczenia. **Oświadczenie adresu e-mail jest wymagany tylko**. W przypadku JIT również udostępniania tylko wiadomości e-mail oświadczenia jest obowiązkowe. Inne oświadczenia niestandardowe mogą się różnić od jednego klienta platformy na innej platformie klienta.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/tutorial_attribute_04.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw puste przestrzeni nazw.
    
    e. Kliknij przycisk **OK**. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/elium-tutorial/tutorial_elium_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/elium-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Elium.

8. Kliknij pozycję **profilu użytkownika** w prawym górnym rogu, a następnie wybierz **administracji**.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user1.png)

9. Wybierz **zabezpieczeń** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user2.png)

10. Przewiń w dół do **logowanie jednokrotne (SSO)** sekcji, a następnie wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user3.png)

    a. Skopiuj wartość **Sprawdź uwierzytelnianie SAML2 działa w przypadku konta** i wklej go w **adres URL logowania** tekstowe na **Elium domena i adresy URL** sekcję na platformie Azure Portal.

    > [!NOTE]
    > Po skonfigurowaniu rejestracji Jednokrotnej, zawsze można uzyskać dostęp domyślną stronę logowania zdalnego następującego adresu URL: `https://<platform_domain>/login/regular/login` 

    b. Wybierz **Federacji Włącz SAML2** pole wyboru.

    c. Wybierz **aprowizacji JIT** pola wyboru.

    d. Otwórz **metadanych SP** , klikając **Pobierz** przycisku.

    e. Wyszukaj **entityID** w **metadanych SP** plików, skopiuj **entityID** wartości, a następnie wklej je **identyfikator** tekstowe na  **Elium domena i adresy URL** sekcji w witrynie Azure portal. 

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user4.png)

    f. Wyszukaj **AssertionConsumerService** w **metadanych SP** plików, skopiuj **lokalizacji** wartości, a następnie wklej je **adres URL odpowiedzi** pole tekstowe na **Elium domena i adresy URL** sekcji w witrynie Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user5.png)

    g. Otwórz plik metadanych pobranych z witryny Azure portal do Notatnika, skopiuj zawartość i wklej go do **metadanych tożsamości** pola tekstowego.

    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/elium-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/elium-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/elium-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/elium-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-elium-test-user"></a>Tworzenie użytkownika testowego Elium

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Elium. Elium obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Elium, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Elium.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Elium, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Elium**.

    ![Link Elium na liście aplikacji](./media/elium-tutorial/tutorial_elium_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Elium w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Elium.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

