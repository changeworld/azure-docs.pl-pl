---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Kanbanize | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60264239"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Kanbanize

W tym samouczku dowiesz się, jak zintegrować Kanbanize w usłudze Azure Active Directory (Azure AD).

Integrowanie Kanbanize z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Kanbanize.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Kanbanize (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Kanbanize, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Kanbanize logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Kanbanize z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-kanbanize-from-the-gallery"></a>Dodawanie Kanbanize z galerii
Aby skonfigurować integrację Kanbanize w usłudze Azure AD, należy dodać Kanbanize z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kanbanize z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Kanbanize**, wybierz opcję **Kanbanize** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Kanbanize na liście wyników](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kanbanize w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Kanbanize do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kanbanize musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Kanbanize, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego Kanbanize](#create-a-kanbanize-test-user)**  — aby odpowiednikiem Britta Simon w Kanbanize połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Kanbanize.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Kanbanize, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Kanbanize** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Na **Kanbanize domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Kanbanize domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    d.  W **tan przekaźnika** pole tekstowe, wpisz adres URL: `/ctrl_login/saml_login`

    e. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, w **adres URL logowania** polu tekstowym wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Kanbanize](mailto:support@ms.kanbanize.com) do uzyskania tych wartości. 

5. Aplikacja Kanbanize oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale Kanbanize oczekuje, że to mają być mapowane z adresem e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości odpowiednich atrybutów, zgodnie z konfiguracją organizacji
    
    ![Konfigurowanie logowania jednokrotnego](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Na **konfiguracji Kanbanize** , kliknij przycisk **skonfigurować Kanbanize** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. W oknie przeglądarki internetowej innej, zaloguj się do Kanbanize jako Administrator zabezpieczeń. 

10. Przejdź do pozycji w prawym górnym rogu strony, kliknij pozycję **ustawienia** logo.

    ![Ustawienia Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Na stronie Administracja panelu z menu po lewej stronie kliknij pozycję **integracje** , a następnie Włącz **logowania jednokrotnego**. 

    ![Integracje Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. W obszarze sekcji dotyczącej integracji kliknij **Konfiguruj** otworzyć **integracji rejestracji jednokrotnej** strony.

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Na **integracji rejestracji jednokrotnej** strony w obszarze **konfiguracje**, wykonaj następujące czynności:

    ![Integracje Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. W **identyfikator jednostki tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, skopiowanej w witrynie Azure portal.

    b. W **punkt końcowy logowania protokołu Idp** pole tekstowe, Wklej wartość **SAML pojedynczego logowania jednokrotnego adres URL usługi**, który skopiowano z witryny Azure portal.

    c. W **końcowego wylogowywania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    d. W **nazwa atrybutu do obsługi poczty E-mail** polu tekstowym wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. W **nazwa atrybutu dla imię** polu tekstowym wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. W **nazwę atrybutu nazwisko** polu tekstowym wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Możesz uzyskać te wartości, łącząc wartości przestrzeni nazw i nazwę odpowiedniego atrybutu z sekcji atrybutów użytkownika w witrynie Azure portal.

    g. W programie Notatnik, otwórz certyfikat zakodowany base-64, pobrany z witryny Azure portal, skopiuj jego zawartość (bez znaczników rozpoczęcia i zakończenia) i następnie wklej go do **certyfikat X.509 tożsamości** pole.

    h. Sprawdź **Włącz logowanie za pomocą logowania jednokrotnego i Kanbanize**.
    
    i. Kliknij pozycję **Save Settings (Zapisz ustawienia)**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/kanbanize-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-kanbanize-test-user"></a>Tworzenie użytkownika testowego Kanbanize

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Kanbanize. Kanbanize obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu Kanbanize, jeśli go jeszcze nie istnieje.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Kanbanize](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Kanbanize.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Kanbanize, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Kanbanize**.

    ![Link Kanbanize na liście aplikacji](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Kanbanize w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Kanbanize.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

