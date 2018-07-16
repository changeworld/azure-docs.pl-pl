---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązania Zscaler ZSCloud | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i rozwiązania Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: ea6008092f8fbfde8963da50d5273d36a4a30fe3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050014"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Samouczek: Integracja usługi Azure Active Directory z ZSCloud rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler ZSCloud za pomocą usługi Azure Active Directory (Azure AD).

Integracja rozwiązania Zscaler ZSCloud z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do ZSCloud rozwiązania Zscaler
- Użytkowników, aby automatycznie uzyskać zalogowanych do rozwiązania Zscaler ZSCloud (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozwiązania Zscaler ZSCloud, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Rozwiązania Zscaler ZSCloud logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rozwiązania Zscaler ZSCloud z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Dodawanie rozwiązania Zscaler ZSCloud z galerii
Aby skonfigurować integrację rozwiązania Zscaler ZSCloud w usłudze Azure AD, należy dodać rozwiązania Zscaler ZSCloud z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać rozwiązania Zscaler ZSCloud z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **ZSCloud rozwiązania Zscaler**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. W panelu wyników wybierz **ZSCloud rozwiązania Zscaler**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurujesz a test usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler ZSCloud oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ZSCloud rozwiązania Zscaler do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w ZSCloud rozwiązania Zscaler musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w ZSCloud rozwiązania Zscaler.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler ZSCloud, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie ustawień serwera proxy](#configuring-proxy-settings)**  — Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego ZSCloud rozwiązania Zscaler](#creating-a-zscaler-zscloud-test-user)**  — aby odpowiednikiem Britta Simon w ZSCloud rozwiązania Zscaler, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ZSCloud rozwiązania Zscaler.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler ZSCloud, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ZSCloud rozwiązania Zscaler** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. Na **rozwiązania Zscaler ZSCloud domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     W **adres URL logowania** pole tekstowe, wpisz adres URL używany przez użytkowników do logowania jednokrotnego do aplikacji ZSCloud rozwiązania ZScaler.
    
    > [!NOTE] 
    > Musisz zaktualizować tę wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta ZSCloud rozwiązania Zscaler](https://help.zscaler.com/zia) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_general_400.png)

6. Na **konfiguracji ZSCloud rozwiązania Zscaler** , kliknij przycisk **konfigurowania rozwiązania Zscaler ZSCloud** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy ZSCloud rozwiązania ZScaler.

8. W menu u góry kliknij **administracji**.
   
    ![Administracja](./media/zscaler-zscloud-tutorial/ic800206.png "administracji")

9. W obszarze **Zarządzanie administratorami i ról**, kliknij przycisk **Zarządzanie użytkownikami i uwierzytelniania**.   
            
    ![Zarządzanie użytkownikami i uwierzytelniania](./media/zscaler-zscloud-tutorial/ic800207.png "zarządzania użytkownikami i uwierzytelniania")

10. W **wybierz opcje uwierzytelniania dla Twojej organizacji** sekcji, wykonaj następujące czynności:   
                
    ![Uwierzytelnianie](./media/zscaler-zscloud-tutorial/ic800208.png "uwierzytelniania")
   
    a. Wybierz **uwierzytelnianie przy użyciu protokołu SAML logowania jednokrotnego**.

    b. Kliknij przycisk **skonfigurować SAML pojedynczego logowania jednokrotnego parametry**.

11. Na **konfigurować SAML pojedynczego logowania jednokrotnego parametry** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **gotowe**

    ![Logowanie jednokrotne](./media/zscaler-zscloud-tutorial/ic800209.png "logowanie jednokrotne")
    
    a. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartością do **adres URL portalu SAML, do którego użytkownicy są wysyłane do uwierzytelniania** pola tekstowego.
    
    b. W **atrybut zawierający nazwę logowania** polu tekstowym wpisz **NameID**.
    
    c. Aby przekazać certyfikat pobrany, kliknij przycisk **pem rozwiązania Zscaler**.
    
    d. Wybierz **Włącz SAML automatycznej aprowizacji**.

12. Na **Konfigurowanie uwierzytelniania użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Administracja](./media/zscaler-zscloud-tutorial/ic800210.png "administracji")
    
    a. Kliknij pozycję **Zapisz**.

    b. Kliknij przycisk **Aktywuj teraz**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer

1. Rozpocznij **programu Internet Explorer**.

2. Wybierz **Opcje internetowe** z **narzędzia** menu Otwórz **Opcje internetowe** okna dialogowego.   
    
     ![Opcje internetowe](./media/zscaler-zscloud-tutorial/ic769492.png "Opcje internetowe")

3. Kliknij przycisk **połączeń** kartę.   
  
     ![Połączenia](./media/zscaler-zscloud-tutorial/ic769493.png "połączeń")

4. Kliknij przycisk **ustawienia sieci LAN** otworzyć **ustawienia sieci LAN** okna dialogowego.

5. W sekcji serwer Proxy wykonaj następujące czynności:   
   
    ![Serwer proxy](./media/zscaler-zscloud-tutorial/ic769494.png "serwera Proxy")

    a. Wybierz **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym adresu wpisz **gateway.zscalerone.net**.

    c. W polu tekstowym portu, wpisz **80**.

    d. Wybierz **używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK** zamknąć **ustawienia sieci lokalnej (LAN)** okna dialogowego.

6. Kliknij przycisk **OK** zamknąć **Opcje internetowe** okna dialogowego.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Tworzenie użytkownika testowego ZSCloud rozwiązania Zscaler

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do rozwiązania ZScaler ZSCloud, mogą zostać aprowizowane rozwiązania ZScaler ZSCloud.  
W przypadku ZSCloud rozwiązania ZScaler aprowizacji to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:

1. Zaloguj się do Twojej **rozwiązania Zscaler** dzierżawy.

2. Kliknij przycisk **administracji**.   
   
    ![Administracja](./media/zscaler-zscloud-tutorial/ic781035.png "administracji")

3. Kliknij przycisk **Zarządzanie użytkownikami**.   
        
     ![Dodaj](./media/zscaler-zscloud-tutorial/ic781037.png "Dodaj")

4. W **użytkowników** kliknij pozycję **Dodaj**.
      
    ![Dodaj](./media/zscaler-zscloud-tutorial/ic781037.png "Dodaj")

5. W sekcji Dodaj użytkownika wykonaj następujące czynności:
        
    ![Dodaj użytkownika](./media/zscaler-zscloud-tutorial/ic781038.png "Dodaj użytkownika")
   
    a. Typ **UserID**, **Nazwa wyświetlana użytkownika**, **hasło**, **Potwierdź hasło**, a następnie wybierz pozycję **grup**i **działu** poprawnego konta usługi AAD do aprowizowania.

    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Można użyć jakichkolwiek innych ZSCloud rozwiązania ZScaler użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez rozwiązania ZScaler ZSCloud do aprowizacji kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler ZSCloud.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon ZSCloud rozwiązania Zscaler, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **ZSCloud rozwiązania Zscaler**.

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu.

Po kliknięciu kafelka rozwiązania Zscaler ZSCloud w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ZSCloud rozwiązania Zscaler.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-zscloud-tutorial/tutorial_general_203.png

