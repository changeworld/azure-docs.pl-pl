---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą TeamSeer | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: ffe94d8d3e08bb35cf8ea20f4b1e32b1ec84aa35
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814158"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą TeamSeer

W tym samouczku dowiesz się, jak zintegrować TeamSeer w usłudze Azure Active Directory (Azure AD).

Integrowanie TeamSeer z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do TeamSeer
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do TeamSeer (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą TeamSeer, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- TeamSeer logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TeamSeer z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-teamseer-from-the-gallery"></a>Dodawanie TeamSeer z galerii
Aby skonfigurować integrację TeamSeer w usłudze Azure AD, należy dodać TeamSeer z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TeamSeer z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **TeamSeer**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/tutorial_teamseer_search.png)

1. W panelu wyników wybierz **TeamSeer**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą TeamSeer w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w TeamSeer do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TeamSeer musi można ustanowić.

W TeamSeer, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą TeamSeer, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego TeamSeer](#creating-a-teamseer-test-user)**  — aby odpowiednikiem Britta Simon w TeamSeer połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji TeamSeer.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TeamSeer, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TeamSeer** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_teamseer_samlbase.png)

1. Na **TeamSeer domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_teamseer_url.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_teamseer_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_general_400.png)

1. Na **konfiguracji TeamSeer** , kliknij przycisk **skonfigurować TeamSeer** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_teamseer_configure.png)

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TeamSeer.

1. Przejdź do **administratora HR**.
   
    ![Administrator działu KADR](./media/teamseer-tutorial/ic789634.png "administratora działu KADR")

1. Kliknij przycisk **Instalatora**.
   
    ![Konfigurowanie](./media/teamseer-tutorial/ic789635.png "Konfigurowanie")

1. Kliknij przycisk **Konfigurowanie SAML dostawcy szczegółów**.
   
    ![Ustawienia języka SAML](./media/teamseer-tutorial/ic789636.png "ustawienia języka SAML")

1. W sekcji Szczegóły dostawcy protokołu SAML wykonaj następujące czynności:
   
    ![Ustawienia języka SAML](./media/teamseer-tutorial/ic789637.png "ustawienia języka SAML")   

    a. Wklej **pojedynczy znak na adres URL usługi** wartość, która **adresu URL** pola tekstowego.
          
    b. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego dostawcy tożsamości** pola tekstowego.

1. Aby ukończyć konfigurację dostawcy protokołu SAML, wykonaj następujące czynności:
    
    ![Ustawienia języka SAML](./media/teamseer-tutorial/ic789638.png "ustawienia języka SAML") 

    a. W **Test adresy E-mail**, wpisz adres e-mail użytkownika testowego. 
  
    b. W **wystawcy** pole tekstowe, wpisz adres URL wystawcy dostawcy usług. 
  
    c. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/teamseer-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-teamseer-test-user"></a>Tworzenie użytkownika testowego TeamSeer

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do TeamSeer, ich musi być obsługiwana w celu ShiftPlanning. W przypadku TeamSeer Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **TeamSeer** witryny firmy jako administrator.

1. Wykonaj poniższe czynności:
   
    ![Administrator działu KADR](./media/teamseer-tutorial/ic789640.png "administratora działu KADR")  
 
    a. Przejdź do **administratora HR \> użytkowników**.
  
    b. Kliknij przycisk **Uruchom Kreatora nowego użytkownika**.

1. W **szczegóły użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Szczegóły użytkownika](./media/teamseer-tutorial/ic789641.png "szczegóły użytkownika")

    a. Typ **imię**, **nazwisko**, **nazwę użytkownika (adres E-mail)** poprawnego konta usługi AAD, którym chcesz udostępnić w celu powiązanych pól tekstowych.
  
    b. Kliknij przycisk **Dalej**.

1. Postępuj zgodnie z wyświetlanymi na ekranie instrukcje dotyczące dodawania nowego użytkownika i kliknij przycisk **Zakończ**.

>[!NOTE]
>Można użyć jakichkolwiek innych TeamSeer użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez TeamSeer można uaktywniać ich konta usługi Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do TeamSeer.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon TeamSeer, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **TeamSeer**.

    ![Konfigurowanie logowania jednokrotnego](./media/teamseer-tutorial/tutorial_teamseer_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej szczegółów na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/teamseer-tutorial/tutorial_general_01.png
[2]: ./media/teamseer-tutorial/tutorial_general_02.png
[3]: ./media/teamseer-tutorial/tutorial_general_03.png
[4]: ./media/teamseer-tutorial/tutorial_general_04.png

[100]: ./media/teamseer-tutorial/tutorial_general_100.png

[200]: ./media/teamseer-tutorial/tutorial_general_200.png
[201]: ./media/teamseer-tutorial/tutorial_general_201.png
[202]: ./media/teamseer-tutorial/tutorial_general_202.png
[203]: ./media/teamseer-tutorial/tutorial_general_203.png

