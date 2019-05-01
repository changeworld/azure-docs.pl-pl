---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą RFPIO | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176e0c80b4b1c70c3f49a35ae04d6233bc080f43
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687816"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą RFPIO

W tym samouczku dowiesz się, jak zintegrować RFPIO w usłudze Azure Active Directory (Azure AD).
Integrowanie RFPIO z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do RFPIO.
* Aby umożliwić użytkownikom można automatycznie zalogowany do RFPIO (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RFPIO, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* RFPIO logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje RFPIO **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-rfpio-from-the-gallery"></a>Dodawanie RFPIO z galerii

Aby skonfigurować integrację RFPIO w usłudze Azure AD, należy dodać RFPIO z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać RFPIO z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **RFPIO**, wybierz opcję **RFPIO** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![RFPIO na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą RFPIO w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w RFPIO musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą RFPIO, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie RFPIO logowania jednokrotnego](#configure-rfpio-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego RFPIO](#create-rfpio-test-user)**  — aby odpowiednikiem Britta Simon w RFPIO połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z RFPIO, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **RFPIO** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wykonaj następujące kroki:

    ![RFPIO domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-identifier.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.rfpio.com`

    b. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    c. W **tan przekaźnika** polu tekstowym wprowadź wartość ciągu. Skontaktuj się z pomocą [zespołu pomocy technicznej RFPIO](https://www.rfpio.com/contact/) aby zyskać tę wartość.

    ![RFPIO domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-preintegrated-relay.png)

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![image](common/both-preintegrated-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.app.rfpio.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL identyfikatora i logowania jednokrotnego. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta RFPIO](https://www.rfpio.com/contact/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **Konfigurowanie RFPIO** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-rfpio-single-sign-on"></a>Konfigurowanie RFPIO logowania jednokrotnego

1. W oknie przeglądarki internetowej innej, zaloguj się do **RFPIO** witryny sieci Web jako administrator.

1. Kliknij listę rozwijaną dolnym lewym rogu.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app1.png)

1. Kliknij pozycję **ustawień organizacji**. 

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app2.png)

1. Kliknij pozycję **funkcje i integracji**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app4.png)

1. W **konfiguracji logowania jednokrotnego SAML** kliknij **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app3.png)

1. W tej części należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app5.png)
    
    a. Skopiuj zawartość **pobierane metadane XML** i wklej go w **Konfiguracja tożsamości** pola.

    > [!NOTE]
    >Aby skopiować zawartość pobrana **XML metadanych Federacji** użyj **Notatnik ++** lub zastosowanie mieszanej **edytora XML**.

    b. Kliknij przycisk **zweryfikować**.

    c. Po kliknięciu przycisku **zweryfikować**, przerzucania **SAML(Enabled)** pozycji włączone.

    d. Kliknij przycisk **Prześlij**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do RFPIO.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **RFPIO**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **RFPIO**.

    ![Link RFPIO na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-rfpio-test-user"></a>Tworzenie użytkownika testowego RFPIO

1. Zaloguj się do witryny firmy RFPIO jako administrator.

1. Kliknij listę rozwijaną dolnym lewym rogu.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app1.png)

1. Kliknij pozycję **ustawień organizacji**. 

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app2.png)

1. Kliknij przycisk **członków zespołu**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app6.png)

1. Kliknij pozycję **Dodaj członków**.

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app7.png)

1. W **Dodawanie nowych elementów członkowskich** sekcji. Wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/rfpio-tutorial/app8.png)

    a. Wprowadź **adres E-mail** w **Podaj jeden adres e-mail dla każdego wiersza** pola.

    b. Wybierz **roli** zgodnie z wymaganiami użytkownika.

    c. Kliknij przycisk **Dodaj członków**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka RFPIO w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze RFPIO, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

