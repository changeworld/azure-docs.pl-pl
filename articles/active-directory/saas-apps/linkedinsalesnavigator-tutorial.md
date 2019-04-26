---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia LinkedIn Sales Navigator | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63e3e98c2c3dc8f99e733174c86965304fe483ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60257744"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia LinkedIn Sales Navigator

W tym samouczku dowiesz się, jak zintegrować narzędzia LinkedIn Sales Navigator w usłudze Azure Active Directory (Azure AD).

Integrowanie narzędzia LinkedIn Sales Navigator z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do narzędzia LinkedIn Sales Navigator
- Użytkowników, aby automatycznie uzyskać zalogowanych do narzędzia LinkedIn Sales Navigator (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, Przeglądaj [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą narzędzia LinkedIn Sales Navigator, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Narzędzia LinkedIn Sales Navigator logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Należy unikać używania środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawania narzędzia LinkedIn Sales Navigator z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Dodawania narzędzia LinkedIn Sales Navigator z galerii
Aby skonfigurować integrację z narzędzia LinkedIn Sales Navigator w usłudze Azure AD, należy dodać narzędzia LinkedIn Sales Navigator z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać narzędzia LinkedIn Sales Navigator z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **narzędzia LinkedIn Sales Navigator**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. W panelu wyników wybierz **narzędzia LinkedIn Sales Navigator**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia LinkedIn Sales Navigator oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w narzędzia LinkedIn Sales Navigator do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w serwisie LinkedIn Sales Navigator musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w narzędzia LinkedIn Sales Navigator.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia LinkedIn Sales Navigator, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego narzędzia LinkedIn Sales Navigator](#creating-a-linkedin-sales-navigator-test-user)**  — aby odpowiednikiem Britta Simon w narzędzia LinkedIn Sales Navigator połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji narzędzia LinkedIn Sales Navigator.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z narzędzia LinkedIn Sales Navigator, wykonaj następujące czynności:**

1. W witrynie Azure portal na **narzędzia LinkedIn Sales Navigator** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okna dialogowego w **tryb** wybierz **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **narzędzia LinkedIn Sales Navigator** witryny sieci Web jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Zaznacz również **Sales Navigator** z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij przycisk **lub kliknij tutaj, aby załadować i skopiuj poszczególne pola w formularzu** i skopiuj **identyfikator jednostki** i **adresu Url asercji klienta dostępu (ACS)**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. W witrynie Azure portal w obszarze **LinkedIn Sales Navigator domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. W **identyfikator** polu tekstowym wprowadź **identyfikator jednostki** skopiowane z portalu usługi LinkedIn 

    b. W **adres URL odpowiedzi** polu tekstowym wprowadź **adresu Url asercji klienta dostępu (ACS)** skopiowane z portalu usługi LinkedIn

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Twoje **narzędzia LinkedIn Sales Navigator** aplikacja oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale narzędzia LinkedIn Sales Navigator oczekuje, że jest mapowany za pomocą adresu e-mail użytkownika. Możesz użyć **user.mail** atrybutu z listy lub użyj wartości odpowiedni atrybut, na podstawie konfiguracji organizacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. W **atrybutów użytkownika** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** i ustawić atrybuty. Użytkownik musi dodać cztery oświadczeń o nazwie **e-mail**, **działu**, **firstname**, i **lastname** , a wartość to mają być mapowane z **user.mail**, **user.department**, **user.givenname**, i **user.surname** odpowiednio

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Kliknij pozycję **Dodawanie atrybutu** aby otworzyć okno dialogowe atrybutu.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Wykonaj następujące czynności na **nazwa** — atrybut

    a. Kliknij pozycję atrybutu, aby otworzyć **Edytuj atrybut** okna.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Usuń wartość adresu URL z **przestrzeni nazw**.
    
    c. Kliknij przycisk **Ok** można zapisać ustawienia.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Kliknij przycisk **pliku XML Przekaż** można przekazać pliku XML metadanych, który został pobrany z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Tworzenie użytkownika testowego narzędzia LinkedIn Sales Navigator

Połączone Sales Navigator aplikacji obsługuje tylko w aprowizacji użytkowników czas (JIT) i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. Aktywuj **automatycznie przypisywać licencje** Aby przypisać licencję do użytkownika.
   
   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do narzędzia LinkedIn Sales Navigator.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon narzędzia LinkedIn Sales Navigator, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **narzędzia LinkedIn Sales Navigator**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka narzędzia LinkedIn Sales Navigator w panelu dostępu, powinno nastąpić przekierowanie do strony organizacji, którym trzeba podać informacje osobowe konta LinkedIn. Łączy on konto osobiste za pomocą konta LinkedIn firmy. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

