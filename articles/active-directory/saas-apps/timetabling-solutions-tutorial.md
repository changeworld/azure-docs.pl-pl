---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z rozwiązaniami do udostępniania czasu | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniami do rozsyłania czasu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d171564f-1acf-4d87-afe9-9d91c2c13e8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10c1db9aa0452ae70564d2025f34d05629d3bc72
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265580"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timetabling-solutions"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z rozwiązaniami do udostępniania czasu

W tym samouczku dowiesz się, jak zintegrować rozwiązania timetabling z usługą Azure Active Directory (Azure AD). Po zintegrowaniu rozwiązań timetabling z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do rozwiązań timetabling.
* Włącz użytkownikom automatyczne logowanie do aplikacji Timetabling Solutions za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej (SSO) firmy Timetabling Solutions.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Timetabling Solutions obsługuje sso inicjowane przez **SP**
* Po skonfigurowaniu rozwiązań timetabling można wymusić kontrolę sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-timetabling-solutions-from-the-gallery"></a>Dodawanie rozwiązań do limitów czasu z galerii

Aby skonfigurować integrację rozwiązań poklatkowych z usługą Azure AD, należy dodać rozwiązania do blokowania czasu z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Timetabling Solutions** w polu wyszukiwania.
1. Wybierz **pozycję Rozwiązania do rozliczania czasu** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-timetabling-solutions"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla rozwiązań do rozsyłania czasu

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą rozwiązań czasowych przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w rozwiązaniach do ustanawiania czasu.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą rozwiązań timetabling, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne rozwiązań do](#configure-timetabling-solutions-sso)** tworzenia terminów pracy - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego timetabling solutions](#create-timetabling-solutions-test-user)** — aby mieć odpowiednik B.Simon w rozwiązaniach timetabling, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Timetabling Solutions** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://auth.timetabling.education/login`

1. W sekcji **Certyfikat podpisywania SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Certyfikat podpisywania SAML**.

    ![Edytowanie certyfikatu podpisywania SAML](common/edit-certificate.png)

1. W sekcji **Certyfikat podpisywania SAML** skopiuj **wartość odcisku palca** i zapisz ją na komputerze.

    ![Kopiowanie wartości Odcisk palca](common/copy-thumbprint.png)

1. W sekcji **Konfigurowanie rozwiązań do wyznaczania czasu** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do timetabling rozwiązań.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Rozwiązania do rozsyłania czasu**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-timetabling-solutions-sso"></a>Konfigurowanie sytua dla rozwiązań sytuanych w zakresie limitów czasu

Aby skonfigurować logowanie jednokrotne po stronie **Rozwiązań do przesyłania książek czasowych,** należy wysłać **wartość odcisku palca** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej timetabling solutions.](https://www.timetabling.com.au/contact-us/) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-timetabling-solutions-test-user"></a>Utwórz użytkownika testowego Timetabling Solutions

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Timetabling Solutions. Współpracuj z [zespołem pomocy technicznej Timetabling Solutions,](https://www.timetabling.com.au/contact-us/) aby dodać użytkowników na platformie Timetabling Solutions. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Rozwiązania z limitami czasu w Panelu dostępu należy automatycznie zalogować się do rozwiązania do ustanawiania informacji o czasie, dla których skonfigurowano logującą się jednokrotność. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj rozwiązania w zakresie limitów czasu za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić rozwiązania timetabling z zaawansowaną widocznością i kontrolą](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

