---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z compasem Netvision | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a compas netvision.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą Netvision Compas

W tym samouczku dowiesz się, jak zintegrować compas netvision z usługą Azure Active Directory (Azure AD). Po zintegrowaniu netvision Compas z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do compas netvision.
* Włącz użytkownikom automatyczne logowanie do aplikacji Netvision Compas za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Netvision Compas (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Netvision Compas obsługuje jednostki SSO inicjowane przez **SP i IDP**
* Po skonfigurowaniu netvision Compas można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Dodawanie netvision Compas z galerii

Aby skonfigurować integrację aplikacji Netvision Compas z usługą Azure AD, należy dodać compas netvision z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netvision Compas** w polu wyszukiwania.
1. Wybierz **netvision Compas** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Netvision Compas

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą aplikacji Netvision Compas przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Netvision Compas.

Aby skonfigurować i przetestować samouszeńców usługi Azure AD za pomocą aplikacji Netvision Compas, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne netvision Compas](#configure-netvision-compas-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Skonfiguruj użytkownika testowego Netvision Compas](#configure-netvision-compas-test-user)** — aby mieć odpowiednik B.Simon w netvision Compas, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Netvision Compas** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Netvision Compas,](mailto:contact@net.vision) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać plik metadanych i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)



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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do netvision Compas.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Netvision Compas**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-netvision-compas-sso"></a>Konfigurowanie compas Netvision Compas SSO

W tej sekcji można włączyć SAML SSO w **Netvision Compas**.
1. Zaloguj się do **Netvision Compas** za pomocą konta administracyjnego i uzyskaj dostęp do obszaru administracyjnego.

    ![Obszar administracyjny](media/netvision-compas-tutorial/admin.png)

1. Znajdź obszar **System** i wybierz **pozycję Dostawcy tożsamości**.

    ![Dostawcy usług administracyjnych](media/netvision-compas-tutorial/admin-idps.png)

1. Wybierz akcję **Dodaj,** aby zarejestrować usługę Azure AD jako nową usługę IDP.

    ![Dodawanie dpi](media/netvision-compas-tutorial/idps-add.png)

1. Wybierz **SAML** dla **typu Dostawca**.
1. Wprowadź wartości miare opisu dla pól **Nazwa wyświetlana** i **Opis.**
1. Przypisz użytkowników **Netvision Compas** do dostawcy tożsamości, wybierając z listy **Dostępni użytkownicy,** a następnie wybierając wybrany przycisk **Dodaj.** Użytkownicy mogą być również przypisane do IDP podczas postępowania zgodnie z procedurą inicjowania obsługi administracyjnej.
1. W przypadku opcji SAML **metadanych** kliknij przycisk **Wybierz plik** i wybierz plik metadanych wcześniej zapisany na komputerze.
1. Kliknij przycisk **Zapisz**.

    ![Edytowanie tożsamości z tożsamością](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Konfigurowanie użytkownika testowego Netvision Compas

W tej sekcji skonfigurujesz istniejącego użytkownika w **usłudze Netvision Compas** do używania usługi Azure AD dla usługi SSO.
1. Postępuj zgodnie z procedurą inicjowania obsługi administracyjnej użytkowników **netvision Compas,** zgodnie z definicją firmy lub edytuj istniejące konto użytkownika.
1. Podczas definiowania profilu użytkownika upewnij się, że adres **e-mail (osobisty)** użytkownika username@companydomain.extensionjest zgodny z nazwą użytkownika usługi Azure AD: . Na przykład `B.Simon@contoso.com`.

    ![Edytuj użytkownika](media/netvision-compas-tutorial/user-config.png)

Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Korzystanie z Panelu dostępu (zainicjowane przez IDP).

Po kliknięciu kafelka Netvision Compas w Panelu dostępu należy automatycznie zalogować się do compas netvision, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Bezpośredni dostęp do netvision Compas (SP inicjowane).

1. Uzyskaj dostęp do adresu URL **compas netvision.** Na przykład `https://tenant.compas.cloud`.
1. Wprowadź nazwę użytkownika **Netvision Compas** i wybierz **pozycję Dalej**.

    ![Logowanie użytkownika](media/netvision-compas-tutorial/login-user.png)

1. **(opcjonalnie)** Jeśli użytkownikowi jest przypisanych wiele adresów IP w **ramach compas netvision,** zostanie wyświetlona lista dostępnych adresów IP. Wybierz usługę Azure AD IDP skonfigurowany wcześniej w **aplikacji Netvision Compas**.

    ![Wybierz pozycję logowania](media/netvision-compas-tutorial/login-choose.png)

1. Użytkownik zostanie przekierowany do usługi Azure AD w celu wykonania uwierzytelniania. Po pomyślnym uwierzytelnieniu należy automatycznie zalogować się do **aplikacji Netvision Compas,** dla której skonfigurowano loga jedno iczkę wstępną.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj netvision Compas z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
