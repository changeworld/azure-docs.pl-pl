---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługami Amazon Web Services (AWS) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługami Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1df79d7a4bd983527ee6d84c7ffe8e5ef61d6197
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107113"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Samouczek: Integracja usługi Azure Active Directory z usługami Amazon Web Services (AWS)

Z tego samouczka dowiesz się, jak zintegrować usługi Amazon Web Services (AWS) z usługą Azure Active Directory (Azure AD).
Integracja usług Amazon Web Services (AWS) z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania za pomocą usługi Azure AD kto ma dostęp do usług Amazon Web Services (AWS).
* Możliwość skonfigurowania użytkowników pod kątem automatycznego logowania do usług Amazon Web Services (AWS) przy użyciu kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Dla wielu wystąpień można skonfigurować wiele identyfikatorów, tak jak pokazano poniżej.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Korzystając z tych wartości, usługa Azure AD usunie wartość **#** i wyśle poprawną wartość `https://signin.aws.amazon.com/saml` jako adres URL odbiorcy w tokenie SAML.

**Zalecamy użycie tego podejścia z następujących względów:**

a. Każda aplikacja udostępnia unikatowy certyfikat X509, więc data wygaśnięcia certyfikatu każdego wystąpienia może być inna. Zarządzanie tym jest możliwe na poziomie poszczególnych kont usług AWS. Ogólnie przerzucanie certyfikatów będzie łatwe w tym przypadku.

b. Istnieje możliwość włączenia aprowizowania użytkowników za pomocą aplikacji usług AWS w usłudze Azure AD. Nasza usługa pobierze wtedy wszystkie role z konta usług AWS. Nie trzeba ręcznie dodawać ani aktualizować ról usług AWS dla aplikacji.

c. Właściciela aplikacji można przypisać dla konkretnej aplikacji. Może on wtedy zarządzać tą aplikacją bezpośrednio w usłudze Azure AD.

> [!Note]
> Upewnij się, że używasz tylko aplikacji z galerii.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługami Amazon Web Services (AWS), są potrzebne następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usług Amazon Web Services (AWS) z włączonym logowaniem jednokrotnym

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Aby zintegrować wiele kont usług AWS z jednym kontem platformy Azure na potrzeby logowania jednokrotnego, zobacz [ten](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artykuł.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługi Amazon Web Services (AWS) obsługują logowanie jednokrotne inicjowane przez **dostawcę usługi i dostawcę tożsamości**

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii

Aby skonfigurować integrację usług Amazon Web Services (AWS) z usługą Azure AD, należy dodać usługi Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługi Amazon Web Services (AWS) z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)** , wybierz pozycję **Amazon Web Services (AWS)** na panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługi Amazon Web Services (AWS) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z usługami Amazon Web Services (AWS), korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usług Amazon Web Services (AWS).

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usługach Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego usług Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** — aby mieć w usługach Amazon Web Services (AWS) odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usługach Amazon Web Services (AWS), wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usług **Amazon Web Services (AWS)** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** przycisku.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. W przypadku konfigurowania wielu wystąpień podaj wartość identyfikatora. Dla drugiego i kolejnych wystąpień podaj wartość identyfikatora w następującym formacie. Użyj znaku **#** , aby określić unikatową główną nazwę usługi.

    `https://signin.aws.amazon.com/saml#2`

    ![Informacje o domenie i adresach URL logowania jednokrotnego usług Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Aplikacja usług Amazon Web Services (AWS) oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | „podaj wartość z zakresu od 900 sekund (15 minut) do 43200 sekund (12 godzin)” |  https://aws.amazon.com/SAML/Attributes |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. W polu tekstowym **Przestrzeń nazw** wpisz wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie usług Amazon Web Services (AWS)** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usług Amazon Web Services (AWS)

1. W innym oknie przeglądarki zaloguj się do usług Amazon Web Services (AWS) jako administrator.

2. Kliknij pozycję **AWS Home** (Strona główna usług AWS).

    ![Konfigurowanie logowania jednokrotnego — strona główna][11]

3. Kliknij pozycję **Identity and Access Management** (Zarządzanie tożsamościami i dostępem).

    ![Konfigurowanie logowania jednokrotnego — tożsamość][12]

4. Kliknij pozycję **Identity Providers** (Dostawcy tożsamości), a następnie pozycję **Create Provider** (Utwórz dostawcę).

    ![Konfigurowanie logowania jednokrotnego — dostawca][13]

5. Na stronie okna dialogowego **Configure Provider** (Konfigurowanie dostawcy) wykonaj następujące kroki:

    ![Okno dialogowe Configure Single Sign-On (Konfigurowanie logowania jednokrotnego)][14]

    a. Dla pozycji **Provider Type** (Typ dostawcy) wybierz opcję **SAML**.

    b. W polu tekstowym **Provider Name** (Nazwa dostawcy) wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Aby przekazać **plik metadanych** pobrany z witryny Azure Portal, kliknij pozycję **Choose File** (Wybierz plik).

    d. Kliknij pozycję **Next Step** (Następny krok).

6. Na stronie okna dialogowego **Verify Provider Information** (Weryfikowanie informacji o dostawcy) kliknij pozycję **Create** (Utwórz).

    ![Konfigurowanie logowania jednokrotnego — weryfikowanie][15]

7. Kliknij pozycję **Roles** (Role), a następnie **Create role** (Utwórz rolę).

    ![Konfigurowanie logowania jednokrotnego — role][16]

8. Na stronie **Create role** (Tworzenie roli) wykonaj następujące kroki:  

    ![Konfigurowanie logowania jednokrotnego — zaufanie][19]

    a. Wybierz pozycję **SAML 2.0 federation** (Federacja SAML 2.0) w obszarze **Select type of trusted entity** (Wybierz typ zaufanej jednostki).

    b. W **sekcji Choose a SAML 2.0 Provider (Wybierz dostawcę SAML 2.0)** określ utworzonego wcześniej **dostawcę SAML** (na przykład: *WAAD*)

    c. Wybierz pozycję **Allow programmatic and AWS Management Console access** (Zezwalaj na dostęp programowy i za pomocą konsoli AWS Management Console).
  
    d. Kliknij pozycję **Next: Permissions** (Dalej: uprawnienia).

9. W oknie dialogowym **Attach Permissions Policies** (Dołączanie zasad uprawnień) dołącz zasady odpowiednie dla Twojej organizacji. Kliknij pozycję **Next: Review** (Dalej: przegląd).  

    ![Konfigurowanie logowania jednokrotnego — zasady][33]

10. W oknie dialogowym **Review** (Przegląd) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego — przegląd][34]

    a. W polu tekstowym **Role name** (Nazwa roli) podaj nazwę roli.

    b. W polu tekstowym **Role description** (Opis roli) podaj opis.

    c. Kliknij pozycję **Create Role** (Utwórz rolę).

    d. Utwórz potrzebną liczbę ról i zamapuj je na dostawcę tożsamości.

11. Użyj poświadczeń konta usług AWS do pobrania ról z konta usług AWS na potrzeby aprowizacji użytkowników usługi Azure AD. W tym celu otwórz stronę główną konsoli usług AWS.

12. Kliknij pozycję **Services** -> **Security, Identity& Compliance** -> **IAM** (Usługi -> Zabezpieczenia, tożsamość i zgodność -> Zarządzanie dostępem i tożsamościami).

    ![pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Wybierz kartę **Policies (Zasady)** w sekcji IAM (Zarządzanie dostępem i tożsamościami).

    ![pobieranie ról z konta usług AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Utwórz nowe zasady, klikając pozycję **Create policy** (Utwórz zasady) w celu pobrania ról z konta usług AWS na potrzeby aprowizacji użytkowników usługi Azure AD.

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Utwórz własne zasady, aby pobrać wszystkie role z kont usług AWS, wykonując następujące kroki:

    ![Tworzenie nowych zasad](./media/amazon-web-service-tutorial/policy1.png)

    a. W sekcji **„Create policy”** („Utwórz zasady”) kliknij kartę **„JSON”** .

    b. Dodaj poniższy kod JSON do dokumentu zasad.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Kliknij **przycisk Review Policy (Przejrzyj zasady)** , aby zweryfikować zasady.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy5.png)

16. Zdefiniuj **nowe zasady**, wykonując następujące kroki:

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy2.png)

    a. W polu **Policy Name** (Nazwa zasad) podaj wartość **AzureAD_SSOUserRole_Policy**.

    b. W polu **Description** (Opis) możesz wpisać opis: **Te zasady umożliwiają pobranie ról z kont usług AWS**.

    c. Kliknij przycisk **„Create Policy”** („Utwórz zasady”).

17. Utwórz nowe konto użytkownika w usłudze zarządzania dostępem i tożsamościami usług AWS, wykonując następujące kroki:

    a. Kliknij element nawigacji **Users** (Użytkownicy) w konsoli usługi zarządzania dostępem i tożsamościami usług AWS.

    ![Definiowanie nowych zasad](./media/amazon-web-service-tutorial/policy3.png)

    b. Kliknij przycisk **Add user** (Dodaj użytkownika), aby utworzyć nowego użytkownika.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/policy4.png)

    c. W sekcji **Add user** (Dodaj użytkownika) wykonaj następujące kroki:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser1.png)

    * Podaj nazwę użytkownika: **AzureADRoleManager**.

    * W polu Access type (Typ dostępu) wybierz opcję **Programmatic access** (Dostęp programowy). Dzięki temu użytkownik może wywołać interfejsy API i pobrać role z konta usług AWS.

    * Kliknij przycisk **Next Permissions** (Dalej: uprawnienia) w prawym dolnym rogu.

18. Teraz utwórz nowe zasady dla tego użytkownika, wykonując następujące kroki:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser2.png)

    a. Kliknij przycisk **Attach existing policies directly** (Dołącz istniejące zasady bezpośrednio).

    b. Wyszukaj nowo utworzone zasady w sekcji **AzureAD_SSOUserRole_Policy** filtru.

    c. Wybierz pozycję **policy** (zasady), a następnie kliknij przycisk **Next: Review** (Dalej: przegląd).

19. Przejrzyj zasady dołączonego użytkownika, wykonując następujące kroki:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser3.png)

    a. Przejrzyj nazwę użytkownika, typ dostępu i zasady zamapowane do użytkownika.

    b. Kliknij przycisk **Create user** (Utwórz użytkownika) w prawym dolnym rogu, aby utworzyć użytkownika.

20. Pobierz poświadczenia użytkownika, wykonując następujące kroki:

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/adduser4.png)

    a. Skopiuj **identyfikator klucza dostępu** i **klucz dostępu wpisu tajnego**.

    b. Podaj te poświadczenia w sekcji aprowizacji użytkowników usługi Azure AD, aby pobrać role z konsoli usług AWS.

    c. Kliknij przycisk **Close** (Zamknij) znajdujący się u dołu.

21. Przejdź do sekcji **User Provisioning** (Aprowizacja użytkowników) dla aplikacji usług Amazon Web Services w portalu zarządzania usługi Azure AD.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning.png)

22. Podaj **klucz dostępu** i **wpis tajny** odpowiednio w polach **Client Secret** (Wpis tajny klienta) i **Secret Token** (Token wpisu tajnego).

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Podaj klucz dostępu użytkownika usług AWS w polu **clientsecret**.

    b. Podaj wpis tajny użytkownika usług AWS w polu **Secret Token** (Token wpisu tajnego).

    c. Kliknij przycisk **Test Connection** (Testuj połączenie). Testowanie połączenia powinno zakończyć się pomyślnie.

    d. Zapisz ustawienie, klikając przycisk **Save** (Zapisz) znajdujący się u góry.

23. Teraz upewnij się, że aprowizacja jest **włączona**: w sekcji Settings (Ustawienia) ustaw przełącznik w pozycji On (Włączone), a następnie kliknij przycisk **Save** (Zapisz) znajdujący się u góry.

    ![Dodawanie użytkownika](./media/amazon-web-service-tutorial/provisioning2.png)

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

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usług Amazon Web Services (AWS).

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, **Wszystkie aplikacje**, a następnie **Amazon Web Services (AWS)** .

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz **Amazon Web Services (AWS)** i wybierz znalezioną pozycję.

    ![Link usług Amazon Web Services (AWS) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-amazon-web-services-aws-test-user"></a>Tworzenie użytkownika testowego usług Amazon Web Services (AWS)

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w usługach Amazon Web Services (AWS). Usługi Amazon Web Services (AWS) nie wymagają utworzenia użytkownika w swoim systemie logowania jednokrotnego, więc w tym miejscu nie trzeba wykonywać żadnych działań.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) na panelu dostępu powinno nastąpić automatyczne zalogowanie do usług Amazon Web Services (AWS), dla których skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="known-issues"></a>Znane problemy

 * W sekcji **Aprowizacja** w podsekcji **Mapowania** będzie wyświetlany komunikat „Trwa ładowanie...” i nigdy nie będą wyświetlane mapowania atrybutów. Jedyny obsługiwany obecnie przepływ pracy aprowizacji to importowanie ról z usług AWS do usługi Azure AD w celu wyboru podczas przypisywania użytkownika/grupy. Mapowania atrybutów na te potrzeby są wstępnie zdefiniowane i nie można ich konfigurować.
 
 * Sekcja **Aprowizacja** obsługuje wprowadzanie tylko jednego zestawu poświadczeń jednocześnie dla jednej dzierżawy usług AWS. Wszystkie zaimportowane role są zapisywane w ramach właściwości appRoles [obiektu servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) usługi Azure AD dla dzierżawy usług AWS. Z poziomu galerii do usługi Azure AD można dodać wiele dzierżaw usług AWS (reprezentowanych przez obiekty servicePrincipal) służących do aprowizacji, jednak istnieje znany problem związany z brakiem możliwości automatycznego zapisywania wszystkich zaimportowanych ról z wielu obiektów servicePrincipal usług AWS używanych do aprowizacji do pojedynczego obiektu servicePrincipal używanego do logowania jednokrotnego. Jako obejście można użyć [interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do wyodrębnienia wszystkich zaimportowanych ról aplikacji do poszczególnych obiektów servicePrincipal usług AWS, dla których skonfigurowano aprowizację. Te ciągi ról można następnie dodawać do obiektu servicePrincipal usług AWS, dla którego skonfigurowano logowanie jednokrotne.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
