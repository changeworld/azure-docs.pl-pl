---
title: 'Samouczek: Integracja usługi Azure Active Directory z Zoho | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: c44eef96bc40e2ccfac9e5dceb106ed2e809dea2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055903"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Samouczek: Integracja usługi Azure Active Directory z Zoho

W tym samouczku dowiesz się, jak zintegrować Zoho w usłudze Azure Active Directory (Azure AD).

Integrowanie Zoho z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Zoho.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Zoho (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Zoho, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zoho logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Zoho z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zoho-from-the-gallery"></a>Dodawanie Zoho z galerii
Aby skonfigurować integrację Zoho w usłudze Azure AD, należy dodać Zoho z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Zoho z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Zoho**, wybierz opcję **Zoho** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Zoho na liście wyników](./media/zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Zoho, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Zoho do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Zoho musi można ustanowić.

W Zoho, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Zoho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Zoho](#create-a-zoho-test-user)**  — aby odpowiednikiem Britta Simon w Zoho, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Zoho.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Zoho, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Zoho** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Na **Zoho domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Zoho domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/zoho-mail-tutorial/tutorial_zoho_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Zoho](https://www.zoho.com/mail/contact.html) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/zoho-mail-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Zoho** , kliknij przycisk **skonfigurować Zoho** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, Zmień hasło, adres URL i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Zoho konfiguracji](./media/zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy poczty Zoho, jako administrator.

8. Przejdź do **Panelu sterowania**.
   
    ![W Panelu sterowania](./media/zoho-mail-tutorial/ic789607.png "w Panelu sterowania")

9. Kliknij przycisk **uwierzytelnianie SAML** kartę.
   
    ![Uwierzytelnianie SAML](./media/zoho-mail-tutorial/ic789608.png "uwierzytelnianie SAML")

10. W **szczegółów uwierzytelniania SAML** sekcji, wykonaj następujące czynności:
   
    ![Szczegóły uwierzytelniania SAML](./media/zoho-mail-tutorial/ic789609.png "szczegółów uwierzytelniania SAML")
   
    a. W **adres URL logowania** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    b. W **adres URL wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.
   
    c. W **Zmień hasło, adres URL** pola tekstowego, Wklej **Zmień hasło, adres URL** skopiowanej w witrynie Azure portal.
       
    d. Otwórz swój certyfikat zakodowany base-64, pobranego z witryny Azure portal w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **PublicKey** pola tekstowego.
   
    e. Jako **algorytm**, wybierz opcję **RSA**.
   
    f. Kliknij przycisk **OK**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/zoho-mail-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/zoho-mail-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/zoho-mail-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/zoho-mail-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-zoho-test-user"></a>Tworzenie użytkownika testowego Zoho

Aby umożliwić użytkownikom usługi Azure AD zalogować się do wiadomości E-mail Zoho, musi być obsługiwana w Zoho poczty. W przypadku poczty Zoho aprowizacji to zadanie ręczne.

> [!NOTE]
> Można użyć jakichkolwiek innych Zoho poczty użytkownika konta tworzenie narzędzi lub interfejsów API udostępniane za pośrednictwem wiadomości E-mail Zoho Uaktywniaj konta użytkowników usługi AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojej **poczty Zoho** witryny firmy jako administrator.

2. Przejdź do **Panel sterowania \> wiadomości E-mail i dokumenty**.

3. Przejdź do **szczegóły użytkownika \> Dodaj użytkownika**.
   
    ![Dodaj użytkownika](./media/zoho-mail-tutorial/ic789611.png "Dodaj użytkownika")

4. Na **dodawania użytkowników** okno dialogowe, należy wykonać następujące czynności:
   
    ![Dodaj użytkownika](./media/zoho-mail-tutorial/ic789612.png "Dodaj użytkownika")
   
    a. W **imię** polu tekstowym wpisz imię użytkownika, takie jak **Britta**.

    b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W **identyfikator poczty E-mail** polu tekstowym wpisz identyfikator e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    d. W **hasło** polu tekstowym Wprowadź hasło użytkownika.
   
    e. Kliknij przycisk **OK**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem do potwierdzenia konta, zanim stanie się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Zoho.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Zoho, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Zoho**.

    ![Link Zoho na liście aplikacji](./media/zoho-mail-tutorial/tutorial_zoho_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Zoho w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Zoho.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/zoho-mail-tutorial/tutorial_general_203.png

