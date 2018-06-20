---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu konta Adobe | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Adobe logowania.
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
ms.openlocfilehash: 9a1a1f1a1866e5221775d583a9bafe86eef17131
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221227"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Samouczek: Integracji Azure Active Directory przy użyciu konta Adobe

Z tego samouczka dowiesz się sposobu integracji Podpisz Adobe z usługi Azure Active Directory (Azure AD).

Integracji Podpisz Adobe z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do programu Adobe logowania.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do podpisania Adobe (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji--portalu Azure.

Aby uzyskać więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu konta Adobe, potrzebne są:

- Subskrypcję usługi Azure AD
- Adobe logowania jednokrotnego włączone subskrypcji

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Adobe logowania z galerii.
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne.

## <a name="add-adobe-sign-from-the-gallery"></a>Dodawanie funkcji rejestracji Adobe z galerii
Aby skonfigurować integrację Adobe logowania do usługi Azure AD, należy dodać Adobe logowania z galerii do listy zarządzanych aplikacji SaaS.

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Zrzut ekranu przedstawiający ikonę usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Zrzut ekranu z usługą Azure Active Directory menu, za pomocą aplikacji przedsiębiorstwa i wszystkich aplikacji wyróżnione][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** w górnej części okna dialogowego.

    ![Zrzut ekranu z nowej opcji aplikacji w górnej części okna dialogowego][3]

4. W polu wyszukiwania wpisz **znak Adobe**.

    ![Zrzut ekranu przedstawiający okno wyszukiwania](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. W panelu wyników wybierz **znak Adobe**, a następnie wybierz **Dodaj**.

    ![Zrzut ekranu przedstawiający panel wyników](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu konta Adobe, na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi rozpoznać połączonego relację między użytkownika usługi Azure AD i danemu użytkownikowi w Adobe logowania.

Ustanowienie relacji w Adobe logowania, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username**.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu konta Adobe, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on) umożliwienie użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user) do testowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. [Tworzenie użytkownika testowego znak Adobe](#creating-an-adobe-sign-test-user) w celu zapewnienia odpowiednikiem Simona Britta Adobe znak, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. [Przypisz użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user) umożliwiające Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. [Test rejestracji jednokrotnej](#testing-single-sign-on) Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Adobe logowania.

1. W portalu Azure na **znak Adobe** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Zrzut ekranu logowania Adobe strony integracja aplikacji, z logowania jednokrotnego wyróżnione][4]

2. Na **logowanie jednokrotne** okno dialogowe dla **tryb**, wybierz pozycję **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Zrzut ekranu pojedynczego logowania jednokrotnego dialogowym z wyróżnionym pole tryb](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. W **Adobe logowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Zrzut ekranu Adobe logowania domeny i adresy URL](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. W **adres URL logowania** wpisz adres URL, który korzysta z następującego wzorca: `https://<companyname>.echosign.com/`

    b. W **identyfikator** wpisz adres URL, który korzysta z następującego wzorca: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) uzyskać te wartości.

4. W **certyfikat podpisywania SAML** wybierz opcję **Certificate(Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Zrzut ekranu z SAML certyfikat podpisywania sekcji](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Zrzut ekranu zapisać przycisku](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. W **konfiguracji logowania Adobe** zaznacz **Konfigurowanie rejestrowania programu Adobe** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL**, **SAML identyfikator jednostki**, i **SAML pojedynczy znak na adres URL usługi** z **krótkimi opisami** sekcji.

    ![Sekcja zrzut ekranu z Adobe znak konfiguracji, przy użyciu konta Adobe skonfigurować wyróżnione](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Przed kontynuowaniem konfiguracji, skontaktuj się z [zespołem pomocy technicznej Adobe logowania klienta](https://helpx.adobe.com/in/contact/support.html) listą dozwolonych adresów IP domenę w Adobe logowania. Poniżej przedstawiono sposób dodawania domeny:

    a. [Zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) wysyła losowo wygenerowany token. Domeny, będzie token podobnie do następującej: **adobe znak weryfikacji = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikowanie tokenu weryfikacji w rekordzie DNS tekstu i powiadom [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Może to potrwać kilka dni lub dłużej. Należy zauważyć, że opóźnienie propagacji DNS oznacza, że wartość opublikowany w systemie DNS może nie być widoczne na godzinę lub dłużej. IT administrator powinien być wiedzą o tym, jak opublikować ten token w rekordzie DNS tekstu.
    
    c. Kiedy powiadomić [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) za pośrednictwem biletu pomocy technicznej po opublikowaniu token one zweryfikować domeny i dodaj go do Twojego konta.
    
    d. Ogólnie rzecz biorąc Oto jak opublikować token do rekordu DNS:

    * Zaloguj się do konta domeny
    * Znajdź stronę aktualizacji rekordu DNS. Ta strona może można wywołać zarządzania DNS, nazwy serwera zarządzania lub ustawienia zaawansowane.
    * Znajdowanie rekordów TXT w domenie.
    * Dodaj rekord TXT z wartością pełnej token dostarczony przez Adobe.
    * Zapisz zmiany.

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Adobe logowania.

9. Wybierz z menu SAML **ustawienia konta** > **ustawienia SAML**.
   
    ![Zrzut ekranu programu Adobe logowania SAML ustawienia strony](./media/adobe-echosign-tutorial/ic789520.png "konta")

10. W **ustawienia SAML** sekcji, wykonaj następujące czynności:
  
    ![Zrzut ekranu przedstawiający ustawienia SAML](./media/adobe-echosign-tutorial/ic789521.png "ustawienia SAML")
   
    a. W obszarze **tryb SAML**, wybierz pozycję **SAML obowiązkowe**.
   
    b. Wybierz **Zezwalaj Administratorzy Echosign konta logowania przy użyciu swoich poświadczeń Echosign**.
   
    c. W obszarze **Tworzenie użytkownika**, wybierz pozycję **automatyczne dodawanie użytkowników uwierzytelnionych za pośrednictwem SAML**.

    d. Wklej **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure do **adres URL Identyfikatora/wystawcy jednostki** pola tekstowego.
    
    e. Wklej **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure do **punktu końcowego adresu URL/logowania jednokrotnego logowania** pola tekstowego.
   
    f. Wklej **Sign-Out URL**, które zostały skopiowane z portalu Azure do **punktu końcowego adresu URL/SLO wylogowania** pola tekstowego.

    g. Otwórz z pobranego **Certificate(Base64)** pliku w Notatniku. Skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu IdP** pola tekstowego.

    h. Wybierz **zapisać zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego, o nazwie Simona Britta w portalu Azure.

![Zrzut ekranu przedstawiający nazwę użytkownika testu w portalu Azure][100]

1. W **portalu Azure**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Zrzut ekranu przedstawiający ikonę usługi Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**i wybierz **wszyscy użytkownicy**.
    
    ![Zrzut ekranu Azure AD menu, za pomocą użytkownicy i grupy Wszyscy użytkownicy wyróżnione](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Zrzut ekranu przedstawiający początku wszystkich okno dialogowe Użytkownicy, z wyróżnioną opcją Dodaj](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego wykonaj następujące czynności:
 
    ![Okno dialogowe zrzut ekranu użytkownika](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym **BrittaSimon**.

    b. W **nazwy użytkownika** tekstu wpisz adres e-mail BrittaSimon.

    c. Wybierz **Pokaż hasło**i zanotuj wartość **hasło**.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-adobe-sign-test-user"></a>Tworzenie użytkownika testowego Adobe logowania

Aby umożliwić użytkownikom usługi Azure AD do logowania się na znak Adobe, muszą mieć przydzielone do programu Adobe logowania. Jest to zadanie ręcznie.

>[!NOTE]
>Inne narzędzia do tworzenia konta użytkownika logowania Adobe lub interfejsów API dostarczonych przez znak Adobe służy do obsługi administracyjnej kont użytkowników usługi Azure AD. 

1. Zaloguj się do Twojego **znak Adobe** witryny firmy jako administrator.

2. W menu u góry wybierz **konta**. Następnie w okienku po lewej stronie wybierz **użytkownicy i grupy** > **utworzenie nowego użytkownika**.
   
    ![Zrzut ekranu logowania Adobe firmy lokacji z konta, użytkownicy i grupy i utworzenie nowego użytkownika z wyróżnioną pozycją](./media/adobe-echosign-tutorial/ic789524.png "konta")
   
3. W **Tworzenie nowego użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Zrzut ekranu z tworzenia nowego użytkownika sekcji](./media/adobe-echosign-tutorial/ic789525.png "Tworzenie użytkownika")
   
    a. Typ **adres E-mail**, **imię**, i **nazwisko** prawidłowy Azure AD konta chcesz udostępnić w polach tekstowych powiązanych.
   
    b. Wybierz **Utwórz użytkownika**.

>[!NOTE]
>Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail zawierającą łącze do potwierdzenia konta, zanim staje się aktywny. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure rejestracji jednokrotnej, za udzielanie dostępu do podpisania Adobe.

![Zrzut ekranu Azure portalu rejestracji jednokrotnej][200] 

1. W portalu Azure Otwórz widok aplikacji. Następnie przejdź do widoku katalogu, przejdź do **aplikacje dla przedsiębiorstw**i wybierz **wszystkie aplikacje**.

    ![Zrzut ekranu Azure aplikacje portalu widoku z aplikacjami przedsiębiorstwa i wszystkich aplikacji wyróżnione][201] 

2. Na liście aplikacji zaznacz **znak Adobe**.

    ![Zrzut ekranu przedstawiający listę aplikacji przy użyciu konta Adobe wyróżnione](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Zrzut ekranu przedstawiający menu z użytkownikami i grupami wyróżnione][202] 

4. Wybierz pozycję **Dodaj**. Następnie w **Dodaj przydziału** zaznacz **użytkowników i grup**.

    ![Zrzut ekranu użytkownicy i grupy i dodać sekcji przypisania][203]

5. W **użytkowników i grup** okno dialogowe, na liście użytkowników, wybierz opcję **Simona Britta**.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz**.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Po wybraniu kafelka Adobe logowania w panelu dostępu użytkownik powinien pobrać automatycznie zalogować się przy do aplikacji Adobe logowania. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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
