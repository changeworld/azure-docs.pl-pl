---
title: 'Samouczek: Integracja usługi Azure Active Directory z programem Lucidchart | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a lucidchart.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eace60445dc9d52f9690da74360282efbb4cbe5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76291215"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z programem Lucidchart

W tym samouczku dowiesz się, jak zintegrować lucidchart z usługi Azure Active Directory (Azure AD). Po zintegrowaniu lucidchart z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do lucidchart.
* Włącz użytkownikom automatyczne logowanie do lucidchart za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Lucidchart rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Lucidchart **obsługuje** sp zainicjowane SSO
* Lucidchart obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time
* Po skonfigurowaniu lucidchart można wymusić formanty sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-lucidchart-from-the-gallery"></a>Dodawanie lucidchart z galerii

Aby skonfigurować integrację lucidchart do usługi Azure AD, należy dodać lucidchart z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Lucidchart** w polu wyszukiwania.
1. Wybierz **lucidchart** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla lucidchart

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą lucidchart przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnowania użytkownika działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w lucidchart.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą lucidchart, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne lucidchart](#configure-lucidchart-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Lucidchart](#create-lucidchart-test-user)** — aby mieć odpowiednik B.Simon w lucidchart, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Lucidchart** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

   W polu **tekstowym Zaloguj adres URL** wpisz adres URL jako:`https://chart2.office.lucidchart.com/saml/sso/azure`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie lucidchart** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do lucidchart.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **lucidchart**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-lucidchart-sso"></a>Konfigurowanie sygn.

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Lucidchart jako administrator.

2. W menu u góry kliknij pozycję **Zespół**.

    ![Zespół](./media/lucidchart-tutorial/ic791190.png "Zespół")

3. Kliknij ** \> pozycję Aplikacje Zarządzaj SAML**.

    ![Zarządzanie SAML](./media/lucidchart-tutorial/ic791191.png "Zarządzanie SAML")

4. Na stronie okna dialogowego **Ustawienia uwierzytelniania SAML** wykonaj następujące czynności:

    a. Wybierz **pozycję Włącz uwierzytelnianie SAML**, a następnie kliknij pozycję **Opcjonalne**.

    ![Ustawienia uwierzytelniania SAML](./media/lucidchart-tutorial/ic791192.png "Ustawienia uwierzytelniania SAML")

    b. W obszarze tekstowym **Domena** wpisz domenę, a następnie kliknij pozycję **Zmień certyfikat**.

    ![Zmień certyfikat](./media/lucidchart-tutorial/ic791193.png "Zmień certyfikat")

    d. Otwórz pobrany plik metadanych, skopiuj zawartość, a następnie wklej go do pola tekstowego **Przekaż metadane.**

    ![Przekaż metadane](./media/lucidchart-tutorial/ic791194.png "Przekaż metadane")

    d. Wybierz pozycję **Automatycznie dodaj nowych użytkowników do zespołu,** a następnie kliknij pozycję Zapisz **zmiany**.

    ![Zapisz zmiany](./media/lucidchart-tutorial/ic791195.png "Zapisz zmiany")

### <a name="create-lucidchart-test-user"></a>Utwórz użytkownika testowego lucidchart

Nie ma żadnego elementu akcji, aby skonfigurować inicjowanie obsługi administracyjnej użytkownika do lucidchart.  Gdy przypisany użytkownik próbuje zalogować się do lucidchart za pomocą panelu dostępu, Lucidchart sprawdza, czy użytkownik istnieje.  

Jeśli nie ma jeszcze dostępnego konta użytkownika, jest ono automatycznie tworzone przez lucidchart.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Lucidchart w Panelu dostępu należy automatycznie zalogować się do lucidchart, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj lucidchart z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Lucidchart z zaawansowaną widocznością i kontrolą](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
