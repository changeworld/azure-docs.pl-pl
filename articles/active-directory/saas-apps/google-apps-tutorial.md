---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą G Suite | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą G Suite.
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
ms.openlocfilehash: d5ef5816759074073c57ef0f616ddea4a159956f
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370352"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą G Suite

W tym samouczku dowiesz się, jak zintegrować pakiet G Suite z usługą Azure Active Directory (Azure AD). Po zintegrowaniu pakietu G Suite z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi G Suite.
* Zezwól użytkownikom na automatyczne logowanie do usługi G Suite przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi G Suite.
- Subskrypcja usługi Google Apps lub usługi Google Cloud Platform

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku. Ten dokument został utworzony przy użyciu nowego środowiska użytkownika służącego do logowania jednokrotnego. Jeśli nadal używasz starego środowiska, konfiguracja będzie przebiegać inaczej. Nowe środowisko możesz włączyć w ustawieniach logowania jednokrotnego aplikacji G Suite. Przejdź do obszaru **Usługa Azure AD, Aplikacje dla przedsiębiorstw**, wybierz pozycję **G Suite** i **Logowanie jednokrotne**, a następnie kliknij pozycję **Wypróbuj nowe środowisko**.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. **P: czy ta integracja obsługuje Google Cloud Platform z integracją SSO z usługą Azure AD?**

    Odp. Tak. Usługi Google Cloud Platform i Google Apps współdzielą tę samą platformę uwierzytelniania. W związku z tym w celu przeprowadzenia integracji usługi GCP należy skonfigurować logowanie jednokrotne w ramach usługi Google Apps.

2. **P: czy Chromebooks i inne urządzenia Chrome są zgodne z logowaniem jednokrotnym w usłudze Azure AD?**
  
    Odp.: tak, użytkownicy mogą logować się do swoich urządzeń Chromebook przy użyciu poświadczeń usługi Azure AD. Zapoznaj się z tym [artykułem pomocy technicznej usługi G Suite](https://support.google.com/chrome/a/answer/6060880), aby dowiedzieć się, dlaczego użytkownikom może być dwukrotnie wyświetlany monit o wprowadzenie poświadczeń.

3. **P: Jeśli włączam Logowanie jednokrotne, użytkownicy będą mogli korzystać z poświadczeń usługi Azure AD w celu zalogowania się do dowolnego produktu Google, takiego jak Google Classroom, GMail, dysk Google, YouTube itd.?**

    Odp.: tak, w zależności od tego, [który zestaw G](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) wybierzesz do włączenia lub wyłączenia dla organizacji.

4. **P: Czy można włączyć logowanie jednokrotne tylko dla podzbioru użytkowników usługi G Suite?**

    Odp.: nie, włączenie logowania jednokrotnego bezpośrednio wymaga od wszystkich użytkowników pakietu G Suite uwierzytelniania przy użyciu poświadczeń usługi Azure AD. Ponieważ usługa G Suite nie obsługuje wielu dostawców tożsamości, dostawcą tożsamości w środowisku usługi G Suite może być albo usługa Azure AD, albo usługa Google — ale nie obie jednocześnie.

5. **P: Jeśli użytkownik jest zalogowany za pomocą systemu Windows, czy jest automatycznie uwierzytelniany do usługi G Suite bez monitowania o hasło?**

    Odp.: dostępne są dwie opcje umożliwiające włączenie tego scenariusza. Pierwsza opcja: użytkownicy mogą logować się do urządzeń z systemem Windows 10 za pośrednictwem [dołączania do usługi Azure Active Directory](../device-management-introduction.md). Druga opcja: użytkownicy mogą logować się do urządzeń z systemem Windows przyłączonych do domeny lokalnej usługi Active Directory, dla której włączono obsługę logowania jednokrotnego do usługi Azure AD za pośrednictwem wdrożenia usług [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). W przypadku obu tych opcji wymagane jest wykonanie czynności opisanych w poniższym samouczku w celu włączenia logowania jednokrotnego między usługami Azure AD i G Suite.

6. **P: co należy zrobić, gdy otrzymuję komunikat o błędzie "nieprawidłowy adres e-mail"?**

    Odp.: w przypadku tej konfiguracji atrybut poczty e-mail jest wymagany, aby użytkownicy mogli się zalogować. Tego atrybutu nie można ustawić ręcznie.

    Atrybut adresu e-mail jest wypełniany automatycznie dla każdego użytkownika z ważną licencją programu Exchange. Jeśli dla użytkownika nie włączono poczty e-mail, ten błąd będzie występował, ponieważ aplikacja musi pobrać ten atrybut w celu udzielenia dostępu.

    Możesz przejść do witryny portal.office.com, korzystając z konta administratora, w obszarze centrum administracyjnego kliknąć pozycję Rozliczenia, kliknąć pozycję Subskrypcje, wybrać subskrypcję usługi Office 365, kliknąć pozycję Przypisz do użytkowników, wybrać użytkowników, dla których chcesz sprawdzić subskrypcje, a następnie w okienku po prawej stronie kliknąć pozycję Edytuj licencje.

    Po przypisaniu licencji usługi O365 może upłynąć kilka minut, zanim odniesie to skutek. Po tym czasie atrybut user.mail będzie automatycznie wypełniany, co powinno spowodować rozwiązanie problemu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa G Suite obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

* Pakiet G Suite obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
* Po skonfigurowaniu programu G Suite można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-g-suite-from-the-gallery"></a>Dodawanie usługi G Suite z galerii

Aby skonfigurować integrację usługi G Suite z usługą Azure AD, należy dodać z galerii usługę G Suite do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **G Suite** w polu wyszukiwania.
1. Wybierz pozycję **G Suite** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pakietu G Suite

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pakietu G Suite przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze G Suite.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu pakietu G Suite, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne w usłudze G Suite](#configure-g-suite-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi G Suite](#create-g-suite-test-user)** , aby dysponować odpowiednikiem B. Simon w usłudze g Suite, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **G Suite** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz utworzyć konfigurację dla usługi **Gmail**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |


1. Jeśli chcesz utworzyć konfigurację dla usług **Google Cloud Platform**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    | |
    |--|
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Pakiet G Suite nie udostępnia wartości identyfikatora/identyfikatora jednostki na potrzeby konfiguracji logowania jednokrotnego, więc po odpisaniu opcji **wystawcy specyficznego dla domeny** wartość identyfikatora zostanie `google.com`. W przypadku zaznaczenia opcji **wystawcy specyficznego dla domeny** zostanie `google.com/a/<yourdomainname.com>`. Aby zaznaczyć/wyczyścić opcję **wystawcy specyficzną dla domeny** , należy przejść do sekcji Konfigurowanie w usłudze **G Suite rejestracji jednokrotnej** , która została omówiona w dalszej części tego samouczka. Aby uzyskać więcej informacji, skontaktuj się z [zespołem pomocy technicznej usługi G Suite](https://www.google.com/contact/).

1. Aplikacja G Suite oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna atrybutu **Unikatowy identyfikator użytkownika** to **user.userprincipalname**, ale usługa G Suite oczekuje, że zostanie ona zamapowana na adres e-mail użytkownika. Do tego celu można użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją organizacji.

    ![image](common/default-attributes.png)


1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj pakiet G Suite** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do usługi G Suite.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **G Suite**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-g-suite-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze G Suite

1. Otwórz nową kartę w przeglądarce, a następnie zaloguj się do [konsoli administracyjnej usługi G Suite](https://admin.google.com/) przy użyciu konta administratora.

2. Kliknij pozycję **Zabezpieczenia**. Jeśli nie widzisz linku, może on znajdować się w menu **More Controls** (Więcej kontrolek) u dołu ekranu.

    ![Kliknij pozycję Security (Zabezpieczenia).][10]

3. Na stronie **Security** (Zabezpieczenia) kliknij pozycję **Set up single sign-on (SSO)** (Konfigurowanie logowania jednokrotnego).

    ![Kliknij pozycję SSO (Logowanie jednokrotne).][11]

4. Wprowadź następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Zaznacz pole wyboru **Setup SSO with third-party identity provider** (Skonfiguruj logowanie jednokrotne za pomocą dostawcy tożsamości innej firmy).

    b. W polu **adres URL strony logowania** w usłudze G Suite wklej wartość **adresu URL logowania** skopiowanego z Azure Portal.

    c. W polu **adres URL strony Wyloguj** w usłudze G Suite wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    d. W polu **Zmień hasło URL** w usłudze G Suite wklej wartość **adresu URL zmiany hasła** skopiowanego z Azure Portal.

    e. W usłudze G Suite w polu **Verification certificate** (Certyfikat weryfikacji) przekaż certyfikat pobrany z witryny Azure Portal.

    f. Zaznacz/Usuń zaznaczenie opcji **Użyj określonego wystawcy dla domeny** zgodnie z uwagą wymienionym w powyższej sekcji podstawowe informacje o **konfiguracji języka SAML** w usłudze Azure AD.

    g. Kliknij przycisk **Save Changes** (Zapisz zmiany).

### <a name="create-g-suite-test-user"></a>Tworzenie użytkownika testowego usługi G Suite

Celem tej sekcji jest [utworzenie użytkownika w usłudze G Suite](https://support.google.com/a/answer/33310?hl=en) o nazwie B. Simon. Po ręcznym utworzeniu użytkownika w usłudze G Suite użytkownik będzie mógł zalogować się przy użyciu poświadczeń logowania do pakietu Office 365.

Pakiet G Suite obsługuje również automatyczne Inicjowanie obsługi użytkowników. Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników, należy najpierw [skonfigurować pakiet G Suite do automatycznej aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Przed testowaniem logowania jednokrotnego upewnij się, że użytkownik już istnieje w usłudze G Suite, jeśli aprowizacja w usłudze Azure AD nie została włączona.

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka G Suite na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi G Suite, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

- [Wypróbuj pakiet G Suite w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić pakiet G Suite z zaawansowaną widocznością i kontrolkami](https://docs.microsoft.com/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png