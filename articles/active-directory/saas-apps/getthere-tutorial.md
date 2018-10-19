---
title: 'Samouczek: Integracja usługi Azure Active Directory z GetThere | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i GetThere.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431318"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>Samouczek: Integracja usługi Azure Active Directory z GetThere

W tym samouczku dowiesz się, jak zintegrować GetThere w usłudze Azure Active Directory (Azure AD).

Integrowanie GetThere z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do GetThere.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do GetThere (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą GetThere, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- GetThere logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie GetThere z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-getthere-from-the-gallery"></a>Dodawanie GetThere z galerii
Aby skonfigurować integrację GetThere w usłudze Azure AD, należy dodać GetThere z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać GetThere z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/getthere-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/getthere-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/getthere-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **GetThere**, wybierz opcję **GetThere** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą GetThere w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w GetThere do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w GetThere musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą GetThere, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego GetThere](#create-a-getthere-test-user)**  — aby odpowiednikiem Britta Simon w GetThere połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji GetThere.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z GetThere, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **GetThere** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![image](./media/getthere-tutorial/tutorial_getthere_url.png)

    a. W **identyfikator** tekstu wpisz jeden z poniższych adresów URL:
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. W **adres URL odpowiedzi** tekstu wpisz jeden z poniższych adresów URL:
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. Aplikacja GetThere oczekuje unikatowego **Username** wartość oświadczenia nazwy użytkownika. Klienta można mapować poprawną wartość oświadczenia nazwy użytkownika. W poniższym przykładzie, możemy zmapowane **Username** do **user.mail**, można jednak zmienić mapowanie zgodnie z ustawieniami organizacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](./media/getthere-tutorial/i4-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkownika** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Name (Nazwa) |  Atrybut źródłowy |  Przestrzeń nazw |
    | ---------------| --------------- | --------------- |
    | Nazwa witryny | "Podaj wartość zgodnie z organizacji" | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | Nazwa użytkownika |  User.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a. Kliknij przycisk **Dodaj nowe oświadczenie** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/getthere-tutorial/i2-attribute.png)

    ![image](./media/getthere-tutorial/i3-attribute.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. W **Namespace** polu tekstowym wpisz przestrzeń nazw atrybutów wyświetlanego dla tego wiersza.

    d. Wybierz źródło jako **atrybutu**.

    e. Z **atrybut źródłowy** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    f. Kliknij pozycję **Zapisz**.

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)** i zapisz go na komputerze.

    ![image](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. Na **Konfigurowanie GetThere** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    Należy zwrócić uwagę na to, czy adres URL może wskazywać następujące czynności:

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. Aby skonfigurować logowanie jednokrotne na **GetThere** stronie, musisz wysłać pobrany **certyfikat (Base64)** i skopiowany **adres URL wylogowania adres URL logowania, usługi Azure Ad identyfikator** do [ Zespół obsługi klienta GetThere](mailto:dataintegration@sabre.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/getthere-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/getthere-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/getthere-tutorial/d_userproperties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-getthere-test-user"></a>Tworzenie użytkownika testowego GetThere

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w GetThere. Praca z [zespołem pomocy technicznej klienta GetThere](mailto:dataintegration@sabre.com) Aby dodać użytkowników na platformie GetThere. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do GetThere.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/getthere-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **GetThere**.

    ![image](./media/getthere-tutorial/tutorial_getthere_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/getthere-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/getthere-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka GetThere w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji GetThere.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
