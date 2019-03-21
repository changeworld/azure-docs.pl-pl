---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem życia SilkRoad | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SilkRoad okres zestaw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7f047d784796b9e7c59d95863aa74b3f72da324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995633"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem życia SilkRoad

W tym samouczku dowiesz się, jak zintegrować SilkRoad życia Suite z usługą Azure Active Directory (Azure AD).

Integrowanie SilkRoad życia Suite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do zestawu życia SilkRoad.
- Użytkowników, aby automatycznie uzyskać zalogowanych do SilkRoad okres zestaw (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem życia SilkRoad, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- SilkRoad okres zestaw logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie pakietu życia SilkRoad z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Dodawanie pakietu życia SilkRoad z galerii
Aby skonfigurować integrację SilkRoad życia pakietu w usłudze Azure AD, należy dodać SilkRoad okres zestaw z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pakiet życia SilkRoad z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **SilkRoad okres zestaw**, wybierz opcję **SilkRoad okres zestaw** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SilkRoad okres zestaw na liście wyników](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą zestawu życia SilkRoad oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w zestawie życia SilkRoad do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w zestawie życia SilkRoad musi zostać nawiązane.

W zestawie życia SilkRoad, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SilkRoad okres zestaw, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego SilkRoad okres zestaw](#create-a-silkroad-life-suite-test-user)**  — aby odpowiednikiem Britta Simon w SilkRoad okres zestaw, który jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SilkRoad okres zestaw.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z pakietem życia SilkRoad, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SilkRoad okres zestaw** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

1. Na **SilkRoad okres zestaw domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![SilkRoad okres zestaw domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
1. Na **SilkRoad okres zestaw konfiguracji** , kliknij przycisk **skonfigurować pakiet życia SilkRoad** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![SilkRoad Life Suite Configuration](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

1. Zaloguj się do witryny firmy SilkRoad jako administrator. 
 
    >[!NOTE] 
    > Aby uzyskać dostęp do aplikacji SilkRoad okres zestaw uwierzytelniania dla Konfigurowanie Federacji przy użyciu usługi Microsoft Azure AD, skontaktuj się z SilkRoad pomocy technicznej lub Twoim przedstawicielem SilkRoad usług.

1. Przejdź do **usługodawcy**, a następnie kliknij przycisk **szczegóły Federacji**. 
   
    ![Usługa Azure AD logowanie jednokrotne][10]

1. Kliknij przycisk **pobierania metadanych Federacji**, a następnie zapisz plik metadanych na tym komputerze.
   
    ![Usługa Azure AD logowanie jednokrotne][11] 

1. W swojej **SilkRoad** aplikacji, kliknij przycisk **źródeł uwierzytelniania**.
   
    ![Usługa Azure AD logowanie jednokrotne][12] 

1. Kliknij przycisk **Dodaj źródło uwierzytelniania**. 
   
    ![Usługa Azure AD logowanie jednokrotne][13] 

1. W **Dodaj źródło uwierzytelniania** sekcji, wykonaj następujące czynności: 
   
    ![Usługa Azure AD logowanie jednokrotne][14]
  
    a. W obszarze **opcja 2 — plik metadanych**, kliknij przycisk **Przeglądaj** można przekazać pliku metadanych pobranych z witryny Azure portal.
  
    b. Kliknij przycisk **utworzyć dostawcy tożsamości przy użyciu danych pliku**.

1. W **źródeł uwierzytelniania** kliknij **Edytuj**. 
    
     ![Usługa Azure AD logowanie jednokrotne][15] 

1. Na **Edytuj źródło uwierzytelniania** okno dialogowe, należy wykonać następujące czynności: 
    
     ![Usługa Azure AD logowanie jednokrotne][16] 

    a. Jako **włączone**, wybierz opcję **tak**.

    b. W **EntityId** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
   
    c. W **opis dostawcy tożsamości** pole tekstowe, wpisz opis dla danej konfiguracji (na przykład: *Logowania jednokrotnego usługi Azure AD*).

    d. W **plik metadanych** polu tekstowym Przekaż **metadanych** pliku, który został pobrany z witryny Azure portal.
  
    e. W **Nazwa dostawcy tożsamości** polu tekstowym wpisz nazwę, które są specyficzne dla konfiguracji (na przykład: *Azure SP*).
  
    f. W **adres URL usługi wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    g. W **adres URL usługi logowania jednokrotnego** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    h. Kliknij pozycję **Zapisz**.

1. Wyłącz wszystkie inne źródła uwierzytelniania. 
    
     ![Usługa Azure AD logowanie jednokrotne][17]

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Tworzenie użytkownika testowego SilkRoad okres zestaw

W tej sekcji utworzysz użytkownika o nazwie Britta Simon SilkRoad życia pakietu. Praca z [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) Aby dodać użytkowników na platformie SilkRoad okres zestaw. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do zestawu życia SilkRoad za pomocą platformy Azure logowania jednokrotnego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon SilkRoad okres zestaw, należy wykonać następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SilkRoad okres zestaw**.

    ![Link SilkRoad okres zestaw na liście aplikacji](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SilkRoad okres zestaw w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do SilkRoad życia pakietu aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
