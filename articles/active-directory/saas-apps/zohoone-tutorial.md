---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą jednego Zoho | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Zoho jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 81e86df270a7286426363c26a0e8a87b99082428
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438277"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą jednego Zoho

W tym samouczku dowiesz się, jak zintegrować Zoho jednego z usługą Azure Active Directory (Azure AD).

Integrowanie Zoho jednego z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do jednego Zoho.
- Użytkowników, aby automatycznie uzyskać zalogowanych do jednego Zoho (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą jednego Zoho, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zoho jeden logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Zoho jednego z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zoho-one-from-the-gallery"></a>Dodawanie Zoho jednego z galerii
Aby skonfigurować integrację z jednego Zoho w usłudze Azure AD, należy dodać jeden Zoho z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać jeden Zoho z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Zoho jeden**, wybierz opcję **Zoho jeden** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Zoho jeden na liście wyników](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w jednym Zoho do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w jednym Zoho musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Zoho jeden](#create-a-zoho-one-test-user)**  — aby odpowiednikiem Britta Simon w Zoho jednego połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Zoho jeden.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą jednego Zoho, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Zoho jeden** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. Na **adresy URL i jednej domenie Zoho** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Adresy URL i jednej domenie Zoho pojedynczego logowania jednokrotnego informacji](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL: `one.zoho.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    c. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**.

    d. W **tan przekaźnika** pole tekstowe, wpisz adres URL:`https://one.zoho.com`

1. Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb wykonaj następujące kroki:

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Poprzedni **adres URL odpowiedzi** i **adres URL logowania** wartość nie jest prawdziwe. Wartość zostanie zaktualizowana o rzeczywisty adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka. 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. Na **Zoho jedną konfigurację** , kliknij przycisk **skonfigurować jeden Zoho** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Zoho jednej konfiguracji](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator Zoho jedną witryną firmy.

1. Na **organizacji** kartę, kliknij przycisk **instalacji** w obszarze **uwierzytelnianie SAML**.

    ![Zoho jednej organizacji](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. W oknie podręcznym strony, wykonaj następujące czynności:

    ![Zoho jeden sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    b. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    c. Kliknij przycisk **Przeglądaj** do przekazania **certyfikat (Base64)** który został pobrany z witryny Azure portal.

    d. Kliknij pozycję **Zapisz**.

1. Po zapisaniu ustawień uwierzytelniania SAML, skopiuj **SAML Identfier** wartości, a następnie użyć tej wartości w **adres URL odpowiedzi** w witrynie Azure portal w obszarze **adresy URL i jednej domenie Zoho** sekcja.

    ![Zoho jednego języka saml](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. Przejdź do **domen** kartę, a następnie kliknij przycisk **dodawania domeny**.

    ![Zoho jedną domenę](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. Na **dodawania domeny** strony, wykonaj następujące czynności:

    ![Dodawanie Zoho jednej domeny](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. W **nazwy domeny** pola tekstowego, typ domeny, takich jak **contoso.com**.

    b. Kliknij pozycję **Add** (Dodaj).

    >[!Note]
    >Po dodaniu domeny postępuj zgodnie z [te](https://www.zoho.com/one/help/admin-guide/domain-verification.html) kroki, aby zweryfikować domenę. Gdy domena jest verfified, użyj nazwy domeny w **adres URL logowania** w **Zoho jednej domeny i adresów URL** sekcji w witrynie Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zohoone-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/zohoone-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zohoone-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zohoone-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-zoho-one-test-user"></a>Utwórz jedną Zoho użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do jednej Zoho, musi być obsługiwana w jednym Zoho. W pierwszej Zoho aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Zoho jeden jako Administrator zabezpieczeń.

1. Na **użytkowników** karcie, kliknij przycisk **logo użytkownika**.

    ![Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. Na **Dodaj użytkownika** strony, wykonaj następujące czynności:

    ![Dodaj Zoho jednego użytkownika](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. W **nazwa** tekstu wprowadź nazwę użytkownika, takich jak **Britta simon**.
    
    b. W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    >[!Note]
    >Wybierz zweryfikowaną domenę z listy domen.

    c. Kliknij pozycję **Add** (Dodaj).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do jednego Zoho.

![Przypisanie roli użytkownika][200] 

**Aby przypisać jeden Zoho Britta Simon, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Zoho jeden**.

    ![Zoho jednego linku na liście aplikacji](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu jednego Zoho kafelka w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Zoho jednej aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

