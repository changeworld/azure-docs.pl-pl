---
title: 'Samouczek: Integracji Azure Active Directory z LearnUpon | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 2796f280b2f95504be5f9691098bff07fdd93393
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Samouczek: Integracji Azure Active Directory z LearnUpon

Z tego samouczka dowiesz się integrowanie LearnUpon z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD LearnUpon zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do LearnUpon
- Umożliwia użytkownikom automatycznie pobrać zalogowane do LearnUpon (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z LearnUpon, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LearnUpon logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie LearnUpon z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-learnupon-from-the-gallery"></a>Dodawanie LearnUpon z galerii
Aby skonfigurować integrację usługi Azure AD LearnUpon, należy dodać LearnUpon z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LearnUpon z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **LearnUpon**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. W panelu wyników wybierz **LearnUpon**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LearnUpon w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w LearnUpon jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w LearnUpon musi się.

W LearnUpon, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LearnUpon, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego LearnUpon](#creating-a-learnupon-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta LearnUpon połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LearnUpon.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z LearnUpon, wykonaj następujące czynności:**

1. W portalu Azure na **LearnUpon** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. Na **LearnUpon domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Należy pamiętać, że nie jest rzeczywistą wartość. należy zaktualizować tę wartość do rzeczywistego adresu URL odpowiedzi. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/).



4. Na **certyfikat podpisywania SAML** sekcji, Znajdź **odcisk palca** — zostanie ona dodana do ustawienia LearnUpon SAML.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. Na **konfiguracji LearnUpon** , kliknij przycisk **skonfigurować LearnUpon** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Otwórz innego wystąpienia przeglądarki i zaloguj do LearnUpon przy użyciu konta administratora. 

8. Kliknij przycisk **ustawienia** kartę.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Kliknij przycisk **rejestracji jednokrotnej - SAML**, a następnie kliknij przycisk **ustawienia ogólne** do konfigurowania ustawień SAML.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. W **ustawienia ogólne** sekcji, wykonaj następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Wybierz **włączone**.

    b. Wybierz **wersji** jako **2.0**.

    c. Wybierz **pominąć warunki** jako **nr**.

    d. W **Post tokenu SAML nazwę param** pole tekstowe, typ nazwę parametru post żądania na adres URL konsumenta SAML wymienionych powyżej, który zawiera potwierdzenia języka SAML ma zostać zweryfikowane i uwierzytelniony — na przykład **SAMLResponse**.

    e. W **Format identyfikatora nazwy** tekstowym, wpisz wartość, która wskazuje, gdzie w Twojej potwierdzenia języka SAML identyfikator użytkowników (adres E-mail) znajduje się — na przykład **urn: oasis: nazwy: tc: SAML:1.1:nameid-format: emailAddress**.
  
    f. W **zidentyfikować lokalizacji dostawcy** tekstowym, wpisz wartość, która wskazuje, gdzie użytkownicy są wysyłane do po kliknięciu Twojej ikonę przekazanego z ekranu logowania do portalu Azure.
  
    g. W **Wyloguj adresu URL** pole tekstowe, Wklej **Sign-Out URL** którego została skopiowana z portalu Azure.
    
    h. Kliknij przycisk **Zarządzanie odbitek palca**, a następnie przekaż odcisk palca certyfikatu pobrane.

11. Kliknij przycisk **ustawienia użytkownika**, a następnie wykonaj następujące czynności:
   
     ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. W **Format identyfikatora imię** tekstowym, wpisz wartość, która informuje NAS w Twoje imię użytkowników potwierdzenia języka SAML znajduje się — na przykład: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. W **ostatniego Format identyfikatora nazwy** tekstowym, wpisz wartość, która informuje NAS w Twojej potwierdzenia języka SAML nazwisko użytkowników znajduje się — na przykład: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-learnupon-test-user"></a>Tworzenie użytkownika testowego LearnUpon

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w LearnUpon. LearnUpon obsługę w czasie, który jest domyślnie włączone.

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik zostanie utworzony podczas próby dostępu LearnUpon, jeśli go jeszcze nie istnieje. [Konfigurowanie usługi Azure AD jednokrotnej](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu LearnUpon.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta LearnUpon, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **LearnUpon**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka LearnUpon w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji LearnUpon.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png

