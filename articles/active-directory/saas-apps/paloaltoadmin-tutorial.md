---
title: 'Samouczek: Azure Active Directory integracji z sieciami Palo Alto - Admin UI | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Azure Active Directory i Palo Alto Networks - Admin interfejsu użytkownika.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: 7a296c1e358bffa70ed88ba40380266e8731afd8
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211591"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrowanie usługi Azure Active Directory z sieciami Palo Alto - interfejsu użytkownika administratora

Z tego samouczka dowiesz się sposobu integracji z sieciami Palo Alto - interfejsu użytkownika administratora usługi Azure Active Directory (Azure AD).

Dzięki całkującej usługi Azure AD z sieciami Palo Alto - Admin interfejsu użytkownika, możesz uzyskać następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do sieci Palo Alto - Admin interfejsu użytkownika.
- Można umożliwić użytkownikom uzyskać zalogowany automatycznie Palo Alto Networks - UI Admin (logowanie jednokrotne lub logowania jednokrotnego) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji, portalu Azure.

Aby dowiedzieć się więcej o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z sieciami Palo Alto - Admin interfejsu użytkownika, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zapora nowej generacji sieci Palo Alto lub Panorama (scentralizowanego zarządzania system dla zapory)

> [!NOTE]
> Podczas testowania czynności w tym samouczku, zaleca się wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, który jest opisane w tym samouczku składa się z dwóch głównych elementów:

* Dodawanie sieci Palo Alto - interfejsu użytkownika administracyjnego z galerii
* Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Dodawanie sieci Palo Alto - interfejsu użytkownika administracyjnego z galerii
Aby skonfigurować integrację usługi Azure AD z sieciami Palo Alto - Admin interfejsu użytkownika, Dodaj Palo Alto Networks — interfejsu użytkownika administracyjnego z galerii do listy zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Okno "Aplikacje przedsiębiorstwa"][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** u góry okna.

    !["Nowa aplikacja" przycisku][3]

4. W polu wyszukiwania wpisz **Palo Alto Networks - interfejsu użytkownika administratora**, wybierz pozycję **Palo Alto Networks - interfejsu użytkownika administratora** w lista wyników, a następnie wybierz **Dodaj**.

    ![Sieci Palo Alto - interfejsu użytkownika administracyjnego na liście wyników](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji należy skonfigurować i test usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto - UI administratora oparte na użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi zidentyfikować sieci Palo Alto - użytkownika interfejsu użytkownika administratora i jego odpowiednik w usłudze Azure AD. Innymi słowy można ustanowić relacji łącza między użytkownika usługi Azure AD i tego samego użytkownika w sieciach Palo Alto - Admin interfejsu użytkownika.

Do ustanawiania relacji łącza, przypisz jako sieci Palo Alto - interfejsu użytkownika administratora *Username* wartość *nazwy użytkownika* w usłudze Azure AD.

Aby konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej z sieciami Palo Alto - Admin interfejsu użytkownika, należy wykonać bloków konstrukcyjnych w sekcjach następnych pięciu.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

Włączanie usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w sieci Palo Alto - interfejsu użytkownika administratora aplikacji, wykonując następujące czynności:

1. W portalu Azure na **Palo Alto Networks - interfejsu użytkownika administratora** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Łącze "Logowanie jednokrotne"][4]

2. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **na języku SAML logowania jednokrotnego**.
 
    ![Okno "Logowanie jednokrotne"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. W obszarze **Palo Alto Networks - administratora domeny interfejsu użytkownika i adres URL**, wykonaj następujące czynności:

    !["Palo Alto sieci - administratora domeny interfejsu użytkownika i adres URL" single informacje logowania jednokrotnego](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<FQDN zapory klienta > /php/login.php*.

    b. W **identyfikator** wpisz adres URL w następującym formacie: *https://\<FQDN zapory klienta >: 443/SAML20/SP*.
    
    c. W **adres URL odpowiedzi** wpisz adres URL usługi konsumenta potwierdzenia (ACS) w następującym formacie: *https://\<FQDN zapory klienta >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Zaktualizuj je z rzeczywisty adres URL logowania i identyfikator. Aby uzyskać wartości, skontaktuj się z [Palo Alto Networks - zespołem pomocy technicznej Admin interfejsu użytkownika klienta](https://support.paloaltonetworks.com/support). 
 
4. Ponieważ sieci Palo Alto - interfejsu użytkownika administratora aplikacji oczekuje potwierdzenia języka SAML w określonym formacie, skonfiguruj oświadczenia, jak pokazano na poniższej ilustracji. Zarządzanie wartości atrybutów w **atrybuty użytkownika** sekcji **integracji aplikacji** strony, wykonując następujące czynności:
    
    ![Na liście atrybutów tokenu SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Ponieważ wartości atrybutów są tylko przykładowe, mapowanie odpowiednie wartości dla *username* i *adminrole*. Istnieje inny atrybut opcjonalny, *accessdomain*, który umożliwia ograniczanie dostępu administratora do konkretnych systemów wirtualnego na zaporze.
   >
        
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | nazwa użytkownika | user.userprincipalname |
    | adminrole | customadmin |

    a. Wybierz **Dodaj atrybut**.  
    
    ![Przycisk "Dodaj atrybut"](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    **Dodawanie atrybutu** zostanie otwarte okno.

    ![Okno "Dodaj atrybut"](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu, który jest wyświetlany dla danego wiersza.
    
    c. W **wartość** wpisz wartość atrybutu, który jest wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat atrybutów zobacz następujące artykuły:
    > * [Profil roli administracyjnej dla interfejsu użytkownika administratora (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domena dostępu do urządzenia interfejsu użytkownika administratora (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. W obszarze **certyfikat podpisywania SAML**, wybierz pozycję **XML metadanych**, a następnie wybierz **zapisać**.

    ![Łącze Pobierz XML metadanych](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Przycisk Zapisz](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Otwórz Interfejsie Palo Alto sieci zapory administratora z uprawnieniami administratora w nowym oknie.

7. Wybierz **urządzenia** kartę.

    ![Na karcie urządzenia](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. W okienku po lewej stronie wybierz **dostawca tożsamości SAML**, a następnie wybierz **zaimportować** do importowania pliku metadanych.

    ![Przycisk Importuj plik metadanych](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. W **SAML zidentyfikować, dostawcy i importowanie profilu serwera** okna, wykonaj następujące czynności:

    ![Okno "SAML zidentyfikować dostawcy serwera importowania profilu"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. W **nazwa profilu** Podaj nazwę (na przykład **interfejsu użytkownika administratora AzureAD**).
    
    b. W obszarze **metadanych dostawcy tożsamości**, wybierz pozycję **Przeglądaj**i wybierz plik metadata.xml, który został wcześniej pobrany z portalu Azure.
    
    c. Wyczyść **zweryfikować certyfikat dostawcy tożsamości** pole wyboru.
    
    d. Kliknij przycisk **OK**.
    
    e. Aby zatwierdzić konfiguracji zapory, wybierz **zatwierdzania**.

10. W okienku po lewej stronie wybierz **dostawca tożsamości SAML**, a następnie wybierz profil dostawca tożsamości SAML (na przykład **interfejsu użytkownika administratora AzureAD**) utworzonego w poprzednim kroku. 

    ![Profil dostawca tożsamości SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. W **profilu serwera dostawca tożsamości SAML** okna, wykonaj następujące czynności:

    ![Okno "Profil serwera dostawca tożsamości SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. W **adres URL SLO dostawcy tożsamości** polu, Zamień poprzednio zaimportowanego URL SLO następujący adres URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Kliknij przycisk **OK**.

12. Zapory sieci Palo Alto interfejsie użytkownika administracyjnego, wybrać **urządzenia**, a następnie wybierz **ról administratora**.

13. Wybierz **Dodaj** przycisku. 

14. W **profilu roli administratora** okna w **nazwa** Podaj nazwę roli administratora (na przykład **fwadmin**).  
    Nazwa roli administratora powinna odpowiadać nazwie atrybutu rolę administratora SAML wysłaną przez dostawcę tożsamości. Nazwa roli administratora oraz wartości zostały utworzone w kroku 4.

    ![Skonfiguruj rolę administratora sieci Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. W Interfejsie administratora zapory, wybierz **urządzenia**, a następnie wybierz **profilu uwierzytelniania**.

16. Wybierz **Dodaj** przycisku. 

17. W **profilu uwierzytelniania** okna, wykonaj następujące czynności: 

    ![Okno "Profil Authentication"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. W **nazwa** Podaj nazwę (na przykład **AzureSAML_Admin_AuthProfile**).
    
    b. W **typu** listy rozwijanej wybierz **SAML**. 
   
    c. W **profilu serwera IdP** listy rozwijanej wybierz odpowiedni profil serwera dostawca tożsamości SAML (na przykład **interfejsu użytkownika administratora AzureAD**).
   
    c. Wybierz **włączyć pojedynczego wylogowania** pole wyboru.
    
    d. W **atrybut Role administratora** wprowadź nazwę atrybutu (na przykład **adminrole**). 
    
    e. Wybierz **zaawansowane** kartę, a następnie w obszarze **liście dozwolonych**, wybierz pozycję **Dodaj**. 
    
    ![Przycisk Dodaj, na karcie Zaawansowane](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Wybierz **wszystkie** pole wyboru, lub wybierz użytkowników i grup, które mogą uwierzytelniać za pomocą tego profilu.  
    Podczas uwierzytelniania użytkownika, Zapora odpowiada skojarzona nazwa użytkownika lub grupy względem wpisy na tej liście. Jeśli nie dodasz wpisów, użytkownicy nie mogą uwierzytelniać.

    g. Kliknij przycisk **OK**.

18. Umożliwia administratorom korzystanie z logowania jednokrotnego SAML za pomocą usługi Azure, wybierz **urządzenia** > **Instalatora**. W **Instalator** okienku wybierz **zarządzania** kartę, a następnie w obszarze **ustawienia uwierzytelniania**, wybierz pozycję **ustawienia** przycisk ("narzędzi") . 

 ![Przycisk ustawień](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Wybierz profil uwierzytelnianie SAML, który został utworzony w kroku 17 (na przykład **AzureSAML_Admin_AuthProfile**).

 ![W polu profilu uwierzytelniania](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Kliknij przycisk **OK**.

21. Aby zatwierdzić konfiguracji, wybierz **zatwierdzania**.


> [!TIP]
> Jak podczas konfigurowania aplikacji, możesz przeczytać zwięzły wersji poprzednich instrukcji w [portalu Azure](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji o funkcji osadzonych dokumentacji, zobacz [usługi Azure AD osadzonych dokumentacji]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji możesz tworzyć użytkownika testowego Simona Britta w portalu Azure w następujący sposób:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W portalu Azure, w lewym okienku wybierz **usługi Azure Active Directory**.

    ![Łącze usługi Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Użytkownika** zostanie otwarte okno.

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![Okno użytkownika](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Tworzenie sieci Palo Alto - użytkownika testu interfejsu użytkownika administratora

Sieci Palo Alto - interfejsu użytkownika administratora obsługę użytkownika w czasie. Jeśli użytkownik nie istnieje, została ona utworzona automatycznie w systemie po pomyślnym uwierzytelnieniu. Nie jest wymagane, aby utworzyć użytkownika żadna akcja.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji możesz umożliwić użytkownikowi Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do sieci Palo Alto - Admin interfejsu użytkownika. Aby to zrobić, wykonaj następujące czynności:

![Przypisanie roli użytkownika][200] 

1. W portalu Azure Otwórz **aplikacji** widok, przejdź do **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje przedsiębiorstwa" i "Wszystkie aplikacje" łącza][201] 

2. W **aplikacji** listy, wybierz **Palo Alto Networks - interfejsu użytkownika administratora**.

    ![Sieci Palo Alto - link Admin interfejsu użytkownika](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** , a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![W okienku Dodaj przydziału][203]

5. W **użytkowników i grup** okna w **użytkowników** listy, wybierz **Simona Britta**.

6. Wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po wybraniu Palo Alto Networks - kafelka interfejsu użytkownika administratora w panelu dostępu należy powinny być podpisane automatycznie z sieciami Palo Alto - Admin interfejsu użytkownika aplikacji.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczki dotyczące integracji aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

