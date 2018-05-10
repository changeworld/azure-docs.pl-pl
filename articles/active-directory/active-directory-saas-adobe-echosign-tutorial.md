---
title: 'Samouczek: Integracji Azure Active Directory przy użyciu konta Adobe | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Adobe logowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: ff059b27dc2bdcbfa8cecad1e46e9c2f268ef8c7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Samouczek: Integracji Azure Active Directory przy użyciu konta Adobe

Z tego samouczka dowiesz się sposobu integracji Podpisz Adobe z usługi Azure Active Directory (Azure AD).

Integracji Podpisz Adobe z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, kto ma dostęp do programu Adobe logowania
- Umożliwia użytkownikom automatycznie pobrać zalogowane do podpisania Adobe (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu konta Adobe, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Adobe logowania jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Adobe logowania z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-adobe-sign-from-the-gallery"></a>Dodawanie Adobe logowania z galerii
Aby skonfigurować integrację Adobe logowania do usługi Azure AD, należy dodać Adobe logowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Adobe logowania z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **znak Adobe**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. W panelu wyników wybierz **znak Adobe**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej znakiem Adobe oparte na koncie użytkownika testu o nazwie "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w Adobe logowania do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi rejestracja Adobe musi określone.

W Adobe logowania, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD logowania jednokrotnego przy użyciu konta Adobe, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego znak Adobe](#creating-an-adobe-sign-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Adobe znaku, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Adobe logowania.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu konta Adobe, wykonaj następujące czynności:**

1. W portalu Azure na **znak Adobe** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Na **Adobe logowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. Na **konfiguracji logowania Adobe** , kliknij przycisk **Konfigurowanie rejestrowania programu Adobe** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Przed kontynuowaniem konfiguracji należy skontaktować się [zespołem pomocy technicznej Adobe logowania klienta](https://helpx.adobe.com/in/contact/support.html) listą dozwolonych adresów IP domenę w Adobe logowania. Wykonaj następujące czynności, aby dodać domenę:

    a. [Zespół obsługi klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) wyśle losowo wygenerowany token. Domeny, takie jak będzie token: **adobe znak weryfikacji = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Należy opublikować tokenu weryfikacji w rekordzie DNS tekstu i powiadomienie [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Można oczekiwać, że może to potrwać kilka dni lub prawdopodobnie już. Należy zauważyć, że opóźnienie propagacji DNS oznacza, że wartość opublikowany w systemie DNS może nie być widoczne na godzinę lub dłużej. Oczekuje się, że IT administrator powinien być wiedzą o tym, jak opublikować ten token w rekordzie DNS tekstu.
    
    c. Po powiadomieniu [zespołem pomocy technicznej klienta logowania Adobe](https://helpx.adobe.com/in/contact/support.html) za pośrednictwem biletu pomocy technicznej po opublikowaniu token one będzie zweryfikować domeny i dodaj go do Twojego konta.
    
    d. Ogólne kroki należy wykonać, aby opublikować token na rekordu DNS-

    * Zaloguj się do konta domeny
    * Znajdź stronę aktualizacji rekordu DNS. Ta strona może zostać wywołana, zarządzanie systemem DNS, nazwy serwera zarządzania lub ustawienia zaawansowane.
    * Znajdowanie rekordów TXT w domenie.
    * Dodaj rekord TXT z wartością pełnej token dostarczony przez Adobe
    * Zapisz zmiany.

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Adobe logowania.

9. W SAML menu kliknij **ustawienia konta**, a następnie kliknij **SAML ustawienia**.
   
    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "konta")

10. W **ustawienia SAML** sekcji, wykonaj następujące czynności:
  
    ![Ustawienia SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "ustawienia SAML")
   
    a. Jako **tryb SAML**, wybierz pozycję **SAML obowiązkowe**.
   
    b. Wybierz **Zezwalaj Adobe znak Administratorzy konta logowania przy użyciu swoich poświadczeń logowania Adobe**.
   
    c. Jako **Tworzenie użytkownika**, wybierz pozycję **automatyczne dodawanie użytkowników uwierzytelnionych za pośrednictwem SAML**.

    d. Wklej **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure do **adres URL Identyfikatora/wystawcy jednostki** pola tekstowego.
    
    e. Wklej **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure do **punktu końcowego adresu URL/logowania jednokrotnego logowania** pola tekstowego.
   
    f. Wklej **Sign-Out URL**, które zostały skopiowane z portalu Azure do **punktu końcowego adresu URL/SLO wylogowania** pola tekstowego.

    g. Otwórz z pobranego **Certificate(Base64)** plików w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu IdP** pole tekstowe

    h. Kliknij przycisk **zapisać zmiany**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Tworzenie użytkownika testowego Adobe logowania

Aby umożliwić użytkownikom usługi Azure AD zalogować się do logowania Adobe, muszą mieć przydzielone do programu Adobe logowania. W przypadku logowania Adobe Inicjowanie obsługi to zadanie ręczne.

>[!NOTE]
>Możesz użyć innych Adobe logowania użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Adobe logowania do kont użytkowników usługi AAD. 

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **znak Adobe** witryny firmy jako administrator.

2. W menu u góry kliknij **konta**, a następnie w okienku nawigacji po lewej stronie kliknij **użytkownicy i grupy**, a następnie kliknij przycisk **utworzenie nowego użytkownika**.
   
    ![Konto](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "konta")
   
3. W **Tworzenie nowego użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Utwórz użytkownika](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "Tworzenie użytkownika")
   
    a. Typ **adres E-mail**, **imię**, i **nazwisko** poprawnego konta usługi AAD ustanawiane do powiązanych pól tekstowych.
   
    b. Kliknij przycisk **Utwórz użytkownika**.

>[!NOTE]
>Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail zawierającą łącze do potwierdzenia konta, zanim staje się aktywny. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do podpisania Adobe.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Adobe logowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **znak Adobe**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Po kliknięciu kafelka Adobe logowania w panelu dostępu należy należy pobrać automatycznie zalogowane do aplikacji Adobe logowania.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png
