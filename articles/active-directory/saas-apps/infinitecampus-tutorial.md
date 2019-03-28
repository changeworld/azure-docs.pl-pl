---
title: 'Samouczek: Integracja usługi Azure Active Directory z Kampusu nieskończonej | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Campus nieskończone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d7d194d810e0fd3b9fb57b0876bee12447f65c6
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519871"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Samouczek: Integracja usługi Azure Active Directory z Kampusu nieskończona

W tym samouczku dowiesz się, jak zintegrować nieskończonej Campus za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie nieskończonej Campus z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Campus nieskończone.
- Użytkowników, aby automatycznie uzyskać zalogowanych do nieskończonej Campus (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Kampusu nieskończoną, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Nieskończona Campus logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska usługi Azure AD w wersji próbnej, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Co najmniej musisz być administratorem usługi Azure Active Directory i mieć rolę zabezpieczeń produktu Campus z "Student informacji systemu (SIS)" Aby ukończyć konfigurację.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie nieskończonej Campus z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-infinite-campus-from-the-gallery"></a>Dodawanie nieskończonej Campus z galerii

Aby skonfigurować integrację z Kampusu nieskończonej w usłudze Azure AD, należy dodać nieskończonej Campus z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać nieskończonej Campus z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Campus nieskończonej**, wybierz opcję **Campus nieskończonej** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Nieskończona Campus na liście wyników](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą nieskończonej Campus oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w nieskończonej Campus do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w nieskończonej Campus musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z Kampusu nieskończoną, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego nieskończonej Campus](#creating-an-infinite-campus-test-user)**  — aby odpowiednikiem Britta Simon w nieskończonej Campus, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Campus nieskończone.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z Kampusu nieskończoną, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Campus nieskończonej** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli masz **plik metadanych usługodawcy** wyeksportowany z Kampusu nieskończoną, wykonaj kroki 4.a za pośrednictwem 4.d, a następnie przejdź do kroku 11.c. Jeśli nie masz pliku metadanych dostawcy usługi, przejdź do kroku 5.

    a. Kliknij przycisk **przekazywania pliku metadanych**.

        ![image](common/b9_saml.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![image](common/b9(1)_saml.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji pola tekstowego, jak pokazano poniżej :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca (przy użyciu modelu hostingu mogą się różnić domeny): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

5. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności (należy pamiętać, że domena zależy od modelu hostingu):

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nieskończona Campus domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. Na **certyfikat podpisywania SAML** stronie **certyfikat podpisywania SAML** , kliknij przycisk kopiowania **ikonę** do skopiowania **adres Url metadanych Federacji aplikacji**  i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Na **Konfigurowanie nieskończonej Campus** sekcji, użyj następujących wartości do sprawdzania poprawności podczas przekazywania lub przy użyciu pliku lub adres URL metadanych platformy Azure.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Nieskończona Campus konfiguracji](common/configuresection.png)

8. W oknie przeglądarki internetowej innej, zaloguj się do nieskończonej Campus jako Administrator zabezpieczeń.

9. W lewej części menu, kliknij polecenie **administrowania systemem**.

    ![Administrator](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Przejdź do **zabezpieczeń użytkownika** > **zarządzania SAML** > **Konfiguracja dostawcy usługi logowania jednokrotnego**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Na **Konfiguracja dostawcy usługi logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![Usługa rejestracji jednokrotnej](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Wybierz **Włącz SAML logowania jednokrotnego**.
    
    b. Edytuj **opcjonalna nazwa atrybutu** zawierać **nazwy**
    
    c. Na **opcję Pobierz dane serwera dostawcy tożsamości (IDP)** wybierz **adres URL metadanych**, Wklej **adres Url metadanych Federacji aplikacji** (z kroku 6 powyżej) w pola, a następnie kliknij przycisk **synchronizacji**.

    d. Kliknij **metadanych dostawcy usługi** łącze, aby zapisać **plik metadanych dostawcy usług** na komputerze, a następnie przekaż go w **podstawową konfigurację protokołu SAML** sekcji, aby automatycznie Wypełnij **identyfikator** i **adres URL odpowiedzi** wartości w witrynie Azure portal (w tym celu należy odwoływać się do kroku 4 do przekazywania i automatyczne wypełnianie wartości, lub krok 5 do ręcznego wprowadzania).

    e. Po kliknięciu przycisku **synchronizacji** wartości pobrać, automatycznie wypełnione w **Konfiguracja dostawcy usługi logowania jednokrotnego** strony.

    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie _pojedynczego_ użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-an-infinite-campus-test-user"></a>Tworzenie użytkownika testowego Campus nieskończona

Nieskończona Campus ma architekturę dane demograficzne, a ich tematyka. Skontaktuj się z pomocą [zespołem pomocy technicznej nieskończonej Campus](mailto:sales@infinitecampus.com) Aby dodać użytkowników na platformie Campus nieskończoną.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Campus nieskończoną.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Campus nieskończonej**.

    ![Konfigurowanie logowania jednokrotnego](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Campus nieskończonej w panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do aplikacji Campus nieskończone. Jeśli logujesz się do aplikacji Campus nieskończonej w tej samej przeglądarce, które są administrowaniu usługą Azure AD, upewnij się, że użytkownik jest zalogowany do usługi Azure AD jako użytkownika testowego. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
