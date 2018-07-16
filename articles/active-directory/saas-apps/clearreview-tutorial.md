---
title: 'Samouczek: Integracja usługi Azure Active Directory z przeglądem Clear | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i wyczyść przeglądu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 6ce6661bf6d3841f7ade78a74d50a1d6eeefbdaf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047998"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Samouczek: Integracja usługi Azure Active Directory z przeglądem wyczyść

W tym samouczku dowiesz się, jak zintegrować wyczyść przeglądu przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie wyczyść przeglądu z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do przeglądu Wyczyść.
- Użytkowników, aby automatycznie uzyskać zalogowanych do zwykłego przeglądu (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z przeglądem Wyczyść, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Wyczyść przeglądu logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie przeglądu Wyczyść z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-clear-review-from-the-gallery"></a>Dodawanie przeglądu Wyczyść z galerii
Aby skonfigurować integrację wyczyść przeglądu w usłudze Azure AD, należy dodać wyczyść przeglądu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać wyczyść przeglądu z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Przeglądanie wyczyść**, wybierz opcję **Przeglądanie wyczyść** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Wyczyść przeglądu na liście wyników](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą wyczyść przeglądu, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przeglądzie wyczyść do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w przeglądzie wyczyść musi zostać nawiązane.

W przeglądzie Wyczyść, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z przeglądem Wyczyść, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Przeglądanie wyczyść](#create-a-clear-review-test-user)**  — aby odpowiednikiem Britta Simon w wyczyść przeglądu, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji wyczyść przeglądu.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z przeglądem Wyczyść, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Przeglądanie wyczyść** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. Na **wyczyść przeglądu domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości inicjowane** trybu:

    ![Wyczyść przeglądu domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customer name>.clearreview.com/sso/metadata/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customer name>.clearreview.com/sso/acs/`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Wyczyść przeglądu domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi. Skontaktuj się z pomocą [wyczyść zespołu pomocy technicznej](https://clearreview.com/contact/) do uzyskania tych wartości.

5. Wyczyść przeglądanie aplikacji oczekiwać, że wartość identyfikatora unikatowego użytkownika oświadczenia identyfikator nazwy. Należy zamapować wartość identyfikatora użytkownika do **user.mail**.

    ![W sekcji atrybut](./media/clearreview-tutorial/attribute.png)


6. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/clearreview-tutorial/tutorial_general_400.png)

8. Na **wyczyść Przejrzyj konfigurację** , kliknij przycisk **skonfigurować Przeglądanie wyczyść** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Wyczyść Przejrzyj konfigurację](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

9. Aby skonfigurować logowanie jednokrotne na **Przeglądanie wyczyść** po stronie Otwórz **Przeglądanie wyczyść** portalu przy użyciu poświadczeń administracyjnych.

10. Wybierz **administratora** w lewym obszarze nawigacji.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

11. Wybierz **zmiany** w dolnej części strony.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

12. Wykonaj poniższe czynności **ustawienia rejestracji jednokrotnej** strony

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. W **adres URL wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    b. W **punktem końcowym SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.    

    c. W **punktu końcowego SLO** pola tekstowego, Wklej wartość **adres URL logowania jednokrotnego usługi** skopiowanej w witrynie Azure portal. 

    d. Otwórz pobranego certyfikatu w programie Notatnik i Wklej zawartość **certyfikat X.509** pola tekstowego.   

13. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/clearreview-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/clearreview-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/clearreview-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/clearreview-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-clear-review-test-user"></a>Tworzenie użytkownika testowego wyczyść przeglądu

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przeglądzie Wyczyść. Skontaktuj się z [wyczyść zespołu pomocy technicznej](https://clearreview.com/contact/) Aby dodać użytkowników na platformie wyczyść przeglądu.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do przeglądu Wyczyść.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon wyczyść przeglądu, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Przeglądanie wyczyść**.

    ![Wyczyść przeglądu łącze na liście aplikacji](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka przeglądu zwykłego panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do wyczyść przeglądu aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
