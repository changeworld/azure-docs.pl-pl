---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Insights adaptacyjne | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i adaptacyjne szczegółowe informacje.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720168"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi Insights adaptacyjne

W tym samouczku dowiesz się, jak zintegrować adaptacyjne szczegółowych informacji z usługi Azure Active Directory (Azure AD).
Integrowanie adaptacyjne szczegółowych informacji z usługi Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do adaptacyjnego szczegółowych informacji.
* Użytkownikom można automatycznie zalogowany do adaptacyjnego Insights (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD za pomocą usługi Insights adaptacyjne, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Adaptacyjne Insights pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Funkcje adaptacyjnego sterowania Insights obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjne szczegółowych informacji z galerii

Aby skonfigurować integrację adaptacyjne wglądu w szczegółowe dane w usłudze Azure AD, należy dodać adaptacyjne szczegółowych informacji z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać funkcje adaptacyjnego sterowania szczegółowych informacji z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **adaptacyjne szczegółowych informacji**, wybierz opcję **Insights funkcje adaptacyjnego sterowania** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![Funkcje adaptacyjnego sterowania szczegółowe informacje na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Insights adaptacyjne w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w adaptacyjne szczegółowych informacji musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą adaptacyjne Insights, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie adaptacyjne szczegółowych informacji logowania jednokrotnego](#configure-adaptive-insights-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego adaptacyjne Insights](#create-adaptive-insights-test-user)**  — aby odpowiednikiem Britta Simon w adaptacyjne szczegółowe informacje, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Insights adaptacyjne, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Insights adaptacyjne** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Funkcje adaptacyjnego sterowania Insights domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Funkcje adaptacyjnego sterowania usługi insights można uzyskać wartości adresu URL odpowiedzi i identyfikator jednostki **ustawień logowania jednokrotnego SAML** strony.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie adaptacyjne usługi Insights** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-adaptive-insights-single-sign-on"></a>Konfigurowanie adaptacyjne szczegółowych informacji logowania jednokrotnego

1. W oknie przeglądarki internetowej innej zarejestrować się do witryny firmy adaptacyjne szczegółowych informacji jako administrator.

2. Przejdź do **administratora**.

    ![Administrator](./media/adaptivesuite-tutorial/ic805644.png "Administrator")

3. W **użytkownikami i rolami** kliknij **Zarządzanie ustawieniami logowania jednokrotnego SAML**.

    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/ic805645.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

4. Na **ustawień logowania jednokrotnego SAML** strony, wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/ic805646.png "ustawień logowania jednokrotnego SAML")

    a. W **nazwę dostawcy tożsamości** polu tekstowym wpisz nazwę dla danej konfiguracji.

    b. Wklej **usługi Azure Ad identyfikator** wartość kopiowana z witryny Azure portal do **dostawcy tożsamości identyfikator jednostki** pola tekstowego.

    c. Wklej **adres URL logowania** wartość kopiowana z witryny Azure portal do **dostawcy tożsamości adres URL logowania jednokrotnego** pola tekstowego.

    d. Wklej **adres URL wylogowania** wartość kopiowana z witryny Azure portal do **adres URL wylogowania niestandardowe** pola tekstowego.

    e. Aby przekazać certyfikat pobrany, kliknij przycisk **Choose file**.

    f. Wybierz następujące polecenie, aby uzyskać:

     * **Identyfikator użytkownika SAML**, wybierz opcję **nazwy użytkownika adaptacyjne Insights**.

     * **Lokalizacja identyfikator użytkownika SAML**, wybierz opcję **identyfikatora użytkownika w NameID podmiotu**.

     * **Format identyfikatora SAML NameID**, wybierz opcję **adres E-mail**.

     * **Włącz SAML**, wybierz opcję **Zezwalaj logowania jednokrotnego SAML i bezpośrednie logowanie adaptacyjne Insights**.

    g. Kopiuj **adaptacyjne adres URL logowania jednokrotnego szczegółowych informacji** i Wklej do **identyfikator jednostki** i **adres URL odpowiedzi** pola tekstowe w **adaptacyjne domena szczegółowych informacji i adresy URL** sekcji w witrynie Azure portal.

    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola "brittasimon@yourcompanydomain.extension. Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do adaptacyjnego szczegółowych informacji.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Insights adaptacyjne**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Insights adaptacyjne**.

    ![Link adaptacyjne szczegółowe informacje na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-adaptive-insights-test-user"></a>Tworzenie użytkownika testowego adaptacyjne szczegółowych informacji

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do adaptacyjnego szczegółowe informacje, musi być obsługiwana w adaptacyjne szczegółowe informacje. W przypadku adaptacyjne Insights aprowizacji to zadanie ręczne.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Insights adaptacyjne** witryny firmy jako administrator.

2. Przejdź do **administratora**.

   ![Administrator](./media/adaptivesuite-tutorial/IC805644.png "Administrator")

3. W **użytkownikami i rolami** kliknij **Dodaj użytkownika**.

   ![Dodawanie użytkownika](./media/adaptivesuite-tutorial/IC805648.png "Dodawanie użytkownika")

4. W **nowego użytkownika** sekcji, wykonaj następujące czynności:

   ![Prześlij](./media/adaptivesuite-tutorial/IC805649.png "przesyłania")

   a. Typ **nazwa**, **logowania**, **wiadomości E-mail**, **hasło** z prawidłowym użytkownikiem usługi Azure Active Directory chcesz aprowizować do powiązane pola tekstowe.

   b. Wybierz **roli**.

   c. Kliknij przycisk **Prześlij**.

> [!NOTE]
> Można użyć jakichkolwiek innych adaptacyjne szczegółowych informacji użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez adaptacyjne wgląd do aprowizacji kont użytkowników usługi AAD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka adaptacyjne wgląd w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze adaptacyjne szczegółowych danych, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)