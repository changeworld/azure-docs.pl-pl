---
title: 'Samouczek: Integracja usługi Azure Active Directory z AirWatch | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: bf95b949d6fee4057f67d1e44ded36f363aa5e2b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848922"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Samouczek: Integracja usługi Azure Active Directory z AirWatch

W tym samouczku dowiesz się, jak zintegrować AirWatch w usłudze Azure Active Directory (Azure AD).

Integrowanie AirWatch z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do AirWatch
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do AirWatch (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą AirWatch, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowania jednokrotnego AirWatch włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie AirWatch z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-airwatch-from-the-gallery"></a>Dodawanie AirWatch z galerii
Aby skonfigurować integrację AirWatch w usłudze Azure AD, należy dodać AirWatch z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać AirWatch z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **AirWatch**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. W panelu wyników wybierz **AirWatch**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą AirWatch, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w AirWatch do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w AirWatch musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w AirWatch.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą AirWatch, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego AirWatch](#creating-a-airwatch-test-user)**  — aby odpowiednikiem Britta Simon w AirWatch, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji AirWatch.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z AirWatch, wykonaj następujące czynności:**

1. W witrynie Azure portal na **AirWatch** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Na **AirWatch, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. W **identyfikator** polu tekstowym wpisz wartość jako `AirWatch`

    > [!NOTE] 
    > Ta wartość nie jest rzeczywistym. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta AirWatch](https://www.air-watch.com/company/contact-us/) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Na **konfiguracji AirWatch** , kliknij przycisk **skonfigurować AirWatch** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy AirWatch.

8. W okienku nawigacji po lewej stronie kliknij **kont**, a następnie kliknij przycisk **Administratorzy**.
   
   ![Administratorzy](./media/airwatch-tutorial/ic791920.png "administratorów")

9. Rozwiń **ustawienia** menu, a następnie kliknij przycisk **usługa katalogowa**.
   
   ![Ustawienia](./media/airwatch-tutorial/ic791921.png "ustawienia")

10. Kliknij przycisk **użytkownika** na karcie **bazowa nazwa Wyróżniająca** polu tekstowym wpisz nazwę domeny, a następnie kliknij przycisk **Zapisz**.
   
   ![Użytkownik](./media/airwatch-tutorial/ic791922.png "użytkownika")

11. Kliknij przycisk **serwera** kartę.
   
   ![Serwer](./media/airwatch-tutorial/ic791923.png "serwera")

12. Wykonaj następujące czynności:
    
    ![Przekaż](./media/airwatch-tutorial/ic791924.png "przekazywania")   
    
    a. Jako **typ katalogu**, wybierz opcję **Brak**.

    b. Wybierz **SAML jest używany do uwierzytelniania**.

    c. Aby przekazać pobranego certyfikatu, kliknij przycisk **przekazywanie**.

13. W **żądania** sekcji, wykonaj następujące czynności:
    
    ![Żądanie](./media/airwatch-tutorial/ic791925.png "żądania")  

    a. Jako **żądania powiązania typu**, wybierz opcję **WPIS**.

    b. W witrynie Azure portal na **skonfigurować logowanie jednokrotne w Airwatch** strony okna dialogowego, kopia **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartość, a następnie wklej go do **tożsamość dostawcy logowania jednokrotnego Adres URL** pola tekstowego.

    c. Jako **Format identyfikatora NameID**, wybierz opcję **adres E-mail**.

    d. Kliknij pozycję **Zapisz**.

14. Kliknij przycisk **użytkownika** karcie ponownie.
    
    ![Użytkownik](./media/airwatch-tutorial/ic791926.png "użytkownika")

15. W **atrybut** sekcji, wykonaj następujące czynności:
    
    ![Atrybut](./media/airwatch-tutorial/ic791927.png "atrybutu")

    a. W **identyfikatora obiektu** polu tekstowym wpisz **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. W **Username** polu tekstowym wpisz **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. W **nazwę wyświetlaną** polu tekstowym wpisz **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. W **imię** polu tekstowym wpisz **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. W **nazwisko** polu tekstowym wpisz **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. W **E-mail** polu tekstowym wpisz **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Kliknij pozycję **Zapisz**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** obiektu Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-airwatch-test-user"></a>Tworzenie użytkownika testowego AirWatch

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w AirWatch, ich musi być obsługiwana w celu AirWatch.

* Gdy AirWatch, inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **AirWatch** witryny firmy jako administrator.
2. W okienku nawigacji po lewej stronie kliknij pozycję **kont**, a następnie kliknij przycisk **użytkowników**.
   
   ![Użytkownicy](./media/airwatch-tutorial/ic791929.png "użytkowników")
3. W **użytkowników** menu, kliknij przycisk **widok listy**, a następnie kliknij przycisk **Dodaj \> Dodaj użytkownika**.
   
   ![Dodaj użytkownika](./media/airwatch-tutorial/ic791930.png "Dodaj użytkownika")
4. Na **Dodaj / Edytuj użytkownika** okno dialogowe, należy wykonać następujące czynności:

   ![Dodaj użytkownika](./media/airwatch-tutorial/ic791931.png "Dodaj użytkownika")   
   1. Typ **Username**, **hasło**, **Potwierdź hasło**, **imię**, **nazwisko**,  **Adres e-mail** poprawnego konta usługi Azure Active Directory do aprowizowania w powiązanych pól tekstowych.
   2. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Można użyć jakichkolwiek innych AirWatch użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez AirWatch do aprowizacji kont użytkowników usługi AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do AirWatch.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon AirWatch, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **AirWatch**.

    ![Konfigurowanie logowania jednokrotnego](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png

