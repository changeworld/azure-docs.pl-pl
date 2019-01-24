---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą LogicMonitor | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2018
ms.author: jeedes
ms.openlocfilehash: 5aa11c15e038c0af8447debba0cd385c944c0bce
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812424"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą LogicMonitor

W tym samouczku dowiesz się, jak zintegrować LogicMonitor w usłudze Azure Active Directory (Azure AD).

Integrowanie LogicMonitor z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LogicMonitor
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do LogicMonitor (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LogicMonitor, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- LogicMonitor logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LogicMonitor z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-logicmonitor-from-the-gallery"></a>Dodawanie LogicMonitor z galerii
Aby skonfigurować integrację LogicMonitor w usłudze Azure AD, należy dodać LogicMonitor z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LogicMonitor z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LogicMonitor**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_search.png)

1. W panelu wyników wybierz **LogicMonitor**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą LogicMonitor w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LogicMonitor do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LogicMonitor musi można ustanowić.

W LogicMonitor, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LogicMonitor, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LogicMonitor](#creating-a-logicmonitor-test-user)**  — aby odpowiednikiem Britta Simon w LogicMonitor połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LogicMonitor.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LogicMonitor, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LogicMonitor** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

1. Na **LogicMonitor domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.logicmonitor.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta LogicMonitor](https://www.logicmonitor.com/contact/) do uzyskania tych wartości. 
 


1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/logicmonitor-tutorial/tutorial_general_400.png)

1. Zaloguj się do Twojej **LogicMonitor** witryny firmy jako administrator.

1. W menu u góry kliknij **ustawienia**.
   
    ![Ustawienia](./media/logicmonitor-tutorial/ic790052.png "Ustawienia")

1. Bat nawigacji po lewej stronie, kliknij **logowanie jednokrotne**
   
    ![Logowanie jednokrotne](./media/logicmonitor-tutorial/ic790053.png "Logowanie jednokrotne")

1. W **ustawień logowania jednokrotnego (SSO)** sekcji, wykonaj następujące czynności:
   
    ![Pojedynczy ustawień logowania jednokrotnego](./media/logicmonitor-tutorial/ic790054.png "pojedynczy ustawień logowania jednokrotnego")
   
    a. Wybierz **włączyć rejestrację jednokrotną**.

    b. Jako **domyślne przypisania roli**, wybierz opcję **tylko do odczytu**.
   
    c. Otwórz plik metadanych pobranego w Notatniku, a następnie wklej zawartość pliku do **metadanych dostawcy tożsamości** pola tekstowego.
   
    d. Kliknij przycisk **Save Changes** (Zapisz zmiany).

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/logicmonitor-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Tworzenie użytkownika testowego LogicMonitor

Dla użytkowników usługi Azure AD można było się zalogować musi być obsługiwana do aplikacji LogicMonitor, używając nazwy użytkowników usługi Azure Active Directory.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy LogicMonitor jako administrator.

1. W menu u góry kliknij **ustawienia**, a następnie kliknij przycisk **ról i użytkowników**.
   
    ![Ról i użytkowników](./media/logicmonitor-tutorial/ic790056.png "ról i użytkowników")

1. Kliknij pozycję **Add** (Dodaj).

1. W **Dodaj konto** sekcji, wykonaj następujące czynności:
   
    ![Dodaj konto](./media/logicmonitor-tutorial/ic790057.png "Dodaj konto")
   
    a. Typ **Username**, **wiadomości E-mail**, **hasło**, i **wpisz ponownie hasło** wartości, które chcesz aprowizować użytkownika usługi Azure Active Directory do powiązanych pól tekstowych.
   
    b. Wybierz **role**, **wyświetlanie uprawnień**i **stan**.
   
    c. Kliknij przycisk **Prześlij**.

>[!NOTE]
>Można użyć jakichkolwiek innych LogicMonitor użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez LogicMonitor do świadczenia usługi Azure Active Directory kont użytkowników. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do LogicMonitor.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LogicMonitor, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LogicMonitor**.

    ![Konfigurowanie logowania jednokrotnego](./media/logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
 
Po kliknięciu kafelka LogicMonitor w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji LogicMonitor.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/logicmonitor-tutorial/tutorial_general_203.png

