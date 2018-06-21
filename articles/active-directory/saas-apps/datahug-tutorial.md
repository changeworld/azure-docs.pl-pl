---
title: 'Samouczek: Integracji Azure Active Directory z Datahug | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: bb2d6194b5a515d89e3204679860ab19a052ba03
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285168"
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Samouczek: Integracji Azure Active Directory z Datahug

Z tego samouczka dowiesz się integrowanie Datahug z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Datahug zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Datahug
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Datahug (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz. [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Datahug, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Datahug jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Datahug z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-datahug-from-the-gallery"></a>Dodawanie Datahug z galerii
Aby skonfigurować integrację usługi Azure AD Datahug, należy dodać Datahug z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Datahug z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Datahug**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/tutorial_datahug_search.png)

5. W panelu wyników wybierz **Datahug**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Datahug na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Datahug jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Datahug musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Datahug.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Datahug, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Datahug](#creating-a-datahug-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Datahug połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Datahug.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Datahug, wykonaj następujące czynności:**

1. W portalu Azure na **Datahug** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_samlbase.png)

3. Na **Datahug domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_ur1.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://apps.datahug.com/identity/<uniqueID>`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://apps.datahug.com/identity/<uniqueID>/acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**. Jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_url2.png)

    W **adres URL logowania** tekstowym, wpisz adres URL jako: `https://apps.datahug.com/`
     
    > [!NOTE] 
    > Wartości te nie są rzeczywistych. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. W tym miejscu zalecamy można używać unikatowej wartości ciągu identyfikatora i adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Datahug](http://datahug.com/about/contact-us/) uzyskać te wartości. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Sprawdź **"Pokaż zaawansowane ustawienia podpisywania certyfikatu"** i wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_cert.png)

    a. W **opcja podpisywania**, wybierz pozycję **potwierdzenia języka SAML logowania**.
    
    b. W **algorytm podpisywania**, wybierz pozycję **SHA1**.
 
7. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_general_400.png)
    
8. Na **konfiguracji Datahug** , kliknij przycisk **skonfigurować Datahug** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** i **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_configure.png) 

9. Aby skonfigurować logowanie jednokrotne w **Datahug** stronie, musisz wysłać pobrany **XML metadanych**, **SAML identyfikator jednostki** i **SAML pojedynczy znak na adres URL usługi**  do [Obsługa Datahug](http://datahug.com/about/contact-us/). Połączenia logowania jednokrotnego SAML prawidłowo po obu stronach instalacji tej aplikacji programu.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/datahug-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-datahug-test-user"></a>Tworzenie użytkownika testowego Datahug

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Datahug, musi być przygotowana do Datahug.  
Gdy Datahug, inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Datahug jako administrator.

2. Umieść kursor nad **koło zębate** w prawym górnym rogu i kliknij **ustawienia**
   
   ![Dodawanie pracownika](./media/datahug-tutorial/1.png)

3. Wybierz **osób** i kliknij przycisk **Dodaj użytkowników** kartę

    ![Dodawanie pracownika](./media/datahug-tutorial/2.png)

4. Wpisz adres e-mail osoby, które chcesz utworzyć konto, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie pracownika](./media/datahug-tutorial/3.png)

    > [!NOTE] 
    > Wiadomość e-mail rejestracji można wysłać do użytkownika, wybierając **wysyłania powitalnej wiadomości e-mail** wyboru.  
    > Jeśli tworzysz konto Salesforce bez wysyłania powitalnej wiadomości e-mail.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Datahug.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Datahug, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Datahug**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/datahug-tutorial/tutorial_datahug_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.
Po kliknięciu kafelka Datahug w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Datahug. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/datahug-tutorial/tutorial_general_01.png
[2]: ./media/datahug-tutorial/tutorial_general_02.png
[3]: ./media/datahug-tutorial/tutorial_general_03.png
[4]: ./media/datahug-tutorial/tutorial_general_04.png

[100]: ./media/datahug-tutorial/tutorial_general_100.png

[200]: ./media/datahug-tutorial/tutorial_general_200.png
[201]: ./media/datahug-tutorial/tutorial_general_201.png
[202]: ./media/datahug-tutorial/tutorial_general_202.png
[203]: ./media/datahug-tutorial/tutorial_general_203.png

