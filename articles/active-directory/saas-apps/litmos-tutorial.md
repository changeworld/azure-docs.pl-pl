---
title: 'Samouczek: Integracja usługi Azure Active Directory z Litmos | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a0c70ee6419280b0975d77fb213f9406286708cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428006"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Samouczek: Integracja usługi Azure Active Directory z Litmos

W tym samouczku dowiesz się, jak zintegrować Litmos w usłudze Azure Active Directory (Azure AD).

Integrowanie Litmos z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Litmos.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Litmos (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Litmos, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Litmos logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Litmos z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-litmos-from-the-gallery"></a>Dodawanie Litmos z galerii
Aby skonfigurować integrację Litmos w usłudze Azure AD, należy dodać Litmos z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Litmos z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Litmos**, wybierz opcję **Litmos** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Litmos na liście wyników](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Litmos w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Litmos do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Litmos musi można ustanowić.

W Litmos, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Litmos, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Litmos](#create-a-litmos-test-user)**  — aby odpowiednikiem Britta Simon w Litmos połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Litmos.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Litmos, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Litmos** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

1. Na **Litmos domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Litmos domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.litmos.com/account/Login`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości z rzeczywistych identyfikatorem i adresu URL odpowiedzi, które opisano w dalszej części samouczka lub skontaktuj się z [zespołu pomocy technicznej Litmos](https://www.litmos.com/contact-us/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/litmos-tutorial/tutorial_litmos_certificate.png)

1. W ramach konfiguracji, należy dostosować **atrybuty tokenu języka SAML** Litmos aplikacji.

    ![Sekcja atrybutu](./media/litmos-tutorial/tutorial_attribute.png)
           
    | Nazwa atrybutu   | Wartość atrybutu |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |User.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Dodaj atrybut](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![Dodaj atrybut Dailog](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.     

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/litmos-tutorial/tutorial_general_400.png)

1. W oknie innej przeglądarki Zaloguj się do witryny firmy Litmos jako administrator.

1. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **kont**.
   
    ![Sekcji konta na stronie aplikacji][22] 

1. Kliknij przycisk **integracje** kartę.
   
    ![Karta Integracja][23] 

1. Na **integracje** karty, przewiń w dół do **3rd integracje innych firm**, a następnie kliknij przycisk **SAML 2.0** kartę.
   
    ![SAML 2.0 sekcji][24] 

1. Skopiuj wartość w obszarze **jest punktem końcowym SAML dla litmos:** i wklej go w **adres URL odpowiedzi** polu tekstowym w **Litmos domena i adresy URL** sekcji w witrynie Azure portal. 
   
    ![Punkt końcowy protokołu SAML][26] 

1. W swojej **Litmos** aplikacji, wykonaj następujące czynności:
    
     ![Litmos aplikacji][25] 
     
     a. Kliknij przycisk **Włącz SAML**.
    
     b. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509 SAML** pola tekstowego.
     
     c. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/litmos-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/litmos-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/litmos-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/litmos-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-litmos-test-user"></a>Tworzenie użytkownika testowego Litmos

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Litmos.  
Aplikacja Litmos obsługuje Just-in-Time inicjowania obsługi administracyjnej. Oznacza to konto użytkownika jest tworzone automatycznie w razie potrzeby podczas próby uzyskania dostępu do aplikacji za pomocą panelu dostępu.

**Aby utworzyć użytkownika o nazwie Britta Simon w Litmos, wykonaj następujące czynności:**

1. W oknie innej przeglądarki Zaloguj się do witryny firmy Litmos jako administrator.

1. Na pasku nawigacyjnym po lewej stronie kliknij pozycję **kont**.
   
    ![Sekcji konta na stronie aplikacji][22] 

1. Kliknij przycisk **integracje** kartę.
   
    ![Karta integracji][23] 

1. Na **integracje** karty, przewiń w dół do **3rd integracje innych firm**, a następnie kliknij przycisk **SAML 2.0** kartę.
   
    ![SAML 2.0][24] 
    
1. Wybierz **automatyczne tworzenie użytkowników**
   
    ![Automatyczne generowanie użytkowników][27] 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Litmos.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Litmos, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Litmos**.

    ![Link Litmos na liście aplikacji](./media/litmos-tutorial/tutorial_litmos_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  

Po kliknięciu kafelka Litmos w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Litmos. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png

