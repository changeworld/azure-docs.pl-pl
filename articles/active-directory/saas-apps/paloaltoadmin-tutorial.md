---
title: 'Samouczek: Integracja usługi Azure Active Directory z Palo Alto Networks - interfejsem użytkownika administratora | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Palo Alto Networks - interfejsem użytkownika administratora.
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
ms.openlocfilehash: b39879cc8548139879a3039c5e0c6b924e83c107
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046675"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integracja usługi Azure Active Directory z Palo Alto Networks - interfejsem użytkownika administratora

W tym samouczku nauczysz się integracji Azure Active Directory (Azure AD) przy użyciu Palo Alto Networks - interfejsem użytkownika administratora.

Przez zintegrowanie usługi Azure AD z Palo Alto Networks - interfejsem użytkownika administratora, możesz uzyskać następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Palo Alto Networks - interfejsem użytkownika administratora.
- Aby umożliwić użytkownikom uzyskiwanie zalogowany automatycznie Palo Alto Networks - interfejsem użytkownika administratora (logowanie jednokrotne, lub logowania jednokrotnego) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji, witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Palo Alto Networks - interfejsem użytkownika administratora, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Palo Alto sieci następnej generacji zapory lub Panorama (system scentralizowanego zarządzania dla zapory)

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, firma Microsoft zaleca wykonanie *nie* za pomocą środowiska produkcyjnego.

Aby przetestować czynności w ramach tego samouczka, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, który jest opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie Palo Alto Networks - interfejsem użytkownika administratora z galerii
* Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Dodaj Palo Alto Networks - interfejsem użytkownika administratora z galerii
Aby skonfigurować integrację usługi Azure AD przy użyciu Palo Alto Networks - interfejsem użytkownika administratora, Dodaj Palo Alto Networks - interfejsem użytkownika administratora z galerii z listą zarządzanych aplikacji SaaS, wykonując następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W oknie "Aplikacje dla przedsiębiorstw"][2]
    
3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna.

    !["Nowa aplikacja" przycisk][3]

4. W polu wyszukiwania wpisz **Palo Alto Networks - interfejsem użytkownika administratora**, wybierz opcję **Palo Alto Networks - interfejsem użytkownika administratora** w na liście wyników, a następnie wybierz **Dodaj**.

    ![Palo Alto Networks — interfejsem użytkownika administratora na liście wyników](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz, a test usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks - interfejsem użytkownika administratora, oparte na użytkownika testu o nazwie "Britta Simon."

Aby uzyskać logowanie jednokrotne do pracy usługi Azure AD musi zidentyfikować Palo Alto Networks - interfejsem użytkownika administratora, użytkownik i jego odpowiednika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownika usługi Azure AD i tego samego użytkownika w Palo Alto Networks - interfejsem użytkownika administratora.

Aby ustanowić relację łącza, przypisz jako Palo Alto Networks - interfejsem użytkownika administratora *Username* wartość *nazwy użytkownika* w usłudze Azure AD.

Aby konfiguracja i testowanie usługi Azure AD logowanie jednokrotne za pomocą Palo Alto Networks - interfejsem użytkownika administratora, należy wykonać bloki konstrukcyjne w pięć następnych sekcjach.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w sieci Palo Alto - interfejsem użytkownika administratora aplikacji, wykonując następujące czynności:

1. W witrynie Azure portal na **Palo Alto Networks - interfejsem użytkownika administratora** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link "Logowanie jednokrotne"][4]

2. W **logowanie jednokrotne** okna w **tryb rejestracji jednokrotnej** wybierz opcję **opartej na SAML logowania jednokrotnego**.
 
    ![W oknie "Logowanie jednokrotne"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. W obszarze **Palo Alto Networks - Admin interfejsu użytkownika domena i adresy URL**, wykonaj następujące czynności:

    !["Palo Alto Networks - Admin interfejsu użytkownika domena i adresy URL" pojedynczy informacje logowania jednokrotnego](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. W **adres URL logowania** wpisz adres URL w następującym formacie: *https://\<FQDN zapory klienta > /php/login.php*.

    b. W **identyfikator** wpisz adres URL w następującym formacie: *https://\<FQDN zapory klienta >: 443/SAML20/SP*.
    
    c. W **adres URL odpowiedzi** wpisz adres URL asercji konsumenta Service (ACS) w następującym formacie: *https://\<FQDN zapory klienta >: 443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Powyższe wartości nie są prawdziwe. Zaktualizuj je za pomocą rzeczywisty adres URL logowania i identyfikator. Aby uzyskać wartości, skontaktuj się z pomocą [Palo Alto Networks - zespół obsługi klienta interfejsu użytkownika administratora](https://support.paloaltonetworks.com/support). 
 
4. Ponieważ Palo Alto Networks - interfejsem użytkownika administratora aplikacji oczekuje twierdzenia SAML w określonym formacie, należy skonfigurować oświadczenia, jak pokazano na poniższej ilustracji. Zarządzanie wartości atrybutów w **atrybutów użytkownika** części **integracji aplikacji** strony, wykonując następujące czynności:
    
    ![Na liście atrybuty tokenu języka SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Ponieważ wartości atrybutów są jedynie przykładowe, mapowanie odpowiednie wartości dla *username* i *adminrole*. Istnieje inny atrybut opcjonalny *accessdomain*, używany do ograniczania dostępu administratora do określonych systemów wirtualnego na zaporze.
   >
        
    | Nazwa atrybutu | Wartość atrybutu |
    | --- | --- |    
    | nazwa użytkownika | user.userprincipalname |
    | adminrole | customadmin |

    a. Wybierz **Dodaj atrybut**.  
    
    ![Przycisk "Dodaj atrybut"](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    **Dodawanie atrybutu** zostanie otwarte okno.

    ![W oknie "Dodaj atrybut"](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. W **nazwa** wpisz nazwę atrybutu, która jest wyświetlana dla tego wiersza.
    
    c. W **wartość** wpisz wartość atrybutu, który jest wyświetlany dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

    > [!NOTE]
    > Aby uzyskać więcej informacji na temat atrybutów zobacz następujące artykuły:
    > * [Profil roli administracyjnej dla użytkownika administratora (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Urządzenie domeny dostępu dla użytkownika administratora (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

5. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **XML metadanych**, a następnie wybierz pozycję **Zapisz**.

    ![Link pobierania pliku XML metadanych](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Przycisk Zapisz](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

6. Otwórz Palo Alto sieci zapory interfejsem użytkownika administratora z uprawnieniami administratora w nowym oknie.

7. Wybierz **urządzenia** kartę.

    ![Na karcie urządzenia](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. W okienku po lewej stronie wybierz **dostawcy tożsamości SAML**, a następnie wybierz pozycję **zaimportować** można zaimportować pliku metadanych.

    ![Przycisk Importuj plik metadanych](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. W **SAML zidentyfikować, dostawcy i importowanie profilu serwera** okna, wykonaj następujące czynności:

    ![W oknie "SAML identyfikacji dostawcy serwera importowania profilu"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. W **nazwa profilu** Podaj nazwę (na przykład **interfejsem użytkownika administratora usługi Azure AD**).
    
    b. W obszarze **metadanych dostawcy tożsamości**, wybierz opcję **Przeglądaj**, a następnie wybierz plik metadata.xml, który został wcześniej pobrany z witryny Azure portal.
    
    c. Wyczyść **Sprawdź poprawność certyfikatu dostawcy tożsamości** pole wyboru.
    
    d. Kliknij przycisk **OK**.
    
    e. Aby zatwierdzić konfiguracje zapory, należy wybrać **zatwierdzenia**.

10. W okienku po lewej stronie wybierz **dostawcy tożsamości SAML**, a następnie wybierz profil dostawcy tożsamości SAML (na przykład **interfejsem użytkownika administratora usługi Azure AD**) utworzonego w poprzednim kroku. 

    ![Profil dostawcy tożsamości SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. W **profilu serwera dostawcy tożsamości SAML** okna, wykonaj następujące czynności:

    ![W oknie "Profil serwera dostawcy tożsamości SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. W **adres URL SLO dostawcy tożsamości** Zastąp poprzednio zaimportowanego URL SLO z następującego adresu URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Kliknij przycisk **OK**.

12. Zapora sieci Palo Alto interfejsem użytkownika administratora, wybierz **urządzenia**, a następnie wybierz pozycję **ról administratora**.

13. Wybierz **Dodaj** przycisku. 

14. W **profilu roli administratora** okna w **nazwa** Podaj nazwę roli administratora (na przykład **fwadmin**).  
    Nazwa roli administratora powinna odpowiadać nazwa atrybutu SAML przypisaną rolę administratora, który został wysłany przez dostawcę tożsamości. Nazwa roli administratora i wartości zostały utworzone w kroku 4.

    ![Konfigurowanie roli administratora sieci Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. W interfejsie użytkownika administratora zapory, należy wybrać **urządzenia**, a następnie wybierz pozycję **profilu uwierzytelniania**.

16. Wybierz **Dodaj** przycisku. 

17. W **profilu uwierzytelniania** okna, wykonaj następujące czynności: 

    ![W oknie "Profil uwierzytelniania"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. W **nazwa** Podaj nazwę (na przykład **AzureSAML_Admin_AuthProfile**).
    
    b. W **typu** listy rozwijanej wybierz **SAML**. 
   
    c. W **profilu serwera tożsamości** listy rozwijanej wybierz odpowiedni profil serwera dostawcy tożsamości SAML (na przykład **interfejsem użytkownika administratora usługi Azure AD**).
   
    c. Wybierz **Włącz wylogowania jednokrotnego** pole wyboru.
    
    d. W **atrybut Role administratora** wprowadź nazwę atrybutu (na przykład **adminrole**). 
    
    e. Wybierz **zaawansowane** kartę a następnie w obszarze **listę dozwolonych**, wybierz opcję **Dodaj**. 
    
    ![Przycisk Dodaj, na karcie Zaawansowane](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Wybierz **wszystkich** pole wyboru, lub wybierz użytkowników i grup, które mogą uwierzytelniać za pomocą tego profilu.  
    Podczas uwierzytelniania użytkownika na podstawie zapory dopasowuje skojarzone nazwy użytkownika lub grupy, względem wpisy na tej liście. Jeśli nie dodasz wpisów, żadni użytkownicy mogą uwierzytelniać.

    g. Kliknij przycisk **OK**.

18. Aby umożliwić administratorom przeprowadzanie za pomocą logowania jednokrotnego SAML, korzystając z platformy Azure, wybierz **urządzenia** > **Instalatora**. W **instalacji** okienku wybierz **zarządzania** kartę a następnie w obszarze **ustawienia uwierzytelniania**, wybierz opcję **ustawienia** przycisku ("koła zębatego") . 

 ![Przycisk Ustawienia](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Wybierz profil uwierzytelnianie SAML, który został utworzony w kroku 17 (na przykład **AzureSAML_Admin_AuthProfile**).

 ![Pole profilu uwierzytelniania](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Kliknij przycisk **OK**.

21. Aby zatwierdzić konfigurację, zaznacz **zatwierdzenia**.


> [!TIP]
> Ponieważ konfigurujesz aplikacji może odczytywać zwięzłe wersję poprzednich instrukcji w [witryny Azure portal](https://portal.azure.com). Po dodaniu aplikacji w **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzone w dokumentacji **konfiguracji** sekcji u dołu. Aby uzyskać więcej informacji na temat funkcji dokumentacja embedded zobacz [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Tworzenie użytkownika testowego usługi Azure AD][100]

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**.

    ![Połącz usługi Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę bieżących użytkowników, wybierz **użytkowników i grup** > **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

3. W górnej części **wszyscy użytkownicy** wybierz **Dodaj**.

    ![Przycisk Dodaj](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Użytkownika** zostanie otwarte okno.

4. W **użytkownika** okna, wykonaj następujące czynności:

    ![W oknie użytkownika](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Utwórz Palo Alto Networks - użytkownika testowego użytkownika administratora

Palo Alto Networks - interfejsem użytkownika administratora obsługę użytkownika just-in-time. Jeśli użytkownik nie istnieje, zostanie automatycznie utworzony w systemie po pomyślnym uwierzytelnieniu. Jest wymagana żadna akcja ze strony użytkownika, aby utworzyć użytkownika.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Palo Alto Networks - interfejsem użytkownika administratora. Aby to zrobić, wykonaj następujące czynności:

![Przypisanie roli użytkownika][200] 

1. W witrynie Azure portal Otwórz **aplikacje** przejdź do widoku **katalogu** wyświetlić, a następnie wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    !["Aplikacje dla przedsiębiorstw" i "Wszystkie aplikacje" linki][201] 

2. W **aplikacje** listy wybierz **Palo Alto Networks - interfejsem użytkownika administratora**.

    ![Sieci Palo Alto - link interfejsem użytkownika administratora](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Okienko Dodawanie przypisania][203]

5. W **użytkowników i grup** okna w **użytkowników** listy wybierz **Britta Simon**.

6. Wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** wybierz **przypisać**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po wybraniu Palo Alto Networks — Kafelek użytkownika administratora w panelu dostępu, możesz powinny być zalogowany automatycznie swoje Palo Alto Networks - interfejsem użytkownika administratora aplikacji.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
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

