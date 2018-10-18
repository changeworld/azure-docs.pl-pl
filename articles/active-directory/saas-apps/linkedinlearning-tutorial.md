---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą LinkedIn Learning | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 618437d0007668800e0a14e8233db1676be2a364
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379267"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Samouczek: Integracja usługi Azure Active Directory z usługą LinkedIn Learning

W tym samouczku dowiesz się, jak zintegrować LinkedIn Learning z usługą Azure Active Directory (Azure AD).

Integracja usługi LinkedIn Learning z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi LinkedIn Learning
- Użytkowników, aby automatycznie uzyskać zalogowanych do LinkedIn Learning (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą LinkedIn Learning, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- LinkedIn Learning logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LinkedIn Learning w galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-linkedin-learning-from-the-gallery"></a>Dodawanie LinkedIn Learning w galerii
Aby skonfigurować integrację usługi LinkedIn Learning w usłudze Azure AD, należy dodać LinkedIn Learning w galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LinkedIn Learning w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LinkedIn Learning**. Panel wyników kliknij **LinkedIn Learning** umożliwiające dodanie aplikacji.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą LinkedIn Learning, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w serwisie LinkedIn Learning do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze LinkedIn Learning musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w serwisie LinkedIn Learning.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą LinkedIn Learning, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LinkedIn Learning](#creating-a-linkedin-learning-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LinkedIn Learning.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z usługą LinkedIn Learning, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LinkedIn Learning** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial-linkedin_01.png)

1. W oknie przeglądarki internetowej innej Zaloguj się do dzierżawy usługi LinkedIn Learning jako administrator.

1. W **Centrum kont**, kliknij przycisk **ustawienia globalne** w obszarze **ustawienia**. Zaznacz również **Learning — domyślne** z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij przycisk **lub kliknij tutaj, aby załadować i skopiuj poszczególne pola w formularzu** i skopiuj **identyfikator jednostki** i **adresu Url asercji konsumenta Service (ACS)**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

1. W witrynie Azure portal w obszarze **LinkedIn Learning domena i adresy URL**, wykonaj następujące kroki, aby skonfigurować logowanie Jednokrotne w **inicjowane przez dostawcę tożsamości** tryb

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. W **identyfikator** polu tekstowym wprowadź **identyfikator jednostki** skopiowane z portalu usługi LinkedIn 

    b. W **adres URL odpowiedzi** polu tekstowym wprowadź **adresu Url asercji konsumenta Service (ACS)** skopiowane z portalu usługi LinkedIn

1. Jeśli chcesz skonfigurować logowanie Jednokrotne w **zainicjowane SP**, następnie kliknij opcję Ustawienia Pokaż zaawansowane adresu URL w sekcji konfiguracji, należy określić adres URL logowania jednokrotnego.  Do utworzenia kopii adres Url logowania **adresu Url asercji konsumenta Service (ACS)** i Zastąp /saml//logowania /.   Po, zostały wykonane, adres URL logowania jednokrotnego powinna mieć następującego wzorca:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
1. Aplikacja LinkedIn Learning oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia przykład tego. Wartość domyślna **identyfikator użytkownika** jest **user.userprincipalname** , ale LinkedIn Learning oczekuje, że to mają być mapowane z adresem e-mail użytkownika. W przypadku którego można użyć **user.mail** atrybutu z listy lub użyj wartości odpowiedni atrybut, na podstawie konfiguracji organizacji. 

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/updateusermail.png)
    
1. W **atrybutów użytkownika** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** i ustawić atrybuty. Użytkownik musi dodać cztery oświadczeń o nazwie **e-mail**, **działu**, **firstname**, i **lastname** , a wartość to mają być mapowane z **user.mail**, **user.department**, **user.givenname**, i **user.surname** odpowiednio

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | e-mail| User.mail |    
    | Dział| User.Department |
    | Imię| user.givenname |
    | nazwisko| user.surname |
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/userattribute.png)
    
    a. Kliknij przycisk **Dodawanie atrybutu** aby otworzyć okno dialogowe atrybutu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_04.png)

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **Ok**

1. Wykonaj następujące czynności na **nazwa** — atrybut

    a. Kliknij pozycję atrybutu, aby otworzyć **Edytuj atrybut** okna.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/url_update.png)

    b. Usuń wartość adresu URL z **przestrzeni nazw**.
    
    c. Kliknij przycisk **Ok** można zapisać ustawienia.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png)

1. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_general_400.png)

1. Przejdź do **ustawienia administratora usługi LinkedIn** sekcji. Przekazywanie pliku XML, który został pobrany z witryny Azure portal, klikając opcję plik XML Przekaż.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **na** do włączenia funkcji logowania jednokrotnego. Zmiany stanu logowania jednokrotnego z **niepołączony** do **połączono**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-linkedin-learning-test-user"></a>Tworzenie użytkownika testowego LinkedIn Learning

LinkedIn Learning aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. Na administrator ustawienia strony na Przerzucanie portalu LinkedIn Learning przełącznika **automatycznie przypisywać licencje** do aktywnego tylko w czasie inicjowania obsługi administracyjnej i to będzie również przypisać licencję do użytkownika.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi LinkedIn Learning.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LinkedIn Learning, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **LinkedIn Learning**.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LinkedIn Learning w panelu dostępu, należy pobrać na stronie logowania platformy Azure, a na po pomyślnym zalogowaniu, należy uzyskać w aplikacji LinkedIn Learning.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/linkedinlearning-tutorial/tutorial_general_203.png
