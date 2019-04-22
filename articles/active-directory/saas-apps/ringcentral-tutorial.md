---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą RingCentral | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: cf7fd46526eec2f4ac295381498eb14a06b2f36c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59697315"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą RingCentral

W tym samouczku dowiesz się, jak zintegrować RingCentral w usłudze Azure Active Directory (Azure AD).
Integrowanie RingCentral z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do RingCentral.
* Aby umożliwić użytkownikom można automatycznie zalogowany do RingCentral (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RingCentral, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* RingCentral logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje RingCentral **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-ringcentral-from-the-gallery"></a>Dodawanie RingCentral z galerii

Aby skonfigurować integrację RingCentral w usłudze Azure AD, należy dodać RingCentral z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać RingCentral z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **RingCentral**, wybierz opcję **RingCentral** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![RingCentral na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w RingCentral musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie RingCentral logowania jednokrotnego](#configure-ringcentral-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego RingCentral](#create-ringcentral-test-user)**  — aby odpowiednikiem Britta Simon w RingCentral połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z RingCentral, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **RingCentral** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji.

    ![RingCentral domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL:
    
    | |
    |--|
    | `https://service.ringcentral.com`|
    | `https://service.ringcentral.com.au`|
    | `https://service.ringcentral.co.uk`|
    | `https://service.ringcentral.eu`|

    > [!Note]
    > Możesz uzyskać **plik metadanych usługodawcy** na stronie konfiguracji logowania jednokrotnego RingCentral, które zostało wyjaśnione w dalszej części tego samouczka.

5. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL:

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. W **identyfikator** pole tekstowe, wpisz adres URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![RingCentral domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-ringcentral-single-sign-on"></a>Konfigurowanie RingCentral logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się do RingCentral jako Administrator zabezpieczeń.

1. Na górze, wybierz polecenie **narzędzia**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Przejdź do **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na **logowania jednokrotnego** w obszarze **konfiguracji logowania jednokrotnego** sekcji z **kroku 1** kliknij **Edytuj** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na **Konfigurowanie logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kliknij przycisk **Przeglądaj** można przekazać pliku metadanych, który został pobrany z witryny Azure portal.

    b. Po przekazaniu metadanych wartości pobrać, automatycznie wypełnione w **logowania jednokrotnego ogólne informacje o** sekcji.

    c. W obszarze **mapowanie atrybutu** zaznacz **mapy atrybut poczty E-mail, aby** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij pozycję **Zapisz**.

    e. Z **krok 2** kliknij **Pobierz** można pobrać **plik metadanych usługodawcy** i przekaż go w **podstawową konfigurację protokołu SAML** sekcji Aby automatycznie wypełniać **identyfikator** i **adres URL odpowiedzi** wartości w witrynie Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na tej samej stronie, przejdź do **włączyć logowanie Jednokrotne** sekcji, a następnie wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Wybierz **Włącz usługę logowania jednokrotnego**.

    * Wybierz **umożliwiają użytkownikom logowanie się przy użyciu poświadczeń logowania jednokrotnego lub RingCentral**.

    * Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do RingCentral.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **RingCentral**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **RingCentral**.

    ![Link RingCentral na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ringcentral-test-user"></a>Tworzenie użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Praca z [zespołem pomocy technicznej klienta RingCentral](https://success.ringcentral.com/RCContactSupp) Aby dodać użytkowników na platformie RingCentral. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka RingCentral w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze RingCentral, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
