---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z iProva | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z iProva

W tym samouczku dowiesz się, jak zintegrować iProva z usługą Azure Active Directory (Azure AD). Po zintegrowaniu iProva z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do iProva.
* Włącz użytkownikom automatyczne logowanie do iProva za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego iProva.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja iProva obsługuje logowanie jednokrotne inicjowane przez **SP**

* Po skonfigurowaniu iProva można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Dodawanie aplikacji iProva z galerii

Aby skonfigurować integrację aplikacji iProva z usługą Azure AD, musisz dodać tę aplikację z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **iProva** w polu wyszukiwania.
1. Wybierz **iProva** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla iProva

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą iProva przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w iProva.

Aby skonfigurować i przetestować syg jako jeden na dział usługi Azure AD za pomocą iProva, wykonaj następujące bloki konstrukcyjne:

1. **[Pobieranie informacji o konfiguracji z aplikacji iProva](#retrieve-configuration-information-from-iprova)** — jako przygotowanie do następnych kroków.
1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego iProva](#create-iprova-test-user)** — aby mieć odpowiednik B.Simon w iProva, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Skonfiguruj iProva SSO](#configure-iprova-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="retrieve-configuration-information-from-iprova"></a>Pobieranie informacji o konfiguracji z aplikacji iProva

W tej sekcji pobierzesz informacje z aplikacji iProva w celu skonfigurowania logowania jednokrotnego usługi Azure AD.

1. Otwórz przeglądarkę internetową i przejdź do **strony z informacjami o formacie SAML2** w aplikacji iProva przy użyciu następującego wzorca adresu URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Wyświetlanie strony z informacjami o formacie SAML2 w aplikacji iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Pozostaw otwartą kartę przeglądarki, gdy wykonujesz następne kroki w innej karcie przeglądarki.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **iProva** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Wypełnij pole **Adres URL logowania** przy użyciu wartości wyświetlonej obok etykiety **Adres URL logowania** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    b. Wypełnij pole **Identyfikator** przy użyciu wartości wyświetlonej obok etykiety **EntityID** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    d. Wypełnij pole **Adres URL odpowiedzi** przy użyciu wartości wyświetlonej obok etykiety **Adres URL odpowiedzi** na stronie**informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

1. Aplikacja iProva oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej aplikacji iProva oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy| Przestrzeń nazw  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

## <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do iProva.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Z listy aplikacji wybierz pozycję **iProva**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="create-iprova-test-user"></a>Tworzenie użytkownika testowego aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **Users** (Użytkownicy) na panelu **Users and user groups** (Użytkownicy i grupy użytkowników).

5. Wybierz pozycję **Dodaj**.

6. W polu **Nazwa użytkownika** wprowadź nazwę `B.Simon@contoso.com`użytkownika, taką jak .

7. W polu **Imię i nazwisko** wprowadź pełną nazwę użytkownika, takiego jak **B.Simon**.

8. Wybierz opcję **Bez hasła (użyj logowania jednokrotnego)**.

9. W polu **Adres e-mail** wprowadź adres e-mail użytkownika, taki jak `B.Simon@contoso.com`.

10. Przewiń w dół do końca strony, a następnie wybierz pozycję **Finish** (Zakończ).

## <a name="configure-iprova-sso"></a>Konfigurowanie identyfikatora SSO iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **General** (Ogólne) na panelu **System settings** (Ustawienia systemowe).

5. Wybierz pozycję **Edit** (Edytuj).

6. Przewiń w dół do pozycji **Kontrola dostępu**.

    ![Ustawienia kontroli dostępu aplikacji iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Znajdź ustawienie **Użytkownicy są automatycznie logowani za pomocą kont sieci** i zmień je na **Tak, uwierzytelniaj za pomocą protokołu SAML**. Pojawią się dodatkowe opcje.

8. Wybierz pozycję **Set up** (Konfiguracja).

9. Wybierz **pozycję Dalej**.

10. Teraz w aplikacji iProva zostanie wyświetlone pytanie o to, czy pobrać dane federacji z adresu URL, czy przekazać je z pliku. Wybierz opcję **From URL** (Z adresu URL).

    ![Pobieranie metadanych usługi Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Wklej adres URL metadanych zapisany w ostatnim kroku w sekcji „Konfigurowanie logowania jednokrotnego usługi Azure AD”.

12. Wybierz przycisk strzałki, aby pobrać metadane z usługi Azure AD.

13. Po zakończeniu pobierania zostanie wyświetlony komunikat potwierdzający **Valid Federation Data file downloaded** (Pobrano prawidłowy plik danych federacji).

14. Wybierz **pozycję Dalej**.

15. Pomiń teraz opcję **Test login** (Testuj logowanie) i wybierz pozycję **Next** (Dalej).

16. Z listy rozwijanej **Claim to use** (Oświadczenie do użycia) wybierz pozycję **windowsaccountname**.

17. Wybierz **pozycję Zakończ**.

18. Teraz wróć do ekranu **Edytowanie ustawień ogólnych**. Przewiń w dół strony, a następnie wybierz przycisk **OK**, aby zapisać konfigurację.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka iProva w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji iProva, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj iProva z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić iProva dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)