---
title: 'Samouczek: Integracji Azure Active Directory z AirWatch | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i AirWatch.
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
ms.openlocfilehash: 4073fbb534ae712f8a5cad6c2e4aa750cec365b7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213288"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Samouczek: Integracji Azure Active Directory z AirWatch

Z tego samouczka dowiesz się integrowanie AirWatch z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD AirWatch zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do AirWatch
- Umożliwia użytkownikom automatycznie pobrać zalogowane do AirWatch (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z AirWatch, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- AirWatch jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie AirWatch z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-airwatch-from-the-gallery"></a>Dodawanie AirWatch z galerii
Aby skonfigurować integrację usługi Azure AD AirWatch, należy dodać AirWatch z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać AirWatch z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **AirWatch**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. W panelu wyników wybierz **AirWatch**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z AirWatch na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w AirWatch jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w AirWatch musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w AirWatch.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z AirWatch, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego AirWatch](#creating-a-airwatch-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta AirWatch połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji AirWatch.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z AirWatch, wykonaj następujące czynności:**

1. W portalu Azure na **AirWatch** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. Na **AirWatch domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. W **identyfikator** tekstowym, wpisz wartość jako `AirWatch`

    > [!NOTE] 
    > Ta wartość nie jest rzeczywistym. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta AirWatch](http://www.air-watch.com/company/contact-us/) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. Na **konfiguracji AirWatch** , kliknij przycisk **skonfigurować AirWatch** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy AirWatch.

8. W okienku nawigacji po lewej stronie kliknij **kont**, a następnie kliknij przycisk **Administratorzy**.
   
   ![Administratorzy](./media/airwatch-tutorial/ic791920.png "administratorów")

9. Rozwiń węzeł **ustawienia** menu, a następnie kliknij przycisk **usług katalogowych**.
   
   ![Ustawienia](./media/airwatch-tutorial/ic791921.png "ustawienia")

10. Kliknij przycisk **użytkownika** karcie **bazowa nazwa Wyróżniająca** tekstowym, wpisz nazwę domeny, a następnie kliknij przycisk **zapisać**.
   
   ![Użytkownik](./media/airwatch-tutorial/ic791922.png "użytkownika")

11. Kliknij przycisk **serwera** kartę.
   
   ![Serwer](./media/airwatch-tutorial/ic791923.png "serwera")

12. Wykonaj następujące czynności:
    
    ![Przekaż](./media/airwatch-tutorial/ic791924.png "Przekaż")   
    
    a. Jako **typ katalogu**, wybierz pozycję **Brak**.

    b. Wybierz **SAML jest używany do uwierzytelniania**.

    c. Aby przekazać pobranego certyfikatu, kliknij przycisk **przekazać**.

13. W **żądania** sekcji, wykonaj następujące czynności:
    
    ![Żądanie](./media/airwatch-tutorial/ic791925.png "żądania")  

    a. Jako **żądania typu powiązania**, wybierz pozycję **POST**.

    b. W portalu Azure na **skonfigurować logowanie jednokrotne w Airwatch** strony okna dialogowego, kopiowania **SAML pojedynczy znak na adres URL usługi** wartość, a następnie wklej ją do **tożsamości pojedynczy znak na adres URL dostawcy** pola tekstowego.

    c. Jako **NameID Format**, wybierz pozycję **adres E-mail**.

    d. Kliknij pozycję **Zapisz**.

14. Kliknij przycisk **użytkownika** karcie ponownie.
    
    ![Użytkownik](./media/airwatch-tutorial/ic791926.png "użytkownika")

15. W **atrybutu** sekcji, wykonaj następujące czynności:
    
    ![Atrybut](./media/airwatch-tutorial/ic791927.png "atrybutu")

    a. W **identyfikator obiektu** pole tekstowe, typ **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. W **Username** pole tekstowe, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. W **Nazwa wyświetlana** pole tekstowe, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. W **imię** pole tekstowe, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. W **nazwisko** pole tekstowe, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. W **E-mail** pole tekstowe, typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Kliknij pozycję **Zapisz**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z Simona Britta.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-airwatch-test-user"></a>Tworzenie użytkownika testowego AirWatch

Aby umożliwić użytkownikom usługi Azure AD zalogować się do AirWatch, ich należy udostępnić w celu AirWatch.

* Gdy AirWatch, inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **AirWatch** witryny firmy jako administrator.
2. W okienku nawigacji po lewej stronie kliknij **kont**, a następnie kliknij przycisk **użytkowników**.
   
   ![Użytkownicy](./media/airwatch-tutorial/ic791929.png "użytkowników")
3. W **użytkowników** menu, kliknij przycisk **widok listy**, a następnie kliknij przycisk **Dodaj \> Dodaj użytkownika**.
   
   ![Dodaj użytkownika](./media/airwatch-tutorial/ic791930.png "Dodaj użytkownika")
4. Na **Dodaj / Edytuj użytkownika** okna dialogowego, wykonaj następujące czynności:

   ![Dodaj użytkownika](./media/airwatch-tutorial/ic791931.png "Dodaj użytkownika")   
   1. Typ **Username**, **hasło**, **Potwierdź hasło**, **imię**, **nazwisko**, **adres E-mail** poprawnego konta usługi Azure Active Directory ustanawiane do powiązanych pól tekstowych.
   2. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Możesz użyć innych AirWatch użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez AirWatch do kont użytkowników usługi AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu AirWatch.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta AirWatch, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **AirWatch**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Jeśli chcesz przetestować jednego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

