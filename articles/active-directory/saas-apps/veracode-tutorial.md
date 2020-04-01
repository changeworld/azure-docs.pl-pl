---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z programem Veracode | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73043556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z programem Veracode

W tym samouczku dowiesz się, jak zintegrować veracode z usługą Azure Active Directory (Azure AD). Po zintegrowaniu veracode z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Veracode.
* Włącz użytkownikom automatyczne logowanie do veracode za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja obsługujący jednokrotne logowanie veracode.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Veracode obsługuje dostawcy tożsamości zainicjowane SSO i just-in-time użytkownika inicjowania obsługi administracyjnej.

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii

Aby skonfigurować integrację veracode z usługą Azure AD, dodaj Veracode z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz "Veracode" w polu wyszukiwania.
1. Wybierz **pozycję Veracode** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla veracode

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą veracode przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuowanych działał, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w veracode.

Aby skonfigurować i przetestować usługę Azure AD SSO przy za pomocą veracode, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj logowanie logowania veracode,](#configure-veracode-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Veracode,](#create-veracode-test-user)** aby mieć odpowiednik B.Simon w Veracode połączone z reprezentacji usługi Azure AD użytkownika.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie integracji aplikacji **Veracode** znajdź sekcję **Zarządzaj.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Wybierz **pozycję Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** w sekcji Saml Signing Certificate (Certyfikat **podpisywania SAML)** znajdź **pozycję Certyfikat (Base64).** Wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnionym łączem Pobierania](common/certificatebase64.png)

1. Veracode oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający sekcję Oświadczenia & atrybutów użytkowników](common/default-attributes.png)

1. Veracode oczekuje również kilka atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | firstname |Nazwa użytkownika.givenname |
    | lastname |User.nazwisko |
    | email |User.mail |

1. W sekcji **Konfigurowanie veracode** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie veracode z wyróżnionymi adresami URL konfiguracji](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Konfigurowanie przyswajaszu dostępu do kodu automatycznego veracode

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Veracode jako administrator.

1. Z menu u góry wybierz pozycję **Ustawienia** > **Administrator**.
   
    ![Zrzut ekranu przedstawiający administrację veracode z wyróżnioną ikoną Ustawienia i wyróżnioną ikoną Admin](./media/veracode-tutorial/ic802911.png "Administracja")

1. Wybierz kartę **SAML.**

1. W sekcji **Ustawienia SAML organizacji** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający sekcję Ustawienia SAML organizacji](./media/veracode-tutorial/ic802912.png "Administracja")

    a.  W przypadku **wystawcy**wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure portal.

    b. W przypadku **certyfikatu podpisywania oświadczeń**wybierz **pozycję Wybierz plik,** aby przekazać pobrany certyfikat z witryny Azure portal.

    d. W obszarze **Rejestracja własna**wybierz pozycję **Włącz rejestrację samoojezdą**.

1. W sekcji **Ustawienia samodzielnej rejestracji** wykonaj następujące czynności, a następnie wybierz pozycję **Zapisz:**

    ![Zrzut ekranu przedstawiający sekcję Ustawienia samodzielnej rejestracji z wyróżnionymi różnymi opcjami](./media/veracode-tutorial/ic802913.png "Administracja")

    a. W przypadku **aktywacji nowego użytkownika**wybierz **opcję Nie wymaga aktywacji**.

    b. W przypadku **aktualizacji danych użytkownika**wybierz pozycję **Preferencje Veracode Dane użytkownika**.

    d. W przypadku **szczegółów atrybutu SAML**wybierz następujące elementy:
      * **Role użytkowników**
      * **Administrator zasad**
      * **Recenzent**
      * **Potencjalny klient zabezpieczeń**
      * **Wykonawczy**
      * **Nadesłał**
      * **Kreator**
      * **Wszystkie typy skanowania**
      * **Członkostwo w zespole**
      * **Domyślny zespół**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** >**Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:

   1. W **yjmij nazwę**, wprowadź `B.Simon`.  
   1. W przypadku **nazwy** username@companydomain.extensionużytkownika wprowadź plik . Na przykład `B.Simon@contoso.com`.
   1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wyświetlaną wartość.
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włącz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Veracode.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Veracode**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający stronę Użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** w oknie **użytkownicy**wybierz pozycję **B.Simon**. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz pozycję **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-veracode-test-user"></a>Tworzenie użytkownika testowego Veracode

Aby zalogować się do Veracode, użytkownicy usługi Azure AD muszą być aprowizowani do Veracode. To zadanie jest zautomatyzowane i nie trzeba nic robić ręcznie. Użytkownicy są automatycznie tworzone w razie potrzeby podczas pierwszej próby logowania jednokrotnego.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia konta użytkownika Veracode lub interfejsów API udostępnianych przez veracode.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po **wybraniu opcji Veracode** w Panelu dostępu należy automatycznie zalogować się do veracode, dla którego skonfigurowano logowanie logowanie. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj veracode z usługą Azure AD](https://aad.portal.azure.com/)