---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą ArcGIS Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ca5bf7ae49cf120c0566419ccadeff92433c6467
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819898"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą ArcGIS Enterprise

W tym samouczku dowiesz się, jak zintegrować ArcGIS przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).

Integracja przedsiębiorstwa ArcGIS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do organizacji ArcGIS.
- Użytkowników, aby automatycznie uzyskać zalogowanych do przedsiębiorstwa ArcGIS (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD z usługą ArcGIS Enterprise, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- ArcGIS Enterprise logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ArcGIS przedsiębiorstwa z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Dodawanie ArcGIS przedsiębiorstwa z galerii

Aby skonfigurować integrację programu ArcGIS Enterprise w usłudze Azure AD, należy dodać ArcGIS przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ArcGIS przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ArcGIS Enterprise**, wybierz opcję **ArcGIS Enterprise** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![ArcGIS Enterprise na liście wyników](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z usługą ArcGIS Enterprise w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przedsiębiorstwie ArcGIS dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie ArcGIS musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą ArcGIS Enterprise, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkowników testowych w przedsiębiorstwie ArcGIS](#create-an-arcgis-enterprise-test-user)**  — aby odpowiednikiem Britta Simon w przedsiębiorstwie ArcGIS, które jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji przedsiębiorstwa ArcGIS.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą ArcGIS Enterprise, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ArcGIS Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Na **ArcGIS Enterprise domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Komponent ArcGIS Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<EXTERNAL_DNS_NAME>.portal`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Komponent ArcGIS Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej ArcGIS Enterprise Client](mailto:nshampur@esri.com) do uzyskania tych wartości. Zostanie pobrana wartość identyfikatora **zestawu dostawcy tożsamości** sekcję, co zostało wyjaśnione w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny ArcGIS Enterprise firmy.

8. Wybierz **organizacji > Edytuj ustawienia**.

    ![Konfiguracja dla przedsiębiorstw ArcGIS](./media/arcgisenterprise-tutorial/configure1.png)

9. Wybierz **zabezpieczeń** kartę.

    ![Konfiguracja dla przedsiębiorstw ArcGIS](./media/arcgisenterprise-tutorial/configure2.png)

10. Przewiń w dół do **Enterprise logowania za pośrednictwem protokołu SAML** i wybierz pozycję **Ustaw logowania ENTERPRISE**.

    ![Konfiguracja dla przedsiębiorstw ArcGIS](./media/arcgisenterprise-tutorial/configure3.png)

11. Na **zestawu dostawcy tożsamości** sekcji, wykonaj następujące czynności:

    ![Konfiguracja dla przedsiębiorstw ArcGIS](./media/arcgisenterprise-tutorial/configure4.png)

    a. Podaj nazwę, takich jak **Azure Active Directory Test** w **nazwa** pola tekstowego.

    b. W **adresu URL** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal.

    c. Kliknij przycisk **Pokaż zaawansowane ustawienia** i skopiuj **identyfikator jednostki** wartość i wklej go w **identyfikator** polu tekstowym w **ArcGIS Enterprise domena i adresy URL** sekcji w witrynie Azure portal.
    
    ![Konfiguracja dla przedsiębiorstw ArcGIS](./media/arcgisenterprise-tutorial/configure5.png)

    d. Kliknij przycisk **dostawcy tożsamości aktualizacji**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-an-arcgis-enterprise-test-user"></a>Tworzenie użytkowników testowych w przedsiębiorstwie ArcGIS

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w przedsiębiorstwie ArcGIS. ArcGIS Enterprise obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu ArcGIS przedsiębiorstwa, jeśli go jeszcze nie istnieje.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej ArcGIS Enterprise](mailto:nshampur@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do przedsiębiorstwa ArcGIS.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon ArcGIS przedsiębiorstwa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **ArcGIS Enterprise**.

    ![Link ArcGIS przedsiębiorstwa, na liście aplikacji](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ArcGIS Enterprise w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji przedsiębiorstwa ArcGIS.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png