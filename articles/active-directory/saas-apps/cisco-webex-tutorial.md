---
title: 'Samouczek: Integracja usługi Azure Active Directory z Cisco Webex | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: accafab55c6c1aa34ccf7aa9cfc4adb2a902f5e3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043437"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Samouczek: Integracja usługi Azure Active Directory z Cisco Webex

W tym samouczku dowiesz się, jak zintegrować Cisco Webex w usłudze Azure Active Directory (Azure AD).

Integrowanie Cisco Webex z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Webex firmy Cisco.
- Aby umożliwić użytkownikom automatyczne pobieranie zarejestrowanie w Cisco Webex za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Cisco Webex, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Cisco Webex pojedynczej logowania na włączona subskrypcji

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [bezpłatna wersja próbna miesięcznej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Cisco Webex z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-cisco-webex-from-the-gallery"></a>Dodaj Cisco Webex z galerii
Aby skonfigurować integrację Cisco Webex w usłudze Azure AD, należy dodać Cisco Webex z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Cisco Webex w galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Cisco Webex**. 

5. Wybierz **Cisco Webex** z panelu wyników. Następnie wybierz pozycję **Dodaj** przycisk, aby dodać aplikację.

    ![Cisco Webex na liście wyników](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Webex Cisco, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w Cisco Webex użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w Webex firmy Cisco.

W Cisco Webex podania wartości **Username** taką samą wartość jak **nazwa_użytkownika** w usłudze Azure AD. Łącze między dwóch użytkowników ma ustanowione. 

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Cisco Webex, wykonaj poniższe bloki konstrukcyjne:

1. [Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) aby umożliwić użytkownikom korzystać z tej funkcji.
2. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. [Tworzenie użytkownika testowego Cisco Webex](#create-a-cisco-webex-test-user) mieć odpowiednikiem Britta Simon Webex Cisco, połączonego z usługi Azure AD reprezentacja użytkownika.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. [Testowanie logowania jednokrotnego](#test-single-sign-on) można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Cisco Webex.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Cisco Webex, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Cisco Webex** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** dialogowym **tryb** listy rozwijanej wybierz **opartej na SAML logowania jednokrotnego**.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. W **Cisco Webex domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Cisco Webex domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. W **adres URL logowania** wpisz adres URL z następującym wzorcem: `https://<subdomain>.webex.com`

    b. W **identyfikator** wpisz adres URL `http://www.webex.com`.

    c. W **adres URL odpowiedzi** wpisz adres URL z następującym wzorcem: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu adresu URL odpowiedzi rzeczywiste i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Webex Cisco](https://www.webex.co.in/support/support-overview.html) do uzyskania tych wartości. 

5. Na **certyfikat podpisywania SAML** zaznacz **XML metadanych**, a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Wybierz pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego przycisk zapisywania](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
6. W **konfiguracji Webex Cisco** zaznacz **skonfigurować Webex Cisco** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy Cisco Webex jako administrator.

8. W menu u góry wybierz **administrowania lokacją**.

    ![Administrowanie lokacją](./media/cisco-webex-tutorial/ic777621.png "administrowanie lokacją")

9. W **zarządzania witryną** zaznacz **konfiguracji logowania jednokrotnego**.
   
    ![Konfiguracja logowania jednokrotnego](./media/cisco-webex-tutorial/ic777622.png "konfiguracji logowania jednokrotnego")

10. W **Konfiguracja logowania jednokrotnego w sieci Web federacyjnego** sekcji, wykonaj następujące czynności:
   
    ![Federacyjna usługa rejestracji Jednokrotnej konfiguracji](./media/cisco-webex-tutorial/ic777623.png "Federacyjna usługa rejestracji Jednokrotnej w konfiguracji")  

    a. W **protokołu Federation** listy wybierz **SAML 2.0**.

    b. Dla **profilu rejestracji Jednokrotnej**, wybierz opcję **zainicjowane SP**.

    c. Otwórz pobrany certyfikat w Notatniku, a następnie skopiuj zawartość.

    d. Wybierz **Importowanie metadanych SAML**, a następnie wkleić skopiowanej zawartości certyfikatu.

    e. W **wystawcy dla języka SAML (identyfikator dostawcy tożsamości)** pole, Wklej wartość **identyfikator jednostki SAML** skopiowaną z witryny Azure portal.

    f. W **adres URL dla klientów logowania jednokrotnego usługi logowania** pole, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który został skopiowany z witryny Azure portal.

    g. Z **Format identyfikatora NameID** listy wybierz **adres E-mail**.

    h. W **AuthnContextClassRef** wpisz **urn: oasis: nazwy: tc: SAML:2.0:ac:classes:Password**.

    i. W **adres URL wylogowania usługi logowania jednokrotnego dla klientów** pole, Wklej **adres URL wylogowania**, który został skopiowany z witryny Azure portal.
   
    j. Wybierz **aktualizacji**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com) podczas konfigurowania aplikacji. Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzonego Dokumentacja usługi za pośrednictwem **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-cisco-webex-test-user"></a>Tworzenie użytkownika testowego Cisco Webex

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Cisco Webex, musi być obsługiwana w Webex firmy Cisco. W przypadku Cisco Webex aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Cisco Webex** dzierżawy.

2. Przejdź do **Zarządzanie użytkownikami** > **Dodaj użytkownika**.
   
    ![Dodawanie użytkowników](./media/cisco-webex-tutorial/ic777625.png "Dodawanie użytkowników")

3. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:
   
    ![Dodaj użytkownika](./media/cisco-webex-tutorial/ic777626.png "Dodaj użytkownika")   

    a. Aby uzyskać **typ konta**, wybierz opcję **hosta**.

    b. W **imię** wpisz imię użytkownika (w tym przypadku **Britta**).

    c. W **nazwisko** wpisz nazwisko użytkownika (w tym przypadku **Simon**).

    d. W **Username** wpisz adres e-mail użytkownika (w tym przypadku **Brittasimon@contoso.com**).

    e. W **E-mail** wpisz adres e-mail użytkownika (w tym przypadku **Brittasimon@contoso.com**).

    f. W **hasło** wpisz hasło użytkownika.

    g. W **Potwierdź** hasło pole, wprowadź ponownie hasło użytkownika.

    h. Wybierz pozycję **Dodaj**.

>[!NOTE]
>Można użyć innych narzędzi do tworzenia konta użytkownika Cisco Webex lub interfejsów API, które są dostarczane przez firmy Cisco Webex można uaktywniać ich konta usługi Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Britta Simon do nadawania im praw do Cisco Webex za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Cisco Webex, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji. Następny, przejdź do widoku katalogu, a następnie do **aplikacje dla przedsiębiorstw**.  

2. Wybierz **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

3. Na liście aplikacji wybierz **Cisco Webex**.

    ![Link Cisco Webex na liście aplikacji](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** w **Dodaj przydziału** okno dialogowe.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** w **użytkowników** listy.

6. W **użytkowników i grup** okno dialogowe, kliknij przycisk **wybierz** przycisku.

7. Wybierz **przypisać** znajdujący się w **Dodaj przydziału** okno dialogowe.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu kafelka Cisco Webex w panelu dostępu, możesz automatycznie Pobierz zarejestrowany w aplikacji Cisco Webex.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

