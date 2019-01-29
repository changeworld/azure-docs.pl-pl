---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą iLMS | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 0a1de782b3b4502107c14282bdd4ce5ef2caa871
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165918"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą iLMS

W tym samouczku dowiesz się, jak zintegrować iLMS w usłudze Azure Active Directory (Azure AD).

Integrowanie iLMS z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do iLMS
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do iLMS (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą iLMS, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowania jednokrotnego iLMS włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie iLMS z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii
Aby skonfigurować integrację iLMS w usłudze Azure AD, należy dodać iLMS z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać iLMS z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **iLMS**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/tutorial_ilms_search.png)

1. W panelu wyników wybierz **iLMS**, następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą iLMS w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w iLMS do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w iLMS musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w iLMS.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą iLMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego iLMS](#creating-an-ilms-test-user)**  — aby mają odpowiednika w pozycji Britta simon w iLMS połączonego z jej reprezentacji usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji iLMS.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z iLMS, wykonaj następujące czynności:**

1. W witrynie Azure portal na **iLMS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

1. Na **iLMS domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. W **identyfikator** pola tekstowego, Wklej **identyfikator** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS.

    b. W **adres URL odpowiedzi** pola tekstowego, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS o następującym wzorcem `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Ta "123456" jest Przykładowa wartość identyfikatora.

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_url1.png)

    W **adres URL logowania** pola tekstowego, Wklej **(adres URL punktu końcowego)** wartości w przypadku kopiowania z **usługodawcy** sekcji Ustawienia języka SAML w portalu administracyjnym iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

1. Aby umożliwić inicjowanie obsługi administracyjnej JIT, iLMS aplikacji oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/4.png)
    
    Tworzenie **działu, Region** i **dzielenia** atrybutów, a następnie dodaj nazwę tych atrybutów w iLMS. Te atrybuty powyżej są wymagane.  

    > [!NOTE] 
    > Należy włączyć **Utwórz konto użytkownika Un-recognized** w iLMS do mapowania tych atrybutów. Postępuj zgodnie z instrukcjami [tutaj](http://support.inspiredelearning.com/customer/portal/articles/2204526) i Uzyskaj konfiguracji atrybutów.

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | Dzielenie | user.department |
    | region | User.state |
    | department | user.jobtitle |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_general_400.png)

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego iLMS** jako administrator.

1. Kliknij przycisk **SSO:SAML** w obszarze **ustawienia** kartę, aby otworzyć ustawienia języka SAML i wykonaj następujące czynności:
    
    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/1.png) 

    a. Rozwiń **usługodawcy** sekcji a następnie skopiuj **identyfikator** i **(adres URL punktu końcowego)** wartość.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/2.png) 

    b. W obszarze **dostawcy tożsamości** kliknij **Importowanie metadanych**.
    
    c. Wybierz **metadanych** plik pobrany z witryny Azure Portal z **certyfikat podpisywania SAML** sekcji.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Jeśli chcesz włączyć JIT inicjowania obsługi administracyjnej do tworzenia iLMS kont un-rozpoznaje użytkowników, wykonaj poniższe kroki:
        
       - Sprawdź **utworzyć konto użytkownika nie jest rozpoznawanym**.
       
       ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapowanie atrybutów w usłudze Azure AD za pomocą atrybutów w iLMS. W kolumnie atrybutów określenie nazwy atrybutów lub wartość domyślną.

    e. Przejdź do **reguły biznesowe** kartę i wykonaj następujące czynności: 
        
       ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

       - Sprawdź **Tworzenie regionów Un-recognized, działów i działów** utworzyć regionów, działów i działów, które jeszcze nie istnieją w czasie rejestracji jednokrotnej.
        
       - Sprawdź **aktualizacji użytkownika profilu podczas logowania** do określenia, czy profil użytkownika jest aktualizowany za pomocą każdego logowania jednokrotnego. 
        
       - Jeśli **"Aktualizacji puste wartości dla innych obowiązkowego pola w profilu użytkownika"** opcja jest zaznaczona, pola opcjonalne profilu, które są puste po zalogowaniu będą również powodować profilu iLMS użytkownika zawiera wartości puste dla tych pól.
        
       - Sprawdź **Wyślij błąd wiadomość E-mail z powiadomieniem** i wprowadź adres e-mail użytkownika, w których chcesz otrzymywać wiadomość e-mail z powiadomieniem błędu.

1. Kliknij przycisk **Zapisz**, aby zapisać ustawienia.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/save.png)

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-ilms-test-user"></a>Tworzenie użytkownika testowego iLMS

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. JIT będzie działać po kliknięciu **Utwórz konto użytkownika Un-recognized** wyboru podczas ustawienia konfiguracji protokołu SAML w portalu administracyjnym iLMS.

Jeśli musisz utworzyć ręcznie przez użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy iLMS jako administrator.

1. Kliknij przycisk **"Zarejestruj użytkownika"** w obszarze **użytkowników** kartę, aby otworzyć **zarejestrowania użytkownika** strony. 
   
   ![Dodawanie pracownika](./media/ilms-tutorial/3.png)

1. Na **"Zarejestruj użytkownika"** strony, wykonaj następujące kroki.

    ![Dodawanie pracownika](./media/ilms-tutorial/create_testuser_add.png)

    a. W **imię** polu tekstowym Nazwa typu pierwszy Britta.
   
    b. W **nazwisko** polu tekstowym wpisz nazwisko Simon.

    c. W **identyfikator poczty E-mail** pole tekstowe, wpisz adres e-mail konta Britta Simon.

    d. W **Region** listy rozwijanej wybierz wartość dla regionu.

    e. W **dzielenia** listy rozwijanej wybierz wartość dla działu.

    f. W **działu** listy rozwijanej wybierz wartość dla działu.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE] 
    > Możesz wysłać wiadomość e-mail dotycząca rejestracji użytkownika, wybierając **Wyślij wiadomość e-mail dotycząca rejestracji** pola wyboru.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon korzystać z platformy Azure logowania jednokrotnego przez udostępnienie jej iLMS.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon iLMS, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **iLMS**.

    ![Konfigurowanie logowania jednokrotnego](./media/ilms-tutorial/tutorial_ilms_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka iLMS w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji iLMS.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

