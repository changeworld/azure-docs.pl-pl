---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą PureCloud przez Genesys | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373147"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą PureCloud przez Genesys

W tym samouczku dowiesz się, jak zintegrować usługę PureCloud przez Genesys z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi PureCloud przez Genesys z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do PureCloud przez Genesys.
* Zezwól użytkownikom na automatyczne logowanie do PureCloud przez Genesys przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* PureCloud przez subskrypcję z włączonym logowaniem jednokrotnym (SSO) Genesys.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja PureCloud by Genesys obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Dodawanie aplikacji PureCloud by Genesys z galerii

Aby skonfigurować integrację aplikacji PureCloud by Genesys z usługą Azure AD, należy dodać tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **PureCloud by Genesys** w polu wyszukiwania.
1. Wybierz pozycję **PureCloud by Genesys** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla PureCloud przez Genesys

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą PureCloud przez Genesys przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w PureCloud przez Genesys.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą PureCloud przez Genesys, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj usługę PureCloud przez Genesys Logowanie jednokrotne](#configure-purecloud-by-genesys-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz PureCloud przez Genesys użytkownika testowego](#create-purecloud-by-genesys-test-user)** , aby dysponować odpowiednikiem B. Simon w PureCloud przez Genesys, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **PureCloud przez Genesys** integrację aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL zgodnie z regionem:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL zgodnie z regionem:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL zgodnie z regionem:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. Aplikacja PureCloud by Genesys oczekuje asercji SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższych PureCloud przez aplikację Genesys oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | Adres e-mail | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie PureCloud według Genesys** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do PureCloud przez Genesys.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **PureCloud by Genesys**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurowanie PureCloud przez Genesys Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się do PureCloud przez Genesys jako administrator.

1. Kliknij pozycję **Admin** (Administrator) u góry strony i przejdź do pozycji **Single Sign-on** (Logowanie jednokrotne) w obszarze **Integrations** (Integracje).

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Przełącz na kartę **ADFS/Azure AD(Premium)** , a następnie wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Kliknij pozycję **Browse** (Przeglądaj), aby przekazać certyfikat zakodowany w formacie base-64 pobrany wcześniej z witryny Azure Portal do pozycji **ADFS Certificate** (Certyfikat ADFS).

    b. W polu tekstowym **ADFS Issuer URI** (Identyfikator URI wystawcy ADFS) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Target URI** (Docelowy identyfikator URI) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Aby uzyskać wartość **Relying Party Identifier** (Identyfikator jednostki uzależnionej), przejdź do witryny Azure Portal, a następnie na stronie integracji aplikacji **PureCloud by Genesys** kliknij kartę **Właściwości** i skopiuj wartość **identyfikator aplikacji**. Wklej ją w polu tekstowym **Relying Party Identifier**. 

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Kliknij pozycję **Zapisz**

### <a name="create-purecloud-by-genesys-test-user"></a>Tworzenie użytkownika testowego aplikacji PureCloud by Genesys

Aby umożliwić użytkownikom usługi Azure AD logowanie się do PureCloud przez Genesys, muszą one być obsługiwane w PureCloud przez Genesys. W aplikacji PureCloud by Genesys aprowizowanie wykonywane jest ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do aplikacji PureCloud by Genesys jako administrator.

1. Kliknij pozycję **Admin** (Administrator) u góry strony i przejdź do pozycji **People** (Osoby) w obszarze **People & Permissions** (Osoby i uprawnienia).

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na stronie People kliknij przycisk **Add Person** (Dodaj osobę).

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure04.png)

1. W wyskakującym oknie **Add People to the Organization** (Dodawanie osób do organizacji) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. W polu tekstowym **pełna nazwa** wprowadź nazwę użytkownika, na przykład **B. Simon**.

    b. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np. **b.Simon\@contoso.com**.

    d. Kliknij przycisk **Utwórz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka PureCloud by Genesys w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji PureCloud by Genesys, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę PureCloud przez Genesys w usłudze Azure AD](https://aad.portal.azure.com/)