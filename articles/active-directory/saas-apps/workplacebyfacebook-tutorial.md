---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Workplace by Facebook | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db14df2dc24afbc8f7982d8d1e4e6429c1c4b1ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086797"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Workplace by Facebook

Z tego samouczka dowiesz się, jak zintegrować aplikację Workplace by Facebook z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Workplace by Facebook z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Workplace by Facebook.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Workplace by Facebook (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Workplace by Facebook potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Workplace by Facebook z włączonym logowaniem jednokrotnym

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Serwis Facebook oferuje dwa produkty: Workplace Standard (bezpłatny) i Workplace Premium (płatny). W przypadku dowolnej dzierżawy Workplace Premium można skonfigurować integrację ze standardem SCIM i logowaniem jednokrotnym bez żadnych dodatkowych kosztów czy licencji. Logowanie jednokrotne i standard SCIM nie są dostępne w wystąpieniach Workplace Standard.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Workplace by Facebook obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**
* Aplikacja Workplace by Facebook obsługuje **aprowizację just in time**
* Aplikacja Workplace by Facebook obsługuje **[automatyczne aprowizowanie użytkowników](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Dodawanie aplikacji Workplace by Facebook z galerii

Aby skonfigurować integrację aplikacji Workplace by Facebook z usługą Azure AD, należy dodać aplikację Workplace by Facebook z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Workplace by Facebook z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Workplace by Facebook**, wybierz pozycję **Workplace by Facebook** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Workplace by Facebook na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Workplace by Facebook, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Workplace by Facebook.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Workplace by Facebook, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Workplace by Facebook](#create-workplace-by-facebook-test-user)** — aby mieć odpowiednik użytkownika Britta Simon w aplikacji Workplace by Facebook, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne w usłudze Azure AD przy użyciu aplikacji Workplace by Facebook, należy wykonać następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Workplace by Facebook** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji Workplace by Facebook](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instancename>.facebook.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Aby uzyskać prawidłowe wartości dla społeczności aplikacji Workplace, otwórz stronę uwierzytelniania pulpitu nawigacyjnego firmy aplikacji Workplace.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Workplace by Facebook** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Workplace by Facebook

1. W osobnym oknie przeglądarki internetowej zaloguj się w swojej witrynie firmowej aplikacji Workplace by Facebook jako administrator.
  
    > [!NOTE]
    > W ramach procesu uwierzytelniania SAML aplikacja Workplace może używać ciągów zapytań o rozmiarze do 2,5 kilobajta w celu przekazania parametrów do usługi Azure AD.

2. W **Panelu administracyjnym** przejdź do karty **Zabezpieczenia**.

    ![Panel administracyjny](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Na karcie **Uwierzytelnianie** wybierz pozycję **Logowanie jednokrotne**, a następnie wykonaj następujące czynności:

    ![Karta Uwierzytelnianie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. W polu tekstowym **Adres URL SAML** wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal.

    b. W polu tekstowym **Identyfikator URI wystawcy SAML** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. W polu **Przekierowanie po wylogowaniu SAML** (opcjonalnie) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku swój **certyfikat zakodowany w formacie base-64** pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat SAML**.

    e. Kopia **adres URL odbiorcy** wystąpienia i wklej go w **identyfikator jednostki** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    f. Kopiuj **adres URL odbiorcy** wystąpienia i wklej go w **adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    g. Przewiń w dół do końca sekcji i kliknij przycisk **Testuj logowanie jednokrotne**. Zostanie wtedy otworzone okno podręczne ze stroną logowania usługi Azure AD. Wprowadź swoje poświadczenia jak zwykle w celu uwierzytelnienia.

    **Rozwiązywanie problemów:** Upewnij się, że adres e-mail zwracany z usługi Azure AD jest taki sama jak konto firmowe platformy Workplace, do którego się zalogowano.

    h. Po pomyślnym zakończeniu testu przewiń w dół do końca strony i kliknij przycisk **Zapisz**.

    i. Wszystkim użytkownikom korzystającym z aplikacji Workplace zostanie wyświetlona strona logowania usługi Azure AD w celu uwierzytelnienia.

4. **Przekierowanie po wylogowaniu SAML (opcjonalnie)**  -

    Możesz opcjonalnie skonfigurować adres URL wylogowywania SAML, który może prowadzić do strony wylogowywania usługi Azure AD. Gdy to ustawienie jest włączone i skonfigurowane, użytkownik nie będzie przekierowywany do strony wylogowywania aplikacji Workplace. Zamiast tego użytkownik zostanie przekierowany do adresu URL dodanego do ustawienia Przekierowanie po wylogowaniu SAML.

### <a name="configuring-reauthentication-frequency"></a>Konfigurowanie częstotliwości ponownego uwierzytelniania

Możesz skonfigurować aplikację Workplace, aby wyświetlała monit o sprawdzenie protokołu SAML codziennie, co tydzień, co 2 tygodnie, co miesiąc lub aby nie wyświetlała go wcale.

> [!NOTE]
> Minimalna wartość sprawdzania protokołu SAML w przypadku aplikacji mobilnych to 1 tydzień.

Możesz też wymusić zresetowanie protokołu SAML dla wszystkich użytkowników za pomocą przycisku: Wymagaj teraz uwierzytelniania SAML dla wszystkich użytkowników.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Workplace by Facebook.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Workplace by Facebook**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz pozycję **Workplace by Facebook**.

    ![Link aplikacji Workplace by Facebook na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workplace-by-facebook-test-user"></a>Tworzenie użytkownika testowego aplikacji Workplace by Facebook

W tej sekcji w aplikacji Workplace by Facebook zostanie utworzony użytkownik o nazwie Britta Simon. Aplikacja Workplace by Facebook obsługuje aprowizację just in time, która jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje w aplikacji Workplace by Facebook, podczas próby uzyskania dostępu do aplikacji Workplace by Facebook zostanie utworzony nowy użytkownik.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Workplace by Facebook](https://workplace.fb.com/faq/)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Workplace by Facebook w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Workplace by Facebook, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](workplacebyfacebook-provisioning-tutorial.md)
