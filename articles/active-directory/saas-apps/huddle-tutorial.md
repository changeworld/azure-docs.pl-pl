---
title: 'Samouczek: Integracji Azure Active Directory z zbijają się | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i zbijają się.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 228354fe86bd8758a9c338d980e2a80e577a11be
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211353"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Samouczek: Integracji Azure Active Directory z zbijają się

Z tego samouczka dowiesz się integrowanie zbijają się w usłudze Azure Active Directory (Azure AD).

Integrowanie zbijają się z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do zbijają się
- Umożliwia użytkownikom automatycznie pobrać zalogowane do zbijają się (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z zbijają się, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zbijają się logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie zbijają się z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-huddle-from-the-gallery"></a>Dodawanie zbijają się z galerii
Aby skonfigurować integrację zbijają się do usługi Azure AD, należy dodać zbijają się z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać zbijają się z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **zbijają się**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/tutorial_huddle_search.png)

5. W panelu wyników wybierz **zbijają się**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z zbijają się na podstawie użytkownika testowego, nazywany "Britta Simona".

Do rejestracji jednokrotnej do pracy usługi Azure AD musi ustalić użytkownika odpowiednika w zbijają się do użytkownika w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w zbijają się potrzeba ustanowienia.

W zbijają się, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z zbijają się, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.

2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.

3. **[Tworzenie użytkownika testowego zbijają się](#creating-a-huddle-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta zbijają się połączonego z usługi Azure AD reprezentację użytkownika.

4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.

5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji zbijają się.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z zbijają się, wykonaj następujące czynności:**

1. W portalu Azure na **zbijają się** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

3. Na **zbijają się w domenie i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_huddle_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `http://<company name>.huddle.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [klienta zbijają się z pomocą techniczną](https://huddle.zendesk.com) aby zyskać tę wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_general_400.png)

6. Na **zbijają się konfiguracji** , kliknij przycisk **skonfigurować zbijają się** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.** 

    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_huddle_configure.png) 
    
7. Aby skonfigurować rejestrację jednokrotną zbijają się strony, musisz wysłać pobrany **certyfikatu**, **SAML pojedynczy znak na adres URL usługi**, i **identyfikator jednostki SAML** do [klienta zbijają się z pomocą techniczną](https://huddle.zendesk.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.  
   
    >[!NOTE]
    > Logowania jednokrotnego musi być włączona przez zbijają się z pomocą techniczną. Otrzymasz powiadomienie po zakończeniu konfiguracji. 
    > 

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
   
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/huddle-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-huddle-test-user"></a>Tworzenie użytkownika testowego zbijają się

Aby umożliwić użytkownikom usługi Azure AD zalogować się do zbijają się, muszą mieć przydzielone do zbijają się. W przypadku zbijają się inicjowanie obsługi to zadanie ręczne.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **zbijają się** witryny firmy jako administrator.
2. Kliknij przycisk **obszaru roboczego**.
3. Kliknij przycisk **osób \> Zaproś inne osoby**.
   
   ![Osoby](./media/huddle-tutorial/IC787838.png "osób")

4. W **utworzyć nowe zaproszenie** sekcji, wykonaj następujące czynności:
   
   ![Nowe zaproszenie](./media/huddle-tutorial/IC787839.png "nowe zaproszenie")
   
   a. W **wybierz zespołowi zapraszanie osób do** listy, wybierz **zespołu**.

   b. Typ **adres E-mail** prawidłowy Azure AD konta chcesz udostępnić w celu **wprowadź adres e-mail osoby, które chcesz zaprosić** pola tekstowego.

   c. Kliknij przycisk **zaprosić**.   
   
    >[!NOTE]
    > Właściciel konta usługi Azure AD zostanie wysłana wiadomość e-mail, łącznie z łączem do potwierdzenia konta, zanim staje się aktywny. 
    > 

>[!NOTE]
>Inne zbijają się narzędzia tworzenia konta użytkownika lub interfejsów API dostarczonych przez zbijają się służy do obsługi administracyjnej kont użytkowników usługi Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do zbijają się.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta zbijają się, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **zbijają się**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka zbijają się w panelu dostępu, należy uzyskać automatycznie strony logowania zbijają się w aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
