---
title: 'Samouczek: Integracja usługi Azure Active Directory z Inkling | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Inkling.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 64c7ee45-ee8a-42f7-bf04-fd0e00833ea9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.openlocfilehash: cd7f8871cedb36157f3a16f093b09073576fe56e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443855"
---
# <a name="tutorial-azure-active-directory-integration-with-inkling"></a>Samouczek: Integracja usługi Azure Active Directory z Inkling

W tym samouczku dowiesz się, jak zintegrować Inkling w usłudze Azure Active Directory (Azure AD).

Integrowanie Inkling z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Inkling
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Inkling (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Inkling, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Logowania jednokrotnego Inkling włączonych subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Inkling z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne


## <a name="adding-inkling-from-the-gallery"></a>Dodawanie Inkling z galerii
Aby skonfigurować integrację Inkling w usłudze Azure AD, należy dodać Inkling z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Inkling z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Inkling**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/tutorial_inkling_001.png)

1. W panelu wyników wybierz **Inkling**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/tutorial_inkling_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Inkling w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Inkling do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Inkling musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Inkling.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Inkling, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Inkling](#creating-an-inkling-test-user)**  — aby odpowiednikiem Britta Simon w Inkling połączonego z jej reprezentacji usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji Inkling.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Inkling, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **Inkling** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_general_300.png)
    
1. Na **Inkling domena i adresy URL** sekcji, wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_inkling_01.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://api.inkling.com/saml/v2/metadata/<user-id>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://api.inkling.com/saml/v2/acs/<user-id>`

    > [!NOTE] 
    > Należy pamiętać, że nie są rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistych identyfikatorem i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej Inkling](mailto:press@inkling.com) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_general_400.png)  

1. Na **Utwórz nowy certyfikat** okno dialogowe, kliknij ikonę kalendarza, a następnie wybierz pozycję **datę wygaśnięcia**. Następnie kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_general_500.png)

1. Na **certyfikat podpisywania SAML** zaznacz **Ustaw nowy certyfikat jako aktywny** i kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_inkling_02.png)

1. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_general_600.png)

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_inkling_03.png) 

1. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, skontaktuj się z pomocą [zespołem pomocy technicznej Inkling](mailto:press@inkling.com) i zapewnić im z pobrane **metadanych**. 


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/inkling-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**. 



### <a name="creating-an-inkling-test-user"></a>Tworzenie użytkownika testowego Inkling

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Inkling. Skontaktuj się z [zespołem pomocy technicznej Inkling](mailto:press@inkling.com) Aby dodać użytkowników na platformie Inkling.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej Inkling.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Inkling, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Inkling**.

    ![Konfigurowanie logowania jednokrotnego](./media/inkling-tutorial/tutorial_inkling_50.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Inkling w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Inkling.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/inkling-tutorial/tutorial_general_01.png
[2]: ./media/inkling-tutorial/tutorial_general_02.png
[3]: ./media/inkling-tutorial/tutorial_general_03.png
[4]: ./media/inkling-tutorial/tutorial_general_04.png

[100]: ./media/inkling-tutorial/tutorial_general_100.png

[200]: ./media/inkling-tutorial/tutorial_general_200.png
[201]: ./media/inkling-tutorial/tutorial_general_201.png
[202]: ./media/inkling-tutorial/tutorial_general_202.png
[203]: ./media/inkling-tutorial/tutorial_general_203.png