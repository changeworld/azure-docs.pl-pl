---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą EBSCO | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 984c5ceab0115d464633094d20deb35761eb80f0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178651"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą EBSCO

W tym samouczku dowiesz się, jak zintegrować EBSCO w usłudze Azure Active Directory (Azure AD).

Integrowanie EBSCO z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do EBSCO.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do EBSCO (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą EBSCO, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Logowania jednokrotnego EBSCO włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie EBSCO z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ebsco-from-the-gallery"></a>Dodawanie EBSCO z galerii
Aby skonfigurować integrację EBSCO w usłudze Azure AD, należy dodać EBSCO z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać EBSCO z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **EBSCO**, wybierz opcję **EBSCO** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![EBSCO na liście wyników](./media/ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą EBSCO w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w EBSCO do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w EBSCO musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą EBSCO, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego EBSCO](#create-an-ebsco-test-user)**  — można zautomatyzować EBSCOhost aprowizacji/Personalizacja użytkowników. EBSCO obsługuje Just-In-Time Inicjowanie obsługi użytkowników.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji EBSCO.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z EBSCO, wykonaj następujące czynności:**

1. W witrynie Azure portal na **EBSCO** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/ebsco-tutorial/tutorial_ebsco_samlbase.png)

1. Na **EBSCO domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![EBSCO domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/ebsco-tutorial/tutorial_ebsco_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `pingsso.ebscohost.com`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![EBSCO domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/ebsco-tutorial/tutorial_ebsco_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta EBSCO](mailto:sso@ebsco.com) można uzyskać wartość. 

    o **unikatowych elementów:**  

    o **Custid** = wprowadź unikatowy identyfikator klienta EBSCO 

    o **profilu** = klientów można dostosować link, aby przekierować użytkowników do konkretnego profilu (zależnie od tego, jakie mogą kupić od EBSCO). Mogą wprowadzić identyfikator określonego profilu. Główne identyfikatory są zewnętrznego źródła danych (Usługa odnajdywania EBSCO) i ehost (EBSOCOhost baz danych). Podano instrukcje dla tego samego [tutaj](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Aplikacja EBSCO oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego działania.
    
    ![Konfigurowanie logowania jednokrotnego](./media/ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > **Nazwa** atrybut jest obowiązkowy i jest mapowany za pomocą **identyfikator użytkownika** EBSCO aplikacji. To jest dodawany domyślnie, więc nie ma potrzeby dodaj ją ręcznie.
    
1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/ebsco-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/ebsco-tutorial/tutorial_attribute_05.png)
    
    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/ebsco-tutorial/tutorial_ebsco_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/ebsco-tutorial/tutorial_general_400.png)
    
1. Aby skonfigurować logowanie jednokrotne na **EBSCO** stronie, musisz wysłać pobrany **XML metadanych** do [zespołu pomocy technicznej EBSCO](mailto:sso@ebsco.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ebsco-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ebsco-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ebsco-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ebsco-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-ebsco-test-user"></a>Tworzenie użytkownika testowego EBSCO

W przypadku EBSCO Inicjowanie obsługi użytkowników odbywa się automatycznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

Usługa Azure AD przekazuje wymagane dane do aplikacji EBSCO. Inicjowanie obsługi użytkowników EBSCO firmy mogą być automatyczne lub wymagają jednorazowe formularza. To zależy od tego, czy klient ma wiele wstępnie istniejących kont EBSCOhost za pomocą ustawienia osobiste zapisane. Taki sam można podawać przy użyciu [zespołu pomocy technicznej EBSCO](mailto:sso@ebsco.com) podczas wdrożenia. W obu przypadkach klient nie ma do tworzenia kont EBSCOhost przed testowania.

   >[!Note]
   >Można zautomatyzować EBSCOhost aprowizacji/Personalizacja użytkowników. Skontaktuj się z pomocą [zespołu pomocy technicznej EBSCO](mailto:sso@ebsco.com) o Just-In-Time Inicjowanie obsługi użytkowników. 
 
### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do EBSCO.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon EBSCO, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **EBSCO**.

    ![Link EBSCO na liście aplikacji](./media/ebsco-tutorial/tutorial_ebsco_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

1. Po kliknięciu kafelka EBSCO w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji EBSCO.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

1. Po zalogowaniu się do aplikacji, kliknij pozycję **Zaloguj** przycisk w prawym górnym rogu.

    ![Rejestrowanie EBSCO na liście aplikacji](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
1. Zostanie wyświetlony monit o jednorazowy pair instytucjonalnych/SAML, zaloguj się za pomocą **teraz Połącz z istniejącym kontem MyEBSCOhost ze swoim kontem instytucji** lub **Utwórz nowe konto MyEBSCOhost i połączyć go do usługi rachunek instytucji**. Konto jest używane na potrzeby personalizacji w aplikacji EBSCOhost. Wybierz opcję **Utwórz nowe konto** i zostanie wyświetlony formularz na potrzeby personalizacji jest wstępnie wypełniony wartościami z odpowiedzi saml, jak pokazano na poniższym zrzucie ekranu. Kliknij przycisk **'Kontynuuj'** można zapisać w zaznacz to pole wyboru.
    
     ![Użytkownik EBSCO na liście aplikacji](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po zakończeniu instalacji powyżej, wyczyść pliki cookie/pamięci podręcznej i zaloguj się ponownie. Nie musisz ręcznie zarejestrować ponownie i ustawienia personalizacji zostaną zapamiętane.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ebsco-tutorial/tutorial_general_01.png
[2]: ./media/ebsco-tutorial/tutorial_general_02.png
[3]: ./media/ebsco-tutorial/tutorial_general_03.png
[4]: ./media/ebsco-tutorial/tutorial_general_04.png

[100]: ./media/ebsco-tutorial/tutorial_general_100.png

[200]: ./media/ebsco-tutorial/tutorial_general_200.png
[201]: ./media/ebsco-tutorial/tutorial_general_201.png
[202]: ./media/ebsco-tutorial/tutorial_general_202.png
[203]: ./media/ebsco-tutorial/tutorial_general_203.png

