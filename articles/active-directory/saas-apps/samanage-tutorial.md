---
title: 'Samouczek: Integracji Azure Active Directory z Samanage | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e338dda7ffaf25a80663c2939fd42342b4049883
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222893"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Samouczek: Integracji Azure Active Directory z Samanage

Z tego samouczka dowiesz się integrowanie Samanage z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Samanage zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Samanage
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Samanage (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Samanage, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Samanage logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Samanage z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-samanage-from-the-gallery"></a>Dodawanie Samanage z galerii
Aby skonfigurować integrację usługi Azure AD Samanage, należy dodać Samanage z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Samanage z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Samanage**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/tutorial_samanage_search.png)

5. W panelu wyników wybierz **Samanage**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Samanage w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Samanage jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Samanage musi się.

W Samanage, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Samanage, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Samanage](#creating-a-samanage-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Samanage połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Samanage.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Samanage, wykonaj następujące czynności:**

1. W portalu Azure na **Samanage** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

3. Na **Samanage domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, który znajduje się w dalszej części tego samouczka, należy zaktualizować te wartości. Więcej informacji skontaktuj się z [zespołem pomocy technicznej klienta Samanage](https://www.samanage.com/support).    
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Samanage** , kliknij przycisk **skonfigurować Samanage** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_samanage_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Samanage jako administrator.

8. Kliknij przycisk **pulpitu nawigacyjnego** i wybierz **Instalator** w lewym okienku nawigacji.
   
    ![Pulpit nawigacyjny](./media/samanage-tutorial/tutorial_samanage_001.png "pulpitu nawigacyjnego")

9. Kliknij przycisk **logowanie jednokrotne**.
   
    ![Logowanie jednokrotne](./media/samanage-tutorial/tutorial_samanage_002.png "logowanie jednokrotne")

10. Przejdź do **logowania za pomocą protokołu SAML** sekcji, wykonaj następujące czynności:
   
    ![Zaloguj się przy użyciu SAML](./media/samanage-tutorial/tutorial_samanage_003.png "logowania za pomocą protokołu SAML")
 
    a. Kliknij przycisk **włączyć logowanie jednokrotne z SAML**.  
 
    b. W **adres URL dostawcy tożsamości** pole tekstowe, Wklej wartość **identyfikator jednostki SAML** którego została skopiowana z portalu Azure.    
 
    c. Potwierdź **adres URL logowania** odpowiada **na adres URL logowania** z **Samanage domeny i adres URL** sekcji w portalu Azure.
 
    d. W **adresu URL wylogowania** pole tekstowe, wprowadź wartość **Sign-Out URL** którego została skopiowana z portalu Azure.
 
    e. W **wystawcy SAML** tekstowym, wpisz identyfikator URI aplikacji ustawiona w dostawcy tożsamości.
 
    f. Otwórz base-64 zakodowany certyfikat pobrany z portalu Azure w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **Wklej dostawcy tożsamości x.509 szyfrowany poniżej certyfikat** pola tekstowego.
 
    g. Kliknij przycisk **tworzenie użytkowników, jeśli nie istnieją w Samanage**.
 
    h. Kliknij przycisk **aktualizacji**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/samanage-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-samanage-test-user"></a>Tworzenie użytkownika testowego Samanage

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Samanage, musi być przygotowana do Samanage.  
W przypadku Samanage Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Samanage jako administrator.

2. Kliknij przycisk **pulpitu nawigacyjnego** i wybierz **Instalator** w przesuwanie nawigacji po lewej stronie.
   
    ![Instalator](./media/samanage-tutorial/tutorial_samanage_001.png "Instalatora")

3. Kliknij przycisk **użytkowników** kartę
   
    ![Użytkownicy](./media/samanage-tutorial/tutorial_samanage_006.png "użytkowników")

4. Kliknij przycisk **nowego użytkownika**.
   
    ![Nowy użytkownik](./media/samanage-tutorial/tutorial_samanage_007.png "nowego użytkownika")

5. Typ **nazwa** i **adres E-mail** konta usługi Azure Active Directory do udostępnienia i kliknij przycisk **tworzenia użytkownika**.
   
    ![Utwórz użytkownika](./media/samanage-tutorial/tutorial_samanage_008.png "Tworzenie użytkownika")
   
   >[!NOTE]
   >Właścicielem konta usługi Azure Active Directory otrzymasz wiadomość e-mail, a następnie kliknij łącze, aby potwierdzić swoje konto, zanim staje się aktywny. Możesz użyć innych Samanage użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Samanage do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Samanage.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Samanage, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Samanage**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/samanage-tutorial/tutorial_samanage_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Samanage w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Samanage.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

