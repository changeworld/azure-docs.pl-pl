---
title: 'Samouczek: integracja usługi Azure Active Directory z usługą ServiceNow | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 016f4fe5e0d1e8c84a13ca97d8f910c29d5984d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091130"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Samouczek: integracja usługi Azure Active Directory z usługą ServiceNow

Z tego samouczka dowiesz się, jak zintegrować usługę ServiceNow z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi ServiceNow z usługą Azure AD zapewnia następujące korzyści:

* Możliwość kontrolowania w usłudze Azure AD, kto ma dostęp do usługi ServiceNow.
* Możliwość skonfigurowania użytkowników pod kątem automatycznego logowania do usługi ServiceNow przy użyciu kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ServiceNow, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi ServiceNow z obsługą logowania jednokrotnego
* W przypadku usługi ServiceNow — wystąpienie lub dzierżawa usługi ServiceNow w wersji Calgary lub wyższej
* W przypadku usługi ServiceNow Express — wystąpienie usługi ServiceNow Express w wersji Helsinki lub wyższej
* W dzierżawie usługi ServiceNow musi być włączona wtyczka [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (wtyczka logowania jednokrotnego u wielu dostawców). Można to uzyskać, [przesyłając żądanie obsługi](https://hi.service-now.com).
* W przypadku konfiguracji automatycznej włącz wtyczkę wielu dostawców dla usługi ServiceNow.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa ServiceNow obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

* Usługa ServiceNow obsługuje [**zautomatyzowaną** aprowizację użytkowników](servicenow-provisioning-tutorial.md)

## <a name="adding-servicenow-from-the-gallery"></a>Dodawanie usługi ServiceNow z galerii

Aby skonfigurować integrację usługi ServiceNow z usługą Azure AD, musisz dodać usługę ServiceNow z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę ServiceNow z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **ServiceNow**, wybierz pozycję **ServiceNow** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa ServiceNow na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w usłudze ServiceNow, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi ServiceNow.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze ServiceNow, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze ServiceNow](#configure-servicenow-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
4. **[Konfigurowanie logowania jednokrotnego w usłudze ServiceNow Express](#configure-servicenow-express-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
5. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
6. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
7. **[Tworzenie użytkownika testowego usługi ServiceNow](#create-servicenow-test-user)** — aby udostępnić w usłudze ServiceNow odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
8. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD na potrzeby usługi ServiceNow

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze ServiceNow, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ServiceNow** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL usługi ServiceNow — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości, używając rzeczywistego identyfikatora i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

    a. Kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacyjnych aplikacji**. Następnie wklej go do Notatnika, ponieważ ten adres URL metadanych federacyjnych aplikacji będzie używany w dalszej części samouczka.

    b. Kliknij pozycję **Pobierz**, aby pobrać **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

6. W sekcji **Konfigurowanie usługi ServiceNow** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-servicenow-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi ServiceNow

1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

2. Aktywuj wtyczkę **Integration - Multiple Provider Single Sign-On Installer** (Integracja — instalator logowania jednokrotnego u wielu dostawców), wykonując kolejne kroki:

    a. W okienku nawigacji po lewej stronie znajdź sekcję **System Definition** (Definicja systemu) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Plugins** (Wtyczki).

    ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktywowanie wtyczki")

    b. Wyszukaj wtyczkę **Integration - Multiple Provider Single Sign-On Installer** (Integracja — instalator logowania jednokrotnego u wielu dostawców).

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktywowanie wtyczki")

    c. Wybierz wtyczkę. Kliknij prawym przyciskiem myszy i wybierz pozycję **Activate/Upgrade** (Aktywuj/uaktualnij).

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate.png "Aktywowanie wtyczki")

    d. Kliknij przycisk **Activate** (Aktywuj).

     ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate1.png "Aktywowanie wtyczki")

3. W okienku nawigacji po lewej stronie znajdź sekcję **Multi-Provider SSO** (Logowanie jednokrotne u wielu dostawców) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Properties** (Właściwości).

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurowanie adresu URL aplikacji")

4. W oknie dialogowym **Multiple Provider SSO Properties** (Właściwości logowania jednokrotnego u wielu dostawców) wykonaj następujące kroki:

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/ic7694981.png "Konfigurowanie adresu URL aplikacji")

    * Dla opcji **Enable multiple provider SSO** (Włącz logowanie jednokrotne u wielu dostawców) wybierz ustawienie **Yes** (Tak).
  
    * Dla opcji **Enable Auto Importing of users from all identity providers into the user table** (Włącz automatyczne importowanie użytkowników od wszystkich dostawców tożsamości do tabeli użytkownika) wybierz ustawienie **Yes** (Tak).

    * Dla opcji **Enable debug logging for the multiple provider SSO integration** (Włącz rejestrowanie debugowania dla integracji logowania jednokrotnego u wielu dostawców) wybierz ustawienie **Yes** (Tak).

    * W polu tekstowym **The field on the user table that...** (Pole w tabeli użytkownika, które...) wpisz **user_name**.
  
    * Kliknij pozycję **Zapisz**.

5. Istnieją dwa sposoby, za pomocą których można skonfigurować aplikację **ServiceNow** — automatycznie i ręcznie.

6. W celu automatycznego skonfigurowania aplikacji **ServiceNow** wykonaj następujące kroki:

    * Wróć do strony logowania jednokrotnego usługi **ServiceNow** w witrynie Azure Portal.

    * Dla aplikacji ServiceNow jest dostępna usługa konfigurowania jednym kliknięciem. Oznacza to, że usługa Azure AD może automatycznie skonfigurować w aplikacji ServiceNow uwierzytelnianie oparte na SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfiguracja aplikacji ServiceNow** i kliknij pozycję **Konfiguruj aplikację ServiceNow**, aby otworzyć okno Konfiguruj logowanie.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Zwróć uwagę, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby wszystko działało. W przeciwnym razie aby ręcznie skonfigurować usługę ServiceNow na potrzeby korzystania z usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **Konfiguruj logowanie jednokrotne ręcznie**, a następnie skopiuj wartości **adresu URL wylogowywania, identyfikatora jednostki SAML i adresu URL usługi logowania jednokrotnego SAML** z sekcji Dokumentacja.

        ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

    * Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    * W ramach konfiguracji automatycznej wszystkie niezbędne ustawienia są konfigurowane po stronie usługi **ServiceNow**, ale **certyfikat X.509** nie jest domyślnie włączony. Musisz zamapować go ręcznie na swojego dostawcę tożsamości w usłudze ServiceNow. Wykonaj poniższe kroki:

    * W okienku nawigacji po lewej stronie znajdź sekcję **Multi-Provider SSO** (Logowanie jednokrotne u wielu dostawców) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Identity Providers** (Dostawcy tożsamości).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

    * Kliknij automatycznie wygenerowanego dostawcę tożsamości

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurowanie logowania jednokrotnego")

    *  W sekcji **Identity Provider** (Dostawca tożsamości) wykonaj następujące kroki:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/automatic_config.png "Konfigurowanie logowania jednokrotnego")

        * W polu tekstowym **Name** (Nazwa) wpisz nazwę swojej konfiguracji (na przykład **Federacyjne logowanie jednokrotne platformy Microsoft Azure**).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Skopiuj wartość **strony głównej usługi ServiceNow** i wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Domena i adresy URL usługi ServiceNow** w witrynie Azure Portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Skopiuj wartość **identyfikatora jednostki/wystawcy** i wklej ją w polu tekstowym **Identyfikator** w sekcji **Domena i adresy URL usługi ServiceNow** w witrynie Azure Portal.

        * Upewnij się, że ustawienie **NameID Policy** (Zasady atrybutu NameID) ma wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

    * Przewiń w dół do sekcji **X.509 Certificate** (Certyfikat X.509) i wybierz pozycję **Edit** (Edytuj).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurowanie logowania jednokrotnego")

    * Wybierz certyfikat, a następnie kliknij ikonę strzałki w prawo, aby dodać certyfikat

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurowanie logowania jednokrotnego")

    * Kliknij pozycję **Zapisz**.

    * Kliknij pozycję **Test Connection** (Testuj połączenie) w prawym górnym rogu strony.

        ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_activate2.png "Aktywowanie wtyczki")

    * Po kliknięciu pozycji **Test Connection** (Testuj połączenie) zostanie wyświetlone wyskakujące okienko, w którym musisz wprowadzić poświadczenia. Zostanie wyświetlona poniższa strona z wynikami. Błąd **SSO Logout Test Results** (Wyniki testu wylogowywania logowania jednokrotnego) jest spodziewany. Zignoruj go i kliknij przycisk **Activate** (Aktywuj).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")
  
7. W celu ręcznego skonfigurowania usługi **ServiceNow** wykonaj następujące kroki:

    * Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    * W okienku nawigacji po lewej stronie kliknij pozycję **Identity Providers** (Dostawcy tożsamości).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

    * W oknie dialogowym **Identity Providers** (Dostawcy tożsamości) kliknij pozycję **New** (Nowy).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694977.png "Konfigurowanie logowania jednokrotnego")

    * W oknie dialogowym **Identity Providers** (Dostawcy tożsamości) kliknij pozycję **SAML**.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694978.png "Konfigurowanie logowania jednokrotnego")

    * W okienku wyskakującym **Import Identity Provider Metadata** (Importuj metadane dostawcy tożsamości) wykonaj następujące kroki:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/idp.png "Konfigurowanie logowania jednokrotnego")

        * Wprowadź **adres URL metadanych federacyjnych aplikacji** skopiowany z witryny Azure Portal.

        * Kliknij przycisk **importu**.

    * Spowoduje to odczytanie adresu URL metadanych dostawcy tożsamości i wypełnienie wszystkich pól danymi.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982.png "Konfigurowanie logowania jednokrotnego")

        * W polu tekstowym **Name** (Nazwa) wpisz nazwę swojej konfiguracji (na przykład **Federacyjne logowanie jednokrotne platformy Microsoft Azure**).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Skopiuj wartość **strony głównej usługi ServiceNow** i wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Domena i adresy URL usługi ServiceNow** w witrynie Azure Portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Skopiuj wartość **identyfikatora jednostki/wystawcy** i wklej ją w polu tekstowym **Identyfikator** w sekcji **Domena i adresy URL usługi ServiceNow** w witrynie Azure Portal.

        * Upewnij się, że ustawienie **NameID Policy** (Zasady atrybutu NameID) ma wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Kliknij pozycję **Advanced** (Zaawansowane). W polu tekstowym **User Field** (Pole użytkownika) wpisz **email** lub **user_name**, w zależności od tego, jakie pole jest używane do jednoznacznego identyfikowania użytkowników w Twoim wdrożeniu usługi ServiceNow.

            > [!NOTE]
            > Możesz skonfigurować usługę Azure AD tak, aby emitowała identyfikator użytkownika usługi Azure AD (główną nazwę użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do sekcji **ServiceNow > Atrybuty > Logowanie jednokrotne** w witrynie Azure Portal i mapując żądane pole na atrybut **nameidentifier**. Wartość wybranego atrybutu przechowywana w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością wprowadzonego pola przechowywaną w usłudze ServiceNow (na przykład user_name)

        * Kliknij pozycję **Test Connection** (Testuj połączenie) w prawym górnym rogu strony.

        * Po kliknięciu pozycji **Test Connection** (Testuj połączenie) zostanie wyświetlone wyskakujące okienko, w którym musisz wprowadzić poświadczenia. Zostanie wyświetlona poniższa strona z wynikami. Błąd **SSO Logout Test Results** (Wyniki testu wylogowywania logowania jednokrotnego) jest spodziewany. Zignoruj go i kliknij przycisk **Activate** (Aktywuj).

          ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD na potrzeby usługi ServiceNow Express

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **ServiceNow** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL usługi ServiceNow — informacje dotyczące logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości, używając rzeczywistego identyfikatora i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Dla aplikacji ServiceNow jest dostępna usługa konfigurowania jednym kliknięciem. Oznacza to, że usługa Azure AD może automatycznie skonfigurować w aplikacji ServiceNow uwierzytelnianie oparte na SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfigurowanie usługi ServiceNow** i kliknij pozycję **Wyświetl instrukcje krok po kroku**, aby otworzyć okno Konfiguruj logowanie.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Zwróć uwagę, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby wszystko działało. W przeciwnym razie aby ręcznie skonfigurować usługę ServiceNow na potrzeby korzystania z usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **Konfiguruj logowanie jednokrotne ręcznie**, a następnie skopiuj wartości **adresu URL wylogowywania, identyfikatora jednostki SAML i adresu URL usługi logowania jednokrotnego SAML** z sekcji Dokumentacja.

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

### <a name="configure-servicenow-express-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi ServiceNow Express

1. Zaloguj się do swojej aplikacji ServiceNow Express jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Single Sign-On** (Logowanie jednokrotne).

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/ic7694980ex.png "Konfigurowanie adresu URL aplikacji")

3. W oknie dialogowym **Single Sign-On** (Logowanie jednokrotne) kliknij ikonę konfiguracji w prawym górnym rogu, a następnie ustaw następujące właściwości:

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/ic7694981ex.png "Konfigurowanie adresu URL aplikacji")

    a. Przestaw przełącznik **Enable multiple provider SSO** (Włącz logowanie jednokrotne u wielu dostawców) w prawo.

    b. Przestaw przełącznik **Enable debug logging for the multiple provider SSO integration** (Włącz rejestrowanie debugowania dla integracji logowania jednokrotnego u wielu dostawców) w prawo.

    c. W polu tekstowym **The field on the user table that...** (Pole w tabeli użytkownika, które...) wpisz **user_name**.

4. W oknie dialogowym **Single Sign-On** (Logowanie jednokrotne) kliknij pozycję **Add New Certificate** (Dodaj nowy certyfikat).

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694973ex.png "Konfigurowanie logowania jednokrotnego")

5. W oknie dialogowym **X.509 Certificates** (Certyfikaty X.509) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694975.png "Konfigurowanie logowania jednokrotnego")

    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę swojej konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz pozycję **Active** (Aktywne).

    c. Dla opcji **Format** wybierz ustawienie **PEM**.

    d. Dla opcji **Type** (Typ) wybierz ustawienie **Trust Store Cert** (Certyfikat magazynu zaufania).

    e. Otwórz w Notatniku certyfikat zakodowany w formacie Base64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **PEM Certificate** (Certyfikat PEM).

    f. Kliknij pozycję **Update** (Aktualizuj)

6. W oknie dialogowym **Single Sign-On** (Logowanie jednokrotne) kliknij pozycję **Add New IdP** (Dodaj nowego dostawcę tożsamości).

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694976ex.png "Konfigurowanie logowania jednokrotnego")

7. W oknie dialogowym **Add New Identity Provider** (Dodawanie nowego dostawcy tożsamości), w obszarze **Configure Identity Provider** (Konfiguruj dostawcę tożsamości) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694982ex.png "Konfigurowanie logowania jednokrotnego")

    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę swojej konfiguracji (na przykład: **SAML 2.0**).

    b. W polu **Identity Provider URL** (Adres URL dostawcy tożsamości) wklej wartość **identyfikatora dostawcy tożsamości** skopiowaną z witryny Azure Portal.

    c. W polu **Identity Provider's AuthnRequest** (Atrybut AuthnRequest dostawcy tożsamości) wklej wartość **adresu URL żądania uwierzytelniania** skopiowaną z witryny Azure Portal.

    d. W polu **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal

    e. Dla opcji **Identity Provider Certificate** (Certyfikat dostawcy tożsamości) wybierz certyfikat utworzony w poprzednim kroku.

8. Kliknij pozycję **Advanced Settings** (Ustawienia zaawansowane), a następnie w obszarze **Additional Identity Provider Properties** (Dodatkowe właściwości dostawcy tożsamości) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694983ex.png "Konfigurowanie logowania jednokrotnego")

    a. W polu tekstowym **Protocol Binding for the IDP's SingleLogoutRequest** (Powiązanie protokołu dla atrybutu SingleLogoutRequest dostawcy tożsamości) wpisz **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. W polu tekstowym **NameID Policy** (Zasady atrybutu NameID) wpisz **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    c. W polu **AuthnContextClassRef Method** (Metoda AuthnContextClassRef) wpisz `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Usuń zaznaczenie opcji **Create an AuthnContextClass** (Utwórz metodę AuthnContextClass).

9. W obszarze **Additional Service Provider Properties** (Dodatkowe właściwości dostawcy usług) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694984ex.png "Konfigurowanie logowania jednokrotnego")

    a. W polu tekstowym **ServiceNow Homepage** (Strona główna usługi ServiceNow) wpisz adres URL strony głównej wystąpienia usługi ServiceNow.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład: `https://fabrikam.service-now.com/navpage.do`).

    b. W polu tekstowym **Entity ID/Issuer** (Identyfikator jednostki/wystawca) wpisz adres URL dzierżawy usługi ServiceNow.

    c. W polu tekstowym **Audience URI** (Identyfikator URI odbiorców) wpisz adres URL dzierżawy usługi ServiceNow.

    d. W polu tekstowym **Clock Skew** (Niedokładność zegara) wpisz wartość **60**.

    e. W polu tekstowym **User Field** (Pole użytkownika) wpisz **email** lub **user_name**, w zależności od tego, jakie pole jest używane do jednoznacznego identyfikowania użytkowników w Twoim wdrożeniu usługi ServiceNow.

    > [!NOTE]
    > Możesz skonfigurować usługę Azure AD tak, aby emitowała identyfikator użytkownika usługi Azure AD (główną nazwę użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do sekcji **ServiceNow > Atrybuty > Logowanie jednokrotne** w witrynie Azure Portal i mapując żądane pole na atrybut **nameidentifier**. Wartość wybranego atrybutu przechowywana w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością wprowadzonego pola przechowywaną w usłudze ServiceNow (na przykład user_name)

    f. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi ServiceNow.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie wybierz pozycję **ServiceNow**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ServiceNow**.

    ![Link do usługi ServiceNow na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze ServiceNow. Aplikacja ServiceNow obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](servicenow-provisioning-tutorial.md).

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ServiceNow na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi ServiceNow, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](servicenow-provisioning-tutorial.md)
