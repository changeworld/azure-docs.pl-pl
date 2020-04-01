---
title: 'Samouczek: Integracja usługi Azure Active Directory z Dropbox dla firm | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Dropbox dla Firm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df7fc366c5087e66c3022c212870397d77e6e34d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046760"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Samouczek: Integracja Dropbox dla Firm z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Dropbox dla Firm z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Dropbox dla Firm z usługą Azure AD możesz:

* Kontrola w usłudze Azure AD, która ma dostęp do Dropbox dla Firm.
* Włącz automatyczne logowanie użytkowników do Dropbox dla firm za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z obsługą logowania jednokrotnego dropbox dla firm(Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

* W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Aplikacja Dropbox dla Firm obsługuje logowanie jednokrotne inicjowane przez **SP**

* Dropbox dla Firm obsługuje [automatyczne inicjowanie obsługi administracyjnej i anulowanie obsługi administracyjnej](dropboxforbusiness-tutorial.md)
* Po skonfigurowaniu Dropbox możesz wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dodawanie aplikacji Dropbox dla Firm z galerii

Aby skonfigurować integrację aplikacji Dropbox dla Firm z usługą Azure AD, musisz dodać aplikację Dropbox dla Firm z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Dropbox dla Firm** w polu wyszukiwania.
1. Wybierz **Pozycję Dropbox dla Firm** w panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO w Dropbox dla firm przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik łączony sytuować działał, musisz ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Dropbox dla Firm.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą Dropbox dla firm, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.    
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Dropbox dla firm](#configure-dropbox-for-business-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Dropbox dla Firm](#create-dropbox-for-business-test-user)** — aby mieć w aplikacji Dropbox dla Firm odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Dropbox dla firm** znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://www.dropbox.com/sso/<id>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz wartość: `Dropbox`

    > [!NOTE]
    > Podana wyżej wartość adresu URL logowania nie jest prawdziwa. Ta wartość zostanie zaktualizowana przy użyciu rzeczywistego adresu URL logowania, co objaśniono w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj aplikację Dropbox dla Firm** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji umożliwisz Firmie Britta Simon korzystanie z logowania jednokrotnego platformy Azure, przyznając dostęp do Dropbox dla Firm.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Dropbox dla firm**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-dropbox-for-business-sso"></a>Konfigurowanie usługi Dropbox dla firm — sytego w witrynie SSO

1. Aby zautomatyzować konfigurację w Dropbox dla Firm, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Konfiguracja Dropbox dla Firm** przekieruje Cię do aplikacji Dropbox dla Firm. W tym miejscu podaj poświadczenia administratora, aby zalogować się do Dropbox dla Firm. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Dropbox dla Firm, otwórz nowe okno przeglądarki internetowej i przejdź do dzierżawy Dropbox dla Firm i zaloguj się do Dropbox dla dzierżawy biznesowej. i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurowanie logowania jednokrotnego")

4. Kliknij **ikonę użytkownika** i wybierz kartę **Settings** (Ustawienia).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurowanie logowania jednokrotnego")

5. W okienku nawigacji po lewej stronie kliknij pozycję **Admin console** (Konsola administracyjna).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurowanie logowania jednokrotnego")

6. W obszarze **Admin console** (Konsola administracyjna) kliknij pozycję **Settings** (Ustawienia) w okienku nawigacji po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurowanie logowania jednokrotnego")

7. Wybierz opcję **Single sign-on** (Logowanie jednokrotne) w sekcji **Authentication** (Uwierzytelnianie).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurowanie logowania jednokrotnego")

8. W sekcji **Logowanie jednokrotne** wykonaj następujące czynności:  

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurowanie logowania jednokrotnego")

    a. Z listy rozwijanej dla logowania **jednokrotnego**wybierz **pozycję Wymagane** jako opcja .

    b. Kliknij pozycję **Add sign-in URL** (Dodaj adres URL logowania), a następnie w polu tekstowym **Identity provider sign-in URL** (Adres URL logowania dostawcy tożsamości) wklej wartość **adresu URL logowania**, która została skopiowana z witryny Azure Portal, i wybierz pozycję ** Done** (Gotowe).

    ![Konfigurowanie logowania jednokrotnego](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurowanie logowania jednokrotnego")

    d. Kliknij pozycję **Upload certificate** (Przekaż certyfikat), a następnie przejdź do **pliku certyfikatu zakodowanego w formacie Base64**, który został pobrany z witryny Azure Portal.

    d. Kliknij pozycję **Copy link** (Kopiuj link) i wklej skopiowaną wartość w polu tekstowym **Adres URL logowania** w sekcji **Domena i adresy URL aplikacji Dropbox dla Firm** w witrynie Azure Portal.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-dropbox-for-business-test-user"></a>Tworzenie użytkownika testowego aplikacji Dropbox dla Firm

W tej sekcji w aplikacji Dropbox dla Firm jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Dropbox dla Firm obsługuje aprowizację użytkowników Just In Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Dropbox dla Firm, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Dropbox dla Firm](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Dropbox dla Firm w Panelu dostępu powinieneś zostać automatycznie zalogowany do Dropbox dla firm, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Dropbox dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/protect-dropbox)