---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu schematu organizacyjnego teraz | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i teraz schematu organizacyjnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: e23d76074f4b428b672e0cd5aeeaba99d080a4cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435938"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Samouczek: Integracja usługi Azure Active Directory z teraz schematu organizacyjnego

W tym samouczku dowiesz się, jak zintegrować teraz schematu organizacyjnego przy użyciu usługi Azure Active Directory (Azure AD).

Integrowanie schematu organizacyjnego teraz z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do chwili schematu organizacyjnego.
- Użytkowników, aby automatycznie uzyskać zalogowanych do teraz schemat organizacyjny (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu schematu organizacyjnego teraz, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Schemat organizacyjny teraz logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie schematu organizacyjnego teraz z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-orgchart-now-from-the-gallery"></a>Dodawanie schematu organizacyjnego teraz z galerii
Aby skonfigurować integrację teraz schematu organizacyjnego w usłudze Azure AD, należy dodać teraz schematu organizacyjnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać teraz schematu organizacyjnego z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **teraz schematu organizacyjnego**, wybierz opcję **teraz schemat organizacyjny** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Schemat organizacyjny teraz na liście wyników](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowania jednokrotnego przy użyciu schematu organizacyjnego teraz oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika teraz schemat organizacyjny do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika teraz schematu organizacyjnego musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu schematu organizacyjnego teraz, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego teraz schematu organizacyjnego](#create-an-orgchart-now-test-user)**  — aby odpowiednikiem Britta Simon teraz schematu organizacyjnego, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji teraz schematu organizacyjnego.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z teraz schematu organizacyjnego, wykonaj następujące czynności:**

1. W witrynie Azure portal na **teraz schematu organizacyjnego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Na **schematu organizacyjnego teraz domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Schemat organizacyjny teraz domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://sso2.orgchartnow.com`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Schemat organizacyjny teraz domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` Identyfikator jednostki SAML został skopiowany z sekcji krótki, opisany w dalszej części samouczka.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Na **schematu organizacyjnego teraz konfiguracji** kliknij **Konfiguruj teraz schematu organizacyjnego** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** z **krótki przewodnik po sekcji** i za jego pomocą wykonaj **adres URL logowania** w **schematu organizacyjnego teraz domena i adresy URL w sekcji**.

    ![Schemat organizacyjny teraz konfiguracji](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **teraz schematu organizacyjnego** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej teraz schemat organizacyjny](mailto:ocnsupport@officeworksoftware.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-orgchart-now-test-user"></a>Tworzenie użytkownika testowego teraz schematu organizacyjnego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do chwili schematu organizacyjnego, musi być obsługiwana w chwili schematu organizacyjnego. 

1. Schemat organizacyjny teraz obsługę just-in-time, który jest domyślnie włączona. Nowy użytkownik jest tworzony podczas próby dostępu teraz schematu organizacyjnego, jeśli go jeszcze nie istnieje. Tylko będzie utworzyć użytkownika w czasie inicjowania obsługi funkcji **tylko do odczytu** użytkownika, gdy żądanie logowania jednokrotnego pochodzi z rozpoznanym dostawcy tożsamości i wiadomości e-mail w potwierdzenie SAML nie znajduje się na liście użytkowników. Automatycznie inicjowania obsługi funkcji, musisz utworzyć grupę dostępu, pod tytułem **ogólne** teraz schematu organizacyjnego. Wykonaj poniższe kroki, aby utworzyć grupę dostępu:

    a. Przejdź do **Zarządzaj grupami** opcja po kliknięciu przycisku **koło zębate** w prawym górnym rogu interfejsu użytkownika.

    ![Schemat organizacyjny teraz grup](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Wybierz **Dodaj** ikonę i nazwy grupy **ogólne** kliknięcie **OK**. 

    ![Dodaj teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Wybierz foldery, które mają użytkownicy ogólne lub tylko do odczytu, aby można było uzyskać dostęp do:

    ![Schemat organizacyjny teraz folderów](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zablokuj** foldery tak, aby można je modyfikować tylko administratorom. Następnie naciśnij klawisz **OK**.

    ![Schemat organizacyjny teraz blokują](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Aby utworzyć **administratora** użytkowników i **odczytu/zapisu** użytkowników, należy ręcznie utworzyć użytkownika w celu uzyskania dostępu do ich poziomu uprawnień przy użyciu logowania jednokrotnego. Aby udostępnić konto użytkownika, wykonaj następujące czynności:

    a. Zaloguj się do schematu organizacyjnego teraz jako Administrator zabezpieczeń.

    b.  Kliknij pozycję **ustawienia** w prawym górnym rogu, a następnie przejdź do **Zarządzanie użytkownikami**.

    ![Schemat organizacyjny teraz ustawienia](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kliknij pozycję **Dodaj** i wykonaj następujące czynności:

    ![Zarządzanie teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * W **identyfikator użytkownika** polu tekstowym wprowadź identyfikator użytkownika, takich jak **brittasimon@contoso.com**.

    * W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    * Kliknij pozycję **Add** (Dodaj).
    
### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do schematu organizacyjnego teraz.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon teraz schematu organizacyjnego, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **teraz schematu organizacyjnego**.

    ![Schemat organizacyjny teraz link na liście aplikacji](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka teraz schematu organizacyjnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikację teraz schematu organizacyjnego.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

