---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą klienta Gorilla ziemi | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Gorilla ziemi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12519a2bd0a5e3951d0ca9ae51a54bd728a782b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65987891"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą klienta Gorilla ziemi

W tym samouczku dowiesz się, jak zintegrować ziemi Gorilla klienta z usługą Azure Active Directory (Azure AD).

Integrowanie ziemi Gorilla klienta z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do klienta Gorilla ziemi
- Użytkowników, aby automatycznie uzyskać zalogowanych do klienta Gorilla ziemi (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą klienta Gorilla ziemi, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Klient Gorilla ziemi logowania jednokrotnego włączonych subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie klienta Gorilla ziemi z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Dodawanie klienta Gorilla ziemi z galerii
Aby skonfigurować integrację ziemi Gorilla klienta w usłudze Azure AD, należy dodać ziemi Gorilla klienta z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać klienta Gorilla ziemi z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **klienta Gorilla ziemi**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. W panelu wyników wybierz **klienta Gorilla ziemi**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą klienta Gorilla ziemi oparte na użytkownika testu o nazwie "B. Simona".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w kliencie Gorilla ziemi jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w kliencie Gorilla ziemi musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w kliencie Gorilla ziemi.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą klienta Gorilla ziemi, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne z ograniczonej grupy.
1. **[Tworzenie użytkownika testowego Gorilla ziemi](#creating-a-land-gorilla-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i skonfigurować logowanie jednokrotne w aplikacji klienckiej Gorilla ziemi.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą klienta Gorilla ziemi, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **klienta Gorilla ziemi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Na **ziemi Gorilla klienta domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu jednej z następującym wzorcem: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu jednej z następującym wzorcem:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Należy pamiętać, że nie są to rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistych identyfikatorem i adres URL odpowiedzi. W tym miejscu zalecamy użycie unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z pomocą [zespołu klienta Gorilla ziemi](https://www.landgorilla.com/support/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Aby uzyskać pełną konfiguracji logowania jednokrotnego dla aplikacji na końcu Gorilla ziemi, skontaktuj się z pomocą [zespołem pomocy technicznej klienta Gorilla ziemi](https://www.landgorilla.com/support/) i udostępniać je pobrany **"XML metadanych** pliku.


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie B. Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **B. Simon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z B. Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**. 

### <a name="creating-a-land-gorilla-test-user"></a>Tworzenie użytkownika testowego Gorilla ziemi

Skontaktuj się z [zespołem pomocy technicznej Gorilla ziemi](https://www.landgorilla.com/support/) Aby dodać użytkowników na platformie Gorilla ziemi.
    
### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Simon B. do nadawania im praw do ziemi Gorilla klienta za pomocą usługi Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać B. Simon klientowi Gorilla ziemi, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **klienta Gorilla ziemi**.

    ![Konfigurowanie logowania jednokrotnego](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ziemi Gorilla klienta w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji klienckiej Gorilla ziemi.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
