---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją Palo Alto Networks - Admin UI | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Palo Alto Networks - Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b0779693433f1b1fad3c5bfd4323eacb9a63b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870249"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją Palo Alto Networks - Admin UI

W tym samouczku dowiesz się, jak zintegrować aplikację Palo Alto Networks - Admin UI z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Palo Alto Networks - Admin UI z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Palo Alto Networks - Admin UI.
* Możesz umożliwić swoim użytkownikom automatyczne logowanie do aplikacji Palo Alto Networks - Admin UI (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Palo Alto Networks - Admin UI, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Palo Alto Networks - Admin UI z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Palo Alto Networks - Admin UI obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Aplikacja Palo Alto Networks - Admin UI obsługuje aprowizację użytkownika **Just in time**

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Dodawanie aplikacji Palo Alto Networks - Admin UI z galerii

Aby skonfigurować integrację aplikacji Palo Alto Networks - Admin UI w usłudze Azure AD, należy dodać pozycję Palo Alto Networks - Admin UI z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Palo Alto Networks - Admin UI z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Palo Alto Networks - Admin UI**, wybierz pozycję **Palo Alto Networks - Admin UI** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Palo Alto Networks - Admin UI na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Palo Alto Networks - Admin UI, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Palo Alto Networks - Admin UI.

W celu skonfigurowania i przetestowania logowania jednokrotnego usługi Azure AD z aplikacją Palo Alto Networks - Admin UI należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Palo Alto Networks - Admin UI](#create-palo-alto-networks---admin-ui-test-user)** — aby w aplikacji Palo Alto Networks - Admin UI istniał odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją Palo Alto Networks - Admin UI, należy wykonać następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Palo Alto Networks - Admin UI** wybierz opcję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego w aplikacji Palo Alto Networks - Admin UI](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<Customer Firewall FQDN>/php/login.php`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL usługi Assertion Consumer Service (ACS) w następującym formacie: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Palo Alto Networks - Admin UI oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Ponieważ wartości atrybutów są jedynie przykładowe, zamapuj odpowiednie wartości dla atrybutów *nazwa użytkownika* i *rola administratora*. Istnieje inny opcjonalny atrybut *domena dostępu*, który jest używany do ograniczania dostępu administratora do określonych systemów wirtualnego na zaporze.
   >

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa |  Atrybut źródłowy|
    | --- | --- |
    | username | user.userprincipalname |
    | rola administratora | customadmin |
    | | |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Aby uzyskać więcej informacji o atrybutach, zobacz następujące artykuły:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Profil roli administracyjnej dla aplikacji Admin UI (adminrole))
    > * [Device access domain for Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain) (Domena dostępu do urządzeń dla aplikacji Admin UI (accessdomain))

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Skonfiguruj aplikację Palo Alto Networks - Admin UI** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Palo Alto Networks - Admin UI

1. W innym oknie przeglądarki otwórz interfejs użytkownika administratora zapory w aplikacji Palo Alto Networks z uprawnieniami administracyjnymi.

2. Wybierz kartę **Device** (Urządzenie).

    ![Karta Device (Urządzenie)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. W okienku po lewej stronie wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML), a następnie wybierz przycisk **Import** (Importuj), aby zaimportować plik metadanych.

    ![Przycisk importowania pliku metadanych](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. W oknie **SAML Identity Provider Server Profile Import** (Importowanie profilu serwera dostawcy tożsamości SAML) wykonaj następujące czynności:

    ![Okno „SAML Identity Provider Server Profile Import” (Importowanie profilu serwera dostawcy tożsamości SAML)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. W polu **Profile Name** (Nazwa profilu) podaj nazwę (na przykład **AzureAD Admin UI**).
    
    b. W obszarze **Identity Provider Metadata** (Metadane dostawcy tożsamości) wybierz pozycję **Browse** (Przeglądaj), a następnie wybierz plik metadata.xml, który został wcześniej pobrany z witryny Azure Portal.
    
    c. Wyczyść pole wyboru **Validate Identity Provider Certificate** (Sprawdź poprawność certyfikatu dostawcy tożsamości).
    
    d. Kliknij przycisk **OK**.
    
    e. Aby zatwierdzić konfiguracje zapory, wybierz pozycję **Commit** (Zatwierdź).

5. W okienku po lewej stronie wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML), a następnie wybierz profil dostawcy tożsamości SAML (na przykład **AzureAD Admin UI**) utworzony w poprzednim kroku.

    ![Profil dostawcy tożsamości SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. W oknie **SAML Identity Provider Server Profile** (Profil serwera dostawcy tożsamości SAML) wykonaj następujące czynności:

    ![Okno „SAML Identity Provider Server Profile” (Profil serwera dostawcy tożsamości SAML)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. W polu **Identity Provider SLO URL** (Adres URL wylogowania jednokrotnego dostawcy tożsamości) zastąp poprzednio zaimportowany adres URL wylogowania jednokrotnego następującym adresem URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Kliknij przycisk **OK**.

7. W interfejsie użytkownika administratora zapory Palo Alto Networks wybierz pozycję **Device** (Urządzenie), a następnie wybierz pozycję **Admin Roles** (Role administratora).

8. Wybierz przycisk **Add** (Dodaj).

9. W oknie **Admin Role Profile** (Profil roli administratora) w polu **Name** (Nazwa) podaj nazwę roli administratora (na przykład **fwadmin**). Nazwa roli administratora powinna być zgodna z nazwą atrybutu roli administratora SAML, która została wysłana przez dostawcę tożsamości. Wartość i nazwa roli administratora zostały utworzone w sekcji **Atrybuty użytkownika** w witrynie Azure Portal.

    ![Konfigurowanie roli administratora oprogramowania Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. W interfejsie użytkownika administratora zapory wybierz pozycję **Device** (Urządzenie), a następnie wybierz pozycję **Authentication Profile** (Profil uwierzytelniania).

11. Wybierz przycisk **Add** (Dodaj).

12. W oknie **Authentication Profile** (Profil uwierzytelniania) wykonaj następujące czynności: 

    ![Okno „Authentication Profile” (Profil uwierzytelniania)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. W polu **Name** (Nazwa) podaj nazwę (na przykład **AzureSAML_Admin_AuthProfile**).

    b. Z listy rozwijanej **Type** (Typ) wybierz pozycję **SAML**. 

    c. Na liście rozwijanej **IdP Server Profile** (Profil serwera dostawcy tożsamości) wybierz odpowiedni profil serwera dostawcy tożsamości SAML (na przykład **AzureAD Admin UI**).

    c. Zaznacz pole wyboru **Enable Single Logout** (Włącz wylogowanie jednokrotne).

    d. W polu **Admin Role Attribute** (Atrybut roli administratora) wprowadź nazwę atrybutu (na przykład **adminrole**).

    e. Wybierz kartę **Advanced** (Zaawansowane), a następnie w obszarze **Allow List** (Lista dozwolonych) wybierz pozycję **Add** (Dodaj).

    ![Przycisk Add (Dodaj) na karcie Advanced (Zaawansowane)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Zaznacz pole wyboru **All** (Wszystko) lub wybierz użytkowników i grupy, które mogą uwierzytelniać się za pomocą tego profilu.  
    Podczas uwierzytelniania użytkownika zapora dopasowuje skojarzoną nazwę użytkownika lub grupę do wpisów na tej liście. Jeśli nie dodasz wpisów, żaden użytkownik nie będzie mógł się uwierzytelnić.

    g. Kliknij przycisk **OK**.

13. Aby umożliwić administratorom używanie logowania jednokrotnego SAML za pomocą platformy Azure, wybierz pozycję **Device (Urządzenie)** > **Setup (Konfiguracja)**. W okienku **Setup** (Konfiguracja) wybierz kartę **Management** (Zarządzanie), a następnie w obszarze **Authentication Settings** (Ustawienia uwierzytelniania) wybierz przycisk **Settings** (Ustawienia), czyli koło zębate.

    ![Przycisk Settings (Ustawienia)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Wybierz profil uwierzytelnianie SAML, który został utworzony w oknie profilu uwierzytelniania (na przykład **AzureSAML_Admin_AuthProfile**).

    ![Pole Authentication Profile (Profil uwierzytelniania)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Kliknij przycisk **OK**.

16. Aby zatwierdzić konfigurację, wybierz pozycję **Commit** (Zatwierdź).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Palo Alto Networks - Admin UI.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Palo Alto Networks - Admin UI**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Palo Alto Networks - Admin UI**.

    ![Link do aplikacji Palo Alto Networks - Admin UI na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks - Admin UI

Aplikacja Palo Alto Networks - Admin UI obsługuje aprowizację użytkownika „just-in-time”. Jeśli użytkownik jeszcze nie istnieje, zostanie automatycznie utworzony w systemie po pomyślnym uwierzytelnieniu. Nie musisz nic robić, aby utworzyć użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Palo Alto Networks - Admin UI w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Palo Alto Networks - Admin UI, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
