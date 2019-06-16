---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą ScaleX Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321387"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą ScaleX Enterprise

W tym samouczku dowiesz się, jak zintegrować ScaleX przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).

Integracja przedsiębiorstwa ScaleX z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do ScaleX przedsiębiorstwa
- Użytkowników, aby automatycznie uzyskać zalogowanych do przedsiębiorstwa ScaleX (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz. Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą [usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ScaleX Enterprise, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- ScaleX Enterprise logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ScaleX przedsiębiorstwa z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Dodawanie ScaleX przedsiębiorstwa z galerii
Aby skonfigurować integrację ScaleX przedsiębiorstwa w usłudze Azure AD, należy dodać ScaleX przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ScaleX przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **ScaleX Enterprise**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. W panelu wyników wybierz **ScaleX Enterprise**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurujesz a test usługi Azure AD logowanie jednokrotne z usługą ScaleX Enterprise oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w przedsiębiorstwie ScaleX do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie ScaleX musi zostać ustanowione.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w przedsiębiorstwie ScaleX.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą ScaleX Enterprise, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  — aby odpowiednikiem Britta Simon w przedsiębiorstwie ScaleX, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji przedsiębiorstwa ScaleX.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą ScaleX Enterprise, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ScaleX Enterprise** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. Na **ScaleX Enterprise domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. W **identyfikator** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://platform.rescale.com/saml2/<company id>/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    W **adres URL logowania** polu tekstowym wpisz wartość, przy użyciu następującego wzorca: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adres URL odpowiedzi lub adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej ScaleX Enterprise Client](https://info.rescale.com/contact_sales) do uzyskania tych wartości. 

1. Aplikacja ScaleX oczekuje twierdzenia SAML w określonym formacie, który wymaga zmodyfikowania mapowania atrybutów niestandardowych, konfiguracji atrybuty tokenu języka SAML. Kliknij przycisk **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pole wyboru umożliwiające otwarcie niestandardowe atrybuty ustawienia.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Kliknij prawym przyciskiem myszy ten atrybut **nazwa** i kliknij przycisk Usuń.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Kliknij przycisk **emailaddress** atrybutu, aby otworzyć okno Edytuj atrybut. Zmień jej wartość z **user.mail** do **user.userprincipalname** i kliknij przycisk Ok.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracja dla przedsiębiorstw ScaleX** , kliknij przycisk **skonfigurować ScaleX Enterprise** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **ScaleX Enterprise** strona, zaloguj się do witryny internetowej firmy ScaleX Enterprise jako administrator.

1. Kliknij przycisk menu w prawym górnym rogu i wybierz pozycję **administracji Contoso**.

    > [!NOTE] 
    > Firma Contoso jest przykładowe. Powinna to być rzeczywista nazwa firmy. 

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Wybierz **integracje** z menu u góry i wybierz przycisk **logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Wypełnij formularz w następujący sposób:

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Wybierz **"Utwórz każdy użytkownik, który może uwierzytelniać przy użyciu logowania jednokrotnego".**

    b. **Dostawca usług saml**: Wklej wartość ***urn: oasis: nazwy: tc: SAML:2.0:nameid — format: stałe***

    c. **Nazwa pola Adres e-mail dostawcy tożsamości w odpowiedzi usługi ACS**: Wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identyfikator jednostki EntityDescriptor dostawcy tożsamości:** Wklej **identyfikator jednostki SAML** wartość skopiowane z portalu Azure.

    e. **Adres URL SingleSignOnService dostawcy tożsamości:** Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** w witrynie Azure portal.

    f. **Certyfikat X509 publicznego dostawcy tożsamości:** Otwórz X509 certyfikat pobrany z platformy Azure w programie Notatnik i Wklej zawartość w tym polu. Upewnij się, że nie podziały wierszy w środku zawartość certyfikatu.
    
    g. Zaznacz następujące pola wyboru: **Włączona, szyfrowanie NameID i podpisz AuthnRequests.**

    h. Kliknij przycisk **aktualizacji ustawień logowania jednokrotnego** Aby zapisać ustawienia.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Tworzenie użytkownika testowego ScaleX przedsiębiorstwa

Aby umożliwić użytkownikom usługi Azure AD zalogować się do przedsiębiorstwa ScaleX, ich musi być obsługiwana w ScaleX przedsiębiorstwa. W przypadku przedsiębiorstw ScaleX inicjowania obsługi administracyjnej jest zadaniem, automatyczne i czynności ręcznej, nie są wymagane. Pomyślnie przeprowadzić uwierzytelnienie przy użyciu poświadczeń logowania jednokrotnego, użytkownik zostanie automatycznie aprowizowana stronie ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez przyznanie dostępu użytkownika do ScaleX Enterprise.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon ScaleX przedsiębiorstwa, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **ScaleX Enterprise**.

    ![Konfigurowanie logowania jednokrotnego](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Kliknij Kafelek ScaleX przedsiębiorstwa, w panelu dostępu, można będzie pobrać automatycznie zalogowanych do aplikacji przedsiębiorstwa ScaleX. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

