---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z sieciami Palo Alto — GlobalProtect | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8faa1d0ecfec629487c9cddee6a01fb9f809393f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70166415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z sieciami Palo Alto — GlobalProtect

W tym samouczku dowiesz się, jak zintegrować siecie Palo Alto Networks — GlobalProtect z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Palo Alto Networks — GlobalProtect z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do sieci Palo Alto Networks — GlobalProtect.
* Włącz użytkownikom automatyczne logowanie do sieci Paleto — GlobalProtect za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Palo Alto Networks — subskrypcja z włączoną funkcją logowania jednokrotnego (GlobalProtect).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Palo Alto Networks - GlobalProtect obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Aplikacja Palo Alto Networks - GlobalProtect obsługuje aprowizację użytkownika **Just in time**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Dodawanie aplikacji Palo Alto Networks - GlobalProtect z galerii

Aby skonfigurować integrację aplikacji Palo Alto Networks - GlobalProtect w usłudze Azure AD, należy dodać pozycję Palo Alto Networks - GlobalProtect z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Palo Alto Networks - GlobalProtect** w polu wyszukiwania.
1. Wybierz **pozycję Palo Alto Networks — GlobalProtect** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla sieci Palo Alto Networks — GlobalProtect

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą palo alto networks — GlobalProtect przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sieciach Palo Alto Networks — GlobalProtect.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą palo Alto Networks — GlobalProtect, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Palo Alto Networks - GlobalProtect SSO](#configure-palo-alto-networks---globalprotect-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz Palo Alto Networks — GlobalProtect użytkownika testowego](#create-palo-alto-networks---globalprotect-test-user)** — aby mieć odpowiednik B.Simon w Palo Alto Networks — GlobalProtect, który jest połączony z reprezentacji azure ad użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie Integracja aplikacji **Palo Alto Networks — GlobalProtect** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie sieci Paletowych Palo — GlobalProtect** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Palo Alto Networks — GlobalProtect.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Palo Alto Networks - GlobalProtect**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurowanie sieci Palo Alto - GlobalProtect SSO

1. W innym oknie przeglądarki otwórz interfejs użytkownika administratora zapory w aplikacji Palo Alto Networks z uprawnieniami administracyjnymi.

2. Kliknij pozycję **Device** (Urządzenie).

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML) w lewym pasku nawigacyjnym, a następnie kliknij przycisk Import (Importuj), aby zaimportować plik metadanych.

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Wykonaj następujące czynności w oknie importowania

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W polu tekstowym **Profile Name** (Nazwa profilu) podaj nazwę np. Azure AD GlobalProtect.

    b. W polu **Identity Provider Metadata** (Metadane dostawcy tożsamości) kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z witryny Azure Portal.

    d. Kliknij **przycisk OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks - GlobalProtect

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Palo Alto Networks - GlobalProtect. Aplikacja Palo Alto Networks - GlobalProtect obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Palo Alto Networks - GlobalProtect, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Palo Alto Networks - GlobalProtect w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Palo Alto Networks - GlobalProtect, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Sieci Palo Alto — GlobalProtect z usługą Azure AD](https://aad.portal.azure.com/)