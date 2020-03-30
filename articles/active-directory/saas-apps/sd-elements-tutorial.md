---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z elementami SD | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a elementami SD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74081677"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z elementami SD

W tym samouczku dowiesz się, jak zintegrować elementy SD z usługą Azure Active Directory (Azure AD). Podczas integrowania elementów SD z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do elementów SD.
* Włącz użytkownikom automatyczne logowanie do elementów SD za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) sd elements.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* SD Elements obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół** SSO protokołu IDP

## <a name="adding-sd-elements-from-the-gallery"></a>Dodawanie elementów SD z galerii

Aby skonfigurować integrację elementów SD z usługą Azure AD, należy dodać elementy SD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **SD Elements** w polu wyszukiwania.
1. Wybierz pozycję **Elementy SD** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla elementów SD

Konfigurowanie i testowanie sytua usługi Azure AD sytuować za pomocą funkcji SD Elements przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w elementach SD.

Aby skonfigurować i przetestować sytua usługi Azure AD sytuować za pomocą elementów SD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne elementów SD](#configure-sd-elements-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego SD Elements](#create-sd-elements-test-user)** — aby mieć odpowiednik B.Simon w elementach SD, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SD Elements** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta elementów SD,](mailto:support@sdelements.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja SD Elements oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji SD Elements oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie elementów SD** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do elementów SD.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SD Elements**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-sd-elements-sso"></a>Konfigurowanie syd elementy SSO

1. Aby włączyć logowanie jednokrotne, skontaktuj się z [zespołem pomocy technicznej SD Elements](mailto:support@sdelements.com) i przekaż im pobrany plik certyfikatu.

1. W innym oknie przeglądarki zaloguj się do dzierżawy elementów SD jako administrator.

1. W menu u góry kliknij pozycję **System**, a następnie **zaloguj się jednokrotnie**.

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. W oknie dialogowym **Ustawienia logowania jednokrotnego** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Jako **typ SSO**wybierz **SAML**.

    b. W polu tekstowym **identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **usługi logowania jednokrotnego dostawcy tożsamości** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    d. Kliknij przycisk **Zapisz**.

### <a name="create-sd-elements-test-user"></a>Utwórz użytkownika testowego SD Elements

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w elementach SD. W przypadku elementów SD tworzenie użytkowników elementów SD jest zadaniem ręcznym.

**Aby utworzyć B.Simon w elementach SD, wykonaj następujące czynności:**

1. W oknie przeglądarki internetowej zaloguj się do witryny firmy SD Elements jako administrator.

1. W menu u góry kliknij pozycję **Zarządzanie użytkownikami**, a następnie **pozycję Użytkownicy**.

    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kliknij pozycję **Dodaj nowego użytkownika**.

    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. W oknie dialogowym **Dodawanie nowego użytkownika** wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. W polu **tekstowym E-mail** wprowadź adres **b.simon@contoso.com**e-mail użytkownika w stylu .

    b. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **B.**.

    d. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

    d. Jako **rola**wybierz **user**.

    e. Kliknij pozycję **Create User** (Utwórz użytkownika).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SD Elements w Panelu dostępu należy automatycznie zalogować się do elementów SD, dla których skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj elementy SD za pomocą usługi Azure AD](https://aad.portal.azure.com/)