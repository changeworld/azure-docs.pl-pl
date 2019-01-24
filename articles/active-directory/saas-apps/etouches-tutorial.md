---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą etouches | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 7e4dcc91ecafab6fc0b7864658b0f7724ec04bf2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813920"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą etouches

W tym samouczku dowiesz się, jak zintegrować etouches w usłudze Azure Active Directory (Azure AD).

Integrowanie etouches z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do etouches
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do etouches (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą etouches, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Etouches logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie etouches z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-etouches-from-the-gallery"></a>Dodawanie etouches z galerii
Aby skonfigurować integrację etouches w usłudze Azure AD, należy dodać etouches z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać etouches z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **etouches**, wybierz opcję **etouches** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![etouches na liście wyników](./media/etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą etouches w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w etouches do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w etouches musi można ustanowić.

W etouches, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą etouches, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego etouches](#create-an-etouches-test-user)**  — aby mają odpowiednika w pozycji Britta simon w etouches połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji etouches.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z etouches, wykonaj następujące czynności:**

1. W witrynie Azure portal na **etouches** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/etouches-tutorial/tutorial_etouches_samlbase.png)

1. Na **etouches domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![etouches domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/etouches-tutorial/tutorial_etouches_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Należy zaktualizować wartość rzeczywisty symbol dla adresu URL i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka.
    > 

1. Aplikacja etouches oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **atrybutu użytkownika** aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania. 

    ![Atrybut użytkownika](./media/etouches-tutorial/tutorial_etouches_attribute.png) 

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Dodaj atrybut](./media/etouches-tutorial/tutorial_attribute_04.png)

    ![Dodaj atrybut okno dialogowe](./media/etouches-tutorial/tutorial_attribute_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/etouches-tutorial/tutorial_etouches_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/etouches-tutorial/tutorial_general_400.png)

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, wykonaj następujące kroki w aplikacji etouches: 

    ![Konfiguracja etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Zaloguj się do **etouches** aplikacji przy użyciu uprawnień administratora.
   
    b. Przejdź do **SAML** konfiguracji.

    c. W **ustawienia ogólne** sekcji, Otwórz swój certyfikat pobrany z witryny Azure portal w programie Notatnik, skopiuj zawartość i wklej go w polu tekstowym metadanych dostawcy tożsamości. 

    d. Kliknij pozycję **Zapisz & pozostają** przycisku.
  
    e. Kliknij pozycję **metadane aktualizacji** przycisk w sekcji metadanych SAML. 

    f. To spowoduje otwarcie strony i wykonać logowanie Jednokrotne. Gdy działa usługa rejestracji Jednokrotnej następnie możesz skonfigurować nazwę użytkownika.

    g. W polu Nazwa użytkownika wybierz **emailaddress** jak pokazano na poniższej ilustracji. 

    h. Kopiuj **identyfikator jednostki SP** wartość i wklej go w **identyfikator** pola tekstowego, który znajduje się w **etouches domena i adresy URL** sekcji w witrynie Azure portal.

    i. Kopiuj **adres URL logowania jednokrotnego / ACS** wartość i wklej go w **adres URL logowania** pola tekstowego, który znajduje się w **etouches domena i adresy URL** sekcji w witrynie Azure portal.
   
> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](./media/etouches-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/etouches-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Przycisk Dodaj](./media/etouches-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Okno dialogowe Użytkownik](./media/etouches-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-etouches-test-user"></a>Tworzenie użytkownika testowego etouches

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w etouches. Praca z [zespołu pomocy technicznej etouches klienta](https://www.etouches.com/event-software/support/customer-support/) Aby dodać użytkowników na platformie etouches.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do etouches.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon etouches, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **etouches**.

    ![Link etouches na liście aplikacji](./media/etouches-tutorial/tutorial_etouches_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego


Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka etouches w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji etouches.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/etouches-tutorial/tutorial_general_01.png
[2]: ./media/etouches-tutorial/tutorial_general_02.png
[3]: ./media/etouches-tutorial/tutorial_general_03.png
[4]: ./media/etouches-tutorial/tutorial_general_04.png

[100]: ./media/etouches-tutorial/tutorial_general_100.png

[200]: ./media/etouches-tutorial/tutorial_general_200.png
[201]: ./media/etouches-tutorial/tutorial_general_201.png
[202]: ./media/etouches-tutorial/tutorial_general_202.png
[203]: ./media/etouches-tutorial/tutorial_general_203.png

