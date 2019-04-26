---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Leapsome | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260627"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Leapsome

W tym samouczku dowiesz się, jak zintegrować Leapsome w usłudze Azure Active Directory (Azure AD).

Integrowanie Leapsome z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Leapsome.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Leapsome (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Leapsome, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Leapsome logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Leapsome z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-leapsome-from-the-gallery"></a>Dodawanie Leapsome z galerii
Aby skonfigurować integrację Leapsome w usłudze Azure AD, należy dodać Leapsome z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Leapsome z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Leapsome**, wybierz opcję **Leapsome** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Leapsome na liście wyników](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Leapsome w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Leapsome do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Leapsome musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Leapsome, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Leapsome](#create-a-leapsome-test-user)**  — aby odpowiednikiem Britta Simon w Leapsome połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Leapsome.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Leapsome, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Leapsome** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. Na **Leapsome domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Leapsome domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.leapsome.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Leapsome domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Podaną wcześniej wartością adresu URL adresu URL odpowiedzi i logowanie jednokrotne nie jest rzeczywistą wartość. Będą one rzeczywistymi wartościami aktualizacji co jest opisane w dalszej części tego samouczka.

1. Aplikacja Leapsome oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład.
    
    ![Konfigurowanie logowania jednokrotnego](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw |
    | ---------------| --------------- | --------- |   
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Obraz | Adres URL obrazu pracownika | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Wartość atrybutu obraz nie jest prawdziwe. Zaktualizuj tę wartość za pomocą adresu URL rzeczywisty obraz. Aby uzyskać to wartość, skontaktuj się z [zespołem pomocy technicznej klienta Leapsome](mailto:support@leapsome.com).
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. W **Namespace** polu tekstowym wpisz identyfikator uri przestrzeni nazw dla tego wiersza.
    
    e. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji Leapsome** , kliknij przycisk **skonfigurować Leapsome** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. W oknie przeglądarki internetowej innej Zaloguj się do Leapsome jako Administrator zabezpieczeń.

1. W prawym górnym rogu, kliknij pozycję Ustawienia logo, a następnie kliknij przycisk **ustawienia administratora**. 

    ![Zestaw Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na pasku menu po lewej stronie kliknij **logowanie jednokrotne (SSO)**, a następnie na **opartej na SAML logowania jednokrotnego (SSO)** strony należy wykonać następujące czynności:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Wybierz **opartej na SAML włączyć logowanie jednokrotne**.

    b. Kopiuj **adres URL logowania (polecenie użytkowników w tym miejscu Rozpocznij logowania)** wartość i wklej go w **adres URL logowania** polu tekstowym w **Leapsome domena i adresy URL** sekcji w witrynie Azure portal.

    c. Kopiuj **adres URL odpowiedzi (odbiera odpowiedź od dostawcy tożsamości)** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **Leapsome domena i adresy URL** sekcji w witrynie Azure portal.

    d. W **rejestracji Jednokrotnej adres URL logowania (udostępnione przez dostawcę tożsamości)** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który został skopiowany z witryny Azure portal.

    e. Skopiuj certyfikat został pobrany z witryny Azure portal bez — certyfikat rozpoczęcia i zakończenia certyfikatu — komentarze, a następnie wklej je **certyfikatu (dostarczonego przez dostawcę tożsamości)** pola tekstowego.

    f. Kliknij przycisk **aktualizacji ustawień logowania jednokrotnego**.
    
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/leapsome-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/leapsome-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/leapsome-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-leapsome-test-user"></a>Tworzenie użytkownika testowego Leapsome

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Leapsome. Praca z [zespołem pomocy technicznej klienta Leapsome](mailto:support@leapsome.com) można dodać użytkowników lub domeny, który musi być umieszczona na białej liście na platformie Leapsome. Jeśli domena jest dodawany przez zespół, użytkownicy będą automatycznie aprowizowany platformę Leapsome. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Leapsome.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Leapsome, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Leapsome**.

    ![Link Leapsome na liście aplikacji](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Leapsome w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Leapsome.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

