---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą portalu wierzchołku | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory a portalem wierzchołku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: db204a46-6460-4ace-bdbb-4353846723ad
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: 4c267313e4851e621b57aa1d2bddc73118405776
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054237"
---
# <a name="tutorial-azure-active-directory-integration-with-apex-portal"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą portalu wierzchołka

W tym samouczku dowiesz się, jak zintegrować wierzchołku Portal przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie portalu wierzchołka z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do portalu wierzchołku.
- Użytkowników, aby automatycznie uzyskać zalogowanych do portalu wierzchołku (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą portalu wierzchołku, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Portal wierzchołku logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie portalu wierzchołka z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-apex-portal-from-the-gallery"></a>Dodawanie portalu wierzchołka z galerii
Aby skonfigurować integrację portalu Apex w usłudze Azure AD, należy dodać Portal wierzchołka z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Portal wierzchołka z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Portal wierzchołku**, wybierz opcję **Portal wierzchołku** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Portal wierzchołku na liście wyników](./media/apexportal-tutorial/tutorial_apexonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą portalu wierzchołku oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w wierzchołku portalu dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w portalu wierzchołku musi nawiązać.

W portalu wierzchołku przypisze się wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą portalu wierzchołku, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Portal wierzchołku](#create-an-apex-online-test-user)**  — aby odpowiednikiem Britta Simon w portalu wierzchołku, który jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Portal wierzchołku.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą portalu wierzchołku, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Portal wierzchołku** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/apexportal-tutorial/tutorial_apexonline_samlbase.png)

3. Na **wierzchołku Portal domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Wierzchołku Portal domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/apexportal-tutorial/tutorial_apexonline_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej w portalu wierzchołku](mailto:support@apexanalytix.com) do uzyskania tych wartości.
 
4. Aplikacja Portal wierzchołku oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/apexportal-tutorial/attribute.png)

5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | IMIĘ | user.givenname |
    | NAZWISKO | user.surname |
    | WIADOMOŚCI E-MAIL | User.mail |    

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/apexportal-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/apexportal-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/apexportal-tutorial/tutorial_apexonline_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/apexportal-tutorial/tutorial_general_400.png)

8. Aby skonfigurować logowanie jednokrotne na **Portal wierzchołku** stronie, musisz wysłać pobrany **XML metadanych** do [wierzchołku portalu zespołu pomocy technicznej](mailto:support@apexanalytix.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/apexportal-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/apexportal-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/apexportal-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/apexportal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-an-apex-portal-test-user"></a>Tworzenie użytkownika testowego wierzchołku portalu

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w portalu wierzchołku. Portal wierzchołku obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu wierzchołku Portal, jeśli go jeszcze nie istnieje.
 
> [!NOTE]
> Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [zespołem pomocy technicznej w portalu wierzchołku](mailto:support@apexanalytix.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do portalu wierzchołku.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon wierzchołku Portal, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Portal wierzchołku**.

    ![Link języka Apex portalu na liście aplikacji](./media/apexportal-tutorial/tutorial_apexonline_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Apex Portal w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Portal wierzchołku.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/apexportal-tutorial/tutorial_general_01.png
[2]: ./media/apexportal-tutorial/tutorial_general_02.png
[3]: ./media/apexportal-tutorial/tutorial_general_03.png
[4]: ./media/apexportal-tutorial/tutorial_general_04.png

[100]: ./media/apexportal-tutorial/tutorial_general_100.png

[200]: ./media/apexportal-tutorial/tutorial_general_200.png
[201]: ./media/apexportal-tutorial/tutorial_general_201.png
[202]: ./media/apexportal-tutorial/tutorial_general_202.png
[203]: ./media/apexportal-tutorial/tutorial_general_203.png

