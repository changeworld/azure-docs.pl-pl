---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z salesforce | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/17/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a875cee7e6796a2c865bde4a62f2f0463eb12130
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78967722"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z salesforce

W tym samouczku dowiesz się, jak zintegrować salesforce z usługą Azure Active Directory (Azure AD). Po zintegrowaniu salesforce z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Salesforce.
* Włącz użytkownikom automatyczne logowanie do salesforce za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Salesforce.Salesforce single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Usługa Salesforce obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

* Salesforce obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej i anulowanie obsługi administracyjnej](salesforce-provisioning-tutorial.md) (zalecane)

* Aplikacja Salesforce obsługuje aprowizowanie użytkowników typu **just in time**

* Aplikację Salesforce Mobile można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia funkcji SSO. W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.
* Po skonfigurowaniu salesforce można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-from-the-gallery"></a>Dodawanie usługi Salesforce z galerii

Aby skonfigurować integrację usługi Salesforce z usługą Azure AD, musisz dodać usługę Salesforce z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Salesforce** w polu wyszukiwania.
1. Wybierz **Salesforce** z panelu wyniki, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla salesforce

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą salesforce przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w salesforce.

Aby skonfigurować i przetestować sytą usługę Azure AD z salesforce, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj salesforce SSO](#configure-salesforce-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Salesforce](#create-salesforce-test-user)** — aby mieć odpowiednik B.Simon w Salesforce, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Salesforce** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz wartość, korzystając z następującego wzorca:

    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. W polu tekstowym **Identyfikator** wpisz wartość, korzystając z następującego wzorca:

    Konto przedsiębiorstwa: `https://<subdomain>.my.salesforce.com`

    Konto dewelopera: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Salesforce](https://help.salesforce.com/support).

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie salesforce** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Salesforce.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Salesforce**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-salesforce-sso"></a>Konfigurowanie agenta SYC salesforce

1. Aby zautomatyzować konfigurację w salesforce, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Ustaw Salesforce** przekieruje Cię do aplikacji rejestracji jednokrotnej Salesforce. W tym miejscu podaj poświadczenia administratora, aby zalogować się do rejestracji jednokrotnej Salesforce. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-13.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować Salesforce, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Salesforce jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/configure1.png)

1. Przewiń w dół do pozycji **USTAWIENIA** w okienku nawigacji i kliknij pozycję **Tożsamość**, aby rozwinąć odpowiednią sekcję. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso.png)

1. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Jeśli nie możesz włączyć ustawień logowania jednokrotnego dla swojego konta usługi Salesforce, może być konieczne skontaktowanie się z [zespołem pomocy technicznej klienta usługi Salesforce](https://help.salesforce.com/support).

1. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-enable-saml.png)

1. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Kliknij pozycję **Wybierz plik**, aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/xmlchoose.png)

1. Na stronie **Ustawienia logowania jednokrotnego SAML** pola wypełniane są automatycznie, wybierz **opcję Włączono inicjowanie obsługi użytkownika,** a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/salesforcexml.png)

1. W okienku nawigacji po lewej stronie w usłudze Salesforce kliknij pozycję **Ustawienia firmowe**, aby rozwinąć odpowiednią sekcję, a następnie kliknij pozycję **Moja domena**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-my-domain.png)

1. Przewiń w dół do sekcji **Konfiguracja uwierzytelniania** i kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. W sekcji **Konfiguracja uwierzytelniania** zaznacz pozycję **AzureSSO** jako **usługę uwierzytelniania** konfiguracji logowania jednokrotnego SAML, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Jeśli zostanie wybrana więcej niż jedna usługa uwierzytelniania, podczas inicjowania logowania w środowisku usługi Salesforce użytkownikom będzie wyświetlany monit o wybranie usługi uwierzytelniania, przy użyciu której chcą się zalogować. Jeśli nie chcesz, aby ten monit był wyświetlany, **pozostaw wszystkie inne usługi uwierzytelniania niezaznaczone**.

### <a name="create-salesforce-test-user"></a>Tworzenie użytkownika testowego usługi Salesforce

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Salesforce. Usługa Salesforce obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze Salesforce, zostanie utworzony podczas próby uzyskania dostępu do usługi Salesforce. Usługa Salesforce obsługuje także automatyczne aprowizowanie użytkowników. Więcej szczegółowych informacji na temat konfigurowania automatycznego aprowizowania użytkowników możesz znaleźć [tutaj](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Salesforce w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Salesforce, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Test SSO dla Salesforce (Mobile)

1. Otwórz aplikację mobilną Salesforce. Na stronie logowania kliknij pozycję **Użyj domeny niestandardowej**.

    ![Aplikacja mobilna Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. W polach **tekstowych Domena niestandardowa** wprowadź zarejestrowaną nazwę domeny niestandardowej i kliknij przycisk **Kontynuuj**.

    ![Aplikacja mobilna Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Wprowadź poświadczenia usługi Azure AD, aby zalogować się do aplikacji Salesforce, a następnie kliknij przycisk **Dalej**.

    ![Aplikacja mobilna Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na stronie **Zezwalaj na dostęp,** jak pokazano poniżej, kliknij pozycję **Zezwalaj,** aby udzielić dostępu do aplikacji Salesforce.

    ![Aplikacja mobilna Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Wreszcie po pomyślnym zalogowaniu zostanie wyświetlona strona główna aplikacji.

    ![Aplikacja mobilna](media/salesforce-tutorial/mobile-app5.png) ![Salesforce Aplikacja mobilna Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](salesforce-provisioning-tutorial.md)

- [Wypróbuj salesforce z usługą Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Salesforce dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/protect-salesforce)