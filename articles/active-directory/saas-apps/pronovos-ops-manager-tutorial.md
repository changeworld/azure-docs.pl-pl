---
title: 'Samouczek: Integracja usługi Azure Active Directory z menedżerem operacji ProNovos | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a menedżerem pronovos ops Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6eb0780c-ff52-464f-8998-e24ea6bbf600
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c898dd36387d4474f5129943c6bd76f754ffe2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70213538"
---
# <a name="tutorial-integrate-pronovos-ops-manager-with-azure-active-directory"></a>Samouczek: Integracja programu ProNovos Ops Manager z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować pronovos ops manager z usługą Azure Active Directory (Azure AD). Po zintegrowaniu pronovos ops manager z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, która ma dostęp do ProNovos Ops Manager.
* Włącz użytkownikom automatyczne logowanie do pronovos ops manager za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) programu ProNovos Ops Manager.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* ProNovos Ops Manager obsługuje **sp i IDP** zainicjowane SSO

## <a name="adding-pronovos-ops-manager-from-the-gallery"></a>Dodawanie ProNovos Ops Manager z galerii

Aby skonfigurować integrację pronovos ops manager do usługi Azure AD, należy dodać ProNovos Ops Manager z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ProNovos Ops Manager** w polu wyszukiwania.
1. Wybierz **ProNovos Ops Manager** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą menedżera operacji ProNovos przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w menedżerze operacji ProNovos.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą menedżera operacji ProNovos, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie logowania do pracy pronovos ops Manager](#configure-pronovos-ops-manager-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego ProNovos Ops Manager](#create-pronovos-ops-manager-test-user)** — aby mieć odpowiednik B.Simon w ProNovos Ops Manager, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **ProNovos Ops Manager** znajdź sekcję **Zarządzaj** i wybierz opcję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana w trybie inicjowanym **przez usługę IDP,** a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk **Zapisz.**

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://gly.smartsubz.com/saml2/acs`


4. Na stronie **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (raw)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

6. W sekcji **Konfigurowanie Menedżera operacji ProNovos** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-pronovos-ops-manager-sso"></a>Konfigurowanie syt managera operacji ProNovos

Aby skonfigurować logowanie jednokrotne po stronie **ProNovos Ops Manager,** należy wysłać pobrany **certyfikat (raw)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej ProNovos Ops Manager](mailto:support@pronovos.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.
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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do ProNovos Ops Manager.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pronovos ops manager**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-pronovos-ops-manager-test-user"></a>Utwórz użytkownika testowego ProNovos Ops Manager

W tej sekcji utworzysz użytkownika o nazwie B.Simon w ProNovos Ops Manager. Współpracuj z [zespołem pomocy technicznej ProNovos Ops Manager,](mailto:support@pronovos.com) aby dodać użytkowników na platformie ProNovos Ops Manager. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ProNovos Ops Manager w Panelu dostępu należy automatycznie zalogować się do Menedżera operacji ProNovos, dla którego skonfigurowano logowanie jednoczycieliowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

