---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z uniFLOW Online | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76262133"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z uniFLOW Online

W tym samouczku dowiesz się, jak zintegrować uniFLOW Online z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi UniFLOW Online z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi uniFLOW Online.
* Włącz użytkownikom logowanie się do usługi uniFLOW Online za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* dzierżawy uniFLOW Online.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* uniFLOW Online obsługuje sso inicjowane przez **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Dodawanie uniFLOW Online z galerii

Aby skonfigurować integrację usługi uniFLOW Online z usługą Azure AD, należy dodać uniFLOW Online z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **uniFLOW Online** w polu wyszukiwania.
1. Wybierz **uniFLOW Online** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi uniFLOW Online

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi uniFLOW Online przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby jedno przysłowie działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze uniFLOW Online.

Aby skonfigurować i przetestować jedno i za pomocą usługi Azure AD SSO przy za pomocą usługi uniFLOW Online, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
   1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
   1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj jednokrotne logowanie jednokrotne uniFLOW Online](#configure-uniflow-online-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Zaloguj się do uniFLOW Online przy użyciu utworzonego użytkownika testowego](#sign-in-to-uniflow-online-using-the-created-test-user)** — do testowania logowania użytkownika po stronie aplikacji.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **uniFLOW Online** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta online uniFLOW,](mailto:support@nt-ware.com) aby uzyskać te wartości. Można również odwołać się do wzorców wyświetlanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal lub odwołać się do adresu URL odpowiedzi wyświetlanego w dzierżawie usługi uniFLOW Online.

1. aplikacja uniFLOW Online oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji uniFLOW Online oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | -----------| --------------- |
    | displayname | user.displayname |
    | Nick | user.onpremisessamaccountname |

   > [!NOTE]
   > Atrybut `user.onpremisessamaccountname` będzie zawierać wartość tylko wtedy, gdy użytkownicy usługi Azure AD są synchronizowane z lokalnej usługi Windows Active Directory.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do uniFLOW Online.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **uniFLOW Online**.
1. Na stronie przegląd aplikacji przejdź do sekcji **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

> [!NOTE]
> Aby umożliwić wszystkim użytkownikom dostęp do aplikacji bez ręcznego przypisywania, przejdź do sekcji **Zarządzaj** i wybierz pozycję **Właściwości**. Następnie zmień parametr **wymagane przypisanie użytkownika** na **NO**.

## <a name="configure-uniflow-online-sso"></a>Konfigurowanie jednoprzez jednoprzeciętne usługi uniFLOW online

1. W innym oknie przeglądarki internetowej zaloguj się do witryny uniFLOW Online jako administrator.

1. W lewym panelu nawigacyjnym wybierz kartę **Użytkownik.**

    ![Konfiguracja uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Kliknij **pozycję Dostawcy tożsamości**.

    ![Konfiguracja uniFLOW Online](./media/uniflow-online-tutorial/configure2.png)

1. Kliknij **pozycję Dodaj dostawcę tożsamości**.

    ![Konfiguracja uniFLOW Online](./media/uniflow-online-tutorial/configure3.png)

1. W sekcji **DODAJ DOSTAWCĘ TOŻSAMOŚCI** wykonaj następujące kroki:


    ![Konfiguracja uniFLOW Online](./media/uniflow-online-tutorial/configure4.png)

    a. Wprowadź nazwę wyświetlaną Ex: *AzureAD SSO*.

    b. W przypadku **typu Dostawca**wybierz opcję **WS-Fed** z listy rozwijanej.

    d. W przypadku **typu WS-Fed**wybierz opcję **Azure Active Directory** z listy rozwijanej.

    d. Kliknij przycisk **Zapisz**.

1. Na karcie **Ogólne** wykonaj następujące czynności:

    ![Konfiguracja uniFLOW Online](./media/uniflow-online-tutorial/configure5.png)

    a. Wprowadź nazwę wyświetlaną Ex: *AzureAD SSO*.

    b. Wybierz opcję **Od adresu URL** **metadanych federacji usługi ADFS**.

    d. W polu tekstowym **URl metadanych federacji** wklej wartość **adresu URL metadanych federacji aplikacji,** która została skopiowana z witryny Azure portal.

    d. Wybierz **dostawcę tożsamości** jako **włączone**.

    e. Wybierz **opcję Automatyczna rejestracja użytkownika** jako **aktywowana**.

    f. Kliknij przycisk **Zapisz**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Zaloguj się do uniFLOW Online przy użyciu utworzonego użytkownika testowego

1. W innym oknie przeglądarki sieci Web przejdź do adresu URL uniFLOW Online dla dzierżawy.

1. Wybierz wcześniej utworzonego dostawcę tożsamości, aby zalogować się za pośrednictwem wystąpienia usługi Azure AD.

1. Zaloguj się przy użyciu użytkownika testowego.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj uniFLOW Online z usługą Azure AD](https://aad.portal.azure.com/)
