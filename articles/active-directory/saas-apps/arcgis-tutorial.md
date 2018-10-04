---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą usługi ArcGIS Online | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługi ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269038"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą usługi ArcGIS Online

W tym samouczku dowiesz się, jak zintegrować usługi ArcGIS Online z usługą Azure Active Directory (Azure AD).

Integrowanie usługi ArcGIS Online z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi ArcGIS Online.
- Użytkowników, aby automatycznie uzyskać zalogowanych do usługi ArcGIS Online (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi ArcGIS Online, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Usługa ArcGIS Online logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi ArcGIS Online za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-arcgis-online-from-the-gallery"></a>Dodawanie usługi ArcGIS Online za pomocą galerii

Aby skonfigurować integrację usługi ArcGIS Online w usłudze Azure AD, należy dodać ArcGIS Online z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi ArcGIS Online z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **ArcGIS Online**, wybierz opcję **ArcGIS Online** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, skonfiguruj i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi ArcGIS Online w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ArcGIS Online jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w usłudze ArcGIS Online musi można ustanowić.

ArcGIS online, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi ArcGIS Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie usługi ArcGIS Online użytkownika testowego](#create-an-arcgis-online-test-user)**  — aby odpowiednikiem Britta Simon w ArcGIS Online, które jest połączone z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usługi ArcGIS Online.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą usługi ArcGIS Online, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **ArcGIS Online** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **pojedynczej zmiany trybu logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.maps.arcgis.com`.

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej ArcGIS Online klienta](http://support.esri.com/en/) do uzyskania tych wartości.

6. Na **certyfikat podpisywania SAML** sekcji, kliknij na **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik xml na tym komputerze.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Aby zautomatyzować konfigurację w ramach **ArcGIS Online**, musisz zainstalować **Moje zabezpieczenia aplikacji logowania rozszerzenia przeglądarki do obsługi** , klikając **zainstalować rozszerzenie**.

    ![image](./media/arcgis-tutorial/install_extension.png)

8. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Instalatora usługa ArcGIS Online** nastąpi bezpośrednie przekierowanie do aplikacji usługa ArcGIS Online. W tym miejscu podaj poświadczenia administratora do logowania się do usługi ArcGIS Online. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzowania czynności 9 – 13.

9. Jeśli chcesz ręcznie skonfigurować usługi ArcGIS Online, Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy ArcGIS jako administrator i wykonaj następujące czynności:

10. Kliknij przycisk **edytowanie ustawień**.

    ![Edytuj ustawienia](./media/arcgis-tutorial/ic784742.png "Edytuj ustawienia")

11. Kliknij przycisk **zabezpieczeń**.

    ![Zabezpieczenia](./media/arcgis-tutorial/ic784743.png "zabezpieczeń")

12. W obszarze **logowania Enterprise**, kliknij przycisk **dostawcy tożsamości zestawu**.

    ![Nazwy logowania organizacji](./media/arcgis-tutorial/ic784744.png "logowania organizacji")

13. Na **zestawu dostawcy tożsamości** konfiguracji strony, wykonaj następujące czynności:

    ![Ustawienie dostawcy tożsamości](./media/arcgis-tutorial/ic784745.png "ustawienie dostawcy tożsamości")

    a. W **nazwa** polu tekstowym wpisz nazwę swojej organizacji.

    b. Dla **metadanych dla dostawcy tożsamości organizacji zostaną udostępnione za pomocą**, wybierz opcję **pliku**.

    c. Aby przekazać plik metadanych pobrany, kliknij przycisk **Choose file**.

    d. Kliknij przycisk **dostawcy tożsamości zestawu**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="create-an-arcgis-online-test-user"></a>Tworzenie usługi ArcGIS Online użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do usługi ArcGIS Online, musi być obsługiwana w ArcGIS Online.  
W przypadku usługi ArcGIS Online aprowizacji to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **ArcGIS** dzierżawy.

2. Kliknij przycisk **ZAPROŚ członków**.
   
    ![Zaproś członków](./media/arcgis-tutorial/ic784747.png "zapraszać członków")

3. Wybierz **dodawać członków automatycznie bez wysyłania wiadomości e-mail**, a następnie kliknij przycisk **dalej**.
   
    ![Dodaj członków automatycznie](./media/arcgis-tutorial/ic784748.png "automatyczne dodawanie członków")

4. Na **członków** okna dialogowego strony, wykonaj następujące czynności:
   
     ![Dodaj i sprawdź](./media/arcgis-tutorial/ic784749.png "Dodaj i przeglądu")
    
     a. Wprowadź **E-mail**, **imię**, i **nazwisko** poprawnego konta usługi AAD do aprowizowania.
  
     b. Kliknij przycisk **Dodaj i przegląd**.
5. Przejrzyj dane zostały wprowadzone, a następnie kliknij przycisk **dodawanie członków**.
   
    ![Dodawanie elementu członkowskiego](./media/arcgis-tutorial/ic784750.png "Dodawanie elementu członkowskiego")
        
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory będzie otrzymywać wiadomości e-mail i kliknij link, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi ArcGIS Online.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługa ArcGIS Online, w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji usługa ArcGIS Online.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



