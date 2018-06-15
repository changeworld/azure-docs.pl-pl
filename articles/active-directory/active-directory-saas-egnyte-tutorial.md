---
title: 'Samouczek: Integracji Azure Active Directory z Egnyte | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: ea9cb831845a4f99929deac5bc4c63f8c53e5238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341209"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Samouczek: Integracji Azure Active Directory z Egnyte

Z tego samouczka dowiesz się integrowanie Egnyte z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Egnyte zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Egnyte
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Egnyte (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Egnyte, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Egnyte logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Egnyte z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie Egnyte z galerii
Aby skonfigurować integrację usługi Azure AD Egnyte, należy dodać Egnyte z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Egnyte z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Egnyte**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. W panelu wyników wybierz **Egnyte**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Egnyte na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Egnyte jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Egnyte musi się.

W Egnyte, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Egnyte, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Egnyte](#creating-an-egnyte-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Egnyte połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Egnyte.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Egnyte, wykonaj następujące czynności:**

1. W portalu Azure na **Egnyte** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. Na **Egnyte domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Egnyte** , kliknij przycisk **skonfigurować Egnyte** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Egnyte.

8. Kliknij przycisk **ustawienia**.
   
   ![Ustawienia](./media/active-directory-saas-egnyte-tutorial/ic787819.png "ustawienia")

9. W menu, kliknij przycisk **ustawienia**.

   ![Ustawienia](./media/active-directory-saas-egnyte-tutorial/ic787820.png "ustawienia")

10. Kliknij przycisk **konfiguracji** , a następnie kliknij pozycję **zabezpieczeń**.

    ![Zabezpieczenia](./media/active-directory-saas-egnyte-tutorial/ic787821.png "zabezpieczeń")

11. W **uwierzytelniania rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:

    ![Rejestracja jednokrotna na uwierzytelnianie](./media/active-directory-saas-egnyte-tutorial/ic787822.png "Rejestracja jednokrotna dotyczących uwierzytelniania")   
    
    a. Jako **uwierzytelniania rejestracji jednokrotnej**, wybierz pozycję **SAML 2.0**.
   
    b. Jako **dostawcy tożsamości**, wybierz pozycję **AzureAD**.
   
    c. Wklej **SAML pojedynczy znak na adres URL usługi** skopiowany z portalu Azure do **adresu URL logowania do dostawcy tożsamości** pola tekstowego.
   
    d. Wklej **identyfikator jednostki SAML** , które zostały skopiowane z portalu Azure do **identyfikator jednostki dostawcy tożsamości** pola tekstowego.
      
    e. Otwórz w Notatniku pobrany z portalu Azure certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy tożsamości** pola tekstowego.
   
    f. Jako **domyślne mapowanie użytkownika**, wybierz pozycję **adres E-mail**.
   
    g. Jako **Użyj wartości wystawcy specyficznego dla domeny**, wybierz pozycję **wyłączone**.
   
    h. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-egnyte-test-user"></a>Tworzenie użytkownika testowego Egnyte

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Egnyte, musi być przygotowana do Egnyte. W przypadku Egnyte Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Egnyte** witryny firmy jako administrator.

2. Przejdź do **ustawienia \> użytkownicy i grupy**.

3. Kliknij przycisk **Dodaj nowego użytkownika**, a następnie wybierz typ użytkownika, które chcesz dodać.
   
   ![Użytkownicy](./media/active-directory-saas-egnyte-tutorial/ic787824.png "użytkowników")

4. W **nowego użytkownika standardowego** sekcji, wykonaj następujące czynności:
   
   ![Nowy użytkownik standardowy](./media/active-directory-saas-egnyte-tutorial/ic787825.png "nowego użytkownika standardowego")   

   a. Typ **E-mail**, **Username**oraz innych szczegółów prawidłowe konto usługi Azure Active Directory, aby udostępnić.
   
   b. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory, otrzymają wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Możesz użyć innych Egnyte użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Egnyte do kont użytkowników usługi AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Egnyte.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Egnyte, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Egnyte**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Egnyte w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Egnyte.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

