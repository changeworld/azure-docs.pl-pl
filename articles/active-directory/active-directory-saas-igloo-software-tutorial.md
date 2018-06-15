---
title: 'Samouczek: Azure Active Directory integracji z oprogramowaniem Igloo | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między oprogramowaniem Igloo i Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 17ccb7fdb765462b8753be26ca823fd68160af34
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348363"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Samouczek: Azure Active Directory integracji z oprogramowaniem Igloo

Z tego samouczka dowiesz się integrowanie Igloo oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania Igloo z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do oprogramowania Igloo
- Umożliwia użytkownikom automatycznie pobrać zalogowane oprogramowania Igloo (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Igloo, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Oprogramowanie Igloo logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie oprogramowania Igloo z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-igloo-software-from-the-gallery"></a>Dodawanie oprogramowania Igloo z galerii
Aby skonfigurować integrację usługi Azure AD Igloo oprogramowania, należy dodać Igloo oprogramowania z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Igloo oprogramowanie z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **oprogramowania Igloo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. W panelu wyników wybierz **oprogramowania Igloo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem Igloo w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w oprogramowaniu Igloo jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w oprogramowaniu Igloo musi określone.

W oprogramowaniu Igloo, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem Igloo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego oprogramowania Igloo](#creating-an-igloo-software-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Igloo oprogramowania, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w używanej aplikacji Igloo.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z oprogramowaniem Igloo, wykonaj następujące czynności:**

1. W portalu Azure na **oprogramowania Igloo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Na **Igloo oprogramowania domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.igloocommmunities.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta oprogramowania Igloo](https://www.igloosoftware.com/services/support) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. Na **konfiguracji oprogramowania Igloo** , kliknij przycisk **Konfigurowanie oprogramowania Igloo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Igloo oprogramowania.

8. Przejdź do **Panel sterowania**.
   
     ![Panel sterowania](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "Panel sterowania")

9. W **członkostwa** , kliknij pozycję **znak ustawień**.
   
    ![Zaloguj się w ustawieniach](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "Zaloguj ustawienia")

10. W sekcji Konfiguracja SAML kliknij **skonfigurować uwierzytelnianie SAML**.
   
    ![Konfiguracja SAML](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "Konfiguracja SAML")
   
11. W **Konfiguracja ogólna** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja ogólna](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "konfiguracji ogólnej")

    a. W **nazwa połączenia** tekstowym, wpisz nazwę niestandardową dla danej konfiguracji.
   
    b. W **adres URL logowania IdP** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    c. W **adresu URL wylogowania IdP** pole tekstowe, Wklej wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
    
    d. Wybierz **wylogowania odpowiedzi i żądania HTTP typu** jako **POST**.
   
    e. Otwórz z **base-64** zakodowanego certyfikatu w programie Notatnik pobrany z portalu Azure, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.
    
12. W **odpowiedzi i konfiguracji uwierzytelniania**, wykonaj następujące czynności:
    
    ![Odpowiedź i konfiguracji uwierzytelniania](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "odpowiedzi i konfiguracji uwierzytelniania")
  
      a. Jako **dostawcy tożsamości**, wybierz pozycję **Microsoft ADFS**.
      
      b. Jako **typ identyfikatora**, wybierz pozycję **adres E-mail**. 

      c. W **atrybut poczty E-mail** pole tekstowe, typ **emailaddress**.

      d. W **atrybutu imię** pole tekstowe, typ **givenname**.

      e. W **ostatniego atrybutu Name** pole tekstowe, typ **nazwisko**.

13. Wykonaj poniższe kroki, aby zakończyć konfigurację:
    
    ![Tworzenie użytkownika na logowania](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Tworzenie użytkownika na logowania") 

     a. Jako **Tworzenie użytkownika na logowania**, wybierz pozycję **utworzenie nowego użytkownika w swojej witrynie, po zalogowaniu**.

     b. Jako **Zaloguj ustawienia**, wybierz pozycję **SAML użyj przycisku na ekranie "Zaloguj"**.

     c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-igloo-software-test-user"></a>Tworzenie użytkownika testowego Igloo oprogramowania

Nie ma elementu akcji do konfiguracji oprogramowania Igloo Inicjowanie obsługi użytkowników.  

Gdy przypisany użytkownik próbuje zalogować się do oprogramowania Igloo za pomocą panelu dostępu, oprogramowania Igloo sprawdza, czy użytkownik istnieje.  Jeśli nie jest Brak konta użytkownika dostępny jeszcze, są tworzone przez oprogramowanie Igloo.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do oprogramowania Igloo.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Igloo oprogramowania, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **oprogramowania Igloo**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka oprogramowania Igloo w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji oprogramowania Igloo.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png

