---
title: 'Samouczek: Integracja usługi Azure Active Directory z BambooHR | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: dc6664321588d383b4656199c3e8ea79159ca850
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437682"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Samouczek: Integracja usługi Azure Active Directory z BambooHR

W tym samouczku dowiesz się, jak zintegrować BambooHR w usłudze Azure Active Directory (Azure AD).

Integrowanie BambooHR z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do BambooHR.
- Aby umożliwić użytkownikom automatycznie pobrać zalogowany do BambooHR przy użyciu logowania jednokrotnego (SSO) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BambooHR, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego BambooHR

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, zaleca się nie używać do środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [uzyskiwanie bezpłatnej miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

W tym samouczku opisano scenariusz składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BambooHR z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-bamboohr-from-the-gallery"></a>Dodaj BambooHR z galerii
Aby skonfigurować integrację BambooHR w usłudze Azure AD, Dodaj BambooHR z galerii z listą zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

1. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać aplikację, wybierz pozycję **nową aplikację**.

    !["Nowa aplikacja" przycisk][3]

1. W polu wyszukiwania wpisz **BambooHR**. Na liście wyników wybierz **BambooHR**, a następnie wybierz pozycję **Dodaj**.

    ![BambooHR na liście wyników](./media/bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą BambooHR za pomocą użytkownika testowego "Britta Simon."

Logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, co użytkownik jego odpowiednika znajduje się w BambooHR. Innymi słowy należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanych użytkownikiem w BambooHR.

Aby ustanowić relację łącza w BambooHR, przypisz usługi Azure AD **nazwy użytkownika** wartość jako BambooHR **Username** wartość.

Aby konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą BambooHR, należy wykonać bloki konstrukcyjne w pięć następnych sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do włączenia funkcji logowania jednokrotnego usługi Azure AD w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BambooHR, wykonując następujące czynności:

1. W witrynie Azure portal na **BambooHR** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. W **logowanie jednokrotne** okna w **tryb** listy rozwijanej wybierz **opartej na SAML logowania jednokrotnego**.
 
    ![Okno rejestracji jednokrotnej](./media/bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

1. W obszarze **BambooHR domena i adresy URL**, wykonaj następujące czynności:

    ![W sekcji BambooHR domena i adresy URL](./media/bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. W **adres URL logowania** wpisz adres URL w następującym formacie: `https://<company>.bamboohr.com`.

    b. W **identyfikator** wpisz wartość: `BambooHR-SAML`.

    > [!NOTE] 
    > **Adres URL logowania** wartość nie jest prawdziwe. Zaktualizuj go z adresem URL rzeczywistej logowania jednokrotnego. Aby uzyskać wartość, skontaktuj się z pomocą [zespołem pomocy technicznej klienta BambooHR](https://www.bamboohr.com/contact.php). 
 
1. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

1. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/bamboo-hr-tutorial/tutorial_general_400.png)

1. W obszarze **konfiguracji BambooHR**, wybierz opcję **skonfigurować BambooHR** otworzyć **Konfigurowanie logowania jednokrotnego** okna. W **krótki** sekcji, skopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do późniejszego użycia.

    ![Konfiguracja BambooHR](./media/bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

1. W nowym oknie Zaloguj się w witrynie firmy BambooHR jako administrator.

1. Na stronie głównej należy wykonać następujące czynności:
   
    ![Na stronie BambooHR logowania jednokrotnego](./media/bamboo-hr-tutorial/ic796691.png "logowania jednokrotnego")   

    a. Wybierz **aplikacji**.
   
    b. W **aplikacje** okienku wybierz **logowania jednokrotnego**.
   
    c. Wybierz **SAML logowania jednokrotnego**.

1. W **SAML logowania jednokrotnego** okienko, wykonaj następujące czynności:
   
    ![W okienku SAML logowania jednokrotnego](./media/bamboo-hr-tutorial/IC796692.png "SAML logowania jednokrotnego")
   
    a. Do **adres Url logowania SSO** pole, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowaną z witryny Azure portal w kroku 6.
      
    b. W programie Notatnik, otwórz certyfikat zakodowany base-64, pobrany z witryny Azure portal, skopiuj jego zawartość i następnie wklej go do **certyfikat X.509** pole.
   
    c. Wybierz pozycję **Zapisz**.

> [!TIP]
> Gdy konfigurujesz aplikacji może odczytywać zwięzłe wersji tych instrukcji w [witryny Azure portal](https://portal.azure.com). Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** sekcji, po prostu zaznacz **logowania jednokrotnego** kartę, a następnie przejść Dokumentacja za pośrednictwem Embedded **konfiguracji** sekcji u dołu. Aby uzyskać informacje, zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

   ![Tworzenie użytkownika testowego w usłudze Azure AD Britta Simon][100]

Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Przycisk usługi Azure Active Directory](./media/bamboo-hr-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/bamboo-hr-tutorial/create_aaduser_02.png)

1. W górnej części **wszyscy użytkownicy** okienku wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/bamboo-hr-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna, wykonaj następujące czynności:

    ![W oknie użytkownika](./media/bamboo-hr-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-bamboohr-test-user"></a>Tworzenie użytkownika testowego BambooHR

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do BambooHR, skonfigurować je ręcznie w BambooHR, wykonując następujące czynności:

1. Zaloguj się do Twojej **BambooHR** lokacji jako administrator.

1. Na pasku narzędzi u góry wybierz **ustawienia**.
   
    ![Przycisk Ustawienia](./media/bamboo-hr-tutorial/IC796694.png "ustawienie")

1. Wybierz pozycję **Przegląd**.

1. W okienku po lewej stronie wybierz **zabezpieczeń** > **użytkowników**.

1. Wpisz nazwę użytkownika, hasło i adres e-mail, prawidłowy usługi Azure AD konta chcesz skonfigurować.

1. Wybierz pozycję **Zapisz**.
        
>[!NOTE]
>Aby skonfigurować konta użytkowników usługi Azure AD, umożliwia także BambooHR narzędzia do tworzenia konta użytkownika lub interfejsów API.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Umożliw Britta Simon do udzielania dostępu do BambooHR za pomocą logowania jednokrotnego usługi Azure.

![Przypisanie roli użytkownika][200] 

Aby przypisać użytkownika Britta Simon BambooHR, wykonaj następujące czynności:

1. W witrynie Azure portal Otwórz widok aplikacji, przejdź do widoku katalogu, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. W **aplikacje dla przedsiębiorstw** listy wybierz **BambooHR**.

    ![Link BambooHR na liście aplikacji dla przedsiębiorstw](./media/bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

1. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Wybierz **Dodaj** przycisk a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

1. W **użytkowników i grup** okna w **użytkowników** listy wybierz **Britta Simon**.

1. Wybierz **wybierz** przycisku.

1. W **Dodaj przydziału** wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu **BambooHR** kafelka w panelu dostępu, należy uzyskać automatycznie zalogowano się do aplikacji BambooHR.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/bamboo-hr-tutorial/tutorial_general_203.png

