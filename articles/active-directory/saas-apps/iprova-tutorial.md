---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją iProva | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: ba3b0e06630665082b62e070dac64e8bc572f6dc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811710"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją iProva

Z tego samouczka dowiesz się, jak zintegrować aplikację iProva z usługą Azure Active Directory (Azure AD).
Integracja aplikacji iProva z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji iProva.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji iProva (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją iProva, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji iProva z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja iProva obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-iprova-from-the-gallery"></a>Dodawanie aplikacji iProva z galerii

Aby skonfigurować integrację aplikacji iProva z usługą Azure AD, musisz dodać tę aplikację z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację iProva z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **iProva**, wybierz pozycję **iProva** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja iProva na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją iProva, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji iProva.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją iProva, należy utworzyć poniższe bloki konstrukcyjne:

1. **[Pobieranie informacji o konfiguracji z aplikacji iProva](#retrieve-configuration-information-from-iprova)** — jako przygotowanie do następnych kroków.
2. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
3. **[Konfigurowanie logowania jednokrotnego w aplikacji iProva](#configure-iprova-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
4. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
5. **[Tworzenie użytkownika testowego aplikacji iProva](#create-iprova-test-user)** — aby mieć w aplikacji iProva odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
7. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="retrieve-configuration-information-from-iprova"></a>Pobieranie informacji o konfiguracji z aplikacji iProva

W tej sekcji pobierzesz podstawowe informacje z aplikacji iProva.
Te informacje są potrzebne do skonfigurowania usługi Azure AD na potrzeby logowania jednokrotnego

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

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji iProva, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **iProva** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Wypełnij pole **Identyfikator** przy użyciu wartości wyświetlanej za etykietą **EntityID** na **stronie z informacjami o formacie SAML2 w aplikacji iProva** (wciąż otwartej na innej karcie przeglądarki).

    b. Wypełnij pole **Adres URL odpowiedzi** przy użyciu wartości wyświetlanej za etykietą **Adres URL odpowiedzi** na **stronie z informacjami o formacie SAML2 w aplikacji iProva** (wciąż otwartej na innej karcie przeglądarki).

    d. Wypełnij pole **Adres URL logowania** przy użyciu wartości wyświetlanej za etykietą **Adres URL logowania** na **stronie z informacjami o formacie SAML2 w aplikacji iProva** (wciąż otwartej na innej karcie przeglądarki).

    ![Informacje o logowaniu jednokrotnym — domena i adresy URL aplikacji iProva](common/sp-identifier-reply.png)

5. Aplikacja iProva oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa | Atrybut źródłowy| Przestrzeń nazw |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Na liście **Przestrzeń nazw** wpisz wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Kliknij pozycję **Zarządzanie aplikacjami**.

4. Kliknij pozycję **Ogólne** w panelu **Ustawienia systemowe**.

5. Kliknij pozycję **Edytuj**.

6. Przewiń w dół do pozycji **Kontrola dostępu**.

    ![Ustawienia kontroli dostępu aplikacji iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Znajdź ustawienie **Użytkownicy są automatycznie logowani za pomocą kont sieci** i zmień je na **Tak, uwierzytelniaj za pomocą protokołu SAML**. Pojawią się dodatkowe opcje.

8. Kliknij przycisk **Konfiguruj**.

9. Kliknij przycisk **Dalej**.

10. Teraz w aplikacji iProva będzie wyświetlane pytanie o to, czy pobrać dane federacyjne z adresu URL, czy przekazać je z pliku. Wybierz opcję **z adresu URL**.

    ![Pobieranie metadanych usługi Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Wklej adres URL metadanych zapisany w ostatnim kroku sekcji **Konfigurowanie logowania jednokrotnego w usłudze Azure AD**.

12. Kliknij przycisk strzałki, aby pobrać metadane z usługi Azure AD.

13. Po zakończeniu pobierania zostanie wyświetlony komunikat potwierdzający **Pobrano prawidłowy plik danych federacyjnych**.

14. Kliknij przycisk **Dalej**.

15. Pomiń teraz opcję **Logowanie testowe** i kliknij przycisk **Dalej**.

16. Z listy rozwijanej **Oświadczenie do użycia** wybierz pozycję **windowsaccountname**.

17. Kliknij przycisk **Zakończ**.

18. Teraz wróć do ekranu **Edytowanie ustawień ogólnych**. **Przewiń w dół** strony, a następnie kliknij przycisk **OK**, aby zapisać konfigurację.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź nazwę, taką jak **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **yourname@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji iProva.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **iProva**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Z listy aplikacji wybierz pozycję **iProva**.

    ![Link do aplikacji iProva na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-iprova-test-user"></a>Tworzenie użytkownika testowego aplikacji iProva

1. Zaloguj się do aplikacji iProva za pomocą konta **administratora**.

2. Otwórz menu **Przejdź do**.

3. Kliknij pozycję **Zarządzanie aplikacjami**.

4. Kliknij pozycję **Użytkownicy** w panelu **Użytkownicy i grupy użytkowników**.

5. Kliknij przycisk **Dodaj**.

6. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

7. W polu **Imię i nazwisko** wprowadź imię i nazwisko, takie jak **BrittaSimon**.

8. Wybierz opcję **Bez hasła (użyj logowania jednokrotnego)**.

9. W polu **Adres e-mail** wpisz **yourname@yourcompanydomain.extension**, na przykład BrittaSimon@contoso.com

10. Przewiń w dół do końca strony i kliknij przycisk **Zakończ**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka iProva w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji iProva, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [iProva — jak skonfigurować logowanie jednokrotne SAML2](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)