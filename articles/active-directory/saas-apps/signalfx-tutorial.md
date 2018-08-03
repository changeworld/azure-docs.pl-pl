---
title: 'Samouczek: Integracja usługi Azure Active Directory z SignalFx | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 0d21a409669cc7d7fceeec9787efbe31d880597c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437852"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Samouczek: Integracja usługi Azure Active Directory z SignalFx

W tym samouczku dowiesz się, jak zintegrować SignalFx w usłudze Azure Active Directory (Azure AD).

Integrowanie SignalFx z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SignalFx.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do SignalFx (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SignalFx, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- SignalFx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SignalFx z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-signalfx-from-the-gallery"></a>Dodawanie SignalFx z galerii
Aby skonfigurować integrację SignalFx w usłudze Azure AD, należy dodać SignalFx z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SignalFx z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **SignalFx**, wybierz opcję **SignalFx** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SignalFx na liście wyników](./media/signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SignalFx w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w SignalFx do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SignalFx musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SignalFx, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SignalFx](#create-a-signalfx-test-user)**  — aby odpowiednikiem Britta Simon w SignalFx połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SignalFx.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SignalFx, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SignalFx** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/signalfx-tutorial/tutorial_signalfx_samlbase.png)

1. Na **SignalFx domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SignalFx domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/signalfx-tutorial/tutorial_signalfx_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://api.signalfx.com/v1/saml/metadata`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Podaną wcześniej wartością nie jest rzeczywistą wartość. Wartość należy zaktualizować przy użyciu rzeczywistego adresu URL odpowiedzi, które wyjaśniono w dalszej części tego samouczka.

1. Aplikacja SignalFx oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.   

    ![Konfigurowanie logowania jednokrotnego](./media/signalfx-tutorial/tutorial_signalfx_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | User.FirstName          | user.givenname |
    | User.email          | User.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/signalfx-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie Addattb rejestracji jednokrotnej](./media/signalfx-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.
 
1. Na **certyfikat podpisywania SAML** sekcji, wykonaj następujące czynności: 

    ![Link pobierania certyfikatu](./media/signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    b. Kliknij przycisk **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/signalfx-tutorial/tutorial_general_400.png)

1. Na **konfiguracji SignalFx** , kliknij przycisk **skonfigurować SignalFx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja SignalFx](./media/signalfx-tutorial/tutorial_signalfx_configure.png) 

1. Zaloguj się do witryny firmy SignalFx jako administrator.

1. W SignalFx po kliknięciu najważniejsze **integracje** aby otworzyć stronę integracji.

    ![SignalFx Integration](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Kliknij pozycję **usługi Azure Active Directory** kafelka w obszarze **logowania usługi** sekcji.
 
    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Kliknij pozycję **nową opcję integracji z** i w obszarze **zainstalować** karcie należy wykonać następujące czynności:
 
    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. W **nazwa** typu pole tekstowe, nazwę nowej integracji, takich jak **logowania jednokrotnego SAML OurOrgName**.

    b. Kopiuj **identyfikator integracji** wartości i dołączenia z zakończeniem **adres URL odpowiedzi** takich jak `https://api.signalfx.com/v1/saml/acs/<integration ID>` w **adres URL odpowiedzi** pole tekstowe z **SignalFx domena i adresy URL** sekcji w witrynie Azure portal.

    c. Kliknij pozycję **Przekaż plik** do przekazania **certyfikatu szyfrowania Base64** pobranego z witryny Azure portal w **certyfikatu** pola tekstowego.

    d. W **adres URL wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal.

    e. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** skopiowanej w witrynie Azure portal.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/signalfx-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/signalfx-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/signalfx-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/signalfx-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-signalfx-test-user"></a>Tworzenie użytkownika testowego SignalFx

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w SignalFx. SignalFx obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu SignalFx, jeśli go jeszcze nie istnieje.

Kiedy użytkownik zaloguje się SignalFx z logowania jednokrotnego SAML po raz pierwszy [zespołem pomocy technicznej SignalFx](mailto:kmazzola@signalfx.com) wysyła do nich wiadomość e-mail zawierającą łącze, które należy kliknąć za pośrednictwem uwierzytelniania. Dzieje się tak tylko po raz pierwszy loguje się użytkownik; próby kolejne logowania nie będzie wymagać weryfikacji wiadomości e-mail.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [SignalFx zespołem pomocy technicznej](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SignalFx.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SignalFx, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SignalFx**.

    ![Link SignalFx na liście aplikacji](./media/signalfx-tutorial/tutorial_signalfx_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SignalFx w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SignalFx.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/signalfx-tutorial/tutorial_general_01.png
[2]: ./media/signalfx-tutorial/tutorial_general_02.png
[3]: ./media/signalfx-tutorial/tutorial_general_03.png
[4]: ./media/signalfx-tutorial/tutorial_general_04.png

[100]: ./media/signalfx-tutorial/tutorial_general_100.png

[200]: ./media/signalfx-tutorial/tutorial_general_200.png
[201]: ./media/signalfx-tutorial/tutorial_general_201.png
[202]: ./media/signalfx-tutorial/tutorial_general_202.png
[203]: ./media/signalfx-tutorial/tutorial_general_203.png

