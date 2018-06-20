---
title: 'Samouczek: Azure Active Directory integracji z jednego Zscaler | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Zscaler jeden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 7bd0e990dcda13452911c0c6d9059e62db43b7a6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224858"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Samouczek: Azure Active Directory integracji z jednego Zscaler

Z tego samouczka dowiesz się integrowanie Zscaler jednego z usługi Azure Active Directory (Azure AD).

Integrowanie Zscaler jednego z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do jednej Zscaler
- Umożliwia użytkownikom automatycznie pobrać zalogowane do jednej Zscaler (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z jednym Zscaler, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Jeden Zscaler logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Zscaler jednego z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-one-from-the-gallery"></a>Dodawanie Zscaler jednego z galerii
Aby skonfigurować integrację Zscaler co w usłudze Azure Active Directory, należy dodać do listy zarządzane aplikacje SaaS Zscaler jednego z galerii.

**Aby dodać Zscaler jednego z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Zscaler jeden**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/tutorial_zscalerone_search.png)

5. W panelu wyników wybierz **Zscaler jeden**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/tutorial_zscalerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z jednego Zscaler w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w jednym Zscaler jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w jednym Zscaler musi określone.

W jednej Zscaler przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z jednego Zscaler, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie ustawień serwera proxy](#configuring-proxy-settings)**  — Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer
3. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
4. **[Tworzenie użytkownika testowego Zscaler jeden](#creating-a-zscaler-one-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Zscaler jednego połączonego z usługi Azure AD reprezentację użytkownika.
5. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
6. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w jednym Zscaler aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z jednego Zscaler, wykonaj następujące czynności:**

1. W portalu Azure na **Zscaler jeden** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_zscalerone_samlbase.png)

3. Na **Zscaler jednej domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_zscalerone_url.png)

    W polu tekstowym adres URL logowania wpisz adres URL używany przez użytkowników do logowania jednokrotnego do aplikacji Zscaler jeden.

    > [!NOTE] 
    > Należy zaktualizować tę wartość z adresem URL logowania rzeczywistych. Skontaktuj się z [zespołem pomocy technicznej Zscaler jeden klient](https://www.zscaler.com/company/contact) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_zscalerone_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_general_400.png)

6. Na **Zscaler jedną konfigurację** kliknij **skonfigurować jeden Zscaler** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_zscalerone_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do jednego Zscaler witryny firmy.

8. W menu u góry kliknij **administracji**.
   
    ![Administracja](./media/zscaler-one-tutorial/ic800206.png "administracji")

9. W obszarze **administratorom zarządzanie & role**, kliknij przycisk **Zarządzanie użytkownikami & uwierzytelniania**.   
            
    ![Zarządzaj użytkownikami & uwierzytelniania](./media/zscaler-one-tutorial/ic800207.png "zarządzania użytkownikami i uwierzytelniania")

10. W **wybierz opcje uwierzytelniania dla Twojej organizacji** sekcji, wykonaj następujące czynności:   
                
    ![Uwierzytelnianie](./media/zscaler-one-tutorial/ic800208.png "uwierzytelniania")
   
    a. Wybierz **uwierzytelnianie przy użyciu SAML logowania jednokrotnego**.

    b. Kliknij przycisk **skonfigurować SAML pojedynczego logowania jednokrotnego parametry**.

11. Na **skonfigurować SAML pojedynczych logowania jednokrotnego parametrów** strony okna dialogowego, wykonaj następujące czynności, a następnie kliknij **gotowe**

    ![Logowanie jednokrotne](./media/zscaler-one-tutorial/ic800209.png "logowanie jednokrotne")
    
    a. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **adres URL portalu SAML, do którego użytkownicy są wysyłane do uwierzytelniania** pola tekstowego.
    
    b. W **atrybutu zawierającego nazwę logowania** pole tekstowe, typ **NameID**.
    
    c. Aby przekazać certyfikat pobrany, kliknij przycisk **Zscaler pem**.
    
    d. Wybierz **Włącz SAML automatycznego inicjowania obsługi**.

12. Na **skonfigurować uwierzytelnianie użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Administracja](./media/zscaler-one-tutorial/ic800210.png "administracji")
    
    a. Kliknij pozycję **Zapisz**.

    b. Kliknij przycisk **Aktywuj teraz**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer

1. Uruchom **programu Internet Explorer**.

2. Wybierz **Opcje internetowe** z **narzędzia** menu otwartego **Opcje internetowe** okna dialogowego.   
    
     ![Opcje internetowe](./media/zscaler-one-tutorial/ic769492.png "Opcje internetowe")

3. Kliknij przycisk **połączeń** kartę.   
  
     ![Połączenia](./media/zscaler-one-tutorial/ic769493.png "połączeń")

4. Kliknij przycisk **ustawienia sieci LAN** otworzyć **ustawienia sieci LAN** okna dialogowego.

5. W sekcji serwer Proxy wykonaj następujące czynności:   
   
    ![Serwer proxy](./media/zscaler-one-tutorial/ic769494.png "serwera Proxy")

    a. Wybierz **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym adres typu **gateway.zscalerone.net**.

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

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/zscaler-one-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-zscaler-one-test-user"></a>Tworzenie Zscaler jednego użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do jednego Zscaler, muszą mieć przydzielone do jednej Zscaler. W przypadku jednego Zscaler Inicjowanie obsługi to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:

1. Zaloguj się do Twojego **Zscaler jeden** dzierżawy.

2. Kliknij przycisk **administracji**.   
   
    ![Administracja](./media/zscaler-one-tutorial/ic781035.png "administracji")

3. Kliknij przycisk **Zarządzanie użytkownikami**.   
        
     ![Dodaj](./media/zscaler-one-tutorial/ic781036.png "Dodaj")

4. W **użytkowników** , kliknij pozycję **Dodaj**.
      
    ![Dodaj](./media/zscaler-one-tutorial/ic781037.png "Dodaj")

5. W sekcji Dodaj użytkownika wykonaj następujące czynności:
        
    ![Dodaj użytkownika](./media/zscaler-one-tutorial/ic781038.png "Dodaj użytkownika")
   
    a. Typ **UserID**, **Nazwa wyświetlana użytkownika**, **hasło**, **Potwierdź hasło**, a następnie wybierz **grup** i **działu** prawidłowy Azure konta AD, które chcesz udostępnić.

    b. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Inne narzędzia do tworzenia Zscaler jednego konta użytkownika lub interfejsów API dostarczonych przez jeden Zscaler służy do obsługi administracyjnej kont użytkowników usługi Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do jednej Zscaler.

![Przypisz użytkownika][200] 

**Aby przypisać jedną Zscaler Simona Britta, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Zscaler jeden**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/zscaler-one-tutorial/tutorial_zscalerone_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu jednego Zscaler kafelka w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane Zscaler jednej aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-one-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-one-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-one-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-one-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-one-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-one-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-one-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-one-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-one-tutorial/tutorial_general_203.png

