---
title: 'Samouczek: Integracja usługi Azure Active Directory z everbridge | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103258"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: Integracja usługi Azure Active Directory z everbridge

W tym samouczku dowiesz się, jak zintegrować everbridge z usługą Azure Active Directory (Azure AD).
Po zintegrowaniu everbridge z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Everbridge.
* Zezwalaj użytkownikom na automatyczne logowanie się do everbridge za pomocą ich kont usługi Azure AD. Ta kontrola dostępu jest nazywana logowanie jednokrotne (Logowanie jednokrotne).
* Zarządzaj kontami w jednej centralnej lokalizacji za pomocą witryny Azure Portal.
Aby uzyskać więcej informacji na temat integracji aplikacji z usługą Azure AD na temat oprogramowania jako usługi ,zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z everbridge, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Everbridge, która korzysta z logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Everbridge obsługuje jednostkę SSO inicjującą zainicjowaną przez protokół IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Dodawanie everbridge z portalu Azure Marketplace

Aby skonfigurować integrację Everbridge z usługą Azure AD, dodaj Everbridge z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS.

Aby dodać Everbridge z portalu Azure Marketplace, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku nawigacji wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji enterprise**, a następnie wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Everbridge**. Wybierz **Everbridge** z panelu wyników i wybierz pozycję **Dodaj**.

     ![Everbridge na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Everbridge na podstawie użytkownika testowego Britta Simon.
Aby logowanie jednokrotne działało, nawiązuj relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Everbridge.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi Everbridge, wykonaj następujące elementy konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Skonfiguruj Everbridge jako logowanie jednokrotne portalu menedżera Everbridge,](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
- [Skonfiguruj Everbridge jako logowanie jednokrotne portalu członka Everbridge,](#configure-everbridge-as-everbridge-member-portal-single-sign-on) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Utwórz użytkownika testowego Everbridge,](#create-an-everbridge-test-user) aby mieć odpowiednik Britta Simon w Everbridge, który jest połączony z reprezentacją użytkownika usługi Azure AD.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi Everbridge, wykonaj następujące kroki.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Everbridge** wybierz pozycję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

    >[!NOTE]
    >Skonfiguruj aplikację jako portal menedżera *lub* jako portal członkowski zarówno w witrynie Azure portal, jak i w portalu Everbridge.

4. Aby skonfigurować aplikację **Everbridge** jako **portal menedżera Everbridge,** w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

    ![Informacje o domenie Everbridge i adresach URL z logami jednokrotnymi](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL zgodny ze wzorcem`https://sso.everbridge.net/<API_Name>`

    b. W polu **Adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości za pomocą rzeczywistych wartości adresu URL identyfikatora i odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Everbridge.](mailto:support@everbridge.com) Można również odwołać się do wzorców pokazanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

5. Aby skonfigurować aplikację **Everbridge** jako **portal członkowski Everbridge**w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

  * Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez IDP, wykonaj następujące kroki:

     ![Informacje o domenie Everbridge i adresach URL z logowaniami jednokrotnymi w trybie inicjowanym przez IDP](common/idp-intiated.png)

    a. W polu **Identyfikator** wprowadź adres URL zgodny ze wzorcem`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. W polu **Adres URL odpowiedzi** wprowadź adres URL zgodny ze wzorcem`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp. **zm.**

     ![Informacje o domenie Everbridge i adresach URL z logami jednokrotnymi w trybie inicjowanym przez sp](common/both-signonurl.png)

     a. W polu **Zaloguj się na adres URL** wprowadź adres URL zgodny ze wzorcem`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > To nie są rzeczywiste wartości. Zaktualizuj te wartości za pomocą rzeczywistych wartości identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej Everbridge.](mailto:support@everbridge.com) Można również odwołać się do wzorców pokazanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **kod XML metadanych federacji**. Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie Everbridge** skopiuj adresy URL, których potrzebujesz do spełnienia wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurowanie everbridge jako logowania jednokrotnego portalu menedżera Everbridge

Aby skonfigurować funkcję SSO w **Everbridge** jako aplikację **portalu menedżera Everbridge,** wykonaj następujące kroki.
 
1. W innym oknie przeglądarki internetowej zaloguj się do Everbridge jako administrator.

1. W menu u góry wybierz kartę **Ustawienia.** W obszarze **Zabezpieczenia**wybierz pozycję **Logowanie jednokrotne**.
   
     ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. W polu **Nazwa** wprowadź nazwę dostawcy identyfikatora. Przykładem jest nazwa firmy.
   
     b. W polu **Nazwa interfejsu API** wprowadź nazwę interfejsu API.
   
     d. Wybierz **pozycję Wybierz plik,** aby przekazać plik metadanych pobrany z witryny Azure portal.
   
     d. W przypadku **lokalizacji tożsamości SAML**wybierz **opcję Tożsamość w elemencie NameIdentifier instrukcji Podmiot**.
   
     e. W polu **Adres URL logowania dostawcy tożsamości** wklej wartość adresu URL **logowania** skopiowaną z witryny Azure portal.
   
     f. W polu **Powiązanie żądania zainicjowane przez dostawcę usług**wybierz opcję **Przekierowanie HTTP**.

     g. Wybierz **pozycję Zapisz**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurowanie everbridge jako logowania jednokrotnego portalu użytkownika Everbridge

Aby skonfigurować logowanie jednokrotne w **everbridge** jako **portalu członkowskim Everbridge,** wyślij pobrany kod **XML z metadanymi federacji** do [zespołu pomocy technicznej Everbridge.](mailto:support@everbridge.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Aby utworzyć użytkownika testowego Britta Simon w witrynie Azure portal, wykonaj następujące kroki.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszyscy użytkownicy linki](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** podaj wartość `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    d. Zaznacz pole wyboru **Pokaż hasło.** Zapisz wartość wyświetlaną w polu **Hasło.**

    d. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz funkcję Britta Simon, aby używać logowania jednokrotnego platformy Azure, udzielając dostępu everbridge.

1. W portalu Azure wybierz **aplikacje** > dla przedsiębiorstw**Wszystkie aplikacje** >**Everbridge**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **everbridge**.

    ![Łącze Everbridge na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Łącze Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Okno dialogowe Dodawanie przydziału](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Użyj pozycji **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Użyj pozycji **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-everbridge-test-user"></a>Tworzenie użytkownika testowego Everbridge

W tej sekcji utworzysz użytkownika testowego Britta Simon w Everbridge. Aby dodać użytkowników na platformie Everbridge, należy współpracować z [zespołem pomocy technicznej Everbridge.](mailto:support@everbridge.com) Użytkownicy muszą zostać stworzeni i aktywowani w Everbridge przed użyciem logowania jednokrotnego. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Przetestuj konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Everbridge w Panelu dostępu należy automatycznie zalogować się do konta Everbridge, dla którego skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

