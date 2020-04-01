---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z in case of crisis - Mobile | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a w przypadku kryzysu — mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6afa98e9-a123-4cbb-add6-afcefc1242e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bff484e4ca3a1579067501be134d71da72abfa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266087"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z in Case of Crisis - Mobile

W tym samouczku dowiesz się, jak zintegrować w przypadku kryzysu — mobile z usługi Azure Active Directory (Azure AD). Po zintegrowaniu w przypadku kryzysu — mobile z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do w przypadku kryzysu — mobile.
* Włącz użytkownikom automatyczne logowanie się do in case of crisis — mobile za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* W przypadku kryzysu — subskrypcja z włączoną funkcją logowania jednokrotnego (Mobile Single Sign-on).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* W przypadku kryzysu - Mobile obsługuje zainicjowane przez **IDP** SSO

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Dodawanie w przypadku kryzysu - Mobile z galerii

Aby skonfigurować integrację w przypadku kryzysu — mobile do usługi Azure AD, należy dodać w przypadku kryzysu — Mobile z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania **wpisz W przypadku kryzysu — Mobile.**
1. Wybierz **opcję W przypadku kryzysu — mobile** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD w przypadku kryzysu — mobilne

Konfigurowanie i testowanie usługi Azure AD SSO z in Case of Crisis - Mobile przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w przypadku kryzysu — mobile.

Aby skonfigurować i przetestować samouszeńców usługi Azure AD z in case of crisis — Mobile, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj w przypadku kryzysu — mobile SSO](#configure-in-case-of-crisis---mobile-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz w przypadku kryzysu — mobile użytkownika testowego](#create-in-case-of-crisis---mobile-test-user)** - mieć odpowiednik B.Simon w przypadku kryzysu — Mobile, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **w przypadku kryzysu — mobilna** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja** SAML aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.** 

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (raw)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. Przejdź do sekcji **Zarządzaj** po lewej stronie strony, kliknij **kartę Właściwości,** a następnie skopiuj **adres URL dostępu użytkownika** i zapisz go na komputerze.

    ![Właściwości logowania jednokrotnego](./media/in-case-of-crisis-mobile-tutorial/properties.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do w przypadku kryzysu — mobile.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz opcję **W przypadku kryzysu — Mobile**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Konfiguracja w przypadku kryzysu — mobilny sytowy

Aby skonfigurować logowanie jednokrotne w przypadku kryzysu — strona **mobilna,** należy wysłać pobrany **certyfikat (raw)** i skopiowany **adres URL dostępu użytkownika** z witryny Azure portal do zespołu pomocy technicznej In Case of Crisis - [Mobile](https://www.rockdovesolutions.com/features/enterprise-ready). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Tworzenie w przypadku kryzysu - mobilny użytkownik testowy

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przypadku kryzysu - Mobile. Praca z [in case of crisis - Mobile support team](https://www.rockdovesolutions.com/features/enterprise-ready) to add the users in the In Case of Crisis - Mobile platform. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka W przypadku kryzysu — Mobilny w panelu dostępu należy automatycznie zalogować się do urządzenia W przypadku kryzysu — Mobile, dla którego skonfigurowano logującą się do rejestru jednokrotnego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj w przypadku kryzysu — mobile z usługą Azure AD](https://aad.portal.azure.com/)

