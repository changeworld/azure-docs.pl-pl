---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą HR2day przez Merces | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i HR2day przez Merces.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 378aab82fac5298c3785f752478e3bfc3c6e325b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60275454"
---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą HR2day przez Merces

W tym samouczku dowiesz się, jak zintegrować HR2day przez Merces w usłudze Azure Active Directory (Azure AD).

Integrowanie HR2day przez Merces z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do HR2day przez Merces.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogujesz się HR2day przy Merces za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą HR2day przez Merces, potrzebne są następujące elementy:

- Subskrypcja usługi Azure AD.
- HR2day przez Merces logowanie jednokrotne włączone subskrypcji.

> [!NOTE]
> Nie zaleca się testowanie kroki opisane w tym samouczku przy użyciu środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Pobierz [miesięcznej bezpłatnej wersji próbnej usługi Azure AD](https://azure.microsoft.com/pricing/free-trial/) Jeśli nie jeszcze.  

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz, który jest opisany w tym miejscu składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie HR2day przez Merces z galerii.
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne.

## <a name="add-hr2day-by-merces-from-the-gallery"></a>Dodaj HR2day przez Merces z galerii
Aby skonfigurować integrację HR2day przez Merces w usłudze Azure AD, należy dodać HR2day przez Merces z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać HR2day przez Merces z galerii, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com), w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **HR2day przez Merces**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_search.png)

1. W panelu wyników wybierz **HR2day przez Merces**, a następnie wybierz pozycję **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą HR2day przez Merces, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, który użytkownik odpowiednika w HR2day przez Merces użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w HR2day przez Merces.

HR2day przez Merces, przypisywanie **nazwa_użytkownika** w usłudze Azure AD **Username** do ustanawiania relacji.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą HR2day przez Merces, należy wykonać poniższe bloki konstrukcyjne:

1. Konfigurowanie usługi Azure AD logowania jednokrotnego: Umożliwianie użytkownikom korzystać z tej funkcji.
1. Utwórz użytkownika testowego usługi Azure AD: Testowanie usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. Utwórz HR2day przez użytkownika testowego Merces: Utwórz odpowiednikiem Britta Simon w HR2day przez Merces połączonego z usługi Azure AD reprezentacja użytkownika.
1. Przypisz użytkownika testowego usługi Azure AD: Włącz Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. Testowanie logowania jednokrotnego: Sprawdź, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w sieci HR2day Merces aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z HR2day przez Merces, wykonaj następujące czynności:**

1. W witrynie Azure portal na **HR2day przez Merces** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Aby włączyć logowanie jednokrotne, w **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego**.
 
    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

1. W **HR2day Merces domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. W **adres URL logowania** wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.force.com/<instancename>`.

    b. W **identyfikator** wpisz adres URL przy użyciu następującego wzorca: `https://hr2day.force.com/<companyname>`.

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl) do uzyskania tych wartości. 
 


1. Na **certyfikat podpisywania SAML** zaznacz **Certificate(Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

1. W tej sekcji opisano, jak umożliwić użytkownikom na uwierzytelnianie HR2day przez Merces za pomocą konta w usłudze Azure AD. Mogą to zrobić przy użyciu Federacji, który jest oparty na protokole SAML.

    Twoje HR2day przez aplikację Merces oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych do tokenu SAML. Poniższy zrzut ekranu przedstawia przykład. 

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2day_00.png)
    
   > [!NOTE]
   >  Aby można było skonfigurować potwierdzenie SAML, należy skontaktować się [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl) i zażądać wartość atrybutu unikatowego identyfikatora dla Twojej dzierżawy. Należy tę wartość, aby wykonać kroki opisane w następnej sekcji. 

1. W **logowanie jednokrotne** dialogowym **atrybutów użytkownika** sekcji, skonfigurowanie atrybutu tokenu SAML, jak pokazano na poniższej ilustracji. Następnie wykonaj następujące kroki.
    
      | Nazwa atrybutu    |   Wartość atrybutu |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | `join([mail],"102938475Z","@"` |
    
      a. Aby otworzyć **Dodawanie atrybutu** okno dialogowe, wybierz opcję **Dodaj atrybut**.

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** wpisz **ATTR_LOGINCLAIM**.

    c. Z **wartość** listy wybierz **Join()**.

    d. Z **ciąg1** listy wybierz **user.mail**.

    e. Aby uzyskać **ciąg2**, wpisz unikatowy identyfikator, który jest udostępniany przez Twój zespół HR2day.

    f. W **Separator** wpisz **\@**.
    
    g. Wybierz przycisk **OK**.

1. Wybierz ikonę **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_general_400.png)

1. W **HR2day przez konfigurację Merces** zaznacz **skonfigurować HR2day przez Merces** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

1. Aby skonfigurować logowanie Jednokrotne dla aplikacji, skontaktuj się z pomocą [HR2day przez zespół pomocy technicznej klienta Merces](mailTo:servicedesk@merces.nl). Dołącz pobrany **Certificate(Base64)** pliku do wiadomości e-mail. Zapewniają również **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** tak, aby można je skonfigurować integrację logowania jednokrotnego.

    > [!NOTE]
    >Wspominanie zespołu Merces, że ta integracja wymaga Identyfikatora jednostki można ustawić za pomocą wzorca **https://hr2day.force.com/INSTANCENAME**.

    > [!TIP]
    >Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę. Następnie uzyskać dostęp do dokumentacji embedded za pomocą **konfiguracji** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzone w [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
   > 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_03.png) 

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/hr2day-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło**, a następnie Zapisz hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-hr2day-by-merces-test-user"></a>Utwórz HR2day przez Merces użytkownika testowego

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w HR2day przez Merces. Aby dodać użytkowników w ramach konta HR2day, pracować [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl). 

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [HR2day przez zespół pomocy technicznej klienta Merces](mailto:servicedesk@merces.nl).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej HR2day przez Merces.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon HR2day przez Merces, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie przejdź do **aplikacje dla przedsiębiorstw**. Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **HR2day przez Merces**.

    ![Konfigurowanie logowania jednokrotnego](./media/hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

1. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Przypisz użytkownika][202] 

1. Wybierz przycisk **Add** (Dodaj). Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Przypisz użytkownika][203]

1. W **użytkowników i grup** dialogowym **użytkowników** listy wybierz **Britta Simon**.

1. Kliknij przycisk **wybierz** przycisku.

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  

Po wybraniu HR2day przez Merces kafelka w panelu dostępu, automatycznie uzyskać zarejestrowaniu w usłudze usługi HR2day Merces aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hr2day-tutorial/tutorial_general_01.png
[2]: ./media/hr2day-tutorial/tutorial_general_02.png
[3]: ./media/hr2day-tutorial/tutorial_general_03.png
[4]: ./media/hr2day-tutorial/tutorial_general_04.png

[100]: ./media/hr2day-tutorial/tutorial_general_100.png

[200]: ./media/hr2day-tutorial/tutorial_general_200.png
[201]: ./media/hr2day-tutorial/tutorial_general_201.png
[202]: ./media/hr2day-tutorial/tutorial_general_202.png
[203]: ./media/hr2day-tutorial/tutorial_general_203.png

