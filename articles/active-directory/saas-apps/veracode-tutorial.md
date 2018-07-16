---
title: 'Samouczek: Integracja usługi Azure Active Directory z Veracode | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 2dd273a1a0b5a5af65c4c40337975cb6f3f858ae
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049834"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Samouczek: Integracja usługi Azure Active Directory z Veracode

W tym samouczku dowiesz się, jak zintegrować Veracode w usłudze Azure Active Directory (Azure AD).

Integrowanie Veracode z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Veracode.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Veracode (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Veracode, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Veracode logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj Veracode z galerii
2. Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

## <a name="add-veracode-from-the-gallery"></a>Dodaj Veracode z galerii
Aby skonfigurować integrację Veracode w usłudze Azure AD, należy dodać Veracode z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Veracode z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Veracode**, wybierz opcję **Veracode** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Veracode na liście wyników](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veracode w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Veracode do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Veracode musi można ustanowić.

W Veracode, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Veracode, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Veracode](#create-a-veracode-test-user)**  — aby odpowiednikiem Britta Simon w Veracode połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Veracode.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Veracode, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Veracode** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

3. Na **Veracode domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure. 

    ![Konfigurowanie logowania jednokrotnego](./media/veracode-tutorial/tutorial_veracode_url.png)

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

5. Celem tej sekcji jest opisują, jak umożliwić użytkownikom na uwierzytelnianie Veracode za pomocą konta w usłudze Azure AD przy użyciu Federacji, na podstawie protokołu SAML.

    Twoja aplikacja Veracode oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych, aby Twoje **atrybuty tokenu języka saml** konfiguracji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Atrybuty](./media/veracode-tutorial/tutorial_veracode_attr.png "atrybutów")

6. Aby dodać mapowania wymaganego atrybutu, wykonaj następujące czynności:

    | Nazwa atrybutu | Wartość atrybutu |
    |--- |--- |
    | Imię |User.givenName |
    | nazwisko |User.surname |
    | wyślij wiadomość e-mail |User.mail |
    
    a. Dla każdego wiersza danych w powyższej tabeli, kliknij przycisk **Dodaj atrybut użytkownika**.
    
    ![Atrybuty](./media/veracode-tutorial/tutorial_veracode_addattr.png "atrybutów")
    
    ![Atrybuty](./media/veracode-tutorial/tutorial_veracode_addattr1.png "atrybutów")
    
    b. W **nazwa atrybutu** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. W **wartość atrybutu** pola tekstowego, wybierz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/veracode-tutorial/tutorial_general_400.png)

8. Na **konfiguracji Veracode** , kliknij przycisk **skonfigurować Veracode** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

9. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Veracode, jako administrator.

10. W menu u góry kliknij **ustawienia**, a następnie kliknij przycisk **administratora**.
   
    ![Administracja](./media/veracode-tutorial/ic802911.png "administracji")

11. Kliknij przycisk **SAML** kartę.

12. W **ustawienia języka SAML organizacji** sekcji, wykonaj następujące czynności:
   
    ![Administracja](./media/veracode-tutorial/ic802912.png "administracji")
   
    a.  W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
    
    b. Aby przekazać certyfikat pobrany z witryny Azure portal, kliknij przycisk **wybierz plik**.
   
    c. Wybierz **umożliwić samodzielną rejestrację**.

13. W **ustawienia rejestracji samoobsługowego** sekcji, wykonaj następujące czynności, a następnie kliknij **Zapisz**:
   
    ![Administracja](./media/veracode-tutorial/ic802913.png "administracji")
   
    a. Jako **nowe Aktywacja użytkownika**, wybierz opcję **wymagane uaktywnienie nie**.
   
    b. Jako **dane użytkownika są aktualizowane**, wybierz opcję **dane użytkownika Veracode preferencji**.
   
    c. Aby uzyskać **szczegółów atrybutów SAML**, wybierz następujące pozycje:
      * **Role użytkowników**
      * **Administrator zasad**
      * **Osoba dokonująca przeglądu**
      * **Potencjalny klient zabezpieczeń**
      * **Wykonawczego**
      * **Osoba przesyłająca**
      * **Twórcy**
      * **Wszystkie typy skanowania**
      * **Członkostwo w zespole**
      * **Domyślny zespół**

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/veracode-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/veracode-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/veracode-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-veracode-test-user"></a>Tworzenie użytkownika testowego Veracode
Aby umożliwić użytkownikom usługi Azure AD zalogować się do Veracode, musi być obsługiwana w Veracode. W przypadku Veracode Inicjowanie obsługi to zadania automatycznego. Brak elementu akcji dla Ciebie. Użytkownicy są tworzone automatycznie w razie potrzeby podczas pierwszej pojedynczego logowania jednokrotnego próby.

> [!NOTE]
> Można użyć jakichkolwiek innych Veracode użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Veracode można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Veracode.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Veracode, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Veracode**.

    ![Link Veracode na liście aplikacji](./media/veracode-tutorial/tutorial_veracode_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Veracode w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Veracode.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

