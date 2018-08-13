---
title: 'Samouczek: Integracja usługi Azure Active Directory z Ceridian Dayforce HCM | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 6577955b275adfda3f0cfafe99a8f95efd16403c
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714584"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Samouczek: Integracja usługi Azure Active Directory z Ceridian Dayforce HCM

W tym samouczku dowiesz się, jak zintegrować Ceridian Dayforce HCM w usłudze Azure Active Directory (Azure AD).

Integrowanie Ceridian Dayforce HCM z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Ceridian Dayforce HCM.
- Użytkowników, aby automatycznie uzyskać zalogowanych do HCM Dayforce Ceridian (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Ceridian Dayforce HCM, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Ceridian Dayforce HCM logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Ceridian Dayforce HCM z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Dodawanie Ceridian Dayforce HCM z galerii
Aby skonfigurować integrację usługi Azure AD Ceridian Dayforce HCM, należy dodać Ceridian Dayforce HCM z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Ceridian Dayforce HCM z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Ceridian Dayforce HCM**, wybierz opcję **Ceridian Dayforce HCM** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Ceridian Dayforce HCM, na liście wyników](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ceridian Dayforce HCM, oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Ceridian Dayforce HCM do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Ceridian Dayforce HCM musi zostać nawiązane.

W Ceridian Dayforce HCM, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ceridian Dayforce HCM, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on) ** — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) ** — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Ceridian Dayforce HCM](#create-a-ceridian-dayforce-hcm-test-user) ** — aby odpowiednikiem Britta Simon w Ceridian Dayforce HCM, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) ** — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on) ** — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Ceridian Dayforce HCM.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Ceridian Dayforce HCM** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_samlbase.png)

1. Na **Ceridian Dayforce HCM domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_url.png)
    
    a. W **na adres URL logowania** pole tekstowe, wpisz adres URL używany przez użytkowników do logowania jednokrotnego do aplikacji Ceridian Dayforce HCM.
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Dla testu | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Dla testu | `https://fs-test.dayforcehcm.com/sp` |
    
    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL używany przez usługę Azure AD do opublikowania w odpowiedzi.
    
    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Dla testu | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |
    
    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta HCM Dayforce Ceridian](https://www.ceridian.com/support) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_certificate.png) 

1. Aplikacja Ceridian Dayforce HCM oczekuje twierdzenia SAML w określonym formacie. Praca z [Ceridian Dayforce HCM wsparcia zespołu](https://www.ceridian.com/support) najpierw po to, aby zidentyfikować identyfikator odpowiednich użytkowników. Firma Microsoft zaleca używanie **"name"** atrybut jako identyfikator użytkownika. Możesz zarządzać wartości te atrybuty z **atrybutów użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.  

    ![Konfigurowanie logowania jednokrotnego](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_07.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu |
    | --------------- | -------------------- |    
    | name  | User.extensionattribute2 |    

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/ceridiandayforcehcm-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. W **wartość** wybierz atrybut użytkownika, którego chcesz użyć dla wdrożenia.
    Na przykład, jeśli chcesz używać identyfikatorem EmployeeID jako użytkownik Unikatowy identyfikator i były przechowywane wartości atrybutu w ExtensionAttribute2, następnie wybierz pozycję **user.extensionattribute2**.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/ceridiandayforcehcm-tutorial/tutorial_general_400.png)
    
1. Na **Ceridian Dayforce HCM konfiguracji** , kliknij przycisk **skonfigurować HCM Dayforce Ceridian** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja HCM Ceridian Dayforce](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Ceridian Dayforce HCM** stronie, musisz wysłać pobrany **XML metadanych** i **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [Ceridian Dayforce HCM wsparcia zespołu](https://www.ceridian.com/support).

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem ** Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ceridiandayforcehcm-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ceridiandayforcehcm-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ceridiandayforcehcm-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ceridiandayforcehcm-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-ceridian-dayforce-hcm-test-user"></a>Tworzenie użytkownika testowego Ceridian Dayforce HCM

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w Ceridian Dayforce HCM. Praca z [Ceridian Dayforce HCM wsparcia zespołu](https://www.ceridian.com/support) można pobrać użytkowników dodanych w aplikacji Ceridian Dayforce HCM. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Ceridian Dayforce HCM.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Ceridian Dayforce HCM**.

    ![Konfigurowanie logowania jednokrotnego](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Ceridian Dayforce HCM.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Ceridian Dayforce HCM, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Ceridian Dayforce HCM**.

    ![Łącze Ceridian Dayforce HCM, na liście aplikacji](./media/ceridiandayforcehcm-tutorial/tutorial_ceridiandayforcehcm_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  
Po kliknięciu kafelka Ceridian Dayforce HCM, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Ceridian Dayforce HCM. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_01.png
[2]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_02.png
[3]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_03.png
[4]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_04.png

[100]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_100.png

[200]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_200.png
[201]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_201.png
[202]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_202.png
[203]: ./media/ceridiandayforcehcm-tutorial/tutorial_general_203.png

