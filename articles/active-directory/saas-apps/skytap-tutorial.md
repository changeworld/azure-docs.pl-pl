---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z logiem jednokrotnym dla programu Skytap | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logowaniem jednokrotnym dla programu Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565778"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z logiem jednokrotnym dla programu Skytap

W tym samouczku dowiesz się, jak zintegrować logowanie jednokrotne dla programu Skytap z usługą Azure Active Directory (Azure AD). Po zintegrowaniu logowania jednokrotnego dla skytap z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, która ma dostęp do logowania jednokrotnego dla skytap.
* Włącz użytkownikom automatyczne logowanie się do logowania jednokrotnego dla skytap za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Logowanie jednokrotne dla subskrypcji z włączoną funkcją logowania jednokrotnego skytap.Single Sign-on for Skytap single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne dla skytap obsługuje SP i IDP zainicjowane logowanie jednokrotne.
* Po skonfigurowaniu logowania jednokrotnego dla skytap, można wymusić kontrolę sesji. Chroni to eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Dodawanie logowania jednokrotnego dla skytap z galerii

Aby skonfigurować integrację logowania jednokrotnego dla programu Skytap z usługą Azure AD, należy dodać logowanie jednokrotne dla programu Skytap z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji dla przedsiębiorstw,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **logowanie jednokrotne dla skytap** w polu wyszukiwania.
1. Wybierz **opcję Logowanie jednokrotne dla skytap** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego dla programu Skytap

Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD za pomocą logowania jednokrotnego dla skytap przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby logowanie jednokrotne działało, nawiązuj połączenie między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w trybie logowania jednokrotnego dla skytap.

Oto ogólne kroki konfigurowania i testowania logowania jednokrotnego usługi Azure AD za pomocą logowania jednokrotnego dla skytap:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.

    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj logowanie jednokrotne dla aplikacji SSO skytap,](#configure-single-sign-on-for-skytap-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    a. **[Utwórz logowanie jednokrotne dla użytkownika testowego Skytap,](#create-single-sign-on-for-skytap-test-user)** aby mieć odpowiednik B.Simona w logach jednokrotnych dla Skytap. Ten odpowiednik jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **logowania jednokrotnego dla skytap** znajdź sekcję **Zarządzanie.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego na stronie SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca:`http://pingone.com/<custom EntityID>`

    b. W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, który używa następującego wzorca:`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**i wykonaj następujące czynności, aby skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, który używa następującego wzorca:`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. W polu tekstowym **Stan przekazywania** wpisz adres URL, który używa następującego wzorca:`https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej klienta klienta Skytap.](mailto:support@skytap.com) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź identyfikator **XML metadanych federacji**. Wybierz **pozycję Pobierz,** aby pobrać plik metadanych i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający łącze do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego dla skytap** skopiuj odpowiedni adres URL lub adresy URL na podstawie wymagań.

    ![Zrzut ekranu przedstawiający adresy URL konfiguracji kopii](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zapisz wartość wyświetlaną w polu **Hasło.**
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do logowania jednokrotnego dla skytap.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **opcję Logowanie jednokrotne dla skytap**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Konfigurowanie logowania jednokrotnego dla logowania sytograficznego skytap

Aby skonfigurować logowanie jednokrotne po stronie logowania jednokrotnego dla programu Skytap, należy wysłać pobrany **kod XML metadanych federacji**i odpowiednie skopiowane adresy URL z portalu Azure do [zespołu pomocy technicznej klienta klienta skytap.](mailto:support@skytap.com) Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Tworzenie logowania jednokrotnego dla użytkownika testowego Skytap

W tej sekcji utworzysz użytkownika o nazwie B.Simon w logowanie jednokrotne dla Skytap. Współpracuj z [zespołem pomocy technicznej logowania jednokrotnego dla klienta Skytap,](mailto:support@skytap.com) aby dodać użytkowników na platformie Logowanie jednokrotne dla skytap. Nie można używać logowania jednokrotnego, dopóki nie utworzysz i nie aktywujesz użytkowników.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Logowania jednokrotnego dla skytap w Panelu dostępu należy automatycznie zalogować się do logowania jednokrotnego dla skytap, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj slack z usługą Azure AD](https://aad.portal.azure.com/)

