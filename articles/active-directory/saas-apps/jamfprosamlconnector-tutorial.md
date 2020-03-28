---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z jamf pro | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77373205"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Samouczek: Integracja sytuowania sytuacji usługi Azure Active Directory z jamf pro

W tym samouczku dowiesz się, jak zintegrować Jamf Pro z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Jamf Pro z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do Jamf Pro.
* Automatyczne logowanie użytkowników do Jamf Pro za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja Jamf Pro, która jest włączona z logiem jednokrotnym.A Jamf Pro subscription that's single sign-on (SSO) enabled.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 

* Jamf Pro obsługuje identyfikatory SSO **inicjowane przez SP** i **inicjowane przez IdP.**
* Po skonfigurowaniu Jamf Pro można wymusić kontrolę sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Dodaj Jamf Pro z galerii

Aby skonfigurować integrację oprogramowania Jamf Pro z usługą Azure AD, musisz dodać oprogramowanie Jamf Pro z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub osobistego konta Microsoft.
1. W lewym okienku wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź *Jamf Pro* w polu wyszukiwania.
1. Wybierz **Jamf Pro** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurowanie i testowanie sytuowania sytua w usłudze Azure AD dla jamf pro

Konfigurowanie i testowanie usługi Azure AD SSO z Jamf Pro przy użyciu użytkownika testowego o nazwie B.Simon. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Jamf Pro.

W tej sekcji można skonfigurować i przetestować usługi Azure AD SSO z Jamf Pro.

1. [Skonfiguruj samouszeńców w usłudze Azure AD,](#configure-sso-in-azure-ad) aby użytkownicy mogli korzystać z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować sytów usługi Azure AD za pomocą konta B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby B.Simon mógł używać funkcji SSO w usłudze Azure AD.
1. [Skonfiguruj logowania sycowego w Jamf Pro,](#configure-sso-in-jamf-pro) aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Jamf Pro,](#create-a-jamf-pro-test-user) aby mieć odpowiednik B.Simon w Jamf Pro, który jest połączony z reprezentacją usługi Azure AD użytkownika.
1. [Przetestuj konfigurację samego systemu SSO,](#test-the-sso-configuration) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-sso-in-azure-ad"></a>Konfigurowanie usługi SSO w usłudze Azure AD

W tej sekcji włączysz usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Jamf Pro** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edytuj podstawową stronę konfiguracji SAML.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez IdP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź adres URL, który używa następującej formuły:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W polu tekstowym **Odpowiedz na adres URL** wprowadź adres URL, który używa następującej formuły:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL**. Jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez dodatek SP,** w polu tekstowym **Zaloguj adres URL** wprowadź adres URL, który używa następującej formuły:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Otrzymasz rzeczywistą wartość identyfikatora z sekcji **logowanie jednokrotne** w jamf pro portalu, który jest wyjaśniony w dalszej części samouczka. Rzeczywistą wartość poddomeny można wyodrębnić z wartości identyfikatora i użyć tych informacji o poddomenach jako adresu URL logowania i adresu URL odpowiedzi. Można również odwołać się do formuł wyświetlanych w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** przejdź do sekcji **Certyfikat podpisywania SAML,** wybierz przycisk **kopiowania,** aby skopiować **adres URL metadanych federacji aplikacji,** a następnie zapisz go na komputerze.

    ![Łącze do pobierania certyfikatu podpisywania SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. W lewym okienku w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.
   1. W polu **Nazwa użytkownika** wpisz [name]@[companydomain]. [rozszerzenie]. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udzielasz B.Simon dostępu do Jamf Pro.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Jamf Pro**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Wybierz przycisk Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika. Następnie wybierz przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-sso-in-jamf-pro"></a>Konfigurowanie syto w Jamf Pro

1. Aby zautomatyzować konfigurację w jamf Pro, zainstaluj **rozszerzenie przeglądarki My Apps Secure Sign-in,** wybierając pozycję Zainstaluj **rozszerzenie**.

    ![Strona rozszerzenia przeglądarki Bezpiecznych logowania aplikacji My Apps](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Skonfiguruj Jamf Pro**. Po otwarciu aplikacji Jamf Pro podaj poświadczenia administratora, aby się zalogować. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki od 3 do 7.

    ![Strona konfiguracji instalacji w Jamf Pro](common/setup-sso.png)

3. Aby ręcznie skonfigurować Jamf Pro, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Jamf Pro jako administrator. Następnie należy wykonać następujące kroki.

4. Wybierz **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Wybierz ikonę ustawień w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Wybierz **opcję Logowanie jednokrotne**.

    ![Wybierz logowanie jednokrotne w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stronie **logowania jednokrotnego** należy wykonać następujące czynności.

    ![Strona logowania jednokrotnego w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Wybierz pozycję **Edit** (Edytuj).

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie jednokrotne.**

  d. Wybierz **platformę Azure** jako opcję z menu rozwijanego **Dostawca tożsamości.**

  d. Skopiuj wartość **identyfikatora JEDNOSTKI** i wklej ją do pola **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

> [!NOTE]
> Użyj wartości w `<SUBDOMAIN>` tym polu, aby ukończyć adres URL logowania i adres URL odpowiedzi w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

  e. Wybierz **adres URL metadanych** z menu rozwijanego **Źródło metadanych dostawcy tożsamości.** W wyświetlonym polu wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z witryny Azure portal.

  f. (Opcjonalnie) Edytuj wartość wygaśnięcia tokenu lub wybierz opcję "Wyłącz wygaśnięcie tokenu SAML".

7. Na tej samej stronie przewiń w dół do sekcji **Mapowanie użytkowników.** Następnie należy wykonać następujące kroki.

    ![Sekcja Mapowanie użytkowników strony logowania jednokrotnego w Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Wybierz opcję **NameID** dla **mapowania użytkowników dostawcy tożsamości**. Domyślnie ta opcja jest ustawiona na **NameID**, ale można zdefiniować atrybut niestandardowy.

    b. Wybierz **pozycję Poczta e-mail** dla **mapowania użytkowników Jamf Pro**. Jamf Pro mapuje atrybuty SAML wysyłane przez IdP najpierw przez użytkowników, a następnie przez grupy. Gdy użytkownik próbuje uzyskać dostęp do Jamf Pro, Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i dopasowuje go do wszystkich kont użytkowników Jamf Pro. Jeśli przychodzące konto użytkownika nie zostanie znalezione, jamf pro spróbuje dopasować je według nazwy grupy.

    d. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w polu **NAZWA ATRYBUTU** GRUPY TOŻSAMOŚCI.

    d. Na tej samej stronie przewiń w dół do sekcji **Zabezpieczenia** i wybierz pozycję **Zezwalaj użytkownikom na pomijanie uwierzytelniania logowania jednokrotnego**. W rezultacie użytkownicy nie zostaną przekierowani do strony logowania dostawcy tożsamości w celu uwierzytelnienia i mogą zalogować się bezpośrednio do Jamf Pro. Gdy użytkownik spróbuje uzyskać dostęp do oprogramowania Jamf Pro za pośrednictwem dostawcy tożsamości, nastąpi uwierzytelnianie i autoryzacja za pomocą logowania jednokrotnego zainicjowanego przez dostawcę tożsamości.

    e. Wybierz **pozycję Zapisz**.

### <a name="create-a-jamf-pro-test-user"></a>Tworzenie użytkownika testowego Jamf Pro

Aby użytkownicy usługi Azure AD zalogowali się do Jamf Pro, muszą być aprowizowani w Jamf Pro. Inicjowanie obsługi administracyjnej w Jamf Pro jest zadaniem ręcznym.

Aby aprowizować konto użytkownika, należy wykonać następujące czynności:

1. Zaloguj się do witryny firmy Jamf Pro jako administrator.

2. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona ustawień w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Wybierz **jamf Pro konta użytkowników & grup**.

    ![Ikona Jamf Pro User Accounts & Groups w ustawieniach Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Wybierz **pozycję Nowy**.

    ![Jamf Pro Konta użytkowników & grupy ustawienia systemu strona](./media/jamfprosamlconnector-tutorial/user2.png)

5. Wybierz pozycję **Utwórz standardowe konto**.

    ![Opcja Utwórz konto standardowe na stronie Jamf Pro User Accounts & Groups](./media/jamfprosamlconnector-tutorial/user3.png)

6. W oknie dialogowym **Nowe konto** wykonaj następujące czynności:

    ![Nowe opcje konfiguracji konta w ustawieniach systemu Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. W polu **NAZWA** `Britta Simon`UŻYTKOWNIKA wprowadź pełną nazwę użytkownika testowego.

    b. Wybierz opcje **dla poziomu dostępu,** **zestawu uprawnień**i **statusu dostępu,** które są zgodne z organizacją.

    d. W polu IMIĘ i `Britta Simon` **NAZWISKO** wprowadź .

    d. W polu **ADRES E-MAIL** wprowadź adres e-mail konta Britta Simon.

    e. W polu **HASŁO** wprowadź hasło użytkownika.

    f. W polu **SPRAWDŹ HASŁO** wprowadź hasło użytkownika ponownie.

    g. Wybierz **pozycję Zapisz**.

## <a name="test-the-sso-configuration"></a>Testowanie konfiguracji sytego systemu sytłego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Jamf Pro w Panelu dostępu należy automatycznie zalogować się na konto Jamf Pro, dla którego skonfigurowano logowanie logowane. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Wypróbuj Jamf Pro z usługą Azure AD](https://aad.portal.azure.com/)