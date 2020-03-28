---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z sumologic | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sumologic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6d941c6efe42993b6bad7c556582831d179250
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659750"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z sumologic

W tym samouczku dowiesz się, jak zintegrować sumologic z usługą Azure Active Directory (Azure AD). Po zintegrowaniu sumologic z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do SumoLogic.
* Włącz użytkownikom automatyczne logowanie do sumologic za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego SumoLogic.SumoLogic single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SumoLogic obsługuje zainicjowane **przez IDP jednostkę** SSO

## <a name="adding-sumologic-from-the-gallery"></a>Dodawanie sumologic z galerii

Aby skonfigurować integrację sumologic z usługą Azure AD, należy dodać SumoLogic z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SumoLogic** w polu wyszukiwania.
1. Wybierz **pozycję SumoLogic** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla sumologic

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą sumologic przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sumologic.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą sumologic, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne SumoLogic](#configure-sumologic-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego SumoLogic](#create-sumologic-test-user)** — aby mieć odpowiednik B.Simon w SumoLogic, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SumoLogic** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://service.sumologic.com`|
    | `https://<tenantname>.us2.sumologic.com`|
    | `https://<tenantname>.us4.sumologic.com`|
    | `https://<tenantname>.eu.sumologic.com`|
    | `https://<tenantname>.jp.sumologic.com`|
    | `https://<tenantname>.de.sumologic.com`|
    | `https://<tenantname>.ca.sumologic.com`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://service.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`|
    | `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta SumoLogic,](https://www.sumologic.com/contact-us/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja SumoLogic oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji SumoLogic oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    |  Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Role | user.assignedroles |

    > [!NOTE]
    > Kliknij [tutaj,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) aby dowiedzieć się, jak skonfigurować **rolę** w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie sumologicznego** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do SumoLogic.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **sumologic**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sumologic-sso"></a>Konfigurowanie sytcha liczby SYC sumologic

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy SumoLogic jako administrator.

1. Przejdź do **tematu Zarządzanie \> zabezpieczeniami**.

    ![Zarządzaj](./media/sumologic-tutorial/ic778556.png "Zarządzaj")

1. Kliknij pozycję **SAML**.

    ![Globalne ustawienia zabezpieczeń](./media/sumologic-tutorial/ic778557.png "Globalne ustawienia zabezpieczeń")

1. Z listy **Wybierz konfigurację lub utwórz nową,** wybierz pozycję **Azure AD**, a następnie kliknij pozycję **Konfiguruj**.

    ![Konfigurowanie saml 2.0](./media/sumologic-tutorial/ic778558.png "Konfigurowanie saml 2.0")

1. W oknie dialogowym **Konfigurowanie SAML 2.0** wykonaj następujące czynności:

    ![Konfigurowanie saml 2.0](./media/sumologic-tutorial/ic778559.png "Konfigurowanie saml 2.0")

    a. W polach **tekstowych Nazwa konfiguracji** wpisz pozycję **Azure AD**.

    b. Wybierz **tryb debugowania**.

    d. W polu tekstowym **wystawcy** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Authn Request URL** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    e. Otwórz certyfikat zakodowany w notatniku base-64, skopiuj jego zawartość do schowka, a następnie wklej cały certyfikat w **textboxie certyfikatu X.509.**

    f. Jako **atrybut wiadomości e-mail**wybierz opcję **Użyj tematu SAML**.  

    g. Wybierz pozycję **Konfiguracja logowania inicjowana przez sp**.

    h. W obszarze tekstowym **Ścieżka logowania** wpisz pozycję **Azure** i kliknij przycisk **Zapisz**.

### <a name="create-sumologic-test-user"></a>Utwórz użytkownika testowego SumoLogic

Aby umożliwić użytkownikom usługi Azure AD zalogować się do SumoLogic, muszą być aprowidzone do SumoLogic. W przypadku SumoLogic inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **SumoLogic.**

1. Przejdź do pozycji **Manage \> Users** (Zarządzaj > Użytkownicy).

    ![Użytkownicy](./media/sumologic-tutorial/ic778561.png "Użytkownicy")

1. Kliknij przycisk **Dodaj**.

    ![Użytkownicy](./media/sumologic-tutorial/ic778562.png "Użytkownicy")

1. W oknie dialogowym **Nowy użytkownik** wykonaj następujące czynności:

    ![Nowy użytkownik](./media/sumologic-tutorial/ic778563.png "Nowy użytkownik")

    a. Wpisz powiązane informacje o koncie usługi Azure AD, które chcesz aprowizować, w polach tekstowych **Imię**, **Nazwisko**i **Wyślij e-mail.**
  
    b. Wybierz rolę.
  
    d. Jako **stan**wybierz pozycję **Aktywny**.
  
    d. Kliknij przycisk **Zapisz**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia konta użytkownika SumoLogic lub interfejsów API dostarczonych przez firmę SumoLogic.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SumoLogic w Panelu dostępu należy automatycznie zalogować się do sumologic, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj sumologic z usługą Azure AD](https://aad.portal.azure.com/)