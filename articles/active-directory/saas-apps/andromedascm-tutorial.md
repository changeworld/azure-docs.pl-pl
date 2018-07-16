---
title: 'Samouczek: Integracja usługi Azure Active Directory z Andromeda | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 047e1ea6a474d95c57ffc2bdff5ad8a5c45e0d36
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048569"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Samouczek: Integracja usługi Azure Active Directory z Andromeda

W tym samouczku dowiesz się, jak zintegrować Andromeda w usłudze Azure Active Directory (Azure AD).

Integrowanie Andromeda z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Andromeda.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Andromeda (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Andromeda, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Andromeda logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Andromeda z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-andromeda-from-the-gallery"></a>Dodawanie Andromeda z galerii
Aby skonfigurować integrację Andromeda w usłudze Azure AD, należy dodać Andromeda z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Andromeda z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Andromeda**, wybierz opcję **Andromeda** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Andromeda na liście wyników](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Andromeda w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Andromeda do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Andromeda musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Andromeda, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Andromeda](#create-an-andromeda-test-user)**  — aby odpowiednikiem Britta Simon w Andromeda połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Andromeda.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Andromeda, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Andromeda** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Na **Andromeda domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Andromeda domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantURL>.ngcxpress.com/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Andromeda domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Podaną wcześniej wartością nie jest rzeczywistą wartość. Wartość zostanie zaktualizowana o rzeczywisty identyfikator, adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka.

5. Aplikacja Andromeda oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Czyści definicji przestrzeni nazw podczas konfigurowania tych.
    
6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | -------------------- |    
    | rola        | Aplikacji określonej roli |
    | type        | Typ aplikacji |
    | Firmy       | Nazwa firmy    |

    > [!NOTE]
    > Nie są rzeczywiste wartości. Te wartości są tylko w celach demonstracyjnych, użyj usługi ról w organizacji.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie Addattb rejestracji jednokrotnej](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji Andromeda** , kliknij przycisk **skonfigurować Andromeda** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Zaloguj się do witryny firmy Andromeda jako administrator.

11. W górnej części paska menu, kliknij przycisk **administratora** i przejdź do **administracji**.

    ![Administrator Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Na pasku narzędzi, w obszarze po lewej stronie **interfejsów** kliknij **plik konfiguracji SAML**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Na **plik konfiguracji SAML** sekcji strony, wykonaj następujące czynności:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Sprawdź **włączenia funkcji logowania jednokrotnego przy użyciu protokołu SAML**.

    b. W obszarze **informacji Andromeda** sekcji, skopiuj **tożsamości SP** wartość i wklej go w **identyfikator** pole tekstowe z **Andromeda domena i adresy URL** sekcji.

    c. Kopiuj **adres URL klienta** wartość i wklej go w **adres URL odpowiedzi** pole tekstowe z **Andromeda domena i adresy URL** sekcji.

    d. Kopiuj **adres URL logowania** wartość i wklej go w **adres URL logowania** pole tekstowe z **Andromeda domena i adresy URL** sekcji.

    e. W obszarze **dostawcy tożsamości SAML** sekcji, wpisz nazwę swojej tożsamości.

    f. W **pojedynczy znak w punkcie końcowym** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** , które zostały skopiowane z witryny Azure portal.

    g. Otwórz pobrany **certyfikatu szyfrowania Base64** z witryny Azure portal w programie Notatnik, wklej go do **X 509 certyfikatu** pola tekstowego.
    
    h. Mapuj następujące atrybuty z odpowiednią wartość w celu ułatwienia logowania SSO z usługi Azure AD. **Identyfikator użytkownika** atrybut jest wymagany do zalogowania się. Do obsługi administracyjnej, **E-mail**, **firmy**, **UserType**, i **roli** są wymagane. W tej sekcji zdefiniowano atrybuty mapowania (nazwy i wartości) które skorelować zdefiniowane w witrynie Azure portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/andromedascm-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-andromeda-test-user"></a>Tworzenie użytkownika testowego Andromeda

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Andromeda. Andromeda obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Andromeda, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Andromeda](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Andromeda.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Andromeda, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Andromeda**.

    ![Link Andromeda na liście aplikacji](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Andromeda w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Andromeda.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
