---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą połączenia ON24 wirtualnego środowiska języka SAML | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i połączenia ON24 wirtualnego środowiska języka SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: a0b5dd169d29dc392274ab5589931f37beb04e9b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273604"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą połączenia ON24 wirtualnego środowiska języka SAML

W tym samouczku dowiesz się, jak zintegrować ON24 wirtualnego środowiska języka SAML połączenia z usługą Azure Active Directory (Azure AD).
Integrowanie ON24 wirtualnego środowiska języka SAML połączenia z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do połączenia ON24 wirtualnego środowiska języka SAML.
* Użytkownikom można automatycznie zalogowany do ON24 wirtualnego środowiska języka SAML połączenia (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Połączenia ON24 wirtualnego środowiska języka SAML logowania jednokrotnego włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje połączenia SAML środowiska wirtualnego ON24 **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Dodawanie ON24 środowiska SAML połączeń wirtualnych z galerii

Aby skonfigurować integrację ON24 wirtualnego środowiska języka SAML połączenia w usłudze Azure AD, należy dodać ON24 wirtualnego środowiska języka SAML połączenia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ON24 wirtualnego środowiska języka SAML połączenia z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ON24 wirtualnego środowiska języka SAML połączenia**, wybierz opcję **ON24 wirtualnego środowiska języka SAML połączenia** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![ON24 wirtualnego środowiska języka SAML połączenia na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą ON24 wirtualnego środowiska języka SAML połączenia na podstawie użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika ON24 wirtualnego środowiska języka SAML połączenie musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie ON24 wirtualnego środowiska języka SAML połączenia logowania jednokrotnego](#configure-on24-virtual-environment-saml-connection-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego połączenia SAML środowiska wirtualnego ON24](#create-on24-virtual-environment-saml-connection-test-user)**  — aby odpowiednikiem Britta Simon w ON24 wirtualnego SAML połączenia ze środowiskiem połączonym z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **ON24 wirtualnego środowiska języka SAML połączenia** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![ON24 wirtualnego środowiska języka SAML połączenia domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL:

     **Adres URL środowiska produkcyjnego**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **Adres URL środowiska pytań i odpowiedzi**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:

     **Adres URL środowiska produkcyjnego**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **Adres URL środowiska pytań i odpowiedzi**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**. 

    d. W **tan przekaźnika** pole tekstowe, wpisz adres URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb, wykonaj następujące kroki:

    ![ON24 wirtualnego środowiska języka SAML połączenia domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL stan przekazywania i logowania jednokrotnego. Skontaktuj się z pomocą [zespołem pomocy technicznej ON24 wirtualnego środowiska języka SAML połączenia klienta](https://www.on24.com/contact-us/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na **skonfigurować połączenie protokołu SAML środowiska wirtualnego ON24** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Konfigurowanie ON24 wirtualnego środowiska języka SAML połączenia logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **ON24 wirtualnego środowiska języka SAML połączenia** stronie, musisz wysłać pobrany **XML metadanych Federacji** i skopiować adresy URL z witryny Azure portal do [ Zespół pomocy technicznej ON24 wirtualnego środowiska języka SAML połączenia](https://www.on24.com/about-us/support/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do połączenia ON24 wirtualnego środowiska języka SAML.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **ON24 wirtualnego środowiska języka SAML połączenia**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ON24 wirtualnego środowiska języka SAML połączenia**.

    ![ON24 wirtualnego środowiska języka SAML połączenie na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Tworzenie użytkownika testowego połączenia ON24 wirtualnego środowiska języka SAML

W tej sekcji utworzysz użytkownika o nazwie Britta Simon ON24 wirtualnego środowiska języka SAML połączenia. Praca z [zespołem pomocy technicznej ON24 wirtualnego środowiska języka SAML połączenia](https://www.on24.com/about-us/support/) Aby dodać użytkowników na platformie ON24 wirtualnego środowiska języka SAML połączenia. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ON24 wirtualnego środowiska języka SAML połączenia w panelu dostępu, użytkownik powinny być automatycznie zalogowany do ON24 wirtualnego środowiska języka SAML połączenia dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

