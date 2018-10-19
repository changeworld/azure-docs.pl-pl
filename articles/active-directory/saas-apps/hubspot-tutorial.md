---
title: 'Samouczek: Integracja usługi Azure Active Directory z HubSpot | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.openlocfilehash: 76a19668453b35b39359bef442f7f34a06623cbe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431607"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Samouczek: Integracja usługi Azure Active Directory z HubSpot

W tym samouczku dowiesz się, jak zintegrować HubSpot w usłudze Azure Active Directory (Azure AD).

Integrowanie HubSpot z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do HubSpot
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do HubSpot (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą HubSpot, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- HubSpot logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie HubSpot z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-hubspot-from-the-gallery"></a>Dodawanie HubSpot z galerii

Aby skonfigurować integrację HubSpot w usłudze Azure AD, należy dodać HubSpot z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać HubSpot z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **HubSpot**. Wybierz **HubSpot** z panel wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą HubSpot, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w HubSpot do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w HubSpot musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą HubSpot, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego HubSpot](#creating-a-hubspot-saml-test-user)**  — aby odpowiednikiem Britta Simon w HubSpot, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji HubSpot.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z HubSpot, wykonaj następujące czynności:**

1. W witrynie Azure portal na **HubSpot** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_general_301.png)

3. Jeśli musisz zmienić **SAML** trybu z dowolnego innego trybu, kliknij przycisk **zmiana jednego tryb logowania jednokrotnego** na górze ekranu.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_general_300.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_general_302.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji należy wykonać następujące czynności, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![HubSpot domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL, za pomocą następującego wzorca: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL, za pomocą następującego wzorca: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi, które zostało wyjaśnione w dalszej części tego samouczka. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta HubSpot](https://help.hubspot.com/) do uzyskania tych wartości.

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![HubSpot domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://app.hubspot.com/login`

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Na **Konfigurowanie HubSpot** sekcji, kliknij przycisk kopiowania, aby skopiować **adres URL logowania** i **usługi Azure AD identyfikator** wartości.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Otwarcie nowej karty w przeglądarce i zaloguj się do konta administratora HubSpot.

9. Kliknij pozycję **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/config1.png)

10. Kliknij pozycję **konto domyślne**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/config2.png)

11. Przewiń w dół do **zabezpieczeń** sekcji, a następnie kliknij polecenie **Konfigurowanie**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/config3.png)

12. Na **Konfigurowanie logowania jednokrotnego** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/config4.png)

    a. Kliknij przycisk **kopiowania** przycisk, aby skopiować **URl(Service Provider Entity ID) odbiorców** wartość i wklej go w **identyfikator** polu tekstowym w **podstawowe SAML Konfiguracja** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **kopiowania** przycisk, aby skopiować **logowanie się na adres URl, ACS, odbiorca lub przekierowania** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **podstawowe SAML Konfiguracja** sekcji w witrynie Azure portal.

    c. W **identyfikator dostawcy tożsamości lub adres URL wystawcy** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    d. W **pojedynczy znak na adres URL dostawcy tożsamości** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    e. Otwórz swoje pobrany **Certificate(Base64)** pliku w Notatniku. Skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat X.509** pole.

    f. Kliknij pozycję **Zweryfikuj**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hubspot-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hubspot-tutorial/create_aaduser_02.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-hubspot-test-user"></a>Tworzenie użytkownika testowego HubSpot

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do HubSpot, musi być obsługiwana w HubSpot.
W przypadku HubSpot Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **HubSpot** witryny firmy jako administrator.

2. Kliknij pozycję **ikonę ustawienia** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/config1.png)

3. Kliknij pozycję **użytkownicy i zespoły**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/user1.png)

4. Kliknij przycisk **Utwórz użytkownika**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/user2.png)

5. Wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com** w **Dodaj adres e-mail addess(es)** polu tekstowym i kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/user3.png)

6. Na **tworzenia użytkowników** sekcji, przechodzą przez co pojedyncza karta i wybierz odpowiednie opcje i uprawnienia dla użytkownika i kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/user4.png)

7. Kliknij przycisk **wysyłania** można wysłać zaproszenia do użytkownika.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Użytkownik będzie aktywować po zaakceptowaniu zaproszenia.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do HubSpot.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **HubSpot**.

    ![Konfigurowanie logowania jednokrotnego](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka HubSpot w panelu dostępu, powinna pojawić się automatycznie strony logowania, HubSpot aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png