---
title: 'Samouczek: Integracji Azure Active Directory z iLMS | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: cdb59a7078b3bbce112356b61e19702fb6ed9df1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228727"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Samouczek: Integracji Azure Active Directory z iLMS

Z tego samouczka dowiesz się integrowanie iLMS z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD iLMS zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do iLMS
- Umożliwia użytkownikom automatycznie pobrać zalogowane do iLMS (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z iLMS, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ILMS jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie iLMS z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-ilms-from-the-gallery"></a>Dodawanie iLMS z galerii
Aby skonfigurować integrację usługi Azure AD iLMS, należy dodać iLMS z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać iLMS z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **iLMS**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/tutorial_ilms_search.png)

5. W panelu wyników wybierz **iLMS**, następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iLMS w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w iLMS jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w iLMS musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w iLMS.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iLMS, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego iLMS](#creating-an-ilms-test-user)**  — mają odpowiednika Simona Britta w iLMS połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji iLMS.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z iLMS, wykonaj następujące czynności:**

1. W portalu Azure na **iLMS** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

3. Na **iLMS domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. W **identyfikator** pole tekstowe, Wklej **identyfikator** wartość skopiować z **usługodawcy** sekcji SAML ustawień w portalu administracyjnym iLMS.

    b. W **adres URL odpowiedzi** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartość skopiować z **usługodawcy** sekcji SAML ustawień w portalu administratora iLMS o następującego wzorca `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Ta 123456 jest Przykładowa wartość identyfikatora.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_url1.png)

    W **adres URL logowania** pole tekstowe, Wklej **(adres URL punktu końcowego)** wartość skopiować z **usługodawcy** sekcji SAML ustawień w portalu administracyjnym iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. Aby włączyć JIT inicjowania obsługi administracyjnej, iLMS aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z **atrybuty użytkownika** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/4.png)
    
    Utwórz **działu, Region** i **dzielenia** atrybutów, a następnie dodaj nazwę tych atrybutów w iLMS. Te atrybuty pokazanym powyżej są wymagane.  

    > [!NOTE] 
    > Należy włączyć **Utwórz konto użytkownika Un-recognized** w iLMS mapować te atrybuty. Postępuj zgodnie z instrukcjami [tutaj](http://support.inspiredelearning.com/customer/portal/articles/2204526) Aby poznać konfiguracji atrybutów.

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | dzielenie | User.Department |
    | region | User.state |
    | dział | user.jobtitle |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **Ok**

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_general_400.png)

9. W oknie przeglądarki innej witryny sieci web, zaloguj się do Twojego **portalu administracyjnego iLMS** jako administrator.

10. Kliknij przycisk **SSO:SAML** w obszarze **ustawienia** kartę, aby otworzyć ustawienia SAML i wykonaj następujące czynności:
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/1.png) 

    a. Rozwiń węzeł **usługodawcy** sekcji i skopiuj **identyfikator** i **(adres URL punktu końcowego)** wartość.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/2.png) 

    b. W obszarze **dostawcy tożsamości** kliknij **Importowanie metadanych**.
    
    c. Wybierz **metadanych** plik pobrany z portalu Azure z **certyfikat podpisywania SAML** sekcji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Jeśli chcesz włączyć JIT inicjowania obsługi administracyjnej na tworzenie kont iLMS dla un-rozpoznaje użytkowników, wykonaj następujące czynności:
        
       - Sprawdź **utworzyć konto użytkownika nie jest rozpoznawaną**.
       
       ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Mapowanie atrybutów w usłudze Azure AD z atrybutów w iLMS. W kolumnie atrybutów Określ nazwę atrybuty lub wartość domyślną.

    e. Przejdź do **reguły biznesowe** karcie i wykonaj następujące czynności: 
        
       ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/5.png)

       - Sprawdź **Tworzenie regionów Un-recognized, działów i działów** do tworzenia regionów, działów i działów, które jeszcze nie istnieją w czasie rejestracji jednokrotnej.
        
       - Sprawdź **aktualizacji użytkownika profilu podczas logowania** do określenia, czy profil użytkownika jest aktualizowany przy każdej rejestracji jednokrotnej. 
        
       - Jeśli **"Aktualizacji puste wartości dla innych niż obowiązkowego pola w profilu użytkownika"** opcja jest zaznaczona, profil opcjonalne pola, które są puste podczas logowania zostanie także spowodować profilu użytkownika iLMS zawiera puste wartości dla tych pól.
        
       - Sprawdź **Wyślij E-mail z powiadomieniem błąd** , a następnie wprowadź adres e-mail użytkownika, których chcesz otrzymywać wiadomość e-mail z powiadomieniem błąd.

11. Kliknij przycisk **zapisać** przycisk, aby zapisać ustawienia.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/save.png)

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/ilms-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-ilms-test-user"></a>Tworzenie użytkownika testowego iLMS

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. JIT będzie działać, jeśli kliknięto **Utwórz konto użytkownika Un-recognized** wyboru podczas SAML ustawienie konfiguracji w portalu administracyjnym iLMS.

Jeśli trzeba ręcznie utworzyć użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy iLMS jako administrator.

2. Kliknij przycisk **"Zarejestruj użytkownika"** w obszarze **użytkowników** kartę, aby otworzyć **zarejestrować użytkownika** strony. 
   
   ![Dodawanie pracownika](./media/ilms-tutorial/3.png)

3. Na **"Zarejestruj użytkownika"** wykonaj następujące kroki.

    ![Dodawanie pracownika](./media/ilms-tutorial/create_testuser_add.png)

    a. W **imię** pole tekstowe, nazwa typu pierwszy Britta.
   
    b. W **nazwisko** tekstowym, wpisz nazwisko Simona.

    c. W **identyfikator wiadomości E-mail** tekstowym, wpisz adres e-mail konta Simona Britta.

    d. W **Region** listy rozwijanej wybierz wartość dla regionu.

    e. W **dzielenia** listy rozwijanej wybierz wartość dla działu.

    f. W **działu** listy rozwijanej wybierz wartość dla działu.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE] 
    > Wiadomość e-mail rejestracji można wysłać do użytkownika, wybierając **wysłać wiadomość E-mail rejestracji** wyboru.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do udostępnienia jej iLMS za pomocą usługi Azure rejestracji jednokrotnej.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta iLMS, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **iLMS**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ilms-tutorial/tutorial_ilms_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka iLMS w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji iLMS.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

