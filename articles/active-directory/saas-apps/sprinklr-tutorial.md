---
title: 'Samouczek: Integracji Azure Active Directory z Sprinklr | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Sprinklr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 7037c509eeb4b7adf1232ebd29b7f7034237f22b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941186"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Samouczek: Integracji Azure Active Directory z Sprinklr

Z tego samouczka dowiesz się integrowanie Sprinklr z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Sprinklr zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Sprinklr
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Sprinklr (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Sprinklr, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sprinklr jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Sprinklr z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sprinklr-from-the-gallery"></a>Dodawanie Sprinklr z galerii
Aby skonfigurować integrację usługi Azure AD Sprinklr, należy dodać Sprinklr z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Sprinklr z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Sprinklr**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

5. W panelu wyników wybierz **Sprinklr**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Sprinklr na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Sprinklr jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Sprinklr musi się.

W Sprinklr, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Sprinklr, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Sprinklr](#creating-a-sprinklr-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Sprinklr połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Sprinklr.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Sprinklr, wykonaj następujące czynności:**

1. W portalu Azure na **Sprinklr** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. Na **Sprinklr domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania i identyfikator. Skontaktuj się z [zespołem pomocy technicznej klienta Sprinklr](https://www.sprinklr.com/contact-us/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sprinklr-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Sprinklr** , kliknij przycisk **skonfigurować Sprinklr** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Sprinklr.

8. Przejdź do **administracji \> ustawienia**.
   
    ![Administracja](./media/sprinklr-tutorial/ic782907.png "administracji")

9. Przejdź do **Zarządzanie partnera \> jednokrotnego** na w lewym okienku.
   
    ![Zarządzanie partnerem](./media/sprinklr-tutorial/ic782908.png "Zarządzanie partnera")

10. Kliknij przycisk **+ dodatki jednokrotnego**.
   
    ![Pojedynczy prób logowania](./media/sprinklr-tutorial/ic782909.png "pojedynczy prób logowania")

11. Na **logowania jednokrotnego** wykonaj następujące czynności:
   
    ![Pojedynczy prób logowania](./media/sprinklr-tutorial/ic782910.png "pojedynczy prób logowania")

    a. W **nazwa** tekstowym, wpisz nazwę konfiguracji (na przykład: *WAADSSOTest*).

    b. Wybierz **włączone**.

    c. Wybierz **korzystania z nowego certyfikatu logowania jednokrotnego**.
             
    e. Otwórz w Notatniku certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy tożsamości** pola tekstowego.

    f. Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure do **identyfikator jednostki** pola tekstowego.

    g. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **adresu URL logowania do dostawcy tożsamości** pola tekstowego.

    h. Wklej **Sign-Out URL** wartość, która została skopiowana z portalu Azure do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.
     
    i. Jako **typ Identyfikatora użytkownika SAML**, wybierz pozycję **potwierdzenia zawiera użytkownika "s sprinklr.com username**.

    j. Jako **lokalizacji identyfikator użytkownika SAML**, wybierz pozycję **identyfikator użytkownika jest w elemencie identyfikator nazwy podmiotu instrukcji**.

    k. Kliknij pozycję **Zapisz**.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-sprinklr-test-user"></a>Tworzenie użytkownika testowego Sprinklr

1. Zaloguj się do witryny firmy Sprinklr jako administrator.

2. Przejdź do **administracji \> ustawienia**.
   
    ![Administracja](./media/sprinklr-tutorial/ic782907.png "administracji")

3. Przejdź do **klienta zarządzania \> użytkowników** w lewym okienku.
   
    ![Ustawienia](./media/sprinklr-tutorial/ic782914.png "ustawienia")

4. Kliknij przycisk **dodać użytkownika**.
   
    ![Ustawienia](./media/sprinklr-tutorial/ic782915.png "ustawienia")

5. Na **Edycja użytkownika** okna dialogowego, wykonaj następujące czynności:
   
    ![Edytowanie użytkownika](./media/sprinklr-tutorial/ic782916.png "Edycja użytkownika") 

    a. W **E-mail**, **imię** i **nazwisko** pól tekstowych, wpisz informacje, które chcesz udostępnić konta użytkownika usługi Azure AD.

    b. Wybierz **wyłączone hasło**.

    c. Wybierz **języka**.

    d. Wybierz **typ użytkownika**.

    e. Kliknij przycisk **aktualizacji**.
   
     >[!IMPORTANT]
     >**Wyłączone hasło** należy wybrać, aby użytkownik mógł zalogować się za pośrednictwem dostawcy tożsamości. 
     
6. Przejdź do **roli**, a następnie wykonaj następujące czynności:
   
    ![Partnera ról](./media/sprinklr-tutorial/ic782917.png "partnera ról")

    a. Z **Global** listy, wybierz **wszystkie\_uprawnienia**.  

    b. Kliknij przycisk **aktualizacji**.

>[!NOTE]
>Możesz użyć innych Sprinklr użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Sprinklr do udostępnienia konta użytkownika usługi Azure AD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Sprinklr.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Sprinklr, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Sprinklr**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Sprinklr w panelu dostępu, należy pobrać automatycznie zalogowane Sprinklr aplikacji można znaleźć więcej informacji na temat panelu dostępu [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

