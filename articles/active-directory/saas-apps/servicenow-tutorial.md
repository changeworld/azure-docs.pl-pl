---
title: 'Samouczek: integracja usługi Azure Active Directory z usługą ServiceNow | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba516aa2c3d2decaa4962f1ccd0394ebe9a4a62
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706129"
---
# <a name="tutorial-integrate-servicenow-with-azure-active-directory"></a>Samouczek: Integracja usługi ServiceNow z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługi ServiceNow z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi ServiceNow z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do usługi ServiceNow.
* Umożliwianie użytkownikom można automatycznie zalogowany do usługi ServiceNow przy użyciu konta usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Usługi ServiceNow pojedynczego logowania jednokrotnego (SSO) włączone subskrypcji.
* W przypadku usługi ServiceNow — wystąpienie lub dzierżawa usługi ServiceNow w wersji Calgary lub wyższej
* W przypadku usługi ServiceNow Express — wystąpienie usługi ServiceNow Express w wersji Helsinki lub wyższej
* W dzierżawie usługi ServiceNow musi być włączona wtyczka [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (wtyczka logowania jednokrotnego u wielu dostawców). Można to uzyskać, [przesyłając żądanie obsługi](https://hi.service-now.com).
* W przypadku konfiguracji automatycznej włącz wtyczkę wielu dostawców dla usługi ServiceNow.
* Aby zainstalować aplikacji ServiceNow klasyczny (urządzenia przenośne), czego potrzebujesz, przejdź do odpowiedniego sklepu i wyszukiwanie aplikacji klasyczne usługi ServiceNow, a następnie kliknij przycisk Pobierz.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Usługi ServiceNow obsługuje **SP** zainicjowane logowania jednokrotnego i obsługuje [ **automatyczne** aprowizacji użytkowników](servicenow-provisioning-tutorial.md).

Można teraz skonfigurować aplikacji ServiceNow klasyczny (urządzenia przenośne) z usługą Azure AD Włączanie logowania jednokrotnego i obsługuje zarówno **Android** i **IOS** użytkowników. W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym.

## <a name="adding-servicenow-from-the-gallery"></a>Dodawanie usługi ServiceNow z galerii

Aby skonfigurować integrację usługi ServiceNow z usługą Azure AD, musisz dodać usługę ServiceNow z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **ServiceNow** w polu wyszukiwania.
1. Wybierz **ServiceNow** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD przy użyciu usługi ServiceNow przy użyciu użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usługi ServiceNow.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD przy użyciu usługi ServiceNow, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie usługi ServiceNow](#configure-servicenow)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
4. **[Konfigurowanie logowania jednokrotnego usługi ServiceNow Express](#configure-servicenow-express-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
5. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
6. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające B.Simon do użycia usługi Azure AD logowania jednokrotnego.
7. **[Tworzenie użytkownika testowego usługi ServiceNow](#create-servicenow-test-user)**  mieć odpowiednikiem B.Simon usługi ServiceNow, połączonego z usługi Azure AD reprezentacja użytkownika.
8. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.
9. **[Testowanie logowania jednokrotnego dla usługi ServiceNow Classic (dla urządzeń przenośnych)](#test-sso-for-servicenow-classic-mobile)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **ServiceNow** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowania jednokrotnego**.
1. Na **wybierz metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

   a. Kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacyjnych aplikacji**. Następnie wklej go do Notatnika, ponieważ ten adres URL metadanych federacyjnych aplikacji będzie używany w dalszej części samouczka.

    b. Kliknij pozycję **Pobierz**, aby pobrać **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

1. Na **Konfigurowanie usługi ServiceNow** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-servicenow"></a>Konfigurowanie usługi ServiceNow

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

    * Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Zwróć uwagę, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby wszystko działało. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z sekcji krótki.

        ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

    * Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    * W ramach konfiguracji automatycznej wszystkie niezbędne ustawienia są konfigurowane po stronie usługi **ServiceNow**, ale **certyfikat X.509** nie jest domyślnie włączony. Musisz zamapować go ręcznie na swojego dostawcę tożsamości w usłudze ServiceNow. Wykonaj poniższe kroki:

    * W okienku nawigacji po lewej stronie znajdź sekcję **Multi-Provider SSO** (Logowanie jednokrotne u wielu dostawców) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Identity Providers** (Dostawcy tożsamości).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

    * Kliknij automatycznie wygenerowanego dostawcę tożsamości

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurowanie logowania jednokrotnego")

    *  W sekcji **Identity Provider** (Dostawca tożsamości) wykonaj następujące kroki:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/automatic_config.png "Konfigurowanie logowania jednokrotnego")

        * W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład **Microsoft Azure federacyjnego logowania jednokrotnego**).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Kopiuj **strony głównej usługi ServiceNow** wartość, wklej ją w **adres URL logowania** polu tekstowym w **ServiceNow podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **identyfikator jednostki / wystawcy** wartość, wklej go w **identyfikator** polu tekstowym w **ServiceNow podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

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

        * W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji (na przykład **Microsoft Azure federacyjnego logowania jednokrotnego**).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Kopiuj **strony głównej usługi ServiceNow** wartość, wklej ją w **adres URL logowania** polu tekstowym w **ServiceNow podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **identyfikator jednostki / wystawcy** wartość, wklej go w **identyfikator** polu tekstowym w **ServiceNow podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

        * Upewnij się, że ustawienie **NameID Policy** (Zasady atrybutu NameID) ma wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Kliknij pozycję **Advanced** (Zaawansowane). W polu tekstowym **User Field** (Pole użytkownika) wpisz **email** lub **user_name**, w zależności od tego, jakie pole jest używane do jednoznacznego identyfikowania użytkowników w Twoim wdrożeniu usługi ServiceNow.

            > [!NOTE]
            > Możesz skonfigurować usługę Azure AD tak, aby emitowała identyfikator użytkownika usługi Azure AD (główną nazwę użytkownika) lub adres e-mail jako unikatowy identyfikator w tokenie SAML, przechodząc do sekcji **ServiceNow > Atrybuty > Logowanie jednokrotne** w witrynie Azure Portal i mapując żądane pole na atrybut **nameidentifier**. Wartość wybranego atrybutu przechowywana w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością wprowadzonego pola przechowywaną w usłudze ServiceNow (na przykład user_name)

        * Kliknij pozycję **Test Connection** (Testuj połączenie) w prawym górnym rogu strony.

        * Po kliknięciu pozycji **Test Connection** (Testuj połączenie) zostanie wyświetlone wyskakujące okienko, w którym musisz wprowadzić poświadczenia. Zostanie wyświetlona poniższa strona z wynikami. Błąd **SSO Logout Test Results** (Wyniki testu wylogowywania logowania jednokrotnego) jest spodziewany. Zignoruj go i kliknij przycisk **Activate** (Aktywuj).

          ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow Express

1. W [witryny Azure portal](https://portal.azure.com/)na **ServiceNow** strona integracji aplikacji, wybierz opcję **logowania jednokrotnego**.

    ![Konfigurowanie linku logowania jednokrotnego](common/select-sso.png)

2. Na **wybierz metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne.

    ![Logowanie jednokrotne wybierz tryb pojedynczej](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Musisz zaktualizować te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Dla aplikacji ServiceNow jest dostępna usługa konfigurowania jednym kliknięciem. Oznacza to, że usługa Azure AD może automatycznie skonfigurować w aplikacji ServiceNow uwierzytelnianie oparte na SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfigurowanie usługi ServiceNow** i kliknij pozycję **Wyświetl instrukcje krok po kroku**, aby otworzyć okno Konfiguruj logowanie.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Zwróć uwagę, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby wszystko działało. W przeciwnym razie ręcznie skonfigurować usługi ServiceNow, używać usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfigurować logowanie jednokrotne** i skopiuj **adres URL wylogowania, identyfikator usługi Azure AD i adres URL logowania** z Szybkie odwołanie do sekcji.

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

### <a name="configure-servicenow-express-sso"></a>Konfigurowanie usługi ServiceNow Express logowania jednokrotnego

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

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do usługi ServiceNow.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ServiceNow**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze ServiceNow. Aplikacja ServiceNow obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](servicenow-provisioning-tutorial.md).

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka usługi ServiceNow w panelu dostępu, możesz powinny być automatycznie zalogowany do usługi ServiceNow, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testowanie logowania jednokrotnego usługi ServiceNow klasyczny (dla urządzeń przenośnych)

1. Otwórz swoje **ServiceNow klasyczny (dla urządzeń przenośnych)** aplikacji i wykonaj następujące czynności:

    a. Kliknij pozycję **Dodaj** symbol poniżej ekranu.

    ![Logowanie](./media/servicenow-tutorial/test03.png)

    b. Wpisz nazwę wystąpienia usługi ServiceNow, a następnie kliknij przycisk **Kontynuuj**.

    ![Logowanie](./media/servicenow-tutorial/test04.png)

    c. Na **Zaloguj** ekranu, należy wykonać następujące czynności:

    ![Logowanie](./media/servicenow-tutorial/test01.png)

    *  Typ **Username** takich jak B.simon@contoso.com.

    *  Kliknij pozycję **logowania zewnętrznego do użycia** i nastąpi przekierowanie do strony logowania usługi Azure AD.
    
    *  Wprowadź swoje poświadczenia i w przypadku uwierzytelniania innych firm lub innych funkcji zabezpieczeń, włączone, użytkownik będzie musiał reagować odpowiednio i aplikacja **strony głównej** będą wyświetlane jak pokazano poniżej:

        ![Strona główna](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](servicenow-provisioning-tutorial.md)