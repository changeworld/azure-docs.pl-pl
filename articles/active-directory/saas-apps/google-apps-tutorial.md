---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z łącznikiem Google Cloud (G Suite) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a łącznikem Google Cloud (G Suite).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3282dd88b62a6811031e95672638d67702215a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z łącznikiem Google Cloud (G Suite)

W tym samouczku dowiesz się, jak zintegrować łącznik Google Cloud (G Suite) z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Google Cloud (G Suite) Connector z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do łącznika Google Cloud (G Suite).
* Włącz automatyczne logowanie użytkowników do łącznika Google Cloud (G Suite) za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

- Subskrypcja usługi Azure AD.
- Subskrypcja z funkcją rejestracji jednokrotnej (SSO) złączem Google Cloud (G Suite).
- Subskrypcja usługi Google Apps lub usługi Google Cloud Platform

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku. Ten dokument został utworzony przy użyciu nowego środowiska użytkownika służącego do logowania jednokrotnego. Jeśli nadal używasz starego środowiska, konfiguracja będzie przebiegać inaczej. Nowe środowisko możesz włączyć w ustawieniach logowania jednokrotnego aplikacji G Suite. Przejdź do **usługi Azure AD, aplikacje dla przedsiębiorstw**, wybierz Google Cloud **(G Suite) Connector**, wybierz **logowanie jednokrotne,** a następnie kliknij **Wypróbuj nasze nowe środowisko.**

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. **Pyt.: Czy ta integracja obsługuje integrację sytuacji sytuacji google cloud platform z usługą Azure AD?**

    Odp. Tak. Usługi Google Cloud Platform i Google Apps współdzielą tę samą platformę uwierzytelniania. W związku z tym w celu przeprowadzenia integracji usługi GCP należy skonfigurować logowanie jednokrotne w ramach usługi Google Apps.

2. **P: Czy Chromebooki i inne urządzenia z Chrome są zgodne z logiem jednokrotnym usługi Azure AD?**
  
    O: Tak, użytkownicy mogą logować się na swoich urządzeniach Chromebooka przy użyciu poświadczeń usługi Azure AD. Zobacz ten [artykuł pomocy technicznej Google Cloud (G Suite) Connector,](https://support.google.com/chrome/a/answer/6060880) aby dostrzec informacje o tym, dlaczego użytkownicy mogą otrzymywać dwa razy monit o podanie poświadczeń.

3. **P: Czy jeśli włączę logowanie jednokrotne, użytkownicy będą mogli używać swoich poświadczeń usługi Azure AD do logowania się do dowolnej usługi Google, takiej jak Google Classroom, GMail, Dysk Google, YouTube itd.?**

    O: Tak, w zależności od tego, [który łącznik Google Cloud (G Suite)](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) wybierzesz, aby włączyć lub wyłączyć dla swojej organizacji.

4. **P: Czy mogę włączyć logowanie jednokrotne tylko dla podzbioru użytkowników łącznika Google Cloud (G Suite)?**

    Odp.: Nie, natychmiastowe włączenie logowania jednokrotnego wymaga od wszystkich użytkowników łącznika Google Cloud (G Suite) uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Ponieważ łącznik Google Cloud (G Suite) nie obsługuje wielu dostawców tożsamości, dostawcą tożsamości dla twojego środowiska łącznika Google Cloud (G Suite) może być Azure AD lub Google — ale nie jednocześnie.

5. **P: Jeśli użytkownik jest zalogowany za pośrednictwem systemu Windows, czy jest on automatycznie uwierzytelniany w złączu Google Cloud (G Suite) bez monitu o podanie hasła?**

    Odp.: Istnieją dwie opcje włączania tego scenariusza. Pierwsza opcja: użytkownicy mogą logować się do urządzeń z systemem Windows 10 za pośrednictwem [dołączania do usługi Azure Active Directory](../device-management-introduction.md). Druga opcja: użytkownicy mogą logować się do urządzeń z systemem Windows przyłączonych do domeny lokalnej usługi Active Directory, dla której włączono obsługę logowania jednokrotnego do usługi Azure AD za pośrednictwem wdrożenia usług [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). Obie opcje wymagają wykonania czynności w poniższym samouczku, aby włączyć logowanie jednokrotne między usługą Azure AD a łącznikiem Google Cloud (G Suite).

6. **Pyt.: Co należy zrobić, gdy pojawia się komunikat o błędzie "nieprawidłowy adres e-mail"?**

    Odp.: W przypadku tej konfiguracji atrybut e-mail jest wymagany, aby użytkownicy mogli się zalogować. Tego atrybutu nie można ustawić ręcznie.

    Atrybut adresu e-mail jest wypełniany automatycznie dla każdego użytkownika z ważną licencją programu Exchange. Jeśli dla użytkownika nie włączono poczty e-mail, ten błąd będzie występował, ponieważ aplikacja musi pobrać ten atrybut w celu udzielenia dostępu.

    Możesz przejść do witryny portal.office.com, korzystając z konta administratora, w obszarze centrum administracyjnego kliknąć pozycję Rozliczenia, kliknąć pozycję Subskrypcje, wybrać subskrypcję usługi Office 365, kliknąć pozycję Przypisz do użytkowników, wybrać użytkowników, dla których chcesz sprawdzić subskrypcje, a następnie w okienku po prawej stronie kliknąć pozycję Edytuj licencje.

    Po przypisaniu licencji usługi O365 może upłynąć kilka minut, zanim odniesie to skutek. Po tym czasie atrybut user.mail będzie automatycznie wypełniany, co powinno spowodować rozwiązanie problemu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Łącznik Google Cloud (G Suite) obsługuje funkcję SSO inicjowane przez **sp**

* Łącznik Google Cloud (G Suite) obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Po skonfigurowaniu łącznika Google Cloud (G Suite) możesz wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Dodawanie łącznika Google Cloud (G Suite) z galerii

Aby skonfigurować integrację łącznika Google Cloud (G Suite) z usługą Azure AD, musisz dodać łącznik Google Cloud (G Suite) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **łącznik Google Cloud (G Suite)** w polu wyszukiwania.
1. Wybierz **łącznik Google Cloud (G Suite)** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Konfigurowanie i testowanie łącznika logowania jednokrotnego usługi Azure AD dla łącznika Google Cloud (G Suite)

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą łącznika Google Cloud (G Suite) przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik pierwszego przysyłał działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Łącznik Google Cloud (G Suite).

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą łącznika Google Cloud (G Suite), wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie logowania do złącza Google Cloud (G Suite)](#configure-google-cloud-g-suite-connector-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Łącznika Google Cloud (G Suite)](#create-google-cloud-g-suite-connector-test-user)** — aby mieć odpowiednik łącznika B.Simon w google cloud (G Suite), który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Łącznika Google Cloud (G Suite)** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz utworzyć konfigurację dla usługi **Gmail**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:  

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Jeśli chcesz utworzyć konfigurację dla usług **Google Cloud Platform**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:  
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Łącznik Google Cloud (G Suite) nie udostępnia identyfikatora/identyfikatora jednostki w konfiguracji logowania jednokrotnego, więc po `google.com`odznaczeniu opcji **wystawcy specyficznej dla domeny** będzie to wartość identyfikatora . Jeśli sprawdzisz opcję **wystawcy specyficznego dla domeny,** będzie `google.com/a/<yourdomainname.com>`to . Aby zaznaczyć/odznaczyć opcję **wystawcy specyficznego dla domeny,** należy przejść do sekcji **Konfigurowanie łącznika łącznika Google Cloud (G Suite),** która została wyjaśniona w dalszej części samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej klienta złącza Google Cloud (G Suite).](https://www.google.com/contact/)

1. Aplikacja Łącznik Google Cloud (G Suite) oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Domyślną wartością **unikatowego identyfikatora użytkownika** jest **nazwa użytkownika.userprincipalname,** ale łącznik Google Cloud (G Suite) oczekuje, że zostanie ono zamapowane przy połącze adresu e-mail użytkownika. Do tego celu można użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją organizacji.

    ![image](common/default-attributes.png)


1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Łącznik Konfigurowanie Google Cloud (G Suite)** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz funkcję B.Simon, aby używać logowania jednokrotnego platformy Azure, przyznając dostęp do łącznika Google Cloud (G Suite).

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Łącznik Google Cloud (G Suite).**
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Konfigurowanie usługi SSO łącznika Google Cloud (G Suite)

1. Otwórz nową kartę w przeglądarce i zaloguj się do [konsoli administracyjnej łącznika Google Cloud (G Suite)](https://admin.google.com/) przy użyciu konta administratora.

2. Kliknij pozycję **Security** (Zabezpieczenia). Jeśli nie widzisz linku, może on znajdować się w menu **More Controls** (Więcej kontrolek) u dołu ekranu.

    ![Kliknij pozycję Security (Zabezpieczenia).][10]

3. Na stronie **Security** (Zabezpieczenia) kliknij pozycję **Set up single sign-on (SSO)** (Konfigurowanie logowania jednokrotnego).

    ![Kliknij pozycję SSO (Logowanie jednokrotne).][11]

4. Wprowadź następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Zaznacz pole wyboru **Setup SSO with third-party identity provider** (Skonfiguruj logowanie jednokrotne za pomocą dostawcy tożsamości innej firmy).

    b. W polu **Adres URL strony logowania w** usłudze Google Cloud (G Suite) Connector wklej wartość adresu URL **logowania** skopiowanego z witryny Azure Portal.

    d. W polu **Adres URL strony wylogowywania** w usłudze Google Cloud (G Suite) Connector wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure Portal.

    d. W polu **Zmień adres URL hasła** w złączu Google Cloud (G Suite) wklej wartość adresu URL zmień **hasło** skopiowane z witryny Azure Portal.

    e. W usłudze Łącznik Google Cloud (G Suite) w przypadku **certyfikatu weryfikacji**przekaż certyfikat pobrany z witryny Azure portal.

    f. Zaznacz/Odznacz opcję Użyj opcji **wystawcy specyficznej dla domeny** zgodnie z notatką wymienioną w powyższej **sekcji Podstawowa konfiguracja SAML** w usłudze Azure AD.

    g. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Tworzenie użytkownika testowego łącznika Google Cloud (G Suite)

Celem tej sekcji jest [utworzenie użytkownika w łączniku Google Cloud (G Suite)](https://support.google.com/a/answer/33310?hl=en) o nazwie B.Simon. Po ręcznym utworzeniu użytkownika w łączniku Google Cloud (G Suite) użytkownik będzie mógł zalogować się przy użyciu swoich danych logowania do usługi Office 365.

Łącznik Google Cloud (G Suite) obsługuje również automatyczne inicjowanie obsługi administracyjnej użytkowników. Aby skonfigurować automatyczne inicjowanie obsługi administracyjnej użytkowników, należy najpierw [skonfigurować łącznik Google Cloud (G Suite) do automatycznego inicjowania obsługi administracyjnej przez użytkowników.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

> [!NOTE]
> Upewnij się, że użytkownik już istnieje w usłudze Łącznik Google Cloud (G Suite), jeśli inicjowanie obsługi administracyjnej w usłudze Azure AD nie zostało włączone przed testowaniem logowania jednokrotnego.

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Łącznika Google Cloud (G Suite) w Panelu dostępu powinieneś automatycznie zalogować się do łącznika Google Cloud (G Suite), dla którego skonfigurowano logującą się do systemu. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Wypróbuj łącznik Google Cloud (G Suite) z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić złącze Google Cloud (G Suite) dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png