---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą usługi ServiceNow | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą ServiceNow.
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
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą usługi ServiceNow

W tym samouczku dowiesz się, jak zintegrować usługę usługi ServiceNow z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi usługi ServiceNow z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi ServiceNow.
* Zezwól użytkownikom na automatyczne logowanie się do usługi usługi ServiceNow przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi ServiceNow.
* W przypadku usługi ServiceNow — wystąpienie lub dzierżawa usługi ServiceNow w wersji Calgary lub wyższej
* W przypadku usługi ServiceNow Express — wystąpienie usługi ServiceNow Express w wersji Helsinki lub wyższej
* W dzierżawie usługi ServiceNow musi być włączona wtyczka [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (wtyczka logowania jednokrotnego u wielu dostawców). Można to uzyskać, [przesyłając żądanie obsługi](https://hi.service-now.com).
* W przypadku konfiguracji automatycznej włącz wtyczkę wielu dostawców dla usługi ServiceNow.
* Aby zainstalować klasyczną aplikację usługi ServiceNow (Mobile), należy przejść do odpowiedniego magazynu i wyszukać klasyczną aplikację usługi ServiceNow, a następnie kliknąć pozycję Pobierz.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa usługi ServiceNow obsługuje usługę **SP** zainicjowaną jako logowanie jednokrotne i obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](servicenow-provisioning-tutorial.md).

Klasycznej aplikacji usługi ServiceNow (Mobile) można teraz skonfigurować za pomocą usługi Azure AD w celu włączenia logowania jednokrotnego i obsługuje zarówno użytkowników **systemu Android** , jak i **iOS** . W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

## <a name="adding-servicenow-from-the-gallery"></a>Dodawanie usługi ServiceNow z galerii

Aby skonfigurować integrację usługi ServiceNow z usługą Azure AD, musisz dodać usługę ServiceNow z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **usługi ServiceNow** w polu wyszukiwania.
1. Wybierz pozycję **usługi ServiceNow** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla usługi ServiceNow

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi ServiceNow przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi ServiceNow.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi ServiceNow, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
    1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD dla usługi ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj usługi ServiceNow](#configure-servicenow)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego usługi ServiceNow](#create-servicenow-test-user)** , aby miał odpowiednik B. Simon w usługi ServiceNow, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
    1. **[Skonfiguruj logowanie](#configure-servicenow-express-sso)** jednokrotne w usłudze usługi ServiceNow Express — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.    
3. **[Przetestuj logowanie](#test-sso)** jednokrotne, aby sprawdzić, czy konfiguracja działa.
4. **[Przetestuj Logowanie jednokrotne dla usługi ServiceNow klasyczny (Mobile)](#test-sso-for-servicenow-classic-mobile)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi ServiceNow** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy zaktualizować te wartości przy użyciu rzeczywistego adresu URL logowania i identyfikatora, który został wyjaśniony w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

   a. Kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacyjnych aplikacji**. Następnie wklej go do Notatnika, ponieważ ten adres URL metadanych federacyjnych aplikacji będzie używany w dalszej części samouczka.

    b. Kliknij pozycję **Pobierz**, aby pobrać **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

1. W sekcji **Konfigurowanie usługi ServiceNow** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi usługi ServiceNow.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ServiceNow**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow Express

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi ServiceNow** wybierz pozycję **Logowanie**jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![tryb wyboru logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij przycisk **Edytuj** ikonę, aby otworzyć okno dialogowe **podstawowe Konfigurowanie protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com/navpage.do`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy zaktualizować te wartości przy użyciu rzeczywistego adresu URL logowania i identyfikatora, który został wyjaśniony w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij pozycję **Pobierz** , aby pobrać **certyfikat (base64)** z określonych opcji zgodnie z wymaganiami i zapisać je na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Dla aplikacji ServiceNow jest dostępna usługa konfigurowania jednym kliknięciem. Oznacza to, że usługa Azure AD może automatycznie skonfigurować w aplikacji ServiceNow uwierzytelnianie oparte na SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfigurowanie usługi ServiceNow** i kliknij pozycję **Wyświetl instrukcje krok po kroku**, aby otworzyć okno Konfiguruj logowanie.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Upewnij się, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usługi ServiceNow do działania. W przeciwnym razie aby ręcznie skonfigurować usługi ServiceNow do korzystania z usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfiguruj logowanie** jednokrotne i skopiuj **adres URL wylogowania, identyfikator usługi Azure AD i adres URL logowania** z sekcji Szybkie informacje.

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

## <a name="configure-servicenow"></a>Konfigurowanie usługi ServiceNow

1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

2. Aktywuj **integrację — wtyczka Instalatora logowania** jednokrotnego z wieloma dostawcami, wykonując następujące czynności:

    a. W okienku nawigacji po lewej stronie znajdź sekcję **System Definition** (Definicja systemu) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Plugins** (Wtyczki).

    ![Aktywowanie wtyczki](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktywowanie wtyczki")

    b. Wyszukaj **integrację — Instalator logowania**jednokrotnego dla wielu dostawców.

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
  
    * Kliknij polecenie **Zapisz**.

5. Istnieją dwa sposoby, za pomocą których można skonfigurować aplikację **ServiceNow** — automatycznie i ręcznie.

6. W celu automatycznego skonfigurowania aplikacji **ServiceNow** wykonaj następujące kroki:

    * Wróć do strony logowania jednokrotnego usługi **ServiceNow** w witrynie Azure Portal.

    * Dla aplikacji ServiceNow jest dostępna usługa konfigurowania jednym kliknięciem. Oznacza to, że usługa Azure AD może automatycznie skonfigurować w aplikacji ServiceNow uwierzytelnianie oparte na SAML. Aby włączyć tę usługę, przejdź do sekcji **Konfiguracja aplikacji ServiceNow** i kliknij pozycję **Konfiguruj aplikację ServiceNow**, aby otworzyć okno Konfiguruj logowanie.

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Wprowadź nazwę wystąpienia usługi ServiceNow, nazwę użytkownika administratora i hasło administratora w formularzu **Konfiguruj logowanie**, a następnie kliknij pozycję **Konfiguruj teraz**. Upewnij się, że podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usługi ServiceNow do działania. W przeciwnym razie, aby ręcznie skonfigurować usługi ServiceNow do korzystania z usługi Azure AD jako dostawcy tożsamości SAML, kliknij pozycję **ręcznie skonfiguruj logowanie** jednokrotne i skopiuj adres URL logowania jednokrotnego **, identyfikator jednostki SAML, a** w sekcji Szybkie informacje.

        ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

    * Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    * W ramach konfiguracji automatycznej wszystkie niezbędne ustawienia są konfigurowane po stronie usługi **ServiceNow**, ale **certyfikat X.509** nie jest domyślnie włączony. Musisz zamapować go ręcznie na swojego dostawcę tożsamości w usłudze ServiceNow. Wykonaj poniższe kroki:

    * W okienku nawigacji po lewej stronie znajdź sekcję **Multi-Provider SSO** (Logowanie jednokrotne u wielu dostawców) za pomocą paska wyszukiwania, a następnie kliknij pozycję **Identity Providers** (Dostawcy tożsamości).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

    * Kliknij automatycznie wygenerowanego dostawcę tożsamości

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurowanie logowania jednokrotnego")

    *  W sekcji **Identity Provider** (Dostawca tożsamości) wykonaj następujące kroki:

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/automatic_config.png "Konfigurowanie logowania jednokrotnego")

        * W polu tekstowym **Nazwa** wpisz nazwę konfiguracji (na przykład **Microsoft Azure federacyjne logowanie**jednokrotne).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Skopiuj wartość **strony głównej usługi ServiceNow** , wklej ją w polu tekstowym **adres URL logowania** w sekcji **usługi servicenow Podstawowa konfiguracja SAML** na Azure Portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **Identyfikator jednostki/** wartość wystawcy, wklej ją w polu tekstowym **Identyfikator** w sekcji **usługi servicenow Podstawowa konfiguracja SAML** na Azure Portal.

        * Upewnij się, że ustawienie **NameID Policy** (Zasady atrybutu NameID) ma wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. 

    * Przewiń w dół do sekcji **X.509 Certificate** (Certyfikat X.509) i wybierz pozycję **Edit** (Edytuj).

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurowanie logowania jednokrotnego")

    * Wybierz certyfikat, a następnie kliknij ikonę strzałki w prawo, aby dodać certyfikat

        ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurowanie logowania jednokrotnego")

    * Kliknij polecenie **Zapisz**.

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

        * W polu tekstowym **Nazwa** wpisz nazwę konfiguracji (na przykład **Microsoft Azure federacyjne logowanie**jednokrotne).

        * Usuń z pola tekstowego wpisaną wartość **Identity Provider's SingleLogoutRequest** (Atrybut SingleLogoutRequest dostawcy tożsamości).

        * Skopiuj wartość **strony głównej usługi ServiceNow** , wklej ją w polu tekstowym **adres URL logowania** w sekcji **usługi servicenow Podstawowa konfiguracja SAML** na Azure Portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Kopiuj **Identyfikator jednostki/** wartość wystawcy, wklej ją w polu tekstowym **Identyfikator** w sekcji **usługi servicenow Podstawowa konfiguracja SAML** na Azure Portal.

        * Upewnij się, że ustawienie **NameID Policy** (Zasady atrybutu NameID) ma wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

        * Kliknij pozycję **Advanced** (Zaawansowane). W polu tekstowym **User Field** (Pole użytkownika) wpisz **email** lub **user_name**, w zależności od tego, jakie pole jest używane do jednoznacznego identyfikowania użytkowników w Twoim wdrożeniu usługi ServiceNow.

            > [!NOTE]
            > Usługę Azure AD można skonfigurować do emisji identyfikatora użytkownika usługi Azure AD (główna nazwa użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML, przechodząc do sekcji **usługi servicenow > atrybuty > logowania** jednokrotnego w Azure Portal i mapowania żądane pole do atrybutu **NameIdentifier** . Wartość wybranego atrybutu przechowywana w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością wprowadzonego pola przechowywaną w usłudze ServiceNow (na przykład user_name)

        * Kliknij pozycję **Test Connection** (Testuj połączenie) w prawym górnym rogu strony.

        * Po kliknięciu pozycji **Test Connection** (Testuj połączenie) zostanie wyświetlone wyskakujące okienko, w którym musisz wprowadzić poświadczenia. Zostanie wyświetlona poniższa strona z wynikami. Błąd **SSO Logout Test Results** (Wyniki testu wylogowywania logowania jednokrotnego) jest spodziewany. Zignoruj go i kliknij przycisk **Activate** (Aktywuj).

          ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")

### <a name="create-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze ServiceNow. Aplikacja ServiceNow obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](servicenow-provisioning-tutorial.md).

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej klienta usługi ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="configure-servicenow-express-sso"></a>Konfigurowanie logowania jednokrotnego usługi ServiceNow Express

1. Zaloguj się do swojej aplikacji ServiceNow Express jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Logowanie**jednokrotne.

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/ic7694980ex.png "Konfigurowanie adresu URL aplikacji")

3. W oknie dialogowym **Logowanie** jednokrotne kliknij ikonę konfiguracji w prawym górnym rogu i ustaw następujące właściwości:

    ![Konfigurowanie adresu URL aplikacji](./media/servicenow-tutorial/ic7694981ex.png "Konfigurowanie adresu URL aplikacji")

    a. Przestaw przełącznik **Enable multiple provider SSO** (Włącz logowanie jednokrotne u wielu dostawców) w prawo.

    b. Przestaw przełącznik **Enable debug logging for the multiple provider SSO integration** (Włącz rejestrowanie debugowania dla integracji logowania jednokrotnego u wielu dostawców) w prawo.

    c. W polu tekstowym **The field on the user table that...** (Pole w tabeli użytkownika, które...) wpisz **user_name**.

4. W oknie dialogowym **Logowanie** jednokrotne kliknij pozycję **Dodaj nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694973ex.png "Konfigurowanie logowania jednokrotnego")

5. W oknie dialogowym **X.509 Certificates** (Certyfikaty X.509) wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/servicenow-tutorial/ic7694975.png "Konfigurowanie logowania jednokrotnego")

    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę swojej konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz pozycję **Active** (Aktywne).

    c. Dla opcji **Format** wybierz ustawienie **PEM**.

    d. Dla opcji **Type** (Typ) wybierz ustawienie **Trust Store Cert** (Certyfikat magazynu zaufania).

    e. Otwórz w Notatniku certyfikat zakodowany w formacie Base64 pobrany z witryny Azure Portal, skopiuj zawartość do Schowka, a następnie wklej ją w polu tekstowym **PEM Certificate** (Certyfikat PEM).

    f. Kliknij pozycję **Update** (Aktualizuj)

6. W oknie dialogowym **Logowanie** jednokrotne kliknij pozycję **Dodaj nowe dostawcy tożsamości**.

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
    > Usługę Azure AD można skonfigurować do emisji identyfikatora użytkownika usługi Azure AD (główna nazwa użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML, przechodząc do sekcji **usługi servicenow > atrybuty > logowania** jednokrotnego w Azure Portal i mapowania żądane pole do atrybutu **NameIdentifier** . Wartość wybranego atrybutu przechowywana w usłudze Azure AD (na przykład główna nazwa użytkownika) musi być zgodna z wartością wprowadzonego pola przechowywaną w usłudze ServiceNow (na przykład user_name)

    f. Kliknij polecenie **Zapisz**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka usługi ServiceNow w panelu dostępu należy automatycznie zalogować się do usługi ServiceNow, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Testowanie logowania jednokrotnego dla usługi ServiceNow klasyczny (Mobile)

1. Otwórz aplikację **klasyczną usługi ServiceNow (Mobile)** i wykonaj następujące czynności:

    a. Kliknij przycisk **Dodaj** symbol poniżej ekranu.

    ![Logowanie](./media/servicenow-tutorial/test03.png)

    b. Wpisz nazwę wystąpienia usługi ServiceNow, a następnie kliknij przycisk **Kontynuuj**.

    ![Logowanie](./media/servicenow-tutorial/test04.png)

    c. Na ekranie **logowania** wykonaj następujące czynności:

    ![Logowanie](./media/servicenow-tutorial/test01.png)

    *  Wpisz **nazwę użytkownika** B.simon@contoso.com.

    *  Kliknij pozycję **Użyj logowania zewnętrznego** i nastąpi przekierowanie do strony usługi Azure AD w celu zalogowania się.
    
    *  Wprowadź swoje poświadczenia, a jeśli istnieje jakiekolwiek uwierzytelnianie innej firmy lub jakakolwiek inna funkcja zabezpieczeń, użytkownik będzie musiał odpowiednio odpowiedzieć i zostanie wyświetlona **Strona główna** aplikacji, jak pokazano poniżej:

        ![Strona główna](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](servicenow-provisioning-tutorial.md)

- [Wypróbuj usługi ServiceNow z usługą Azure AD](https://aad.portal.azure.com)