---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą G Suite | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d48977c60fb4a13e1fc0dbd294fa4e7708f1cd5d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878971"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Samouczek: Integracja usługi Azure Active Directory z usługą G Suite

Z tego samouczka dowiesz się, jak zintegrować usługę G Suite z usługą Azure Active Directory (Azure AD).
Integracja usługi G Suite z usługą Azure AD zapewnia następujące korzyści:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do usługi G Suite.
* Umożliwianie użytkownikom automatycznego logowania się do usługi G Suite (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą G Suite, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja usługi G Suite z obsługą logowania jednokrotnego
- Subskrypcja usługi Google Apps lub usługi Google Cloud Platform

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego. Ten dokument został utworzony przy użyciu nowego środowiska użytkownika służącego do logowania jednokrotnego. Jeśli nadal używasz starego środowiska, konfiguracja będzie przebiegać inaczej. Nowe środowisko możesz włączyć w ustawieniach logowania jednokrotnego aplikacji G Suite. Przejdź do obszaru **Usługa Azure AD, Aplikacje dla przedsiębiorstw**, wybierz pozycję **G Suite** i **Logowanie jednokrotne**, a następnie kliknij pozycję **Wypróbuj nowe środowisko**.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. **Pyt.: Integracja ta obsługuje usługa Google Cloud Platform Usługa rejestracji Jednokrotnej integracji z usługą Azure AD?**

    Odp.: Tak. Usługi Google Cloud Platform i Google Apps współdzielą tę samą platformę uwierzytelniania. W związku z tym w celu przeprowadzenia integracji usługi GCP należy skonfigurować logowanie jednokrotne w ramach usługi Google Apps.

2. **Pyt.: Są zgodne z usługi Azure AD logowania jednokrotnego Chromebooks i innych urządzeń dla programu Chrome?**
  
    Odp.: Tak, użytkownicy mogą logować się do swoich urządzeń Chromebook przy użyciu poświadczeń usługi Azure AD. Zapoznaj się z tym [artykułem pomocy technicznej usługi G Suite](https://support.google.com/chrome/a/answer/6060880), aby dowiedzieć się, dlaczego użytkownikom może być dwukrotnie wyświetlany monit o wprowadzenie poświadczeń.

3. **Pyt.: Jeśli włączyć logowanie jednokrotne, użytkownicy będą mogli korzystać z poświadczeń usługi Azure AD do logowania się do dowolnego produktu Google, takich jak Google Classroom, GMail, dysk Google, YouTube i tak dalej?**

    Odp.: Tak, w zależności od tego, [która usługa G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) została włączona lub wyłączona dla Twojej organizacji.

4. **Pyt.: Można włączyć logowanie jednokrotne dla tylko podzbiór Moi użytkownicy usługi G Suite?**

    Odp.: Nie, włączenie logowania jednokrotnego powoduje, że od wszystkich użytkowników usługi G Suite od razu wymagane jest uwierzytelnianie się przy użyciu poświadczeń usługi Azure AD. Ponieważ usługa G Suite nie obsługuje wielu dostawców tożsamości, dostawcą tożsamości w środowisku usługi G Suite może być albo usługa Azure AD, albo usługa Google — ale nie obie jednocześnie.

5. **Pyt.: Jeśli użytkownik jest zalogowany przy użyciu Windows, czy automatycznie uwierzytelniają do usługi G Suite bez pobierania zostanie wyświetlony monit o podanie hasła?**

    Odp.: Dostępne są dwie opcje włączenia tego scenariusza. Pierwsza opcja: użytkownicy mogą logować się do urządzeń z systemem Windows 10 za pośrednictwem [dołączania do usługi Azure Active Directory](../device-management-introduction.md). Druga opcja: użytkownicy mogą logować się do urządzeń z systemem Windows przyłączonych do domeny lokalnej usługi Active Directory, dla której włączono obsługę logowania jednokrotnego do usługi Azure AD za pośrednictwem wdrożenia usług [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md). W przypadku obu tych opcji wymagane jest wykonanie czynności opisanych w poniższym samouczku w celu włączenia logowania jednokrotnego między usługami Azure AD i G Suite.

6. **Pyt.: Co należy zrobić, gdy pojawia się komunikat o błędzie "nieprawidłowy adres e-mail"?**

    Odp.: W przypadku tej konfiguracji atrybut adresu e-mail jest wymagany, aby możliwe było zalogowanie się użytkowników. Tego atrybutu nie można ustawić ręcznie.

    Atrybut adresu e-mail jest wypełniany automatycznie dla każdego użytkownika z ważną licencją programu Exchange. Jeśli dla użytkownika nie włączono poczty e-mail, ten błąd będzie występował, ponieważ aplikacja musi pobrać ten atrybut w celu udzielenia dostępu.

    Możesz przejść do witryny portal.office.com, korzystając z konta administratora, w obszarze centrum administracyjnego kliknąć pozycję Rozliczenia, kliknąć pozycję Subskrypcje, wybrać subskrypcję usługi Office 365, kliknąć pozycję Przypisz do użytkowników, wybrać użytkowników, dla których chcesz sprawdzić subskrypcje, a następnie w okienku po prawej stronie kliknąć pozycję Edytuj licencje.

    Po przypisaniu licencji usługi O365 może upłynąć kilka minut, zanim odniesie to skutek. Po tym czasie atrybut user.mail będzie automatycznie wypełniany, co powinno spowodować rozwiązanie problemu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa G Suite obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Usługa G Suite obsługuje **[automatyczną aprowizację użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Dodawanie usługi G Suite z galerii

Aby skonfigurować integrację usługi G Suite z usługą Azure AD, należy dodać z galerii usługę G Suite do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi G Suite z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **G Suite**, wybierz pozycję **G Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa G Suite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w usłudze G Suite, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi G Suite.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze G Suite, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze G Suite](#configure-g-suite-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi G Suite](#create-g-suite-test-user)** — aby mieć w usłudze G Suite odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze G Suite, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **G Suite** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz utworzyć konfigurację dla usługi **Gmail**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego w usłudze G Suite](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi G Suite](https://www.google.com/contact/).

5. Jeśli chcesz utworzyć konfigurację dla usług **Google Cloud Platform**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego w usłudze G Suite](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi G Suite](https://www.google.com/contact/).

6. Aplikacja G Suite oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna atrybutu **Unikatowy identyfikator użytkownika** to **user.userprincipalname**, ale usługa G Suite oczekuje, że zostanie ona zamapowana na adres e-mail użytkownika. W tym celu możesz użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją w organizacji.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:

    | Name (Nazwa) | Atrybut źródłowy |
    | ---------------| --------------- |
    | Unikatowy identyfikator użytkownika | User.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

9. W sekcji **Konfigurowanie usługi G Suite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-g-suite-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze G Suite

1. Otwórz nową kartę w przeglądarce, a następnie zaloguj się do [konsoli administracyjnej usługi G Suite](https://admin.google.com/) przy użyciu konta administratora.

2. Kliknij pozycję **Zabezpieczenia**. Jeśli nie widzisz linku, może on znajdować się w menu **More Controls** (Więcej kontrolek) u dołu ekranu.

    ![Kliknij pozycję Security (Zabezpieczenia).][10]

3. Na stronie **Security** (Zabezpieczenia) kliknij pozycję **Set up single sign-on (SSO)** (Konfigurowanie logowania jednokrotnego).

    ![Kliknij pozycję SSO (Logowanie jednokrotne).][11]

4. Wprowadź następujące zmiany w konfiguracji:

    ![Konfigurowanie logowania jednokrotnego][12]

    a. Zaznacz pole wyboru **Setup SSO with third-party identity provider** (Skonfiguruj logowanie jednokrotne za pomocą dostawcy tożsamości innej firmy).

    b. W polu **Sign-in page URL** (Adres URL strony logowania) w usłudze G Suite wklej wartość pola  **Adres URL logowania**  skopiowaną z witryny Azure Portal.

    c. W polu **Sign-out page URL** (Adres URL strony wylogowywania) w usłudze G Suite wklej wartość pola  **Adres URL wylogowywania**  skopiowaną z witryny Azure Portal.

    d. W polu **Change password URL** (Adres URL zmiany hasła) w usłudze G Suite wklej wartość pola  **Adres URL zmiany hasła**  skopiowaną z witryny Azure Portal.

    e. W usłudze G Suite w polu **Verification certificate** (Certyfikat weryfikacji) przekaż certyfikat pobrany z witryny Azure Portal.

    f. Zaznacz pole wyboru **Use a domain specific issuer**  (Użyj wystawcy specyficznego dla domeny).

    g. Kliknij przycisk **Save Changes** (Zapisz zmiany).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi G Suite.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie wybierz pozycję **G Suite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz nazwę usługi **G Suite** i wybierz ją.

    ![Link do usługi G Suite na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-g-suite-test-user"></a>Tworzenie użytkownika testowego usługi G Suite

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniu G Suite. Usługa G Suite obsługuje automatyczną aprowizację, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w oprogramowaniu G Suite, zostanie utworzony podczas próby uzyskania dostępu do tego oprogramowania.

> [!NOTE]
> Przed testowaniem logowania jednokrotnego upewnij się, że użytkownik już istnieje w usłudze G Suite, jeśli aprowizacja w usłudze Azure AD nie została włączona.

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej firmy Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka G Suite na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi G Suite, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurowanie Aprowizowania użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
