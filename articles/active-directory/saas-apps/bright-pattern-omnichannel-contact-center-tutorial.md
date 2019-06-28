---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą jasny wzorzec Omnichannel skontaktuj się z Centrum | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i jasny wzorzec Omnichannel skontaktuj się z Centrum.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: de1e0091-ca10-4e7e-8014-f4579d8eabf6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205b1746bac30a015d4efe4bde573be44563e2f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450261"
---
# <a name="tutorial-integrate-bright-pattern-omnichannel-contact-center-with-azure-active-directory"></a>Samouczek: Integrowanie jasny wzorzec Omnichannel skontaktuj się z Centrum z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować jasny wzorzec Omnichannel skontaktuj się z Centrum w usłudze Azure Active Directory (Azure AD). Po zintegrowaniu jasny wzorzec Omnichannel skontaktuj się z Centrum z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do jasny wzorzec Omnichannel skontaktuj się z Centrum.
* Umożliwianie użytkownikom można automatycznie zalogowany do jasny wzorzec Omnichannel skontaktuj się z Centrum przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Jasny wzorzec Omnichannel skontaktuj się z Centrum logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym.

* Jasny wzorzec Omnichannel skontaktuj się z Centrum obsługuje **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez
* Jasny wzorzec Omnichannel skontaktuj się z Centrum obsługuje **Just In Time** aprowizacji użytkowników

## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Dodawanie jasny wzorzec Omnichannel skontaktuj się z Centrum z galerii

Aby skonfigurować integrację jasny wzorzec Omnichannel skontaktuj się z Centrum w usłudze Azure AD, należy dodać jasny wzorzec Omnichannel skontaktuj się z Centrum z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **jasny wzorzec Omnichannel skontaktuj się z Centrum** w polu wyszukiwania.
1. Wybierz **jasny wzorzec Omnichannel skontaktuj się z Centrum** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie usługi Azure AD usługa rejestracji Jednokrotnej z jasny wzorzec Omnichannel skontaktuj się z Centrum przy użyciu użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników, w jasny wzorzec Omnichannel skontaktuj się z Centrum.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą jasny wzorzec Omnichannel skontaktuj się z Centrum, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego jasny wzorzec Omnichannel skontaktuj się z Centrum](#configure-bright-pattern-omnichannel-contact-center-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego jasny wzorzec Omnichannel skontaktuj się z Centrum](#create-bright-pattern-omnichannel-contact-center-test-user)**  — aby odpowiednikiem B.Simon w jasny wzorzec Omnichannel skontaktuj się z Centrum jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **jasny wzorzec Omnichannel skontaktuj się z Centrum** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **pojedynczego Logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `<SUBDOMAIN>_sso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej jasny wzorzec Omnichannel skontaktuj się z Centrum klienta](mailto:support@brightpattern.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Jasny wzorzec Omnichannel skontaktuj się z Centrum aplikacji oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) | Przestrzeń nazw  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie jasny wzorzec Omnichannel skontaktuj się z Centrum** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Konfigurowanie logowania jednokrotnego jasny wzorzec Omnichannel skontaktuj się z Centrum

Aby skonfigurować logowanie jednokrotne na **jasny wzorzec Omnichannel skontaktuj się z Centrum** stronie, musisz wysłać pobrany **certyfikat (Base64)** i skopiować adresy URL z witryny Azure portal do [ Jasny wzorzec Omnichannel skontaktuj się z Centrum pomocy technicznej zespół](mailto:support@brightpattern.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do jasny wzorzec Omnichannel skontaktuj się z Centrum.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **jasny wzorzec Omnichannel skontaktuj się z Centrum**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Tworzenie użytkownika testowego jasny wzorzec Omnichannel skontaktuj się z Centrum

W tej sekcji użytkownika o nazwie B.Simon jest tworzony w jasny wzorzec Omnichannel skontaktuj się z Centrum. Jasny wzorzec Omnichannel skontaktuj się z Centrum obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w jasny wzorzec Omnichannel skontaktuj się z Centrum, nowy katalog jest tworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka jasny wzorzec Omnichannel skontaktuj się z Centrum w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze jasny wzorzec Omnichannel skontaktuj się z Centrum dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)