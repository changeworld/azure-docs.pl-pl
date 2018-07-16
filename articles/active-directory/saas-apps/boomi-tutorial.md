---
title: 'Samouczek: Integracja usługi Azure Active Directory z Boomi | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: e0128d4422c462d4424583306af0b30174178bac
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049256"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Samouczek: Integracja usługi Azure Active Directory z Boomi

W tym samouczku dowiesz się, jak zintegrować Boomi w usłudze Azure Active Directory (Azure AD).

Integrowanie Boomi z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Boomi.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Boomi (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Boomi, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Boomi logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Boomi z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-boomi-from-the-gallery"></a>Dodawanie Boomi z galerii
Aby skonfigurować integrację Boomi w usłudze Azure AD, należy dodać Boomi z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Boomi z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Boomi**, wybierz opcję **Boomi** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Boomi na liście wyników](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Boomi w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Boomi do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Boomi musi można ustanowić.

W Boomi, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Boomi, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Boomi](#create-a-boomi-test-user)**  — aby odpowiednikiem Britta Simon w Boomi połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Boomi.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Boomi, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Boomi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

3. Na **Boomi domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Boomi domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://platform.boomi.com/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Wartość adresu URL odpowiedzi nie jest prawdziwe. Zaktualizuj wartość za pomocą rzeczywisty adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Boomi](https://boomi.com/company/contact/) można uzyskać wartość.
 
4. Aplikacja Boomi oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/boomi-tutorial/tutorial_attribute.png)

5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, dla każdego wiersza pokazano w poniższej tabeli, wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | --------------- |
    | FEDERATION_ID | User.mail |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/boomi-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Boomi** , kliknij przycisk **skonfigurować Boomi** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Boomi](./media/boomi-tutorial/tutorial_boomi_configure.png) 

9. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Boomi, jako administrator. 

10. Przejdź do **nazwa firmy** i przejdź do **Konfigurowanie**.

11. Kliknij przycisk **opcje logowania jednokrotnego** karcie, a następnie wykonaj poniższe kroki.

    ![Konfigurowanie logowania jednokrotnego w aplikacji po stronie](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Sprawdź **Włącz SAML logowania jednokrotnego** pola wyboru.

    b. Kliknij przycisk **importu** można przekazać certyfikatu pobrany z usługi Azure AD do **certyfikatu dostawcy tożsamości**.
    
    c. W **adres URL logowania dostawcy tożsamości** pola tekstowego, umieść wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z okna konfiguracji aplikacji usługi Azure AD.

    d. Jako **lokalizacja identyfikator Federacji**, wybierz opcję **identyfikator federacyjnej jest w elemencie atrybut FEDERATION_ID** przycisku radiowego. 

    e. Kliknij przycisk **Zapisz** przycisku.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/boomi-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/boomi-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/boomi-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/boomi-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-boomi-test-user"></a>Tworzenie użytkownika testowego Boomi

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Boomi, musi być obsługiwana w Boomi. W przypadku Boomi Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Boomi jako administrator.

2. Po zalogowaniu się przejdź do **Zarządzanie użytkownikami** i przejdź do **użytkowników**.

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_001.png "użytkowników")

3. Kliknij przycisk **+** ikonę i **ról użytkownika Add/Obsługa** zostanie otwarte okno dialogowe.

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_002.png "użytkowników")

    ![Użytkownicy](./media/boomi-tutorial/tutorial_boomi_003.png "użytkowników")

    a. W **adres e-mail użytkownika** polu tekstowym wpisz adres e-mail użytkownika, takie jak BrittaSimon@contoso.com.
    
    b. W **imię** polu tekstowym Nazwa typu pierwszego użytkownika, takich jak Britta.

    c. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon.
    
    d. Wprowadź użytkownika **identyfikator federacyjnej**. Każdy użytkownik musi mieć identyfikator federacyjnej, która jednoznacznie identyfikuje użytkowników w ramach konta.
    
    e. Przypisz **użytkownika standardowego** roli do użytkownika. Nie należy przypisywać rolę administratora, ponieważ, spowodowałoby to nadanie mu normalny dostęp atmosfery, a także dostępu rejestracji jednokrotnej.
    
    f. Kliknij przycisk **OK**.
    
    > [!NOTE]
    > Użytkownik nie otrzyma wiadomość e-mail z powiadomieniem powitalny zawierający hasła, który może służyć do logowania się do konta AtomSphere, ponieważ jego hasła odbywa się za pośrednictwem dostawcy tożsamości. Może używać jakichkolwiek innych Boomi użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Boomi do aprowizacji kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Boomi.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Boomi, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Boomi**.

    ![Link Boomi na liście aplikacji](./media/boomi-tutorial/tutorial_boomi_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Boomi w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Boomi.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

