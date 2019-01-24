---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą języka T & E Express | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: jeedes
ms.openlocfilehash: 67cc50f3b6812abc566620396369b195d106f2be
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815178"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą języka T & E Express

W tym samouczku dowiesz się, jak zintegrować T & E Express z usługą Azure Active Directory (Azure AD).

Integrowanie T & E Express z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do T & E Express
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do T & E Express (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą języka T & E Express, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- T & E Express logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie T & E Express z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-te-express-from-the-gallery"></a>Dodawanie T & E Express z galerii
Aby skonfigurować integrację T & E Express w usłudze Azure AD, należy dodać T & E Express z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać T & E Express z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **T & E Express**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_search.png)

1. W panelu wyników wybierz **T & E Express**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/tutorial_tyeexpress_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą języka T & E Express w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w T & E Express dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników T & E Express w musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w T & E Express.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą języka T & E Express, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego T & E Express](#creating-a-te-express-test-user)**  — aby odpowiednikiem Britta Simon T & E Express, która jest połączona z jej reprezentacji usługi Azure AD w.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji T & E Express.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą języka T & E Express, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **T & E Express** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tutorial_tyeexpress_samlbase.png)

1. Na **T & E Express domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tutorial_tyeexpress_url.png)

    a. W **identyfikator** polu tekstowym wpisz wartość jako: `https://<domain>.tyeexpress.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE] 
    > Należy pamiętać, że nie są to rzeczywiste wartości. Musisz zaktualizować te wartości z rzeczywistych identyfikatorem i adres URL odpowiedzi. W tym miejscu zalecamy użycie unikatowej wartości ciągu w identyfikatorze. Skontaktuj się z pomocą [T & E Express zespołu pomocy technicznej](http://www.tyeexpress.com/contacto.aspx) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tutorial_tyeexpress_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **T & E Express** strona, zaloguj się do T & E express aplikacji bez SAML logowania jednokrotnego przy użyciu poświadczeń administratora.

1. W obszarze **administratora** kartę, kliknij pozycję **domeny SAML** aby otworzyć stronę ustawień języka SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tye-SAML.png)

1. Wybierz **Activar(Activate)** opcję **nie** do **SI(Yes)**. W **metadanych dostawcy tożsamości** pola tekstowego, Wklej metadane XML, który masz pobrano z witryny Azure portal.

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Kliknij pozycję **Guardar(Save)** przycisk, aby zapisać ustawienia.  


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tyeexpress-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-te-express-test-user"></a>Tworzenie użytkownika testowego T & E Express

Aby umożliwić użytkownikom usługi Azure AD zalogować się do T & E Express, musi być obsługiwana w T & E Express.  
W przypadku T & E Express Inicjowanie obsługi to zadanie ręczne.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy T & E Express jako administrator.

1. W obszarze tagu administratora kliknij na użytkowników, aby otworzyć stronę wzorcową użytkowników.

    ![Dodawanie pracownika](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na stronie głównej kliknij **+** Aby dodać użytkowników.

    ![Dodawanie pracownika](./media/tyeexpress-tutorial/tye-usershome.png)

1. Wprowadź wszystkie wymagane informacje, jak pytanie, w formularzu i kliknij przycisk Zapisz, aby zapisać szczegóły.

    ![Dodawanie pracownika](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Dodawanie pracownika](./media/tyeexpress-tutorial/tye-userssave.png)


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej do T & E Express.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon T & E Express, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **T & E Express**.

    ![Konfigurowanie logowania jednokrotnego](./media/tyeexpress-tutorial/tutorial_tyeexpress_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka T & E Express w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji T & E Express.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tyeexpress-tutorial/tutorial_general_01.png
[2]: ./media/tyeexpress-tutorial/tutorial_general_02.png
[3]: ./media/tyeexpress-tutorial/tutorial_general_03.png
[4]: ./media/tyeexpress-tutorial/tutorial_general_04.png

[100]: ./media/tyeexpress-tutorial/tutorial_general_100.png

[200]: ./media/tyeexpress-tutorial/tutorial_general_200.png
[201]: ./media/tyeexpress-tutorial/tutorial_general_201.png
[202]: ./media/tyeexpress-tutorial/tutorial_general_202.png
[203]: ./media/tyeexpress-tutorial/tutorial_general_203.png

