---
title: 'Samouczek: Integracja usługi Azure Active Directory z Adobe Sign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054002"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Samouczek: Integracja usługi Azure Active Directory z Adobe Sign

W tym samouczku dowiesz się, jak zintegrować Adobe Sign w usłudze Azure Active Directory (Azure AD).

Integrowanie Adobe Sign z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Adobe Sign.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Adobe Sign (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Adobe Sign, potrzebne są:

- Subskrypcję usługi Azure AD
- Adobe Sign logowanie jednokrotne włączone subskrypcji

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Adobe Sign z galerii.
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne.

## <a name="add-adobe-sign-from-the-gallery"></a>Dodaj Adobe Sign z galerii
Aby skonfigurować integrację programu Adobe Sign w usłudze Azure AD, należy dodać Adobe Sign z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Zrzut ekranu przedstawiający ikonę usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Menu zrzut ekranu z usługi Azure Active Directory, aplikacje dla przedsiębiorstw i wszystkie aplikacje z wyróżnioną pozycją][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** w górnej części okna dialogowego.

    ![Zrzut ekranu z nowej opcji aplikacji w górnej części okna dialogowego][3]

4. W polu wyszukiwania wpisz **Adobe Sign**.

    ![Zrzut ekranu przedstawiający okno wyszukiwania](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. W panelu wyników wybierz **Adobe Sign**, a następnie wybierz pozycję **Dodaj**.

    ![Zrzut ekranu przedstawiający panel wyników](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD jednokrotne logowanie w usłudze Adobe Sign, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługa Azure AD wymaga rozpoznawania połączone relację między użytkownikiem usługi Azure AD i powiązanych użytkownika w Adobe Sign.

Ustanowienie relacji łączenia w Adobe Sign, przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username**.

Aby skonfigurować i testowanie usługi Azure AD jednokrotne logowanie w usłudze Adobe Sign, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego Adobe Sign](#creating-an-adobe-sign-test-user) mieć odpowiednikiem Britta Simon Adobe Sign, który jest połączony z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#testing-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Adobe Sign.

1. W witrynie Azure portal na **Adobe Sign** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Zrzut ekranu Adobe Sign strona integracji aplikacji, za pomocą logowania jednokrotnego wyróżniony][4]

2. Na **logowanie jednokrotne** okno dialogowe dla **tryb**, wybierz opcję **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Zrzut ekranu pojedynczego logowania jednokrotnego dialogowym pole tryb wyróżniony](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. W **Adobe Sign domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Zrzut ekranu Adobe Sign domena i adresy URL sekcji](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL, który używa następującego wzorca: `https://<companyname>.echosign.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL, który używa następującego wzorca: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) do uzyskania tych wartości.

4. W **certyfikat podpisywania SAML** zaznacz **Certificate(Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Zrzut ekranu z sekcji certyfikat podpisywania SAML](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Zapisz zrzut ekranu przycisku](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. W **Adobe Sign konfiguracji** zaznacz **skonfigurować Adobe Sign** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po** sekcji.

    ![Zrzut ekranu Adobe Sign konfiguracji sekcję skonfigurować Adobe Sign wyróżniony](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Przed kontynuowaniem konfiguracji, skontaktuj się z pomocą [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) do listy dozwolonych swoją domenę na platformie Adobe Sign. Poniżej przedstawiono sposób dodawania domeny:

    a. [Zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) wyśle do Ciebie losowo wygenerowany token. Dla Twojej domeny będzie token, podobnie do następującego: **adobe sign weryfikacji = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikowanie tokenu weryfikacji w rekordzie DNS tekstu i powiadom [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Może to potrwać kilka dni lub dłużej. Należy pamiętać, że opóźnienia propagacji DNS oznacza, że wartość opublikowany w systemie DNS mogą być niewidoczne na godzinę lub dłużej. IT administrator powinien być odpowiednią wiedzę na temat sposobu publikowania tego tokenu w rekordzie DNS tekstu.
    
    c. Gdy powiadomisz [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) za pośrednictwem bilet pomocy technicznej po opublikowaniu token ich weryfikowanie domeny i dodać go do swojego konta.
    
    d. Ogólnie rzecz biorąc poniżej przedstawiono sposób publikowania token rekord DNS:

    * Zaloguj się do swojego konta domeny
    * Znajdź stronę do aktualizowania rekordów DNS. Na tej stronie może mieć nazwę, zarządzanie systemem DNS, nazwę serwera zarządzania lub ustawienia zaawansowane.
    * Znajdź rekordy TXT dla swojej domeny.
    * Dodaj rekord TXT wartością pełną token dostarczony przez firmy Adobe.
    * Zapisz zmiany.

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Adobe Sign.

9. Wybierz z menu SAML **ustawienia konta** > **ustawienia języka SAML**.
   
    ![Zrzut ekranu programu Adobe Sign ustawienia języka SAML strony](./media/adobe-echosign-tutorial/ic789520.png "konta")

10. W **ustawienia języka SAML** sekcji, wykonaj następujące czynności:
  
    ![Zrzut ekranu przedstawiający ustawienia języka SAML](./media/adobe-echosign-tutorial/ic789521.png "ustawienia języka SAML")
   
    a. W obszarze **tryb SAML**, wybierz opcję **SAML obowiązkowe**.
   
    b. Wybierz **Zezwalaj Echosign Administratorzy kont mogą się zalogować przy użyciu swoich poświadczeń Echosign**.
   
    c. W obszarze **Tworzenie użytkownika**, wybierz opcję **automatycznie dodawaj użytkowników uwierzytelnienia przy użyciu protokołu SAML**.

    d. Wklej **identyfikator jednostki SAML**, skopiowanej w witrynie Azure portal do **adres URL Identyfikatora/wystawcy jednostki** pola tekstowego.
    
    e. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal do **punkt końcowy adres URL/logowania jednokrotnego logowania** pola tekstowego.
   
    f. Wklej **adres URL wylogowania**, który skopiowano z witryny Azure portal do **końcowego adresu URL/SLO wylogowywania** pola tekstowego.

    g. Otwórz swoje pobrany **Certificate(Base64)** pliku w Notatniku. Skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu tożsamości** pola tekstowego.

    h. Wybierz **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego, o nazwie Britta Simon w witrynie Azure portal.

![Zrzut ekranu przedstawiający nazwę użytkownika testu w witrynie Azure portal][100]

1. W **witryny Azure portal**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Zrzut ekranu przedstawiający ikona usługi Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**i wybierz **wszyscy użytkownicy**.
    
    ![Menu zrzut ekranu programu Azure AD, za pomocą użytkownicy i grupy Wszyscy użytkownicy z wyróżnioną pozycją](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Zrzut ekranu przedstawiający górnej części wszystkich dialogowym użytkowników z podświetloną opcją Dodaj](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Zrzut ekranu użytkownika, okno dialogowe](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym **BrittaSimon**.

    b. W **nazwa_użytkownika** tekstu wpisz adres e-mail BrittaSimon.

    c. Wybierz **Pokaż hasło**i zanotuj wartość **hasło**.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-adobe-sign-test-user"></a>Tworzenie użytkownika testowego Adobe Sign

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w usłudze Adobe Sign, musi być obsługiwana w Adobe Sign. To zadanie ręczne.

>[!NOTE]
>Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta Adobe Sign użytkownika lub interfejsów API dostarczonych przez Adobe Sign. 

1. Zaloguj się do Twojej **Adobe Sign** witryny firmy jako administrator.

2. W menu u góry wybierz **konta**. Następnie w okienku po lewej stronie wybierz **użytkownikami i grupami** > **Utwórz nowego użytkownika**.
   
    ![Zrzut ekranu Adobe Sign firmowe witryny przy użyciu konta, użytkownikami i grupami i Utwórz nowego użytkownika z wyróżnioną pozycją](./media/adobe-echosign-tutorial/ic789524.png "konta")
   
3. W **Utwórz nowego użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Zrzut ekranu z tworzenia nowego użytkownika sekcji](./media/adobe-echosign-tutorial/ic789525.png "Tworzenie użytkownika")
   
    a. Typ **adres E-mail**, **imię**, i **nazwisko** prawidłowe platformy Azure konto usługi AD do aprowizowania w polach tekstowych powiązane.
   
    b. Wybierz **Utwórz użytkownika**.

>[!NOTE]
>Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail zawierającą link do potwierdzenia konta, zanim stanie się aktywny. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne, za udzielanie dostępu do Adobe Sign.

![Zrzut ekranu Azure portalu rejestracji jednokrotnej][200] 

1. W witrynie Azure portal Otwórz widok aplikacji. Następnie przejdź do widoku katalogu, przejdź do **aplikacje dla przedsiębiorstw**i wybierz **wszystkie aplikacje**.

    ![Zrzut ekranu z platformy Azure aplikacje portalu widoku, za pomocą aplikacji dla przedsiębiorstw i wszystkie aplikacje z wyróżnioną pozycją][201] 

2. Na liście aplikacji wybierz **Adobe Sign**.

    ![Zrzut ekranu przedstawiający listę aplikacji z Adobe Sign wyróżniony](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Zrzut ekranu przedstawiający menu z użytkownikami i grupami wyróżniony][202] 

4. Wybierz pozycję **Dodaj**. Następnie w **Dodaj przydziału** zaznacz **użytkowników i grup**.

    ![Zrzut ekranu użytkowników i grup strony i Dodaj sekcję przypisania][203]

5. W **użytkowników i grup** w listy użytkowników, wybierz w oknie dialogowym **Britta Simon**.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz**.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Po wybraniu kafelka Adobe Sign w panelu dostępu użytkownik powinien uzyskać automatycznie zalogować się do aplikacji Adobe Sign. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
