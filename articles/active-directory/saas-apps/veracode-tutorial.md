---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Veracode | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e80a2e554d31ad85ddb1111f84a96e1814c3969f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638824"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Veracode

W tym samouczku dowiesz się, jak zintegrować Veracode w usłudze Azure Active Directory (Azure AD).

Integrowanie Veracode z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Veracode.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Veracode (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Veracode, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Veracode logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj Veracode z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii
Aby skonfigurować integrację Veracode w usłudze Azure AD, należy dodać Veracode z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Veracode z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Veracode**, wybierz opcję **Veracode** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Veracode na liście wyników](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veracode w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Veracode do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Veracode musi można ustanowić.

W Veracode, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veracode, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Veracode](#create-a-veracode-test-user)**  — aby odpowiednikiem Britta Simon w Veracode połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Veracode.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Veracode, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Veracode** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. Na **Veracode domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure. 

    ![Konfigurowanie logowania jednokrotnego](./media/veracode-tutorial/tutorial_veracode_url.png)

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. Celem tej sekcji jest opisują, jak umożliwić użytkownikom na uwierzytelnianie Veracode za pomocą konta w usłudze Azure AD przy użyciu Federacji, na podstawie protokołu SAML.

    Twoja aplikacja Veracode oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych, aby Twoje **atrybuty tokenu języka saml** konfiguracji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Karta Atrybuty](./media/veracode-tutorial/tutorial_veracode_attr.png "Karta Atrybuty")

1. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |
    
    a. Dla każdego wiersza danych w powyższej tabeli, kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![Karta Atrybuty](./media/veracode-tutorial/tutorial_veracode_addattr.png "Karta Atrybuty")
    
    ![Karta Atrybuty](./media/veracode-tutorial/tutorial_veracode_addattr1.png "Karta Atrybuty")
    
    b. W **nazwa atrybutu** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. W **wartość atrybutu** pola tekstowego, wybierz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/veracode-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Veracode** , kliknij przycisk **skonfigurować Veracode** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Veracode, jako administrator.

1. W menu u góry kliknij **ustawienia**, a następnie kliknij przycisk **administratora**.
   
    ![Administracja](./media/veracode-tutorial/ic802911.png "Administracja")

1. Kliknij przycisk **SAML** kartę.

1. W **ustawienia języka SAML organizacji** sekcji, wykonaj następujące czynności:
   
    ![Administracja](./media/veracode-tutorial/ic802912.png "Administracja")
   
    a.  W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
    
    b. Aby przekazać certyfikat pobrany z witryny Azure portal, kliknij przycisk **wybierz plik**.
   
    c. Wybierz **umożliwić samodzielną rejestrację**.

1. W **ustawienia rejestracji samoobsługowego** sekcji, wykonaj następujące czynności, a następnie kliknij **Zapisz**:
   
    ![Administracja](./media/veracode-tutorial/ic802913.png "Administracja")
   
    a. Jako **nowe Aktywacja użytkownika**, wybierz opcję **wymagane uaktywnienie nie**.
   
    b. Jako **dane użytkownika są aktualizowane**, wybierz opcję **dane użytkownika Veracode preferencji**.
   
    c. Aby uzyskać **szczegółów atrybutów SAML**, wybierz następujące pozycje:
      * **Role użytkowników**
      * **Administrator zasad**
      * **Osoba dokonująca przeglądu**
      * **Potencjalny klient zabezpieczeń**
      * **Wykonawczego**
      * **Osoba przesyłająca**
      * **Twórcy**
      * **Wszystkie typy skanowania**
      * **Członkostwo w zespole**
      * **Domyślny zespół**

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/veracode-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/veracode-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/veracode-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-veracode-test-user"></a>Tworzenie użytkownika testowego Veracode
Aby umożliwić użytkownikom usługi Azure AD zalogować się do Veracode, musi być obsługiwana w Veracode. W przypadku Veracode Inicjowanie obsługi to zadania automatycznego. Brak elementu akcji dla Ciebie. Użytkownicy są tworzone automatycznie w razie potrzeby podczas pierwszej pojedynczego logowania jednokrotnego próby.

> [!NOTE]
> Można użyć jakichkolwiek innych Veracode użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Veracode można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Veracode.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Veracode, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Veracode**.

    ![Link Veracode na liście aplikacji](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Veracode w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Veracode.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

