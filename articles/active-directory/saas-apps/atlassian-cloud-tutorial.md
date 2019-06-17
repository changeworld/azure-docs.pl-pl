---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Atlassian Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106584"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Samouczek: Integracja Atlassian chmury z usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Atlassian chmury z usługi Azure Active Directory (Azure AD). Po zintegrowaniu Atlassian chmury z usługą Azure AD możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do chmury Atlassian.
* Umożliwianie użytkownikom można automatycznie zalogowany do chmury Atlassian za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Chmura Atlassian logowania jednokrotnego (SSO) włączone subskrypcji.
* Aby włączyć logowanie jednokrotne SAML (Security Assertion Markup Language) dla produktów Atlassian Cloud, należy skonfigurować usługę Atlassian Access. Dowiedz się więcej o usłudze [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Usługa Atlassian Cloud obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Dodawanie usługi Atlassian Cloud z galerii

Aby skonfigurować integrację usługi Atlassian Cloud z usługą Azure AD, należy dodać usługę Atlassian Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **chmury Atlassian** w polu wyszukiwania.
1. Wybierz **chmury Atlassian** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD przy użyciu chmury Atlassian za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w chmurze Atlassian.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD przy użyciu chmury Atlassian, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego chmury Atlassian](#configure-atlassian-cloud-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego w chmurze Atlassian](#create-atlassian-cloud-test-user)**  — aby odpowiednikiem B.Simon w chmurze Atlassian, która jest połączona z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **chmury Atlassian** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, wprowadź wartości dla następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://auth.atlassian.com/saml/<unique ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Otrzymasz te wartości rzeczywiste z **Atlassian konfiguracji SAML w chmurze** ekranu, która została wyjaśniona później w **skonfigurować Atlassian chmury logowania jednokrotnego** samouczka.

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Wklej wartość z wystąpienia elementu, którego używasz do logowania do portalu administracyjnego Atlassian chmury.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Aplikacja Atlassian Cloud oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Atlassian Cloud oczekuje, że atrybut **nameidentifier** będzie zamapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj usługę Atlassian Cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-atlassian-cloud-sso"></a>Konfigurowanie chmury Atlassian logowania jednokrotnego

1. Aby zautomatyzować konfigurację w chmurze Atlassian, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **chmury Atlassian Instalatora** nastąpi bezpośrednie przekierowanie do aplikacji w chmurze Atlassian. W tym miejscu podaj poświadczenia administratora do logowania się do chmury Atlassian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Atlassian chmury, Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy Atlassian chmury z uprawnieniami administratora i wykonaj następujące czynności:

4. Musisz zweryfikować swoją domenę przed przejściem do konfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz dokument [Weryfikacja domeny Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

5. W okienku po lewej stronie wybierz **zabezpieczeń** > **SAML logowania jednokrotnego**. Jeśli nie zostało to jeszcze zrobione, zasubskrybuj usługę Atlassian Identity Manager.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. W oknie **Add SAML configuration** (Dodawanie konfiguracji SAML) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. W **dostawcy tożsamości identyfikator jednostki** pole, Wklej **usługi Azure AD identyfikator** skopiowaną z witryny Azure portal.

    b. W **dostawcy tożsamości adres URL logowania jednokrotnego** pole, Wklej **adres URL logowania** skopiowaną z witryny Azure portal.

    c. Otwórz pobrany certyfikat w witrynie Azure Portal w pliku TXT, skopiuj wartość (bez wierszy *Begin Certificate* i *End Certificate*), a następnie wklej ją w polu **Public X509 certificate** (Publiczny certyfikat X509).

    d. Kliknij pozycję **Save Configuration** (Zapisz konfigurację).

7. Aby upewnić się, że skonfigurowano poprawne adresy URL, zaktualizuj ustawienia usługi Azure AD, wykonując następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. W oknie SAML kopiować **identyfikator tożsamości SP** a następnie w witrynie Azure portal w obszarze chmura Atlassian **podstawową konfigurację protokołu SAML**, wklej ją w **identyfikator** pole.

    b. W oknie SAML kopiować **URL usługi konsumenta potwierdzenie SP** a następnie w witrynie Azure portal w obszarze chmura Atlassian **podstawową konfigurację protokołu SAML**, wklej ją w **adres URL odpowiedzi**pole. Adres URL logowania jest adresem URL dzierżawy Twojej usługi Atlassian Cloud.

    > [!NOTE]
    > Jeśli jesteś istniejącym klientem, po zaktualizowaniu wartości **Identyfikator tożsamości SP** i **Adres URL usługi Assertion Consumer Service SP** w witrynie Azure Portal wybierz opcję **Tak, zaktualizuj konfigurację**. Jeśli jesteś nowym klientem, możesz pominąć ten krok.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do udzielania dostępu do chmury Atlassian za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **chmury Atlassian**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-atlassian-cloud-test-user"></a>Tworzenie użytkownika testowego usługi Atlassian Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie do usługi Atlassian Cloud, aprowizuj ręcznie konta użytkowników w usłudze Atlassian Cloud, wykonując następujące czynności:

1. W okienku **Administracja** okienku wybierz pozycję **Użytkownicy**.

    ![Link Użytkownicy usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Aby utworzyć użytkownika w usłudze Atlassian Cloud, wybierz pozycję **Zaproś użytkownika**.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. W polu **Adres e-mail** wprowadź adres e-mail użytkownika, a następnie przypisz dostęp do aplikacji.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Aby wysłać wiadomość e-mail z zaproszeniem do użytkownika, wybierz pozycję **Zaproś użytkowników**. Wiadomość e-mail z zaproszeniem zostanie wysłana do użytkownika, a po zaakceptowaniu zaproszenia użytkownik będzie aktywny w systemie.

> [!NOTE]
> Możesz również zbiorczo utworzyć użytkowników, wybierając przycisk **Tworzenie zbiorcze** w sekcji **Użytkownicy**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Atlassian chmury w panelu dostępu powinny być automatycznie zarejestrowaniu w usłudze Atlassian Cloud, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)