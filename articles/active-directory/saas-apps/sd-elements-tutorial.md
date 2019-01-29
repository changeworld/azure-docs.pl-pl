---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą SD Elements | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SD Elements.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 0081946ff62e42e1fb601b135c7102dd83e84fe3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158914"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Samouczek: Integracja usługi Azure Active Directory z elementami SD

W tym samouczku dowiesz się, jak zintegrować SD Elements w usłudze Azure Active Directory (Azure AD).

Integrowanie SD Elements z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do elementów SD
- Użytkowników, aby automatycznie uzyskać zalogowanych do SD Elements (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z elementami SD, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- SD Elements logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie SD elementów z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sd-elements-from-the-gallery"></a>Dodawanie SD elementów z galerii
Aby skonfigurować integrację SD Elements w usłudze Azure AD, należy dodać SD Elements z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SD Elements z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **SD Elements**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_search.png)

1. W panelu wyników wybierz **SD Elements**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/tutorial_sdelements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą SD Elements w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w SD Elements jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SD Elements musi nawiązać.

SD Elements przypisywanie wartości **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z elementami SD, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SD Elements](#creating-a-sd-elements-test-user)**  — aby odpowiednikiem Britta Simon w SD Elements, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji SD Elements.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z SD Elements, wykonaj następujące czynności:**

1. W witrynie Azure portal na **SD Elements** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_samlbase.png)

1. Na **SD elementy domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej SD Elements](mailto:support@sdelements.com) do uzyskania tych wartości.

1. SD Elements aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **"Atrybut użytkownika"** kartę w aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności: 

    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |
    | email |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_officespace_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_general_400.png)

1. Na **SD elementy konfiguracji** , kliknij przycisk **skonfigurować SD Elements** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_configure.png)

1. Aby uzyskać logowanie jednokrotne włączone, skontaktuj się z Twojego [zespołu pomocy technicznej SD Elements](mailto:support@sdelements.com) i udostępnia je z pliku pobranego certyfikatu. 

1. W oknie innej przeglądarki logowanie jednokrotne do swojej dzierżawy SD Elements jako administrator.

1. W menu u góry kliknij **systemu**, a następnie **logowania jednokrotnego**. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sd-elements_09.png) 

1. Na **ustawienia rejestracji jednokrotnej** okno dialogowe, należy wykonać następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a. Jako **typu logowania jednokrotnego**, wybierz opcję **SAML**.
   
    b. W **Identyfikatora jednostki dostawcy tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal. 
   
    c. W **usłudze dostawcy tożsamości pojedynczego logowania jednokrotnego** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal. 
   
    d. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sd-elements-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-sd-elements-test-user"></a>Tworzenie użytkownika testowego SD Elements

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w SD Elements. W przypadku SD Elements tworzenia użytkowników SD Elements jest zadanie ręczne.

**Aby utworzyć Britta Simon w elementach SD, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci web Zaloguj się do witryny firmy SD Elements jako administrator.

1. W menu u góry kliknij **Zarządzanie użytkownikami**, a następnie **użytkowników**.
   
    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kliknij pozycję **Dodaj nowego użytkownika**.
   
    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)
 
1. Na **Dodaj nowego użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Tworzenie użytkownika testowego SD Elements](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
    a. W **E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.
   
    b. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.
   
    c. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.
   
    d. Jako **roli**, wybierz opcję **użytkownika**. 
   
    e. Kliknij przycisk **Utwórz użytkownika**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do SD Elements.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon SD Elements, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **SD Elements**.

    ![Konfigurowanie logowania jednokrotnego](./media/sd-elements-tutorial/tutorial_sdelements_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
  
Po kliknięciu kafelka SD Elements w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji SD Elements.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/sd-elements-tutorial/tutorial_general_04.png

[100]: ./media/sd-elements-tutorial/tutorial_general_100.png

[200]: ./media/sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/sd-elements-tutorial/tutorial_general_201.png
[202]: ./media/sd-elements-tutorial/tutorial_general_202.png
[203]: ./media/sd-elements-tutorial/tutorial_general_203.png

