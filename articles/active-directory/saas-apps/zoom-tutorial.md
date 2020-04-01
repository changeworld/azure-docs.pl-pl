---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z funkcją Zoom | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70743964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z zoomem

W tym samouczku dowiesz się, jak zintegrować powiększenie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu zoomu z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do powiększenia.
* Włącz użytkownikom automatyczne logowanie do powiększania za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Powiększ subskrypcję z włączoną funkcją logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Zoom obsługuje inicjowane przez **sp** sygnowane funkcje SSO i 
* Zoom obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

## <a name="adding-zoom-from-the-gallery"></a>Dodawanie aplikacji Zoom z galerii

Aby skonfigurować integrację aplikacji Zoom w usłudze Azure AD, należy dodać aplikację Zoom z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Powiększenie** w polu wyszukiwania.
1. Wybierz **pozycję Powiększ** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla powiększenia

Konfigurowanie i testowanie sygn przez usługę Azure AD SSO przy użyciu funkcji Zoom przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w powiększeniu.

Aby skonfigurować i przetestować sygnatora usługi Azure AD z powiększeniem, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj powiększenie logowania powiększenia](#configure-zoom-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego powiększenia](#create-zoom-test-user)** — aby mieć odpowiednik B.Simon w powiększeniu, który jest połączony z reprezentacją użytkownika usługi Azure AD.
3. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Powiększenia** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.zoom.us`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `<companyname>.zoom.us`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoom](https://support.zoom.us/hc/) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie powiększenia** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

> [!NOTE]
> Aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD, zobacz [Konfigurowanie oświadczenia o roli wystawionego w tokenie SAML dla aplikacji korporacyjnych.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

> [!NOTE]
> Zoom może oczekiwać roszczenia grupy w ładunku SAML. Jeśli utworzono jakieś grupy, skontaktuj się z [zespołem pomocy technicznej klienta powiększenia](https://support.zoom.us/hc/) z informacjami o grupie, aby mógł skonfigurować informacje o grupie na ich końcu. Należy również podać identyfikator obiektu do [zespołu pomocy technicznej klienta zoom,](https://support.zoom.us/hc/) aby mogli skonfigurować identyfikator obiektu na ich końcu. Aby uzyskać identyfikator obiektu, zobacz [Konfigurowanie powiększenia za pomocą platformy Azure](https://support.zoom.us/hc/articles/115005887566).

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do powiększenia.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Powiększenie**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-zoom-sso"></a>Konfigurowanie funkcji SSO powiększenia

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Zoom jako administrator.

2. Kliknij kartę **Single Sign-On** (Logowanie jednokrotne).

    ![Karta logowania jednokrotnego](./media/zoom-tutorial/ic784700.png "Logowanie jednokrotne")

3. Kliknij kartę **Security Control** (Kontrola zabezpieczeń), a następnie przejdź do ustawień **Single Sign-On** (Logowanie jednokrotne).

4. W sekcji Single sign-on (Logowanie jednokrotne) wykonaj następujące kroki:

    ![Sekcja logowania jednokrotnego](./media/zoom-tutorial/ic784701.png "Logowanie jednokrotne")

    a. W polu tekstowym **Sign-in page URL** (Adres URL strony logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    b. Aby uzyskać wartość **adresu URL strony wylogowywania,** musisz przejść do witryny Azure portal i kliknąć **usługę Azure Active Directory** po lewej stronie, a następnie przejść do **rejestracji aplikacji**.

    ![Przycisk Azure Active Directory](./media/zoom-tutorial/appreg.png)

    d. Kliknij **punkty końcowe**

    ![Przycisk Punkt końcowy](./media/zoom-tutorial/endpoint.png)

    d. Skopiuj **punkt końcowy SAML-P SIGN-OUT** i wklej go do pola tekstowego **adresu URL strony wylogowywania.**

    ![Przycisk Kopiuj punkt końcowy](./media/zoom-tutorial/endpoint1.png)

    e. Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w zawartość w polu tekstowym **Certyfikat dostawcy tożsamości**.

    f. W polu tekstowym **wystawcy** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal. 

    g. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Aby uzyskać więcej informacji, zapoznaj się z dokumentacją zoomu[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Tworzenie użytkownika testowego aplikacji Zoom

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w zoom. Zoom obsługuje automatyczne inicjowanie obsługi administracyjnej użytkownika, które jest domyślnie włączone. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial).

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta zoomu](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Zoom w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Zoom, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Spróbuj powiększyć za pomocą usługi Azure AD](https://aad.portal.azure.com/)
