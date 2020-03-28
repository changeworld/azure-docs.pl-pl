---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą PureCloud by Genesys | Dokumenty firmy Microsoft'
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
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897781"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z purecloud by Genesys

W tym samouczku dowiesz się, jak zintegrować PureCloud by Genesys z usługą Azure Active Directory (Azure AD). Po zrobienie tego, można:

* Użyj usługi Azure AD, aby kontrolować, którzy użytkownicy mogą uzyskiwać dostęp do PureCloud przez Genesys.
* Włącz użytkownikom automatyczne logowanie do purecloud przez Genesys za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli go nie masz, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego PureCloud by Genesys.A PureCloud by Genesys single sign-on (SSO)-enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* PureCloud by Genesys obsługuje **sp i IDP**-inicjowane SSO.

> [!NOTE]
> Ponieważ identyfikator dla tej aplikacji jest wartością stałego ciągu, tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Dodawanie aplikacji PureCloud by Genesys z galerii

Aby skonfigurować integrację purecloud przez Genesys z usługą Azure AD, należy dodać PureCloud by Genesys z galerii do listy zarządzanych aplikacji SaaS. W tym celu wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub szkolnego lub przy użyciu osobistego konta Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji dla przedsiębiorstw,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **PureCloud by Genesys** w polu wyszukiwania.
1. Wybierz **PureCloud by Genesys** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi PureCloud przez Genesys

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą technologii PureCloud by Genesys przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w PureCloud by Genesys.

Aby skonfigurować i przetestować przychylić do usługi Azure AD sycowe za pomocą purecloud by Genesys, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj PureCloud by Genesys SSO,](#configure-purecloud-by-genesys-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego PureCloud by Genesys,](#create-purecloud-by-genesys-test-user)** aby mieć odpowiednik B.Simon w PureCloud by Genesys, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć usługę Azure AD SSO w witrynie Azure portal, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **PureCloud by Genesys** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli **aplikacja** ma być skonfigurowana w trybie inicjowanym przez **IDP,** wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wprowadź adres URL odpowiadający regionowi:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. W polu **Odpowiedz na adres URL** wprowadź adres URL odpowiadający Twojemu regionowi:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Wybierz **pozycję Ustaw dodatkowe adresy URL** i podejmij następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **"Zaloguj się adres URL"** wprowadź adres URL odpowiadający Twojemu regionowi:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. Aplikacja PureCloud by Genesys oczekuje asercji SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych:

    ![image](common/default-attributes.png)

1. Ponadto aplikacja PureCloud by Genesys oczekuje, że kilka dodatkowych atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML, jak pokazano w poniższej tabeli. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć w razie potrzeby.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | Adres e-mail | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie PureCloud by Genesys** skopiuj odpowiedni adres URL (lub adresy URL) na podstawie twoich wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B.Simon w witrynie Azure portal:

1. W lewym okienku witryny Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika w username@companydomain.extensionnastępującym formacie: . Na przykład: `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zanotuj wartość wyświetlaną w polu **Hasło.**
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji skonfigurujesz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do PureCloud przez Genesys.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **PureCloud by Genesys**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurowanie PureCloud przez Genesys SSO

1. W innym oknie przeglądarki internetowej zaloguj się do PureCloud przez Genesys jako administrator.

1. Wybierz **pozycję Administrator** u góry, a następnie przejdź do pozycji **Logowanie jednokrotne w** obszarze **Integracje**.

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Przełącz się na kartę **usługi ADFS/Azure AD(Premium),** a następnie wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Wybierz **opcję Przeglądaj,** aby przekazać do **certyfikatu usługi ADFS**zakodowany certyfikat base-64 pobrany z portalu Azure.

    b. W polu **Identyfikator URI wystawcy usługi ADFS** wklej wartość **identyfikatora usługi Azure AD** skopiowanego z witryny Azure portal.

    d. W polu **Docelowy identyfikator URI** wklej wartość **adresu URL logowania** skopiowany z witryny Azure portal.

    d. Dla wartości **identyfikatora jednostki uzależniona,** przejdź do witryny Azure portal, a następnie na **PureCloud przez Genesys** aplikacji integracji strony, wybierz **właściwości** kartę i skopiować wartość **identyfikator aplikacji.** Wklej go do pola **Identyfikator jednostki uzależniającej.**

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Wybierz **pozycję Zapisz**.

### <a name="create-purecloud-by-genesys-test-user"></a>Tworzenie użytkownika testowego aplikacji PureCloud by Genesys

Aby umożliwić użytkownikom usługi Azure AD logowanie się do PureCloud przez Genesys, muszą one być aprowizowane do PureCloud przez Genesys. W aplikacji PureCloud by Genesys aprowizowanie wykonywane jest ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do PureCloud przez Genesys jako administrator.

1. Wybierz **pozycję Administrator** u góry i przejdź do sekcji **Osoby** & **uprawnienia**.

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na stronie **Kontakty** wybierz pozycję **Dodaj osobę**.

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure04.png)

1. W oknie dialogowym **Dodawanie osób do organizacji** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. W polu **Imię i nazwisko** wprowadź nazwę użytkownika. Na przykład: **B.simon**.

    b. W polu **Poczta e-mail** wprowadź adres e-mail użytkownika. Na przykład: **b.simon\@contoso.com**.

    d. Wybierz **pozycję Utwórz**.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka **PureCloud by Genesys** w Panelu dostępu powinieneś zostać automatycznie zalogowany do konta PureCloud by Genesys, dla którego skonfigurowano logującą się do konta. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj PureCloud by Genesys z usługą Azure AD](https://aad.portal.azure.com/)
