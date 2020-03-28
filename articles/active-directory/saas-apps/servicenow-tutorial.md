---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą ServiceNow | Dokumenty firmy Microsoft'
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c18613233d6dec59c76db120ed7f089dfbb5fbac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046717"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą ServiceNow

W tym samouczku dowiesz się, jak zintegrować servicenow z usługą Azure Active Directory (Azure AD). Podczas integrowania usługi ServiceNow z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do ServiceNow.
* Włącz użytkownikom automatyczne logowanie do usługi ServiceNow za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacją —SaaS( SaaS), zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego ServiceNow.
* Dla ServiceNow wystąpienia lub dzierżawy ServiceNow, wersja Calgary lub nowsza.
* Dla ServiceNow Express wystąpienie ServiceNow Express, helsinki wersji lub nowszej.
* W dzierżawie usługi ServiceNow musi być włączona wtyczka [Multiple Provider Single Sign On Plugin](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) (wtyczka logowania jednokrotnego u wielu dostawców). Można to zrobić, [przesyłając żądanie usługi](https://hi.service-now.com).
* W przypadku konfiguracji automatycznej włącz wtyczkę wielu dostawców dla usługi ServiceNow.
* Aby zainstalować aplikację ServiceNow Classic (Mobile), przejdź do odpowiedniego magazynu i wyszukaj aplikację ServiceNow Classic. Następnie pobierz go.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 

* ServiceNow obsługuje syto inicjowane przez usługę **SP.**

* ServiceNow obsługuje [automatyczne inicjowanie obsługi administracyjnej użytkowników](servicenow-provisioning-tutorial.md).

* Po skonfigurowaniu ServiceNow można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* Aplikację ServiceNow Classic (Mobile) można skonfigurować za pomocą usługi Azure AD w celu włączenia usługi SSO. Obsługuje zarówno użytkowników Androida, jak i iOS. W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

## <a name="add-servicenow-from-the-gallery"></a>Dodawanie servicenow z galerii

Aby skonfigurować integrację usługi ServiceNow z usługą Azure AD, musisz dodać usługę ServiceNow z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub szkolnego lub przy użyciu osobistego konta Microsoft.
1. W lewym okienku wybierz usługę **Azure Active Directory.**
1. Przejdź do aplikacji **dla przedsiębiorstw**i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **servicenow** w polu wyszukiwania.
1. Wybierz **serviceNow** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi ServiceNow

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą serviceNow przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze ServiceNow.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą servicenow, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
    1. [Skonfiguruj usługę Azure AD SSO dla usługi ServiceNow Express,](#configure-azure-ad-sso-for-servicenow-express) aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj servicenow,](#configure-servicenow) aby skonfigurować ustawienia logowania przysłłowiowego po stronie aplikacji.
    1. [Utwórz użytkownika testowego ServiceNow,](#create-servicenow-test-user) aby mieć odpowiednik B.Simon w ServiceNow, połączone z reprezentacji usługi Azure AD użytkownika.
    1. [Skonfiguruj logowanie jednokrotne servicenow express,](#configure-servicenow-express-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.  
3. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.
4. [Przetestuj syg jako pierwszy dla servicenow classic (mobile),](#test-sso-for-servicenow-classic-mobile) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie integracji aplikacji **ServiceNow** znajdź sekcję **Zarządzaj.** Wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego ze stroną SAML z wyróżnioną ikoną pióra](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W **polu Zaloguj się na adres URL**wprowadź adres URL, który używa następującego wzorca:`https://<instance-name>.service-now.com/navpage.do`

    b. W **identyfikatorze (identyfikatorze jednostki)** wprowadź adres URL, który używa następującego wzorca:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy zaktualizować te wartości za pomocą rzeczywistego adresu URL logowania i identyfikatora, który jest wyjaśniony w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** w sekcji Saml Signing Certificate (Certyfikat **podpisywania SAML)** znajdź **pozycję Certyfikat (Base64).** 

   ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnioną pokaźą Pobierania](common/certificatebase64.png)

   a. Wybierz przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji,** i wklej go do Notatnika. Ten adres URL będzie używany w dalszej części samouczka.

    b. Wybierz **pozycję Pobierz,** aby pobrać **certyfikat (Base64),** a następnie zapisz plik certyfikatu na komputerze.

1. W sekcji **Konfigurowanie ServiceNow** skopiuj odpowiednie adresy URL na podstawie wymagań użytkownika.

   ![Zrzut ekranu przedstawiający sekcję Konfigurowanie servicenow z wyróżnionymi adresami URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B.Simon w witrynie Azure portal.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W **yjmij nazwę**, wprowadź `B.Simon`.  
   1. W przypadku **nazwy** username@companydomain.extensionużytkownika wprowadź plik . Na przykład `B.Simon@contoso.com`.
   1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość wyświetlaną w polu **Hasło.**
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do ServiceNow.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **ServiceNow**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi wyróżnionymi wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu przedstawiający użytkowników i grupy z wyróżnioną podaniem dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy użytkowników, a następnie wybierz pozycję **Wybierz**.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Następnie wybierz pozycję **Wybierz**.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurowanie usługi Azure AD SSO dla usługi ServiceNow Express

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **ServiceNow** wybierz opcję **logowania jednokrotnego**.

    ![Zrzut ekranu przedstawiający stronę integracji aplikacji ServiceNow z wyróżnioną wykąpienia jednokrotnym](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Zrzut ekranu przedstawiający opcję Wybierz metodę logowania jednokrotnego z wyróżnioną wyróżnioną funkcją SAML](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę pióra, aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML.**

    ![Zrzut ekranu przedstawiający konfigurowanie logowania jednokrotnego na stronie SAML z wyróżnioną ikoną pióra](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu **Wpisz adres URL**logowania wprowadź adres URL, który używa następującego wzorca:`https://<instance-name>.service-now.com/navpage.do`

    b. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL, który używa następującego wzorca:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy zaktualizować te wartości za pomocą rzeczywistego adresu URL logowania i identyfikatora, który jest wyjaśniony w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** wybierz pozycję **Pobierz,** aby pobrać **certyfikat (Base64)** z określonych opcji, zgodnie z wymaganiami. Zapisz go na komputerze.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnioną pokaźą Pobierania](common/certificatebase64.png)

6. Usługa Azure AD może automatycznie konfigurować usługę ServiceNow dla uwierzytelniania opartego na saml. Aby włączyć tę usługę, przejdź do sekcji **Konfigurowanie servicenow** i wybierz pozycję **Wyświetl instrukcje krok po kroku,** aby otworzyć okno **Konfigurowanie logowania.**

    ![Zrzut ekranu przedstawiający sekcję Konfigurowanie usługiUnow z wyróżnionymi instrukcjami krok po kroku](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. W formularzu **Konfigurowanie logowania** wprowadź nazwę wystąpienia ServiceNow, nazwę użytkownika administratora i hasło administratora. Wybierz **pozycję Konfiguruj teraz**. Podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby to działało. W przeciwnym razie, aby ręcznie skonfigurować usługę ServiceNow do używania usługi Azure AD jako dostawcy tożsamości SAML, wybierz opcję **Ręcznie konfiguruj logowanie jednokrotne**. Skopiuj **adres URL wylogowania, identyfikator usługi Azure AD i adres URL logowania** z sekcji Podręczne dane referencyjne.

    ![Zrzut ekranu przedstawiający formularz logowania konfigurowania z wyróżnioną wyróżnioną pozycją Konfiguruj teraz](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

## <a name="configure-servicenow"></a>Konfigurowanie usługi Teraz

1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

2. Aktywuj wtyczkę **Integration - Multiple Provider, logowanie jednokrotne,** wykonując następujące kroki:

    a. W lewym okienku wyszukaj sekcję **Definicja systemu** w polu wyszukiwania, a następnie wybierz pozycję **Wtyczki**.

    ![Zrzut ekranu przedstawiający sekcję Definicji systemu z wyróżnioną definicją systemu i wtyczkami](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktywuj wtyczkę")

    b. Wyszukaj **integrację — Instalator logowania jednokrotnego wielu dostawców**.

     ![Zrzut ekranu przedstawiający stronę Wtyczki systemowe z wyróżnioną integracją — instalator logowania jednokrotnego wielu dostawców](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktywuj wtyczkę")

    d. Wybierz wtyczkę. Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Aktywuj/Uaktualnij**.

     ![Zrzut ekranu przedstawiający menu typu plug-in z wyróżnionym poleceniem Aktywuj/Uaktualnij](./media/servicenow-tutorial/tutorial_activate.png "Aktywuj wtyczkę")

    d. Wybierz **pozycję Aktywuj**.

     ![Zrzut ekranu przedstawiający okno dialogowe Aktywowanie wtyczki z wyróżnioną wyróżnioną funkcją Aktywuj](./media/servicenow-tutorial/tutorial_activate1.png "Aktywuj wtyczkę")

3. W lewym okienku wyszukaj sekcję **Samosojom samoso** dla wielu dostawców na pasku wyszukiwania, a następnie wybierz pozycję **Właściwości**.

    ![Zrzut ekranu przedstawiający sekcję SSO dla wielu dostawców z wyróżnionym łączem samosoi dla wielu dostawców i właściwości](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurowanie adresu URL aplikacji")

4. W oknie dialogowym **Właściwości jednokrotne wielu dostawców** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Właściwości jednokrotnego wielu dostawców](./media/servicenow-tutorial/ic7694981.png "Konfigurowanie adresu URL aplikacji")

    * Aby **włączyć funkcję SSO dla wielu dostawców,** wybierz opcję **Tak**.
  
    * Aby **włączyć automatyczne importowanie użytkowników ze wszystkich dostawców tożsamości do tabeli użytkowników,** wybierz opcję **Tak**.

    * Dla **włącz rejestrowanie debugowania dla integracji logowania jednokrotnego wielu dostawców,** wybierz **tak**.

    * W polu **w tabeli użytkownika, które...**, wprowadź **user_name**.
  
    * Wybierz **pozycję Zapisz**.

6. Usługę ServiceNow można skonfigurować automatycznie lub ręcznie. Aby skonfigurować usługę ServiceNow automatycznie, wykonaj następujące czynności:

    1. Wróć do strony logowania jednokrotnego **ServiceNow** w witrynie Azure portal.

    1. Usługa konfiguracji jednym kliknięciem jest dostępna dla ServiceNow. Aby włączyć tę usługę, przejdź do sekcji **Konfiguracja ServiceNow** i wybierz pozycję **Konfiguruj usługę Teraz,** aby otworzyć okno **Konfigurowanie logowania.**

        ![Zrzut ekranu przedstawiający pozycję Konfigurowanie usługi ServiceNow z wyróżnioną wyróżnioną instrukcją krok po kroku](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. W formularzu **Konfigurowanie logowania** wprowadź nazwę wystąpienia ServiceNow, nazwę użytkownika administratora i hasło administratora. Wybierz **pozycję Konfiguruj teraz**. Podana nazwa użytkownika administratora musi mieć przypisaną rolę **security_admin** w usłudze ServiceNow, aby to działało. W przeciwnym razie, aby ręcznie skonfigurować usługę ServiceNow do używania usługi Azure AD jako dostawcy tożsamości SAML, wybierz opcję **Ręcznie konfiguruj logowanie jednokrotne**. Skopiuj **adres URL wylogowania, identyfikator jednostki SAML i adres URL usługi logowania jednokrotnego SAML** z sekcji Podręczna dokumentacja.

        ![Zrzut ekranu przedstawiający formularz logowania konfigurowania z wyróżnioną wyróżnioną pozycją Konfiguruj teraz](./media/servicenow-tutorial/configure.png "Konfigurowanie adresu URL aplikacji")

    1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

       * W konfiguracji automatycznej wszystkie niezbędne ustawienia są konfigurowane po stronie **ServiceNow,** ale **certyfikat X.509** nie jest domyślnie włączony. Musisz zamapować go ręcznie do dostawcy tożsamości w ServiceNow. Wykonaj następujące kroki:

         1. W lewym okienku wyszukaj sekcję **samosojesznik typumika** dla wielu dostawców w polu wyszukiwania i wybierz pozycję **Dostawcy tożsamości**.

            ![Zrzut ekranu przedstawiający sekcję samosojone dla wielu dostawców z wyróżnioną pozycją dostawców tożsamości](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

         1. Wybierz automatycznie wygenerowanego dostawcę tożsamości.

            ![Zrzut ekranu przedstawiający dostawców tożsamości z wyróżnionym automatycznie wygenerowanym dostawcą tożsamości](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurowanie logowania jednokrotnego")

         1.  W sekcji **Identity Provider** (Dostawca tożsamości) wykonaj następujące kroki:

             ![Zrzut ekranu przedstawiający sekcję Dostawca tożsamości](./media/servicenow-tutorial/automatic_config.png "Konfigurowanie logowania jednokrotnego")

               * W obszarze **Nazwa**wprowadź nazwę konfiguracji (na przykład **microsoft azure federacyjne logowanie jednokrotne).**

               * Usuń wartość **SingleLogoutRequest** dostawcy tożsamości z pola tekstowego.

               * Skopiuj wartość **strony głównej ServiceNow** i wklej ją w **adresie URL logowania** w sekcji **ServiceNow Basic SAML Configuration** w witrynie Azure portal.

                  > [!NOTE]
                  > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

              * Skopiuj wartość **identyfikatora jednostki / wystawcy** i wklej ją w **identyfikatorze** w sekcji **ServiceNow Basic SAML Configuration** w witrynie Azure portal.

              * Upewnij się, że zasada `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` **NameID** jest ustawiona na wartość. 

         1. Przewiń w dół do sekcji **Certyfikat X.509** i wybierz pozycję **Edytuj**.

             ![Zrzut ekranu przedstawiający sekcję Certyfikat X.509 z wyróżnioną pokażeniem Edytuj](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurowanie logowania jednokrotnego")

         1. Wybierz certyfikat i wybierz ikonę strzałki w prawo, aby dodać certyfikat

            ![Zrzut ekranu przedstawiający kolekcję z wyróżnioną ikoną certyfikatu i strzałki w prawo](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurowanie logowania jednokrotnego")

          1. Wybierz **pozycję Zapisz**.

          1. W prawym górnym rogu strony wybierz pozycję **Testuj połączenie**.

             ![Zrzut ekranu przedstawiający stronę z wyróżnioną pozycją Test Connection](./media/servicenow-tutorial/tutorial_activate2.png "Aktywuj wtyczkę")

          1. Gdy zostaniesz poproszony o podanie danych uwierzytelniających, wprowadź je. Zobaczysz następną stronę. Oczekiwany jest błąd **wyników testu logowania** logowania logowania. Zignoruj błąd i wybierz pozycję **Aktywuj**.

             ![Zrzut ekranu przedstawiający stronę Wyniki testów](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")
  
6. Aby skonfigurować **usługę ServiceNow** ręcznie, wykonaj następujące czynności:

    1. Zaloguj się do swojej aplikacji ServiceNow jako administrator.

    1. W lewym okienku wybierz pozycję **Dostawcy tożsamości**.

        ![Zrzut ekranu przedstawiający wielodostawowy dokument SSO z wyróżnionymi dostawcami tożsamości](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurowanie logowania jednokrotnego")

    1. W oknie dialogowym **Dostawcy tożsamości** wybierz pozycję **Nowy**.

        ![Zrzut ekranu przedstawiający okno dialogowe Dostawcy tożsamości z wyróżnioną pojednawką Nową](./media/servicenow-tutorial/ic7694977.png "Konfigurowanie logowania jednokrotnego")

    1. W oknie dialogowym **Dostawcy tożsamości** wybierz pozycję **SAML**.

        ![Zrzut ekranu przedstawiający okno dialogowe Dostawcy tożsamości z wyróżnioną wyróżnioną wyróżnioną funkcją SAML](./media/servicenow-tutorial/ic7694978.png "Konfigurowanie logowania jednokrotnego")

    1. W **zaimportuj metadane dostawcy tożsamości**wykonaj następujące czynności:

        ![Zrzut ekranu przedstawiający metadane dostawcy tożsamości importu z wyróżnionym adresem URL i importem](./media/servicenow-tutorial/idp.png "Konfigurowanie logowania jednokrotnego")

        1. Wprowadź **adres URL metadanych federacji aplikacji** skopiowany z witryny Azure Portal.

        1. Wybierz pozycję **Import**.

    1. Odczytuje adres URL metadanych IdP i wypełnia wszystkie informacje o polach.

        ![Zrzut ekranu przedstawiający dostawcę tożsamości](./media/servicenow-tutorial/ic7694982.png "Konfigurowanie logowania jednokrotnego")

        * W obszarze **Nazwa**wprowadź nazwę konfiguracji (na przykład **microsoft azure federacyjne logowanie jednokrotne).**

        * Usuń wartość **SingleLogoutRequest** dostawcy tożsamości z pola tekstowego.

        * Skopiuj wartość **strony głównej ServiceNow.** Wklej go w **adresie URL logowania** w sekcji **Konfiguracja saml podstawowy servicenow** w witrynie Azure portal.

            > [!NOTE]
            > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład:`https://fabrikam.service-now.com/navpage.do`).

        * Skopiuj wartość **identyfikatora jednostki / wystawcy.** Wklej go w **identyfikatorze** w sekcji **Konfiguracja saml podstawowy servicenow** w witrynie Azure portal.

        * Upewnij się, że zasada `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` **NameID** jest ustawiona na wartość.

        * Wybierz **pozycję Zaawansowane**. W **polu użytkownika**wprowadź adres **e-mail** lub **user_name**, w zależności od tego, które pole jest używane do jednoznacznej identyfikacji użytkowników we wdrożeniu ServiceNow.

            > [!NOTE]
            > Można skonfigurować usługę Azure AD do emitowania identyfikatora użytkownika usługi Azure AD (nazwa główna użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML. W tym celu przechodząc do **servicenow** > **atrybuty** > **logowania jednokrotnego** sekcji witryny Azure portal i mapowanie żądanego pola do **atrybutu nameidentifier.** Wartość przechowywana dla wybranego atrybutu w usłudze Azure AD (na przykład nazwa główna użytkownika) musi być zgodna z wartością przechowywaną w servicenow dla wprowadzonego pola (na przykład user_name).

        * Wybierz **opcję Testuj połączenie** w prawym górnym rogu strony.

        * Gdy zostaniesz poproszony o podanie danych uwierzytelniających, wprowadź je. Zobaczysz następną stronę. Oczekiwany jest błąd **wyników testu logowania** logowania logowania. Zignoruj błąd i wybierz pozycję **Aktywuj**.

          ![Zrzut ekranu przedstawiający stronę Wyniki testów](./media/servicenow-tutorial/servicenowactivate.png "Konfigurowanie logowania jednokrotnego")

### <a name="create-servicenow-test-user"></a>Tworzenie użytkownika testowego usługi ServiceNow

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w ServiceNow. ServiceNow obsługuje automatyczne inicjowanie obsługi administracyjnej użytkowników, która jest domyślnie włączona.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta ServiceNow](https://www.servicenow.com/support/contact-support.html).

### <a name="configure-servicenow-express-sso"></a>Konfigurowanie usługi ServiceNow Express — sygatora SYC

1. Zaloguj się do swojej aplikacji ServiceNow Express jako administrator.

2. W lewym okienku wybierz pozycję **Logowanie jednokrotne**.

    ![Zrzut ekranu przedstawiający aplikację ServiceNow Express z wyróżnioną wykąpią się z logiem jednokrotnym](./media/servicenow-tutorial/ic7694980ex.png "Konfigurowanie adresu URL aplikacji")

3. W oknie dialogowym **Logowanie jednokrotne** wybierz ikonę konfiguracji w prawym górnym rogu i ustaw następujące właściwości:

    ![Zrzut ekranu przedstawiający okno dialogowe Logowanie jednokrotne](./media/servicenow-tutorial/ic7694981ex.png "Konfigurowanie adresu URL aplikacji")

    a. Przestaw przełącznik **Enable multiple provider SSO** (Włącz logowanie jednokrotne u wielu dostawców) w prawo.

    b. Przestaw przełącznik **Enable debug logging for the multiple provider SSO integration** (Włącz rejestrowanie debugowania dla integracji logowania jednokrotnego u wielu dostawców) w prawo.

    d. W **polu w tabeli użytkownika, które...**, wprowadź **user_name**.

4. W oknie dialogowym **Logowanie jednokrotne** wybierz pozycję **Dodaj nowy certyfikat**.

    ![Zrzut ekranu przedstawiający okno dialogowe Logowanie jednokrotne z wyróżnioną podaniem nowego certyfikatu](./media/servicenow-tutorial/ic7694973ex.png "Konfigurowanie logowania jednokrotnego")

5. W oknie dialogowym **Certyfikaty X.509** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Certyfikaty X.509](./media/servicenow-tutorial/ic7694975.png "Konfigurowanie logowania jednokrotnego")

    a. W przeglądarce **Nazwa**wprowadź nazwę konfiguracji (na przykład: **TestSAML2.0**).

    b. Wybierz pozycję **Active** (Aktywne).

    d. W przypadku **opcji Format**wybierz polecenie **PEM**.

    d. W obszarze **Typ**wybierz pozycję **Cert magazynu zaufania**.

    e. Otwórz certyfikat zakodowany w standardzie Base64 pobrany z witryny Azure portal w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej ją do pola tekstowego **Certyfikat PEM.**

    f. Wybierz **pozycję Aktualizuj**

6. W oknie dialogowym **Logowanie jednokrotne** wybierz pozycję **Dodaj nowy idP**.

    ![Zrzut ekranu przedstawiający okno dialogowe Logowanie jednokrotne z wyróżnionym znakiem Add New IdP](./media/servicenow-tutorial/ic7694976ex.png "Konfigurowanie logowania jednokrotnego")

7. W oknie dialogowym **Dodawanie nowego dostawcy tożsamości** w obszarze **Konfigurowanie dostawcy tożsamości**wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości](./media/servicenow-tutorial/ic7694982ex.png "Konfigurowanie logowania jednokrotnego")

    a. W **yjmij nazwę**, wprowadź nazwę konfiguracji (na przykład: **SAML 2.0**).

    b. W przypadku **adresu URL dostawcy tożsamości**wklej wartość identyfikatora dostawcy tożsamości skopiowanego z witryny Azure portal.

    d. W przypadku **AuthnRequest dostawcy tożsamości**wklej wartość adresu URL żądania uwierzytelniania skopiowanego z witryny Azure portal.

    d. W przypadku **wyjątku SingleLogoutRequest dostawcy tożsamości**wklej wartość adresu URL wylogowania skopiowanego z witryny Azure portal.

    e. W przypadku **certyfikatu dostawcy tożsamości**wybierz certyfikat utworzony w poprzednim kroku.

8. Wybierz pozycję **Ustawienia zaawansowane**. W obszarze **Właściwości dodatkowego dostawcy tożsamości**wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości z wyróżnioną pojednawcze ustawieniami zaawansowanymi](./media/servicenow-tutorial/ic7694983ex.png "Konfigurowanie logowania jednokrotnego")

    a. W przypadku **powiązania protokołu dla pojedynczego zapisu IDP**wprowadź **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

    b. Dla **NameID Policy**, wprowadź **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

    d. W przypadku **metody AuthnContextClassRef**wprowadź . `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`

    d. W przypadku **opcji Utwórz AuthnContextClass**przełącz ją na wyłączona (niezaznaczona).

9. W obszarze **Additional Service Provider Properties** (Dodatkowe właściwości dostawcy usług) wykonaj następujące kroki:

    ![Zrzut ekranu przedstawiający okno dialogowe Dodawanie nowego dostawcy tożsamości z wyróżnionymi różnymi właściwościami](./media/servicenow-tutorial/ic7694984ex.png "Konfigurowanie logowania jednokrotnego")

    a. W przypadku **strony głównej ServiceNow**wprowadź adres URL strony głównej wystąpienia ServiceNow.

    > [!NOTE]
    > Strona główna wystąpienia usługi ServiceNow składa się z **adresu URL Twojej dzierżawy ServiceNow** i ciągu **/navpage.do** (na przykład: `https://fabrikam.service-now.com/navpage.do`).

    b. W przypadku **identyfikatora jednostki / wystawcy**wprowadź adres URL dzierżawy ServiceNow.

    d. W przypadku **identyfikatora URI odbiorców**wprowadź adres URL dzierżawy ServiceNow.

    d. Dla **Clock Skew**, wprowadź **60**.

    e. W polu **Użytkownika**wprowadź **adres e-mail** lub **user_name**, w zależności od tego, które pole jest używane do jednoznacznej identyfikacji użytkowników we wdrożeniu ServiceNow.

    > [!NOTE]
    > Można skonfigurować usługę Azure AD do emitowania identyfikatora użytkownika usługi Azure AD (nazwa główna użytkownika) lub adresu e-mail jako unikatowego identyfikatora w tokenie SAML. W tym celu przechodząc do **servicenow** > **atrybuty** > **logowania jednokrotnego** sekcji witryny Azure portal i mapowanie żądanego pola do **atrybutu nameidentifier.** Wartość przechowywana dla wybranego atrybutu w usłudze Azure AD (na przykład nazwa główna użytkownika) musi być zgodna z wartością przechowywaną w servicenow dla wprowadzonego pola (na przykład user_name).

    f. Wybierz **pozycję Zapisz**.

## <a name="test-sso"></a>Test SSO

Po wybraniu kafelka ServiceNow w Panelu dostępu należy automatycznie zalogować się do ServiceNow, dla którego skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Test SSO dla ServiceNow Classic (Mobile)

1. Otwórz aplikację **ServiceNow Classic (Mobile)** i wykonaj następujące czynności:

    a. Wybierz znak plus w prawym dolnym rogu.

    ![Zrzut ekranu przedstawiający aplikację ServiceNow Classic z podświetlenym znakiem plus](./media/servicenow-tutorial/test03.png)

    b. Wprowadź nazwę wystąpienia ServiceNow i wybierz pozycję **Kontynuuj**.

    ![Zrzut ekranu przedstawiający stronę Dodaj wystąpienie z wyróżnioną wyróżnioną pozycją Kontynuuj](./media/servicenow-tutorial/test04.png)

    d. Na stronie **Logowanie** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawiający stronę Logowania z wyróżnionym funkcją Użyj logowania zewnętrznego](./media/servicenow-tutorial/test01.png)

    *  Wprowadź **nazwę** B.simon@contoso.comużytkownika , jak .

    *  Wybierz **OPCJĘ UŻYJ LOGOWANIA ZEWNĘTRZNEGO**. Zostaniesz przekierowany do strony usługi Azure AD w celu zalogowania się.
    
    *  Wprowadź poświadczenia. Jeśli istnieje uwierzytelnianie innych firm lub włączono jakąkolwiek inną funkcję zabezpieczeń, użytkownik musi odpowiednio odpowiedzieć. Zostanie wyświetlona **strona główna** aplikacji.

        ![Zrzut ekranu przedstawiający stronę główną aplikacji](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](servicenow-provisioning-tutorial.md)

- [Wypróbuj usługę ServiceNow z usługą Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić ServiceNow dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/protect-servicenow)