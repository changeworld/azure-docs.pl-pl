---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą FilesAnywhere | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i FilesAnywhere.
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
ms.date: 03/17/2017
ms.author: jeedes
ms.openlocfilehash: a6cc65feb49052a00fed94fb318570182dd9a49b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154630"
---
# <a name="tutorial-azure-active-directory-integration-with-filesanywhere"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą FilesAnywhere

W tym samouczku dowiesz się, jak zintegrować FilesAnywhere w usłudze Azure Active Directory (Azure AD).

Integrowanie FilesAnywhere z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do FilesAnywhere
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do FilesAnywhere (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą FilesAnywhere, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- FilesAnywhere logowania jednokrotnego włączonych subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie FilesAnywhere z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne


## <a name="adding-filesanywhere-from-the-gallery"></a>Dodawanie FilesAnywhere z galerii
Aby skonfigurować integrację FilesAnywhere w usłudze Azure AD, należy dodać FilesAnywhere z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać FilesAnywhere z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **FilesAnywhere**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_search.png)

1. W panelu wyników wybierz **FilesAnywhere**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą FilesAnywhere w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w FilesAnywhere do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w FilesAnywhere musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w FilesAnywhere.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą FilesAnywhere, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego FilesAnywhere](#creating-a-filesanywhere-test-user)**  — aby odpowiednikiem Britta Simon w FilesAnywhere połączonego z jej reprezentacji usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji FilesAnywhere.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z FilesAnywhere, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **FilesAnywhere** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_samlbase.png)

1. Na **FilesAnywhere domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tryb inicjowane przez dostawcę tożsamości**:

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_filesanywhere_url.png)
    
    a. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<company name>.filesanywhere.com/saml20.aspx?c=215`
> [!NOTE]
> Należy pamiętać, że wartość **215** jest **clientid** i jest tylko przykładowe. Należy zastąpić wartości clientid rzeczywistych.

1. Na **FilesAnywhere domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **SP zainicjowano tryb**, wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_filesanywhere_url1.png)

    a. Kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL** opcji

    b. W **na adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<sub domain>.filesanywhere.com/`

    > [!NOTE] 
    > Należy pamiętać, że nie są to rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistych na adres URL logowania i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej FilesAnywhere](mailto:support@FilesAnywhere.com) do uzyskania tych wartości. 

1. Aplikacja oprogramowania FilesAnywhere oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_filesanywhere_attribute.png)
    
    Gdy użytkownicy rejestruje się za pomocą FilesAnywhere otrzymują wartość **clientid** atrybut z [zespołu FilesAnywhere](mailto:support@FilesAnywhere.com). Należy dodać atrybut "Identyfikator klienta" przy użyciu unikatowych wartości dostarczone przez FilesAnywhere. Te atrybuty powyżej są wymagane.
    > [!NOTE] 
    > Należy pamiętać, że wartość **2331** z **clientid** jest przykładowe. Należy podać wartości rzeczywistej.


1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | ClientID | *"uniquevalue"* |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_05.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_general_400.png)

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_certificate.png) 

1. Na **konfiguracji FilesAnywhere** , kliknij przycisk **skonfigurować FilesAnywhere** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configure.png) 

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_configuresignon.png)

1.  Aby uzyskać pełną konfigurację logowania jednokrotnego dla aplikacji na końcu FilesAnywhere, skontaktuj się z pomocą [zespołem pomocy technicznej FilesAnywhere](mailto:support@FilesAnywhere.com) i zapewnić im pobrany token SAML podpisywania certyfikatu i logowanie jednokrotne (SSO) adres URL.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/filesanywhere-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**. 



### <a name="creating-a-filesanywhere-test-user"></a>Tworzenie użytkownika testowego FilesAnywhere

Aplikacja obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy zostaną automatycznie utworzeni w aplikacji. 


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej FilesAnywhere.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon FilesAnywhere, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **FilesAnywhere**.

    ![Konfigurowanie logowania jednokrotnego](./media/filesanywhere-tutorial/tutorial_FilesAnywhere_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka FilesAnywhere w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji FilesAnywhere.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/FilesAnywhere-tutorial/tutorial_general_01.png
[2]: ./media/FilesAnywhere-tutorial/tutorial_general_02.png
[3]: ./media/FilesAnywhere-tutorial/tutorial_general_03.png
[4]: ./media/FilesAnywhere-tutorial/tutorial_general_04.png

[100]: ./media/FilesAnywhere-tutorial/tutorial_general_100.png

[200]: ./media/FilesAnywhere-tutorial/tutorial_general_200.png
[201]: ./media/FilesAnywhere-tutorial/tutorial_general_201.png
[202]: ./media/FilesAnywhere-tutorial/tutorial_general_202.png
[203]: ./media/FilesAnywhere-tutorial/tutorial_general_203.png
