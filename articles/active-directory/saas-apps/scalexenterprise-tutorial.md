---
title: 'Samouczek: Integrację usługi Azure Active Directory ze ScaleX | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 840dcabdc4a35f746b3f842991aae4cc338ad369
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941055"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Samouczek: Integrację usługi Azure Active Directory ze ScaleX

Z tego samouczka dowiesz się integrowanie ScaleX przedsiębiorstwa w usłudze Azure Active Directory (Azure AD).

Integrowanie ScaleX organizacji z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ScaleX Enterprise
- Umożliwia użytkownikom automatycznie pobrać zalogowane ScaleX przedsiębiorstwem (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz. Co to jest dostęp do aplikacji i logowanie jednokrotne z [usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ScaleX przedsiębiorstwa, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ScaleX Enterprise jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ScaleX przedsiębiorstwa z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Dodawanie ScaleX przedsiębiorstwa z galerii
Aby skonfigurować integrację ScaleX przedsiębiorstwa w usłudze Azure AD, należy dodać ScaleX przedsiębiorstwa z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ScaleX przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **ScaleX Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. W panelu wyników wybierz **ScaleX Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z ScaleX Enterprise oparte na użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w przedsiębiorstwie ScaleX jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w przedsiębiorstwie ScaleX musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w przedsiębiorstwie ScaleX.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ScaleX przedsiębiorstwa, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ScaleX przedsiębiorstwo, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji przedsiębiorstwa ScaleX.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ScaleX przedsiębiorstwa, wykonaj następujące czynności:**

1. W portalu Azure na **ScaleX Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Na **ScaleX domeny przedsiębiorstwa i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. W **identyfikator** tekstowym, wpisz wartość, przy użyciu następującego wzorca: `https://platform.rescale.com/saml2/<company id>/`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    W **adres URL logowania** tekstowym, wpisz wartość, przy użyciu następującego wzorca: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Nie są to rzeczywiste wartości. Rzeczywisty identyfikator, adres URL odpowiedzi lub adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Enterprise ScaleX](http://info.rescale.com/contact_sales) uzyskać te wartości. 

5. Aplikacja ScaleX oczekuje potwierdzenia języka SAML w określonym formacie, który wymaga zmodyfikowania mapowań atrybutów niestandardowych do konfiguracji atrybuty tokenu SAML. Kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** wyboru umożliwiające otwarcie niestandardowe atrybuty ustawień.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Kliknij prawym przyciskiem myszy atrybut **nazwa** i kliknij przycisk Usuń.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Kliknij przycisk **emailaddress** atrybutu, aby otworzyć okno edycji atrybucie. Zmień wartość z **user.mail** do **user.userprincipalname** i kliknij przycisk Ok.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracja dla przedsiębiorstw ScaleX** , kliknij przycisk **skonfigurować ScaleX Enterprise** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** i **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Aby skonfigurować logowanie jednokrotne w **ScaleX Enterprise** strona, zaloguj się do witryny internetowej firmy ScaleX Enterprise jako administrator.

9. Kliknij menu w górnym prawym rogu i wybierz pozycję **administracji Contoso**.

    > [!NOTE] 
    > Firma Contoso jest tylko przykładowe. Powinno to być rzeczywista nazwa firmy. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/Test_Admin.png) 

10. Wybierz **integracji** z górnego menu i wybierz **rejestracji jednokrotnej**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/admin_sso.png) 

11. Wypełnij formularz w następujący sposób:

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Wybierz **"Utwórz każdy użytkownik, który może uwierzytelniać z logowania jednokrotnego."**

    b. **Saml dostawcy usług**: wkleić wartość ***urn: oasis: nazwy: tc: SAML:2.0:nameid-format: stałe***

    c. **Nazwa dostawcy tożsamości pola wiadomości e-mail w odpowiedzi ACS**: wkleić wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identyfikator jednostki EntityDescriptor dostawcy tożsamości:** Wklej **identyfikator jednostki SAML** wartość skopiowany z portalu Azure.

    e. **Adres URL SingleSignOnService dostawcy tożsamości:** Wklej **SAML pojedynczy znak na adres URL usługi** z portalu Azure.

    f. **Certyfikat publiczny X509 dostawcy tożsamości:** Otwórz X509 certyfikat pobrany z platformy Azure w programie Notatnik i Wklej zawartość w tym polu. Upewnić się, że nie podziały wierszy w środku zawartości certyfikatu.
    
    g. Sprawdź poniższe pola wyboru: **włączone, NameID szyfrowania i AuthnRequests logowania.**

    h. Kliknij przycisk **ustawienia logowania jednokrotnego aktualizacji** Aby zapisać ustawienia.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego, kliknij przycisk **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Tworzenie użytkownika testowego ScaleX Enterprise

Aby umożliwić użytkownikom usługi Azure AD do logowania do organizacji ScaleX, ich należy udostępnić w ScaleX przedsiębiorstwem. W przypadku organizacji ScaleX Inicjowanie obsługi to zadanie automatycznej i nie są wymagane żadne czynności ręcznych. Każdy użytkownik, który może pomyślnie wykonać uwierzytelnienia przy użyciu poświadczeń logowania jednokrotnego będą automatycznie udostępniane na stronie ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu użytkownika do ScaleX przedsiębiorstwa.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta ScaleX przedsiębiorstwa, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ScaleX Enterprise**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Kliknij Kafelek ScaleX przedsiębiorstwa w panelu dostępu, można będzie uzyskać automatycznie zalogowane do aplikacji ScaleX przedsiębiorstwa. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

