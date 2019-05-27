---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Displayr | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7a2d793a1fbd68d6a71f48b556a77ddcaaaf111
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66112195"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Samouczek: Integracja Displayr za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Displayr w usłudze Azure Active Directory (Azure AD). W ramach Displayr integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Displayr.
* Umożliwianie użytkownikom można automatycznie zalogowany do Displayr za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Displayr logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Displayr **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-displayr-from-the-gallery"></a>Dodawanie Displayr z galerii

Aby skonfigurować integrację Displayr w usłudze Azure AD, należy dodać Displayr z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Displayr** w polu wyszukiwania.
1. Wybierz **Displayr** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Displayr za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Displayr.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Displayr, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Displayr](#configure-displayr)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Displayr](#create-displayr-test-user)**  mieć odpowiednikiem Britta Simon Displayr połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Displayr** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **konfiguracji logowania jednokrotnego przy użyciu protokołu SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YOURDOMAIN>.displayr.com`

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Displayr](mailto:support@displayr.com) do uzyskania tych wartości. Może również odnosić się do wzorców przedstawione w sekcji konfiguracji podstawowej SAML w witrynie Azure portal.

1. Na **konfiguracji logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Aplikacja Displayr oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. Ponadto powyżej Displayr aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W **atrybutów użytkowników i oświadczeń** sekcji na **oświadczenia grupy (wersja zapoznawcza)** okno dialogowe, należy wykonać następujące czynności:

    a. Kliknij przycisk **pióra** obok **grup zwracane w oświadczenie**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Wybierz **wszystkich grup** z listy opcji.

    c. Wybierz **atrybutu źródła** z **identyfikator grupy**.

    d. Sprawdź **dostosować nazwy oświadczenia grupy**.

    e. Sprawdź **emisji grupy jako oświadczenia roli**.

    f. Kliknij pozycję **Zapisz**.

1. Na **Displayr konfiguracji** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurowanie Displayr

1. Aby zautomatyzować konfigurację w ramach Displayr, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Displayr Instalatora** nastąpi bezpośrednie przekierowanie do aplikacji Displayr. W tym miejscu należy podać poświadczenia administratora do logowania się do Displayr. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki od 3 do 6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Displayr, Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy Displayr jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **ustawienia** a następnie przejdź do **konta**.

    ![Konfigurowanie](./media/displayr-tutorial/config01.png)

5. Przełącz się do **ustawienia** z górnego menu i przewiń w dół strony kliknięcie **konfigurowanie pojedynczego logowania na (SAML)**.

    ![Konfigurowanie](./media/displayr-tutorial/config02.png)

6. Na **pojedynczy znak na (SAML)** strony, wykonaj następujące czynności:

    ![Konfigurowanie](./media/displayr-tutorial/config03.png)

    a. Sprawdź **Włącz pojedynczy znak na (SAML)** pole.

    b. Skopiuj wartość rzeczywistą **identyfikator** wartość z **podstawową konfigurację protokołu SAML** sekcji usługi Azure AD i wklej go w **wystawcy** pola tekstowego.

    c. W **adres URL logowania** tekstu pole, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania** tekstu pole, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Otwórz certyfikat (Base64) w programie Notatnik, skopiuj jego zawartość i wklej go do **certyfikatu** pola tekstowego.

    f. **Grupy mapowania** są opcjonalne.

    g. Kliknij pozycję **Zapisz**.  

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Displayr.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Displayr**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-displayr-test-user"></a>Tworzenie użytkownika testowego Displayr

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w celu Displayr, ich musi być obsługiwana w Displayr. W Displayr Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Displayr jako Administrator.

2. Kliknij pozycję **ustawienia** a następnie przejdź do **konta**.

    ![Konfiguracja Displayr](./media/displayr-tutorial/config01.png)

3. Przełącz się do **ustawienia** w górnym menu i przewiń stronę w dół do momentu **użytkowników** sekcji, a następnie kliknij pozycję **nowego użytkownika**.

    ![Konfiguracja Displayr](./media/displayr-tutorial/config07.png)

4. Na **nowego użytkownika** strony, wykonaj następujące czynności:

    ![Konfiguracja Displayr](./media/displayr-tutorial/config06.png)

    a. W **nazwa** tekstu wprowadź nazwę użytkownika, takich jak **Brittasimon**.

    b. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `Brittasimon@contoso.com`.

    c. Wybierz swoje odpowiednie **członkostwo w grupie**.

    d. Kliknij pozycję **Zapisz**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Displayr w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Displayr, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)