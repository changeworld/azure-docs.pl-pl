---
title: 'Samouczek: Integracji Azure Active Directory z przodu | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Azure Active Directory i do przodu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 7d70a04535c769bc2fa754887d79f474bc4ced0c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214124"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Samouczek: Integracji Azure Active Directory z przodu

Z tego samouczka dowiesz integrowanie przodu z usługi Azure Active Directory (Azure AD).

Integrowanie przodu z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do przodu.
- Umożliwia użytkownikom automatycznie pobrać zalogowane na wierzch (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z przodu, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Front logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie przodu z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-front-from-the-gallery"></a>Dodawanie przodu z galerii
Aby skonfigurować integrację z przodu do usługi Azure AD, należy dodać przodu z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać przodu z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Front**, wybierz pozycję **Front** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Front na liście wyników](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z przodu w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć z przodu użytkownik odpowiednikiem jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi na wierzchu.

Na wierzchu przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z przodu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego przodu](#create-a-front-test-user)**  — aby odpowiednikiem Simona Britta na wierzchu połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do przodu aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z przodu, wykonaj następujące czynności:**

1. W portalu Azure na **Front** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/front-tutorial/tutorial_front_samlbase.png)

3. Na **Front domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/front-tutorial/tutorial_front_url1.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.frontapp.com`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizować te wartości z rzeczywistego identyfikatora i adres URL odpowiedzi które opisano szczegółowo w dalszej części samouczka lub skontaktuj się z [zespołem pomocy technicznej klienta Front](mailto:support@frontapp.com) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/front-tutorial/tutorial_front_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/front-tutorial/tutorial_general_400.png)
    
6. Na **konfiguracji frontonu** kliknij **skonfigurować Front** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/front-tutorial/tutorial_front_configure.png) 

7. Logowanie do przodu dzierżawy z uprawnieniami administratora.

8. Przejdź do **ustawień (koło zębate ikonę na dole po lewej stronie paska bocznego) > Preferencje**.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_000.png)

9. Kliknij przycisk **rejestracji jednokrotnej** łącza.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_001.png)

10. Wybierz **SAML** na liście rozwijanej **rejestracji jednokrotnej**.
   
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_002.png)

11. W **punktu wejścia** pole tekstowe umieścić wartość elementu **pojedynczy znak na adres URL usługi** z Kreatora konfiguracji aplikacji usługi Azure AD.
    
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_003.png)

12. Otwórz z pobranego **Certificate(Base64)** plików w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu podpisywania** pola tekstowego.
    
    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_004.png)

13. Na **ustawień dostawcy usług** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie jednej logowania w aplikacji po stronie](./media/front-tutorial/tutorial_front_005.png)

    a. Skopiuj wartość **identyfikator jednostki** i wklej ją do **identyfikator** textbox w **Front domeny i adres URL** sekcji w portalu Azure.

    b. Skopiuj wartość **adres URL usługi ACS** i wklej ją do **adres URL odpowiedzi** textbox w **Front domeny i adres URL** sekcji w portalu Azure.
    
14. Kliknij przycisk **zapisać** przycisku.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/front-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/front-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/front-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/front-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-front-test-user"></a>Tworzenie użytkownika testowego przodu

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta na wierzchu. Praca z [zespołem pomocy technicznej klienta Front](mailto:support@frontapp.com) Aby dodać użytkowników do przodu platformy. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do przodu.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do przodu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Front**.

    ![Łącze przodu na liście aplikacji](./media/front-tutorial/tutorial_front_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji służy do testowania programu Azure AD SSOconfiguration, za pomocą panelu dostępu.

Po kliknięciu kafelka przodu w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do przodu aplikacji. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png

