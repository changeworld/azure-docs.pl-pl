---
title: 'Samouczek: Integracji Azure Active Directory z Zscaler | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: dbfe3ec8ab1df90941272810be4598dc27a6e76f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230203"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Samouczek: Integracji Azure Active Directory z Zscaler

Z tego samouczka dowiesz się integrowanie Zscaler z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Zscaler zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Zscaler
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Zscaler (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Zscaler, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zscaler logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Zscaler z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-from-the-gallery"></a>Dodawanie Zscaler z galerii
Aby skonfigurować integrację usługi Azure AD Zscaler, należy dodać Zscaler z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Zscaler z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Zscaler**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/tutorial_zscaler_search.png)

5. W panelu wyników wybierz **Zscaler**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Zscaler w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Zscaler jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Zscaler musi się.

W Zscaler, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Zscaler, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie ustawień serwera proxy](#configuring-proxy-settings)**  — Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer
3. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
4. **[Tworzenie użytkownika testowego Zscaler](#creating-a-zscaler-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Zscaler połączonego z usługi Azure AD reprezentację użytkownika.
5. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
6. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Zscaler.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Zscaler, wykonaj następujące czynności:**

1. W portalu Azure na **Zscaler** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. Na **Zscaler domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_zscaler_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zscaler](https://www.zscaler.com/company/contact) aby zyskać tę wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Zscaler** , kliknij przycisk **skonfigurować Zscaler** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_zscaler_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy ZScaler.

8. W menu u góry kliknij **administracji**.
   
    ![Administracja](./media/zscaler-tutorial/ic800206.png "administracji")

9. W obszarze **administratorom zarządzanie & role**, kliknij przycisk **Zarządzanie użytkownikami & uwierzytelniania**.   
            
    ![Zarządzaj użytkownikami & uwierzytelniania](./media/zscaler-tutorial/ic800207.png "zarządzania użytkownikami i uwierzytelniania")

10. W **wybierz opcje uwierzytelniania dla Twojej organizacji** sekcji, wykonaj następujące czynności:   
                
    ![Uwierzytelnianie](./media/zscaler-tutorial/ic800208.png "uwierzytelniania")
   
    a. Wybierz **uwierzytelnianie przy użyciu SAML logowania jednokrotnego**.

    b. Kliknij przycisk **skonfigurować SAML pojedynczego logowania jednokrotnego parametry**.

11. Na **skonfigurować SAML pojedynczych logowania jednokrotnego parametrów** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **gotowe**

    ![Logowanie jednokrotne](./media/zscaler-tutorial/ic800209.png "logowanie jednokrotne")
    
    a. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **adres URL portalu SAML, do którego użytkownicy są wysyłane do uwierzytelniania** pola tekstowego.
    
    b. W **atrybutu zawierającego nazwę logowania** pole tekstowe, typ **NameID**.
    
    c. Aby przekazać certyfikat pobrany, kliknij przycisk **Zscaler pem**.
    
    d. Wybierz **Włącz SAML automatycznego inicjowania obsługi**.

12. Na **skonfigurować uwierzytelnianie użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Administracja](./media/zscaler-tutorial/ic800210.png "administracji")
    
    a. Kliknij pozycję **Zapisz**.

    b. Kliknij przycisk **Aktywuj teraz**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer

1. Uruchom **programu Internet Explorer**.

2. Wybierz **Opcje internetowe** z **narzędzia** menu otwartego **Opcje internetowe** okna dialogowego.   
    
     ![Opcje internetowe](./media/zscaler-tutorial/ic769492.png "Opcje internetowe")

3. Kliknij przycisk **połączeń** kartę.   
  
     ![Połączenia](./media/zscaler-tutorial/ic769493.png "połączeń")

4. Kliknij przycisk **ustawienia sieci LAN** otworzyć **ustawienia sieci LAN** okna dialogowego.

5. W sekcji serwer Proxy wykonaj następujące czynności:   
   
    ![Serwer proxy](./media/zscaler-tutorial/ic769494.png "serwera Proxy")

    a. Wybierz **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym adres typu **gateway.zscaler.net**.

    c. W polu tekstowym portu, wpisz **80**.

    d. Wybierz **używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK** zamknąć **ustawienia sieci lokalnej (LAN)** okna dialogowego.

6. Kliknij przycisk **OK** zamknąć **Opcje internetowe** okna dialogowego.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-zscaler-test-user"></a>Tworzenie użytkownika testowego Zscaler

Aby umożliwić użytkownikom usługi Azure AD zalogować się do ZScaler, muszą mieć przydzielone do ZScaler.  
W przypadku ZScaler Inicjowanie obsługi to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:

1. Zaloguj się do Twojego **Zscaler** dzierżawy.

2. Kliknij przycisk **administracji**.   
   
    ![Administracja](./media/zscaler-tutorial/ic781035.png "administracji")

3. Kliknij przycisk **Zarządzanie użytkownikami**.   
        
     ![Dodaj](./media/zscaler-tutorial/ic781036.png "Dodaj")

4. W **użytkowników** , kliknij pozycję **Dodaj**.
      
    ![Dodaj](./media/zscaler-tutorial/ic781037.png "Dodaj")

5. W sekcji Dodaj użytkownika wykonaj następujące czynności:
        
    ![Dodaj użytkownika](./media/zscaler-tutorial/ic781038.png "Dodaj użytkownika")
   
    a. Typu **UserID**, **Nazwa wyświetlana użytkownika**, **hasło**, **Potwierdź hasło**, a następnie wybierz **grup** i **działu** poprawnego konta usługi AAD chcesz udostępnić.

    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Możesz użyć innych ZScaler użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez ZScaler do kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Zscaler.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Zscaler, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Zscaler**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-tutorial/tutorial_zscaler_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Zscaler w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Zscaler.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-tutorial/tutorial_general_203.png

