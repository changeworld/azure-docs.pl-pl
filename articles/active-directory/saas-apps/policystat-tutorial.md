---
title: 'Samouczek: Integracja usługi Azure Active Directory z PolicyStat | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 97154f0ee8f07e0fa4fe8d70fef997144251c27d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041990"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Samouczek: Integracja usługi Azure Active Directory z PolicyStat

W tym samouczku dowiesz się, jak zintegrować PolicyStat w usłudze Azure Active Directory (Azure AD).

Integrowanie PolicyStat z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do PolicyStat
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do PolicyStat (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą PolicyStat, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- PolicyStat logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie PolicyStat z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-policystat-from-the-gallery"></a>Dodawanie PolicyStat z galerii
Aby skonfigurować integrację PolicyStat w usłudze Azure AD, należy dodać PolicyStat z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać PolicyStat z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **PolicyStat**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/tutorial_policystat_search.png)

5. W panelu wyników wybierz **PolicyStat**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą PolicyStat w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w PolicyStat do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w PolicyStat musi można ustanowić.

W PolicyStat, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą PolicyStat, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego PolicyStat](#creating-a-policystat-test-user)**  — aby odpowiednikiem Britta Simon w PolicyStat połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji PolicyStat.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z PolicyStat, wykonaj następujące czynności:**

1. W witrynie Azure portal na **PolicyStat** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

3. Na **PolicyStat domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.policystat.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta PolicyStat](http://www.policystat.com/support/) do uzyskania tych wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

5. Celem tej sekcji jest opisują, jak umożliwić użytkownikom na uwierzytelnianie PolicyStat za pomocą konta w usłudze Azure AD przy użyciu Federacji, na podstawie protokołu SAML.

    Aplikacja PolicyStat oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych, aby Twoje **atrybuty tokenu języka SAML** konfiguracji.  

     Poniższy zrzut ekranu przedstawia przykład.

     ![Atrybuty](./media/policystat-tutorial/tutorial_policystat_attribute.png "atrybutów")

6. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:

    | Nazwa atrybutu    |   Wartość atrybutu |
    |------------------- | -------------------- |
    | Identyfikator UID | ExtractMailPrefix([mail]) |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. W **nazwa atrybutu** polu tekstowym wpisz **uid**.

    c. W **wartość atrybutu** pola tekstowego, wybierz opcję **ExtractMailPrefix()**.    
   
    d. Z **poczty** listy wybierz **User.mail**.
    
    e. Kliknij przycisk **Ok**

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_general_400.png)

8. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy PolicyStat, jako administrator.

9. Kliknij przycisk **administratora** kartę, a następnie kliknij przycisk **konfiguracji rejestracji jednokrotnej** w okienku nawigacji po lewej stronie.
   
    ![Menu administratora](./media/policystat-tutorial/ic808633.png "Menu administratora")

10. W **instalacji** zaznacz **Włącz logowanie jednokrotne integracji**.
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/policystat-tutorial/ic808634.png "pojedynczy konfiguracji logowania jednokrotnego")

11. Kliknij przycisk **Konfigurowanie atrybutów**, a następnie w **Konfigurowanie atrybutów** sekcji, wykonaj następujące czynności:
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/policystat-tutorial/ic808635.png "pojedynczy konfiguracji logowania jednokrotnego")
   
    a. W **atrybut Username** polu tekstowym wpisz **uid**.

    b. W **atrybut imię** polu tekstowym wpisz **firstname** użytkownika **Britta**.

    c. W **ostatniego atrybutu nazwy** polu tekstowym wpisz **lastname** użytkownika **Simon**.

    d. W **atrybut E-mail** polu tekstowym wpisz **emailaddress** użytkownika **BrittaSimon@contoso.com**.

    e. Kliknij przycisk **Zapisz zmiany**.

12. Kliknij przycisk **Your metadanych tożsamości**, a następnie w **Your metadanych tożsamości** sekcji, wykonaj następujące czynności:
   
    ![Pojedynczy konfiguracji logowania jednokrotnego](./media/policystat-tutorial/ic808636.png "pojedynczy konfiguracji logowania jednokrotnego")
   
    a. Otwórz plik metadanych pobrany, skopiuj zawartość, a następnie wklej go do **Your metadanych dostawcy tożsamości** pola tekstowego.

    b. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/policystat-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-policystat-test-user"></a>Tworzenie użytkownika testowego PolicyStat

Aby umożliwić użytkownikom usługi Azure AD zalogować się do PolicyStat, musi być obsługiwana w PolicyStat.  

Obsługuje PolicyStat dokładnie na czas Inicjowanie obsługi użytkowników. Oznacza to, że nie trzeba ręcznie dodać użytkowników do PolicyStat. Użytkownicy będą poproś o dodanie Cię automatycznie podczas ich pierwszego logowania za pomocą logowania jednokrotnego.

>[!NOTE]
>Można użyć jakichkolwiek innych PolicyStat użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez PolicyStat można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do PolicyStat.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon PolicyStat, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **PolicyStat**.

    ![Konfigurowanie logowania jednokrotnego](./media/policystat-tutorial/tutorial_policystat_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka PolicyStat w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji PolicyStat.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

