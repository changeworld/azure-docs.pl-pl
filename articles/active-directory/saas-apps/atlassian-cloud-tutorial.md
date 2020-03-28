---
title: 'Samouczek: Integracja usługi Azure Active Directory z atlassian cloud | Dokumenty firmy Microsoft'
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
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a54b096019e9e38bc800ae313016a430062dab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74964331"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Samouczek: Integracja chmury Atlassian z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować atlassian cloud z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Atlassian Cloud z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do atlassian cloud.
* Włącz użytkownikom automatyczne logowanie się do usługi Atlassian Cloud za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego atlassian Cloud(SSO).
* Aby włączyć logowanie jednokrotne SAML (Security Assertion Markup Language) dla produktów Atlassian Cloud, należy skonfigurować usługę Atlassian Access. Dowiedz się więcej o usłudze [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 

* Usługa Atlassian Cloud obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**
* Atlassian Cloud obsługuje [automatyczne inicjowanie obsługi administracyjnej i anulowanie obsługi administracyjnej](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Dodawanie usługi Atlassian Cloud z galerii

Aby skonfigurować integrację usługi Atlassian Cloud z usługą Azure AD, należy dodać usługę Atlassian Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Atlassian Cloud** w polu wyszukiwania.
1. Wybierz **atlassian cloud** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi Atlassian Cloud przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Atlassian Cloud.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Atlassian Cloud, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne w chmurze atlassian](#configure-atlassian-cloud-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Atlassian Cloud](#create-atlassian-cloud-test-user)** — aby mieć odpowiednik B.Simon w atlassian cloud, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Atlassian Cloud** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://auth.atlassian.com/saml/<unique ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Te rzeczywiste wartości zostaną wyświetlone na ekranie **konfiguracji SAML atlassian cloud,** który został wyjaśniony w dalszej części **samouczka Konfigurowanie logowania jednokrotnego atlassian cloud.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Wklej wartość z wystąpienia, którego używasz do logowania do portalu administracyjnego atlassian cloud.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Aplikacja Atlassian Cloud oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Atlassian Cloud oczekuje, że atrybut **nameidentifier** będzie zamapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj usługę Atlassian Cloud** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do atlassian cloud.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Atlassian Cloud**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-atlassian-cloud-sso"></a>Konfigurowanie usługi SSO chmury atlasian

1. Aby zautomatyzować konfigurację w atlassian cloud, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Atlassian Cloud** przekieruje Cię do aplikacji Atlassian Cloud. W tym miejscu podaj poświadczenia administratora, aby zalogować się do atlassian cloud. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować atlassian cloud, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Atlassian Cloud jako administrator i wykonaj następujące czynności:

1. Musisz zweryfikować swoją domenę przed przejściem do konfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz dokument [Weryfikacja domeny Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

1. W lewym okienku wybierz pozycję **Logowanie** > **jednokrotne saml**zabezpieczeń . Jeśli nie zostało to jeszcze zrobione, zasubskrybuj usługę Atlassian Identity Manager.

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. W oknie **Add SAML configuration** (Dodawanie konfiguracji SAML) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. W polu **Identyfikator jednostki dostawcy tożsamości** wklej identyfikator usługi Azure **AD** skopiowany z witryny Azure portal.

    b. W polu **adresu URL logowania dostawcy tożsamości** wklej adres URL **logowania** skopiowany z witryny Azure portal.

    d. Otwórz pobrany certyfikat w witrynie Azure Portal w pliku TXT, skopiuj wartość (bez wierszy *Begin Certificate* i *End Certificate*), a następnie wklej ją w polu **Public X509 certificate** (Publiczny certyfikat X509).

    d. Kliknij pozycję **Save Configuration** (Zapisz konfigurację).

1. Aby upewnić się, że skonfigurowano poprawne adresy URL, zaktualizuj ustawienia usługi Azure AD, wykonując następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. W oknie SAML skopiuj **identyfikator tożsamości SP,** a następnie w portalu Azure w obszarze Atlassian Cloud **Basic SAML Configuration**, wklej go w polu **Identyfikator.**

    b. W oknie SAML skopiuj **adres URL usługi konsumenta oświadczeń SP,** a następnie w witrynie Azure portal w obszarze Atlassian Cloud **Basic SAML Configuration**wklej go w polu Adres URL **odpowiedzi.** Adres URL logowania jest adresem URL dzierżawy Twojej usługi Atlassian Cloud.

    > [!NOTE]
    > Jeśli jesteś istniejącym klientem, po zaktualizowaniu wartości **Identyfikator tożsamości SP** i **Adres URL usługi Assertion Consumer Service SP** w witrynie Azure Portal wybierz opcję **Tak, zaktualizuj konfigurację**. Jeśli jesteś nowym klientem, możesz pominąć ten krok.

### <a name="create-atlassian-cloud-test-user"></a>Tworzenie użytkownika testowego usługi Atlassian Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie do usługi Atlassian Cloud, aprowizuj ręcznie konta użytkowników w usłudze Atlassian Cloud, wykonując następujące czynności:

1. W okienku **Administracja** okienku wybierz pozycję **Użytkownicy**.

    ![Link Użytkownicy usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Aby utworzyć użytkownika w usłudze Atlassian Cloud, wybierz pozycję **Zaproś użytkownika**.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. W polu **Adres e-mail** wprowadź adres e-mail użytkownika, a następnie przypisz dostęp do aplikacji.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Aby wysłać wiadomość e-mail z zaproszeniem do użytkownika, wybierz pozycję **Zaproś użytkowników**. Wiadomość e-mail z zaproszeniem zostanie wysłana do użytkownika, a po zaakceptowaniu zaproszenia użytkownik będzie aktywny w systemie.

> [!NOTE]
> Możesz również zbiorczo utworzyć użytkowników, wybierając przycisk **Tworzenie zbiorcze** w sekcji **Użytkownicy**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Atlassian Cloud w panelu dostępu należy automatycznie zalogować się do chmury Atlassian Cloud, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj atlassian cloud z usługą Azure AD](https://aad.portal.azure.com/)
