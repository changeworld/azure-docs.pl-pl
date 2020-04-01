---
title: 'Samouczek: Integracja usługi Azure Active Directory z amazon business | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496910"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Samouczek: Integracja usługi Amazon Business z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować amazon business z usługą Azure Active Directory (Azure AD). Po zintegrowaniu [amazon business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Amazon Business.
* Włącz automatyczne logowanie użytkowników do usługi Amazon Business za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z funkcją logowania jednokrotnego (SSO) firmy Amazon Business. Przejdź do strony [Amazon Business,](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) aby utworzyć konto Amazon Business.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouczycień samouszeńców usługi Azure AD w istniejącym koncie Amazon Business.

* Amazon Business obsługuje **sp i idp** zainicjowane SSO
* Amazon Business obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-amazon-business-from-the-gallery"></a>Dodawanie Amazon Business z galerii

Aby skonfigurować integrację amazon business z usługą Azure AD, musisz dodać amazon business z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Amazon Business** w polu wyszukiwania.
1. Wybierz **Amazon Business** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO z usługą Amazon Business przy użyciu użytkownika testowego o nazwie **B.Simon**.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Amazon Business, wykonaj następujące kroki tworzenia:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Amazon Business](#configure-amazon-business-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Amazon Business](#create-amazon-business-test-user)** — aby mieć odpowiednik B.Simon w Amazon Business, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Amazon Business** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL przy użyciu jednego z następujących wzorców:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Otrzymasz `<idpid>` wartość z sekcji konfiguracji SSO Amazon Business, która jest wyjaśniona w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://www.amazon.com/`

1. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Edytuj atrybuty, klikając ikonę **Edytuj** w sekcji **Atrybuty & oświadczenia użytkownika.**

    ![Atrybuty](media/amazon-business-tutorial/map-attribute3.png)

1. Edytuj atrybuty i **skopiuj** wartość obszaru nazw tych atrybutów do Notatnika.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute4.png)

1. Oprócz powyższej, Aplikacja Amazon Business oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. W sekcji **Atrybuty & oświadczenia użytkownika** w oknie dialogowym **Oświadczenia grupy** wykonaj następujące czynności:

    a. Kliknij **pióro** obok pozycji **Grupy zwrócone w zgłęceniu**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Wybierz **pozycję Wszystkie grupy** z listy radiowej.

    d. Wybierz **pozycję Identyfikator grupy** jako atrybut **źródło**.

    d. Zaznacz **pole wyboru Dostosuj nazwę oświadczenia grupy** i wprowadź nazwę grupy zgodnie z wymaganiami organizacji.

    e. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Certyfikat podpisywania SAML** znajdź **metadane XML** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Amazon Business** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Konfigurowanie ujeżdna 2/84 Amazon Business

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Amazon Business jako administrator.

1. Kliknij **profil użytkownika** i wybierz pozycję **Ustawienia biznesowe**.

    ![Profil użytkownika](media/amazon-business-tutorial/user-profile.png)

1. W kreatorze **integracji systemu** wybierz pozycję **Logowanie jednokrotne .**

    ![Logowanie jednokrotne (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. W kreatorze **konfigurowania jednośmiętnego dostawcy** wybierz dostawcę zgodnie z wymaganiami organizacyjnymi i kliknij przycisk **Dalej**.

    ![Grupa domyślna](media/amazon-business-tutorial/default-group1.png)

1. W kreatorze **Nowe konto użytkownika wybierz** **grupę domyślną,** a następnie wybierz **pozycję Domyślna rola zakupu** zgodnie z rolą użytkownika w organizacji i kliknij przycisk **Dalej**.

    ![Grupa domyślna](media/amazon-business-tutorial/dafault-group2.png)

1. W kreatorze **przekazywania pliku metadanych** kliknij przycisk **Przeglądaj,** aby przekazać plik **XML metadanych,** który został pobrany z witryny Azure portal, a następnie kliknij przycisk **Przekaż**.

    ![Dane połączenia](media/amazon-business-tutorial/connection-data1.png)

1. Po przesłaniu pobranego pliku metadanych pola w sekcji **Dane połączenia** zostaną automatycznie wypełnione. Następnie kliknij przycisk **Dalej**.

    ![Dane połączenia](media/amazon-business-tutorial/connection-data2.png)

1. W kreatorze **przekazywania instrukcji atrybutu** kliknij pozycję **Pomiń**.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute1.png)

1. W Kreatorze **mapowania atrybutów** dodaj pola wymagań, klikając opcję **+ Dodaj pole.** Dodaj wartości atrybutów, w tym obszar nazw, które zostały skopiowane z sekcji **Atrybuty & oświadczenia użytkownika** w witrynie Azure portal do pola **SAML AttributeName,** a następnie kliknij przycisk **Dalej**.

    ![Atrybuty](media/amazon-business-tutorial/map-attribute2.png)

1. W kreatorze **danych połączeń Amazon** kliknij przycisk **Dalej**.

    ![Połączenie](media/amazon-business-tutorial/amazon-connect.png)

1. Sprawdź **stan** ceł, które zostały skonfigurowane i kliknij przycisk **Rozpocznij testowanie**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection1.png)

1. W Kreatorze **testuj połączenie jedno i so** kliknij przycisk **Testuj**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection2.png)

1. W kreatorze **adresów URL inicjowanych przez usługę IDP** przed kliknięciem **przycisku Aktywuj,** skopiuj wartość przypisaną do **identyfikatora** i wklej do parametru **idpid** w **adresie URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    ![Połączenie](media/amazon-business-tutorial/sso-connection3.png)

1. Na **jesteś gotowy, aby przełączyć się na aktywny SSO?** kreatora, sprawdź mam **w pełni przetestowane SSO i jestem gotowy do pracy na żywo** pole wyboru i kliknij na Switch do **active**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection4.png)

1. Na koniec w sekcji **Szczegóły połączenia logowania przyłącza SSO** **stan** jest wyświetlany jako **Aktywny**.

    ![Połączenie](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

> [!NOTE]
> Administratorzy muszą utworzyć użytkowników testowych w ich dzierżawy w razie potrzeby. Poniższe kroki pokazują, jak utworzyć użytkownika testowego.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Tworzenie grupy zabezpieczeń usługi Azure AD w witrynie Azure portal

1. Kliknij **pozycję Azure Active Directory > wszystkie grupy**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Kliknij **pozycję Nowa grupa**:

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Wypełnij **typ grupy,** **Nazwa grupy,** **Opis grupy,** **Typ członkostwa**. Kliknij strzałkę, aby wybrać członków, a następnie wyszukaj lub kliknij członka, którego chcesz dodać do grupy. Kliknij **wybierz,** aby dodać wybranych członków, a następnie kliknij **przycisk Utwórz**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Amazon Business.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Amazon Business**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

    >[!NOTE]
    > Jeśli nie przypiszesz użytkowników w usłudze Azure AD, zostanie wyświetlony następujący błąd.

    ![Łącze Dodaj użytkownika](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Przypisywanie grupy zabezpieczeń usługi Azure AD w witrynie Azure portal

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **pozycję Amazon Business**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **pozycję Amazon Business**.

    ![Łącze Amazon Business na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Wyszukaj grupę zabezpieczeń, której chcesz użyć, a następnie kliknij grupę, aby dodać ją do sekcji Wybierz członków. Kliknij **pozycję Zaznacz**, a następnie kliknij pozycję **Przypisz**.

    ![Grupa zabezpieczeń wyszukiwania](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Sprawdź powiadomienia na pasku menu, aby otrzymywać powiadomienia, że grupa została pomyślnie przypisana do aplikacji Enterprise w witrynie Azure portal.

### <a name="create-amazon-business-test-user"></a>Tworzenie użytkownika testowego Amazon Business

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Amazon Business. Amazon Business obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Amazon Business, nowy jest tworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Business w Panelu dostępu należy automatycznie zalogować się do amazon business, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
