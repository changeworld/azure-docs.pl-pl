---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z moxtrą | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a moxtrą.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74889638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z moxtrą

W tym samouczku dowiesz się, jak zintegrować moxtra z usługą Azure Active Directory (Azure AD). Po zintegrowaniu moxtry z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do moxtry.
* Włącz użytkownikom automatyczne logowanie do moxtry za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) moxtra.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Moxtra obsługuje sso inicjowane przez **SP**

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-moxtra-from-the-gallery"></a>Dodawanie Moxtry z galerii

Aby skonfigurować integrację moxtry z usługą Azure AD, należy dodać moxtra z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **moxtra** w polu wyszukiwania.
1. Wybierz **moxtra** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla moxtry

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi Moxtra przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w moxtrze.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Moxtra, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj moxtra SSO](#configure-moxtra-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Moxtra](#create-moxtra-test-user)** — aby mieć odpowiednik B.Simon w moxtrze, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Moxtra** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego.**
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://www.moxtra.com/service/#login`

1. Aplikacja Moxtra oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Moxtra oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji Oświadczenia użytkownika w oknie dialogowym Atrybuty użytkownika wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | identyfikator    | < > identyfikatora usługi Azure AD

    > [!Note]
    > Wartość atrybutu **idpid** nie jest rzeczywista. Możesz uzyskać rzeczywistą wartość z Sekcji **Konfigurowanie Moxtry** z kroku #8. 

    1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.

    1. Dla opcji Źródło wybierz wartość **Atrybut**.

    1. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    1. Kliknij przycisk **OK**.

    1. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie moxtry** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do moxtra.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **moxtra**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-moxtra-sso"></a>Konfigurowanie sytcha syd w moxtrze

1. W innym oknie przeglądarki zaloguj się do witryny firmy Moxtra jako administrator.

2. Na pasku narzędzi po lewej stronie kliknij pozycję **Konsola administracyjna > samoobsłanie JEDNOkrotne SAML,** a następnie kliknij przycisk **Nowy**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na stronie **SAML** wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. W polach tekstowych **Nazwa** wpisz nazwę swojej konfiguracji *SAML*(np. 
  
    b. W polu tekstowym **identyfikator jednostki IdP** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal. 
 
    d. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal. 
 
    d. W polach tekstowych **AuthnContextClassRef** wpisz **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**. 
 
    e. W obszarze tekstowym **Format nazwy** wpisz **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**. 
 
    f. Otwórz certyfikat pobrany z witryny Azure portal w notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu.**    
 
    g. W polach tekstowych domeny e-mail SAML wpisz domenę poczty e-mail SAML.    
  
    >[!NOTE]
    >Aby wyświetlić kroki weryfikacji domeny, kliknij "**i**" poniżej.

    h. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-moxtra-test-user"></a>Utwórz użytkownika testowego Moxtra

Celem tej sekcji jest utworzenie użytkownika o nazwie B.simon w Moxtra.

**Aby utworzyć użytkownika o nazwie B.simon w moxtrze, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Moxtra jako administrator.

1. Na pasku narzędzi po lewej stronie kliknij pozycję **Konsola administracyjna > zarządzanie użytkownikami,** a następnie **pozycję Dodaj użytkownika**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
  
    a. W polach tekstowych **Imię** wpisz **B**.
  
    b. W polach tekstowych **Nazwisko** wpisz **Simon**.
  
    d. W obszarze **tekstowym Poczta e-mail** wpisz adres e-mail B.simon taki sam jak w witrynie Azure portal.
  
    d. W polach tekstowych **Podział** wpisz **Dev**.
  
    e. W polach tekstowych **Dział** **wpisz**IT .
  
    f. Wybierz **pozycję Administrator**.
  
    g. Kliknij przycisk **Dodaj**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Moxtra w Panelu dostępu należy automatycznie zalogować się do moxtry, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj moxtra z usługą Azure AD](https://aad.portal.azure.com/)

