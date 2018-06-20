---
title: 'Samouczek: Integracji Azure Active Directory z InsideView | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 8bfb98dd3b97a306aae33c450cb19429fa135109
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219921"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Samouczek: Integracji Azure Active Directory z InsideView

Z tego samouczka dowiesz się integrowanie InsideView z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD InsideView zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do InsideView
- Umożliwia użytkownikom automatycznie pobrać zalogowane do InsideView (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z InsideView, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- InsideView logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie InsideView z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-insideview-from-the-gallery"></a>Dodawanie InsideView z galerii
Aby skonfigurować integrację InsideView w usłudze Azure AD, należy dodać InsideView z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać InsideView z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **InsideView**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

5. W panelu wyników wybierz **InsideView**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z InsideView na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w InsideView jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w InsideView musi się.

W InsideView, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z InsideView, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego InsideView](#creating-a-insideview-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta InsideView połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji InsideView.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z InsideView, wykonaj następujące czynności:**

1. W portalu Azure na **InsideView** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

3. Na **InsideView domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej InsideView ](mailto:support@insideview.com) aby zyskać tę wartość.
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_general_400.png)

6. Na **konfiguracji InsideView** , kliknij przycisk **skonfigurować InsideView** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_insideview_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy InsideView.

8. Na pasku narzędzi u góry, kliknij przycisk **Admin**, **ustawienia SingleSignOn**, a następnie kliknij przycisk **dodać SAML**.
   
   ![SAML logowania jednokrotnego ustawienia](./media/insideview-tutorial/ic794135.png "SAML logowania jednokrotnego ustawienia")

9. W **Dodaj nowe SAML** sekcji, wykonaj następujące czynności:

    ![Dodaj nowy SAML](./media/insideview-tutorial/ic794136.png "Dodaj nowe SAML")
   
    a. W **nazwę STS** tekstowym, wpisz nazwę dla danej konfiguracji.

    b. W **SamlP/WS-Fed niechciane punktu końcowego** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
    
    c. Otwórz base-64 zakodowanego certyfikatu, który został już pobrany z portalu Azure, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat STS** pola tekstowego.

    d. W **mapowanie identyfikatora użytkownika Crm** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. W **mapowania E-mail Crm** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. W **mapowania imię Crm** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. W **nazwisko Crm mapowania** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-insideview-test-user"></a>Tworzenie użytkownika testowego InsideView

Aby umożliwić użytkownikom usługi Azure AD zalogować się do InsideView, ich należy udostępnić w celu InsideView. W przypadku InsideView Inicjowanie obsługi to zadanie ręczne.

Aby uzyskać użytkowników lub kontakty utworzone w InsideView, skontaktuj się z [zespołem pomocy technicznej InsideView](mailto:support@insideview.com).

>[!NOTE]
>Możesz użyć innych InsideView użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez InsideView do udostępnienia konta użytkownika usługi Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu InsideView.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta InsideView, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **InsideView**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/insideview-tutorial/tutorial_insideview_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka InsideView w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji InsideView.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

