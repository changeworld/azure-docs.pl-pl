---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją iProva | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395d3887e35d6e9c043d4d947b605e71eb58bd6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60270905"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją iProva

Z tego samouczka dowiesz się, jak zintegrować aplikację iProva z usługą Azure Active Directory (Azure AD).
Integracja aplikacji iProva z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji iProva.
* Możesz zezwolić użytkownikom na automatyczne logowanie do aplikacji iProva (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS (oprogramowania jako usługi) z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją iProva, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej w witrynie internetowej platformy [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja aplikacji iProva z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym:

* Aplikacja iProva obsługuje logowanie jednokrotne inicjowane przez dostawcę usługi.

## <a name="add-iprova-from-the-gallery"></a>Dodawanie aplikacji iProva z galerii

Aby skonfigurować integrację aplikacji iProva z usługą Azure AD, dodaj tę aplikację z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać aplikację iProva z galerii, wykonaj następujące kroki:

1. W [witrynie Azure Portal](https://portal.azure.com) w okienku po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **iProva**. Wybierz pozycję **iProva** na panelu wyników, a następnie wybierz polecenie **Dodaj**, aby dodać aplikację.

     ![Aplikacja iProva na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją iProva, korzystając z danych użytkownika testowego Britta Simon.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji iProva.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją iProva, wykonaj następujące bloki konstrukcyjne:

- [Pobieranie informacji o konfiguracji z aplikacji iProva](#retrieve-configuration-information-from-iprova) — jako przygotowanie do następnych kroków.
- [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
- [Konfigurowanie logowania jednokrotnego aplikacji iProva](#configure-iprova-single-sign-on) — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
- [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
- [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
- [Tworzenie użytkownika testowego aplikacji iProva](#create-an-iprova-test-user) — aby mieć w aplikacji iProva odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
- [Testowanie logowania jednokrotnego](#test-single-sign-on) — aby sprawdzić, czy konfiguracja działa.

### <a name="retrieve-configuration-information-from-iprova"></a>Pobieranie informacji o konfiguracji z aplikacji iProva

W tej sekcji pobierzesz informacje z aplikacji iProva w celu skonfigurowania logowania jednokrotnego usługi Azure AD.

1. Otwórz przeglądarkę internetową i przejdź do **strony z informacjami o formacie SAML2** w aplikacji iProva przy użyciu następującego wzorca adresu URL:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Wyświetlanie strony z informacjami o formacie SAML2 w aplikacji iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Pozostaw otwartą kartę przeglądarki, gdy wykonujesz następne kroki w innej karcie przeglądarki.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji iProva, wykonaj następujące kroki.

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **iProva** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** wybierz ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Ikona Edytuj w oknie Podstawowa konfiguracja protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki.

    a. Wypełnij pole **Identyfikator** przy użyciu wartości wyświetlonej obok etykiety **EntityID** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    b. Wypełnij pole **Adres URL odpowiedzi** przy użyciu wartości wyświetlonej obok etykiety **Adres URL odpowiedzi** na stronie**informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    c. Wypełnij pole **Adres URL logowania** przy użyciu wartości wyświetlonej obok etykiety **Adres URL logowania** na stronie **informacji o formacie SAML2 aplikacji iProva**. Ta strona jest wciąż otwarta w innej karcie przeglądarki.

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji iProva](common/sp-identifier-reply.png)

5. Aplikacja iProva oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** wybierz ikonę **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Okno dialogowe Atrybuty użytkownika](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na poprzedniej ilustracji. Wykonaj następujące kroki.

    | Name (Nazwa) | Atrybut źródłowy| Przestrzeń nazw |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Oświadczenia użytkownika](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    c. Na liście **Przestrzeń nazw** podaj wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Wybierz opcję **Źródło** dla pozycji **Atrybut**.

    e. Na liście **Atrybut źródłowy** podaj wartość atrybutu pokazanego dla tego wiersza.

    f. Wybierz przycisk **OK**.

    g. Wybierz pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** w sekcji **Certyfikat podpisywania SAML** wybierz ikonę **Kopiuj**, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **General** (Ogólne) na panelu **System settings** (Ustawienia systemowe).

5. Wybierz pozycję **Edit** (Edytuj).

6. Przewiń w dół do pozycji **Kontrola dostępu**.

    ![Ustawienia kontroli dostępu aplikacji iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Znajdź ustawienie **Użytkownicy są automatycznie logowani za pomocą kont sieci** i zmień je na **Tak, uwierzytelniaj za pomocą protokołu SAML**. Pojawią się dodatkowe opcje.

8. Wybierz pozycję **Set up** (Konfiguracja).

9. Wybierz opcję **Dalej**.

10. Teraz w aplikacji iProva zostanie wyświetlone pytanie o to, czy pobrać dane federacji z adresu URL, czy przekazać je z pliku. Wybierz opcję **From URL** (Z adresu URL).

    ![Pobieranie metadanych usługi Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Wklej adres URL metadanych zapisany w ostatnim kroku w sekcji „Konfigurowanie logowania jednokrotnego usługi Azure AD”.

12. Wybierz przycisk strzałki, aby pobrać metadane z usługi Azure AD.

13. Po zakończeniu pobierania zostanie wyświetlony komunikat potwierdzający **Valid Federation Data file downloaded** (Pobrano prawidłowy plik danych federacji).

14. Wybierz opcję **Dalej**.

15. Pomiń teraz opcję **Test login** (Testuj logowanie) i wybierz pozycję **Next** (Dalej).

16. Z listy rozwijanej **Claim to use** (Oświadczenie do użycia) wybierz pozycję **windowsaccountname**.

17. Wybierz pozycję **Finish** (Zakończ).

18. Teraz wróć do ekranu **Edytowanie ustawień ogólnych**. Przewiń w dół strony, a następnie wybierz przycisk **OK**, aby zapisać konfigurację.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure Portal w okienku po lewej wybierz kolejno pozycje **Azure Active Directory** > **Użytkownicy** > **Wszyscy użytkownicy**.

    ![Linki Użytkownicy i grupy oraz Wszyscy użytkownicy](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** podaj nazwę, na przykład **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź *twojanazwa\@yourcompanydomain.extension*. 
    Może to być na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji iProva.

1. W witrynie Azure Portal wybierz kolejno pozycje **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje** > **iProva**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Z listy aplikacji wybierz pozycję **iProva**.

    ![Link aplikacji iProva na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okno dialogowe Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście **Użytkownicy**, a następnie użyj pozycji **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz jakiejkolwiek wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybieranie roli** wybierz odpowiednią rolę dla użytkownika z listy. Użyj pozycji **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-iprova-test-user"></a>Tworzenie użytkownika testowego aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Wybierz pozycję **Application management** (Zarządzanie aplikacją).

4. Wybierz pozycję **Users** (Użytkownicy) na panelu **Users and user groups** (Użytkownicy i grupy użytkowników).

5. Wybierz pozycję **Dodaj**.

6. W **Username** wprowadź *brittasimon\@yourcompanydomain.extension*. 
    Może to być na przykład BrittaSimon@contoso.com.

7. W polu **Full name** (Imię i nazwisko) podaj imię i nazwisko, na przykład **BrittaSimon**.

8. Wybierz opcję **Bez hasła (użyj logowania jednokrotnego)**.

9. W **adres E-mail** wprowadź *twojanazwa\@yourcompanydomain.extension*. 
   Może to być na przykład BrittaSimon@contoso.com.

10. Przewiń w dół do końca strony, a następnie wybierz pozycję **Finish** (Zakończ).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka iProva na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji iProva, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva — jak skonfigurować logowanie jednokrotne SAML2](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
