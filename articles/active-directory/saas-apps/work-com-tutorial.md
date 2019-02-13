---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Work.com | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e5d097afa3af111a7de8341b90d8c6c05ac0347
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205753"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Work.com

W tym samouczku dowiesz się, jak zintegrować Work.com w usłudze Azure Active Directory (Azure AD).

Integrowanie Work.com z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Work.com
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Work.com (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Work.com, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Work.com logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodaj Work.com z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="add-workcom-from-the-gallery"></a>Dodaj Work.com z galerii
Aby skonfigurować integrację Work.com w usłudze Azure AD, należy dodać Work.com z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Work.com z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Work.com**, wybierz opcję **Work.com** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dodaj z galerii](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Work.com w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Work.com do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Work.com musi można ustanowić.

W Work.com, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Work.com, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Work.com](#create-a-workcom-test-user)**  — aby odpowiednikiem Britta Simon w Work.com połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Work.com.

>[!NOTE]
>Aby skonfigurować logowanie jednokrotne, musisz skonfigurować niestandardową nazwę domeny Work.com jeszcze. Należy zdefiniować co najmniej nazwę domeny, test nazwa domeny i wdróż je w całej organizacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Work.com, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Work.com** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Logowanie na podstawie protokołu SAML](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. Na **Work.com domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sekcja Work.com domena i adresy URL](./media/work-com-tutorial/tutorial_work-com_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) aby zyskać tę wartość. 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Sekcji certyfikat podpisywania SAML](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Przycisk Zapisz](./media/work-com-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Work.com** , kliknij przycisk **skonfigurować Work.com** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Sekcja konfiguracji Work.com](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. Zaloguj się do dzierżawy Work.com jako administrator.

1. Przejdź do **Instalatora**.
   
    ![Konfigurowanie](./media/work-com-tutorial/ic794108.png "Konfigurowanie")

1. W okienku nawigacji po lewej stronie w **administrowanie** kliknij **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** strony. 
   
    ![My Domain](./media/work-com-tutorial/ic767825.png "My Domain")

1. Aby sprawdzić, czy domenę zostało skonfigurowane prawidłowo, upewnij się, że jest on "**kroku 4 wdrożone dla użytkowników**" i zapoznaj się z "**Moje ustawienia domeny**".
   
    ![Nie wdrożono użytkownika domeny](./media/work-com-tutorial/ic784377.png "domeny wdrożenia użytkownika")

1. Zaloguj się do dzierżawy Work.com.

1. Przejdź do **Instalatora**.
    
    ![Konfigurowanie](./media/work-com-tutorial/ic794108.png "Konfigurowanie")

1. Rozwiń **środki kontroli bezpieczeństwa** menu, a następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.
    
    ![Pojedynczy ustawień logowania jednokrotnego](./media/work-com-tutorial/ic794113.png "pojedynczy ustawień logowania jednokrotnego")

1. Na **ustawienia rejestracji jednokrotnej** okna dialogowego strony, wykonaj następujące czynności:
    
    ![SAML włączone](./media/work-com-tutorial/ic781026.png "SAML włączone")
    
    a. Wybierz **SAML włączone**.
    
    b. Kliknij przycisk **Nowy**.

1. W **SAML pojedynczego ustawień logowania jednokrotnego** sekcji, wykonaj następujące czynności:
    
    ![SAML pojedynczego logowania jednokrotnego ustawienia](./media/work-com-tutorial/ic794114.png "SAML pojedynczego logowania jednokrotnego ustawienia")
    
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.  
       
    > [!NOTE]
    > Podanie wartości dla **nazwa** automatycznie wypełnić **Nazwa interfejsu API** pola tekstowego.
    
    b. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.
    
    c. Aby przekazać certyfikat pobrany z witryny Azure portal, kliknij przycisk **Przeglądaj**.
    
    d. W **identyfikator jednostki** polu tekstowym wpisz `https://salesforce-work.com`.
    
    e. Jako **typu tożsamości SAML**, wybierz opcję **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**.
    
    f. Jako **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentfier instrukcji podmiotu**.
    
    g. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    h. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.
    
    i. Jako **dostawcy inicjowane żądania powiązania usługi**, wybierz opcję **żądania HTTP Post**.
    
    j. Kliknij pozycję **Zapisz**.

1. W portalu klasycznym Work.com, w okienku nawigacji po lewej stronie kliknij pozycję **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** Strona. 
    
    ![My Domain](./media/work-com-tutorial/ic794115.png "My Domain")

1. Na **Moja domena** stronie **znakowanie strony logowania** kliknij **Edytuj**.
    
    ![Strona logowania znakowania](./media/work-com-tutorial/ic767826.png "strony logowania, znakowania")

1. Na **znakowanie strony logowania** strony w **usługi uwierzytelniania** sekcji Nazwa Twojej **ustawień logowania jednokrotnego SAML** jest wyświetlana. Zaznacz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Strona logowania znakowania](./media/work-com-tutorial/ic784366.png "strony logowania, znakowania")

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/work-com-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Użytkownicy i grupy -> Wszyscy użytkownicy](./media/work-com-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Add](./media/work-com-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Strony okna dialogowego użytkownika](./media/work-com-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-workcom-test-user"></a>Tworzenie użytkownika testowego Work.com
Dla użytkowników usługi Azure Active Directory można było zarejestrować się w musi być obsługiwana na Work.com. W przypadku Work.com Inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:
1. Zaloguj się do witryny firmy Work.com jako administrator.

1. Przejdź do **Instalatora**.
   
    ![Konfigurowanie](./media/work-com-tutorial/IC794108.png "Konfigurowanie")
1. Przejdź do **Zarządzanie użytkownikami \> użytkowników**.
   
    ![Zarządzanie użytkownikami](./media/work-com-tutorial/IC784369.png "Zarządzanie użytkownikami")

1. Kliknij pozycję **New User** (Nowy użytkownik).
   
    ![Wszyscy użytkownicy](./media/work-com-tutorial/IC794117.png "wszystkich użytkowników")

1. W sekcji użytkownikowi edytować, wykonaj następujące czynności, w atrybutach prawidłowe Azure konto usługi AD do aprowizowania w powiązanych pól tekstowych:
   
    ![Edycja użytkownika](./media/work-com-tutorial/ic794118.png "Edycja użytkownika")
   
    a. W **imię** polu tekstowym wpisz **imię** użytkownika **Britta**.
    
    b. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika **Simon**.
    
    c. W **Alias** polu tekstowym wpisz **nazwa** użytkownika **BrittaS**.
    
    d. W **E-mail** polu tekstowym wpisz **adres e-mail** użytkownika **Brittasimon@contoso.com**.
    
    e. W **nazwa_użytkownika** pole tekstowe, wpisz nazwę użytkownika, takie jak **Brittasimon@contoso.com**.
    
    f. W **pseudonim** polu tekstowym wpisz **pseudonim** użytkownika **Simon**.
    
    g. Wybierz **roli**, **licencji użytkownika**, i **profilu**.
    
    h. Kliknij pozycję **Zapisz**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure AD otrzyma wiadomość e-mail, w tym link do potwierdzenia konta, zanim stanie się aktywny.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Work.com.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Work.com, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Work.com**.

    ![Work.com na liście aplikacji](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Work.com w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Work.com.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

