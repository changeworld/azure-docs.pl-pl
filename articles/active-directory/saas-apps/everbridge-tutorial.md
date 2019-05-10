---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Everbridge | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Everbridge.
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
ms.openlocfilehash: f8dd11e7fb0b9fda0e0f1c7d3f794f6bfd766cdf
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231469"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Everbridge

W tym samouczku dowiesz się, jak zintegrować Everbridge w usłudze Azure Active Directory (Azure AD).
W ramach Everbridge integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Everbridge.
* Umożliwia użytkownikom automatyczne logowanie do Everbridge za pomocą kont usługi Azure AD. Ta kontrola dostępu do nosi nazwę logowania jednokrotnego (SSO).
* Zarządzanie Twoimi kontami, w jednej centralnej lokalizacji, za pomocą witryny Azure portal.
Aby uzyskać więcej informacji na temat oprogramowania jako usługi (SaaS) integracji aplikacji z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Everbridge, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja Everbridge, która korzysta z logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Everbridge obsługuje logowanie Jednokrotne zainicjowane przez dostawcę tożsamości.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Dodaj Everbridge z witryny Azure Marketplace

Aby skonfigurować integrację Everbridge w usłudze Azure AD, należy dodać Everbridge w portalu Azure Marketplace z listą zarządzanych aplikacji SaaS.

Aby dodać Everbridge w portalu Azure Marketplace, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Everbridge**. Wybierz **Everbridge** z panelu wyników, a następnie wybierz pozycję **Dodaj**.

     ![Everbridge na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Everbridge na podstawie użytkownika testu Britta Simon.
Dla logowania jednokrotnego do pracy należy ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Everbridge.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Everbridge, wykonaj poniższe bloki konstrukcyjne:

- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Konfigurowanie Everbridge jako Everbridge Menedżer portalu rejestracji jednokrotnej](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
- [Konfigurowanie Everbridge jako Everbridge elementu członkowskiego portalu rejestracji jednokrotnej](#configure-everbridge-as-everbridge-member-portal-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Tworzenie użytkownika testowego Everbridge](#create-an-everbridge-test-user) mieć odpowiednikiem Britta Simon Everbridge połączonego z usługi Azure AD reprezentacja użytkownika.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Everbridge, wykonaj następujące kroki.

1. W [witryny Azure portal](https://portal.azure.com/)na **Everbridge** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

    >[!NOTE]
    >Skonfiguruj aplikację zarówno jako manager portal *lub* jako portal elementu członkowskiego, zarówno w witrynie Azure portal, jak i w portalu Everbridge.

4. Aby skonfigurować **Everbridge** jako **portalu manager Everbridge**w **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki:

    ![Everbridge domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    a. W **identyfikator** wprowadź adres URL, który jest zgodny ze wzorcem `https://sso.everbridge.net/<API_Name>`

    b. W **adres URL odpowiedzi** wprowadź adres URL, który jest zgodny ze wzorcem `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistych wartości identyfikatora i adres URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej Everbridge](mailto:support@everbridge.com). Możesz również zapoznać się z wzorców objętego **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

5. Aby skonfigurować **Everbridge** jako **Everbridge elementu członkowskiego portalu**w **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki:

  * Jeśli chcesz skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, wykonaj następujące kroki:

     ![Everbridge domena i adresy URL pojedynczy informacje logowania jednokrotnego dla trybu inicjowane przez dostawcę tożsamości](common/idp-intiated.png)

    a. W **identyfikator** wprowadź adres URL, który jest zgodny ze wzorcem `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. W **adres URL odpowiedzi** wprowadź adres URL, który jest zgodny ze wzorcem `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Ustaw dodatkowe adresy URL** i wykonaj poniższe czynności:

     ![Everbridge domena i adresy URL pojedynczy informacje logowania jednokrotnego dla trybu zainicjowanego przez dostawcę usług](common/both-signonurl.png)

     a. W **adres URL logowania** wprowadź adres URL, który jest zgodny ze wzorcem `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > To nie są rzeczywiste wartości. Zaktualizuj te wartości identyfikatorem rzeczywisty adres URL odpowiedzi i zaloguj się na wartości adresu URL. Aby uzyskać te wartości, skontaktuj się z pomocą [zespołem pomocy technicznej Everbridge](mailto:support@everbridge.com). Możesz również zapoznać się z wzorców objętego **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

6. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **XML metadanych Federacji** . Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W **Konfigurowanie Everbridge** sekcji, skopiuj adresy URL muszą zgodnie z wymaganiami dotyczącymi:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - Adres URL logowania
    - Identyfikator usługi Azure AD
    - Adres URL wylogowywania

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Konfigurowanie Everbridge jako Everbridge Menedżer portalu rejestracji jednokrotnej

Aby skonfigurować logowanie Jednokrotne na **Everbridge** jako **portalu manager Everbridge** aplikacji, wykonaj następujące kroki.
 
1. W oknie przeglądarki internetowej innej Zaloguj się w Everbridge jako administrator.

1. W menu u góry wybierz **ustawienia** kartę. W obszarze **zabezpieczeń**, wybierz opcję **logowania jednokrotnego**.
   
     ![Konfigurowanie logowania jednokrotnego](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. W **nazwa** wprowadź nazwę dostawcy identyfikatora. Przykładem jest nazwę swojej firmy.
   
     b. W **Nazwa interfejsu API** wprowadź nazwę interfejsu API.
   
     c. Wybierz **wybierz plik** można przekazać plik metadanych, który został pobrany z witryny Azure portal.
   
     d. Dla **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentifier instrukcji podmiotu**.
   
     e. W **adres URL logowania dostawcy tożsamości** pole, Wklej **adres URL logowania** wartością skopiowaną z witryny Azure portal.
   
     f. Dla **usługodawcy inicjowane żądania powiązania**, wybierz opcję **przekierowania HTTP**.

     g. Wybierz pozycję **Zapisz**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Konfigurowanie Everbridge jako Everbridge elementu członkowskiego portalu rejestracji jednokrotnej

Aby skonfigurować logowanie jednokrotne na **Everbridge** jako **Everbridge elementu członkowskiego portalu**, Wyślij pobrany **XML metadanych Federacji** do [Everbridge zespołu pomocy technicznej](mailto:support@everbridge.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Aby utworzyć użytkownika testowego Britta Simon w witrynie Azure portal, wykonaj następujące kroki.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Użytkownicy i wszystkie linki użytkowników](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź `brittasimon@yourcompanydomain.extension`. Może to być na przykład BrittaSimon@contoso.com.

    c. Wybierz **Pokaż hasło** pole wyboru. Zanotuj wartość, która wyświetla w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Włącz Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Everbridge.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** >**Everbridge**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Everbridge**.

    ![Link Everbridge na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Łączy użytkowników i grup](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika**. W **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Dodaj przypisanie, okno dialogowe](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy. Użyj pozycji **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Użyj pozycji **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-everbridge-test-user"></a>Tworzenie użytkownika testowego Everbridge

W tej sekcji opisano tworzenie użytkownika testowego Britta Simon w Everbridge. Aby dodać użytkowników, na platformie Everbridge, współpracować z [zespołem pomocy technicznej Everbridge](mailto:support@everbridge.com). Użytkownicy muszą być tworzone i aktywowana w Everbridge, aby używać logowania jednokrotnego. 

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

Testowanie konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka Everbridge w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze Everbridge konta, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

