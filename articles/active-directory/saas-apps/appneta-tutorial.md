---
title: 'Samouczek: Integracja usługi Azure Active Directory z Monitora wydajności AppNeta | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i AppNeta monitora wydajności.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: ccedc0288e313df2639862a14078d8cad9951286
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054553"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą AppNeta Performance Monitor

W tym samouczku dowiesz się, jak zintegrować AppNeta monitora wydajności z usługi Azure Active Directory (Azure AD).

Integrowanie AppNeta monitora wydajności z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do AppNeta monitora wydajności.
- Użytkowników, aby automatycznie uzyskać zalogowanych do AppNeta monitora wydajności (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu Monitora wydajności AppNeta, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Monitor wydajności AppNeta logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodanie monitora wydajności AppNeta z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Dodanie monitora wydajności AppNeta z galerii
Aby skonfigurować integrację AppNeta monitora wydajności w usłudze Azure AD, należy dodać AppNeta monitora wydajności z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Monitor wydajności AppNeta z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **monitora wydajności AppNeta**, wybierz opcję **monitora wydajności AppNeta** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Monitor wydajności AppNeta na liście wyników](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Monitora wydajności AppNeta w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Monitorze wydajności AppNeta do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Monitorze wydajności AppNeta musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą AppNeta Performance Monitor, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego monitora wydajności AppNeta](#create-an-appneta-performance-monitor-test-user)**  — aby odpowiednikiem Britta Simon w Monitorze wydajności AppNeta połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji AppNeta monitora wydajności.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu Monitora wydajności AppNeta, wykonaj następujące czynności:**

1. W witrynie Azure portal na **monitora wydajności AppNeta** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. Na **AppNeta wydajności Monitor domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domen monitora wydajności AppNeta pojedynczego logowania jednokrotnego informacji](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.pm.appneta.com`

    b. W **identyfikator** polu tekstowym wpisz wartość: `PingConnect`

    > [!NOTE] 
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta monitora wydajności AppNeta](mailto:support@appneta.com) aby zyskać tę wartość. 

5. Monitor wydajności AppNeta aplikacja oczekuje twierdzenia SAML, w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie logowania jednokrotnego](./media/appneta-tutorial/attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
           
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| ----------------|
    | Imię| user.givenname|
    | Nazwisko| user.surname|
    | wyślij wiadomość e-mail| user.userprincipalname|
    | name| user.userprincipalname|
    | grupy   | User.assignedroles |
    | telefon| User.telephonenumber |
    | tytuł| user.jobtitle|

    > [!NOTE]
    > "groups" odnosi się do grupy zabezpieczeń w Appneta, która jest mapowana do roli w usłudze Azure AD. Zapoznaj się [to](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) doc, w którym wyjaśniono, jak utworzyć niestandardowe role w usłudze Azure AD.
        
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurowanie logowania jednokrotnego](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw puste przestrzeni nazw.
    
    e. Kliknij przycisk **OK**.  

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/appneta-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne na **monitora wydajności AppNeta** stronie, musisz wysłać pobrany **XML metadanych** do [AppNeta monitora wydajności zespołu pomocy technicznej](mailto:support@appneta.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/appneta-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/appneta-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/appneta-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/appneta-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Tworzenie użytkownika testowego AppNeta monitora wydajności

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Monitorze wydajności AppNeta. Monitor wydajności AppNeta obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu AppNeta monitora wydajności, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej w Monitorze wydajności AppNeta](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do monitora wydajności AppNeta za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon AppNeta monitora wydajności, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **monitora wydajności AppNeta**.

    ![Monitor wydajności AppNeta łącze na liście aplikacji](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka AppNeta monitora wydajności w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do AppNeta monitora wydajności aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

