---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem jednokrotnym ns1 dla platformy Azure | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logajną NS1 dla platformy Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77565591"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z logiem jednokrotnym ns1 dla platformy Azure

W tym samouczku dowiesz się, jak zintegrować jednonę NS1 dla platformy Azure z usługą Azure Active Directory (Azure AD). Po zintegrowaniu jedno i tak jest NS1 dla platformy Azure z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, która ma dostęp do sytuaj jednośmiętne NS1 dla platformy Azure.
* Włącz użytkownikom automatyczne logowanie się do ns1 logowanie jednośmigłowe dla platformy Azure za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* NS1 SSO dla subskrypcji logowania jednokrotnego platformy Azure .NS1 Logowanie jednokrotne na platformie Azure (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* NS1 SSO for Azure obsługuje jednostkę SSO inicjowane przez protokół SP iDP.
* Po skonfigurowaniu jedno i tak ns1 dla platformy Azure, można wymusić kontrolę sesji. Chroni to eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Dodawanie sytua dla platformy Azure ns1 z galerii

Aby skonfigurować integrację wpisu SSO NS1 dla platformy Azure z usługą Azure AD, musisz dodać jednosojowe przysłowie NS1 dla platformy Azure z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **NS1 SSO for Azure** w polu wyszukiwania.
1. Wybierz **ns1 SSO dla platformy Azure** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla logowania jednokrotnego ns1 dla platformy Azure

Konfigurowanie i testowanie sytua obsługi usługi Azure AD za pomocą identyfikatora SSO NS1 dla platformy Azure przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby jedno przysłowie działało, nawiązuj połączenie między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze NS1 SSO dla platformy Azure.

Oto ogólne kroki konfigurowania i testowania jednosojnika usługi Azure AD z przysłówem jednoliderowym NS1 dla platformy Azure:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.

    a. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.

    b. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj logowanie jednokrotne ns1 dla usługi Azure,](#configure-ns1-sso-for-azure-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    a. **[Utwórz jedno i tak dla użytkownika testowego platformy Azure,](#create-an-ns1-sso-for-azure-test-user)** aby mieć odpowiednik B.Simon w NS1 SSO dla platformy Azure. Ten odpowiednik jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **NS1 dla aplikacji platformy Azure** znajdź sekcję **Zarządzaj.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego na stronie SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz następujący adres URL:`https://api.nsone.net/saml/metadata`

    b. W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, który używa następującego wzorca:`https://api.nsone.net/saml/sso/<ssoid>`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz następujący adres URL:`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj wartość adresu URL odpowiedzi przy użyciu rzeczywistego adresu URL odpowiedzi. Skontaktuj się z [ns1 SSO dla klienta platformy Azure zespół pomocy technicznej,](mailto:techops@nsone.net) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. NS1 SSO dla aplikacji platformy Azure oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartości tych atrybutów można zarządzać z sekcji **Atrybuty użytkownika & Oświadczenia** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka, aby otworzyć okno dialogowe **Atrybuty użytkownika.**

    ![Zrzut ekranu przedstawiający atrybuty użytkownika & sekcji Oświadczenia z wyróżnioną ikoną ołówka](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Wybierz nazwę atrybutu, aby edytować oświadczenie.

    ![Zrzut ekranu przedstawiający sekcję Atrybuty & oświadczenia użytkownika z wyróżnioną nazwą atrybutu](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Wybierz **opcję Transformacja**.

    ![Zrzut ekranu przedstawiający sekcję Zarządzanie oświadczeniami z wyróżnioną pojednawcą połączeń Transformacja](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. W sekcji **Zarządzanie transformacją** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję Zarządzanie transformacją z wyróżnionymi różnymi polami](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Wybierz **ExactMailPrefix()** jako **transformacja**.

    1. Wybierz **nazwa użytkownika.userprincipalname** jako **parametr 1**.

    1. Wybierz pozycję **Dodaj**.

    1. Wybierz **pozycję Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** wybierz przycisk kopiowania. Spowoduje to skopiowanie **adresu URL metadanych federacji aplikacji** i zapisanie go na komputerze.

    ![Zrzut ekranu przedstawiający certyfikat podpisywania SAML z wyróżnionym przyciskiem kopiowania](common/copy-metadataurl.png)

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

W tej sekcji można włączyć B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do logowania jednokrotnego NS1 dla platformy Azure.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **NS1 SSO for Azure**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurowanie jednosojnika jednolinika NS1 dla usługi Azure

Aby skonfigurować logowanie jednokrotne po stronie logowania jednokrotnego ns1 dla platformy Azure, musisz wysłać adres URL metadanych federacji aplikacji do [logowania jednokrotnego NS1 dla zespołu pomocy technicznej platformy Azure.](mailto:techops@nsone.net) Ustawi on to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Tworzenie identyfikatora jednośmiękowego NS1 dla użytkownika testowego platformy Azure

W tej sekcji utworzysz użytkownika o nazwie B.Simon w NS1 SSO dla platformy Azure. Praca z ns1 SSO dla zespołu pomocy technicznej platformy Azure, aby dodać użytkowników w ns1 SSO dla platformy Azure platformy. Nie można używać logowania jednokrotnego, dopóki nie utworzysz i nie aktywujesz użytkowników.

## <a name="test-sso"></a>Test SSO 

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka logującego jednośli dla platformy Azure w panelu programu Access należy automatycznie zalogować się do logajniającego jednośliżanie NS1 dla platformy Azure, dla którego skonfigurowano logującą jednośliżanie. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj jedno i tak i jednoolicie NS1 dla platformy Azure za pomocą usługi Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)