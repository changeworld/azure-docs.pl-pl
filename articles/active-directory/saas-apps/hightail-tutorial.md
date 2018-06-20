---
title: 'Samouczek: Integracji Azure Active Directory z Hightail | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 6fe7cad2910bed2dc08180d28fdf1af1d6cffd9a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223420"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Samouczek: Integracji Azure Active Directory z Hightail

Z tego samouczka dowiesz się integrowanie Hightail z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Hightail zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Hightail
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Hightail (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Hightail, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Hightail logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Hightail z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-hightail-from-the-gallery"></a>Dodawanie Hightail z galerii
Aby skonfigurować integrację usługi Azure AD Hightail, należy dodać Hightail z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Hightail z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Hightail**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/tutorial_hightail_search.png)

5. W panelu wyników wybierz **Hightail**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Hightail w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Hightail jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Hightail musi się.

W Hightail, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Hightail, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Hightail](#creating-a-hightail-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Hightail połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Hightail.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Hightail, wykonaj następujące czynności:**

1. W portalu Azure na **Hightail** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

3. Na **Hightail domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_url.png)

     W **adres URL odpowiedzi** tekstowym, wpisz adres URL jako: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE] 
    > Powyższe nie jest rzeczywistą wartość. Wartość zaktualizuje rzeczywiste odpowiedzi adres URL, który znajduje się w dalszej części tego samouczka.
 
4. Na **Hightail domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjował tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_url1.png)

    a. Kliknij przycisk **Pokaż zaawansowane ustawienia adresu URL**.

    b. W **na adres URL logowania** tekstowym, wpisz adres URL jako: `https://www.hightail.com/loginSSO`

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

5. Hightail aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **"Atrribute"** aplikacji. Poniższy zrzut ekranu przedstawia przykład tego. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |
    | Imię | user.givenname |
    | Nazwisko | user.surname |
    | Email | User.mail |    
    | Tożsamości użytkownika | User.mail |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_general_400.png)

8. Na **Hightail konfiguracji** kliknij **skonfigurować Hightail** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Przed rozpoczęciem konfigurowania rejestracji jednokrotnej w aplikacji Hightail, skontaktuj się z białą listę domenę poczty e-mail z Hightail zespołu, aby wszyscy użytkownicy, którzy korzystają z tej domeny można używać funkcji rejestracji jednokrotnej.


9. Aby uzyskać logowania jednokrotnego skonfigurowane dla aplikacji, należy logowania jednokrotnego dla Twojej dzierżawy Hightail jako administrator.
   
    a. W menu u góry kliknij **konta** i wybierz **skonfigurować SAML**.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_001.png) 

    b. Zaznacz pole wyboru z **Włącz uwierzytelnianie SAML**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_002.png) 

    c. Otwórz w Notatniku pobrany z portalu Azure certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **SAML certyfikat podpisywania tokenu** pola tekstowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_003.png) 

    d. W **SAML urzędu (dostawcy tożsamości)** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** skopiowany z portalu Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_004.png)

    e. Jeśli chcesz skonfigurować aplikację w **IDP zainicjował tryb** wybierz **"Dostawcy tożsamości (IdP) zainicjował logowania"**. Jeśli **SP zainicjował tryb** wybierz **"Dostawcy usług (SP) zainicjował logowania"**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_006.png)

    f. Skopiuj adres URL klienta SAML dla swojego wystąpienia, a następnie wklej je **adres URL odpowiedzi** textbox w **Hightail domeny i adres URL** sekcji z portalu Azure.
    
    g. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hightail-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-hightail-test-user"></a>Tworzenie użytkownika testowego Hightail

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Hightail. 

Nie ma elementu akcji można w tej sekcji. Hightail obsługuje użytkownika w czasie inicjowania obsługi na podstawie niestandardowych oświadczeń. Jeśli skonfigurowano niestandardowe oświadczenia, jak pokazano w sekcji **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)** powyżej, użytkownik zostanie automatycznie utworzone w aplikacji nie istnieje. 

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Hightail.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Hightail, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Hightail**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/hightail-tutorial/tutorial_hightail_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Hightail w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Hightail.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

