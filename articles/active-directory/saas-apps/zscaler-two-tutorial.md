---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą rozwiązania Zscaler dwóch | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i dwa rozwiązania Zscaler
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.openlocfilehash: 3e954d8535b5ac78da2066af5c97afac12c09319
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789419"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą dwóch rozwiązania Zscaler

W tym samouczku dowiesz się, jak zintegrować rozwiązania Zscaler dwa z usługą Azure Active Directory (Azure AD).

Integracja rozwiązania Zscaler dwa z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler dwa.
- Użytkowników, aby automatycznie uzyskać zalogowanych do rozwiązania Zscaler dwóch (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą rozwiązania Zscaler dwóch, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Dwa rozwiązania Zscaler logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie rozwiązania Zscaler dwa z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-zscaler-two-from-the-gallery"></a>Dodawanie rozwiązania Zscaler dwa z galerii

Aby skonfigurować integrację rozwiązania Zscaler dwa w usłudze Azure AD, należy dodać dwa rozwiązania Zscaler z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać dwa rozwiązania Zscaler z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **dwa rozwiązania Zscaler**, wybierz opcję **dwa rozwiązania Zscaler** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Na liście wyników dwa rozwiązania Zscaler](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler dwóch oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w drugiej rozwiązania Zscaler do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w dwa rozwiązania Zscaler musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler dwóch, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego dwa rozwiązania Zscaler](#creating-a-zscaler-two-test-user)**  — aby odpowiednikiem Britta Simon w rozwiązania Zscaler dwóch połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji dwa rozwiązania Zscaler.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą rozwiązania Zscaler dwóch, wykonaj następujące czynności:**

1. W witrynie Azure portal na **dwa rozwiązania Zscaler** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Rozwiązania Zscaler dwóch domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    W polu tekstowym adres URL logowania wpisz adres URL używany przez użytkowników do logowania się na dwa rozwiązania ZScaler aplikacji.

    > [!NOTE] 
    > Musisz zaktualizować tę wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta dwa rozwiązania Zscaler](https://www.zscaler.com/company/contact) do uzyskania tych wartości.

5. Dwa rozwiązania Zscaler aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty i oświadczenia użytkownika**.

    ![Link do atrybutu](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa  | Atrybut źródłowy  |
    | ---------| ------------ |
    | MemberOf     | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Z listy **Atrybut źródłowy** wybierz wartość atrybutu.

    c. Kliknij przycisk **OK**.

    d. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management), aby dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD

7. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. Na **skonfigurować dwa rozwiązania Zscaler** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja dwóch rozwiązania Zscaler](common/configuresection.png)

9. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do rozwiązania Zscaler dwie witryny firmy.

10. Przejdź do obszaru **Administracja > Uwierzytelnianie > Ustawienia uwierzytelniania** i wykonaj następujące kroki:
   
    ![Administracja](./media/zscaler-two-tutorial/ic800206.png "Administracja")

    a. W obszarze Typ uwierzytelniania wybierz pozycję **SAML**.

    b. Kliknij pozycję **Skonfiguruj język SAML**.

11. Na **Edytuj SAML** okna, wykonaj następujące kroki: i kliknij przycisk Zapisz.  
            
    ![Zarządzanie użytkownikami i uwierzytelnianiem](./media/zscaler-two-tutorial/ic800208.png "Zarządzanie użytkownikami i uwierzytelnianiem")
    
    a. W polu tekstowym **Adres URL portalu języka SAML** wklej **adres URL logowania** skopiowany z witryny Azure Portal.

    b. W polu tekstowym **Atrybut nazwy logowania** wprowadź identyfikator **NameID**.

    c. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    d. Przełącz element **Włącz automatyczne aprowizowanie języka SAML**.

    e. W polu tekstowym **Atrybut nazwy wyświetlanej użytkownika** wprowadź ciąg **displayName**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu displayName.

    f. W polu tekstowym **Atrybut nazwy grupy** wprowadź ciąg **memberOf**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu memberOf.

    g. W polu **Atrybut nazwy działu** wprowadź ciąg **department**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu department.

    i. Kliknij pozycję **Zapisz**.

12. Na stronie okna dialogowanie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące kroki:

    ![Administracja](./media/zscaler-two-tutorial/ic800207.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

## <a name="configuring-proxy-settings"></a>Konfigurowanie ustawień serwera proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Aby skonfigurować ustawienia serwera proxy w programie Internet Explorer

1. Rozpocznij **programu Internet Explorer**.

1. Wybierz **Opcje internetowe** z **narzędzia** menu Otwórz **Opcje internetowe** okna dialogowego.   
    
     ![Opcje internetowe](./media/zscaler-two-tutorial/ic769492.png "Opcje internetowe")

1. Kliknij przycisk **połączeń** kartę.   
  
     ![Połączenia](./media/zscaler-two-tutorial/ic769493.png "połączeń")

1. Kliknij przycisk **ustawienia sieci LAN** otworzyć **ustawienia sieci LAN** okna dialogowego.

1. W sekcji serwer Proxy wykonaj następujące czynności:   
   
    ![Serwer proxy](./media/zscaler-two-tutorial/ic769494.png "serwera Proxy")

    a. Wybierz **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym adresu wpisz **bramy. Rozwiązania Zscaler Two.net**.

    c. W polu tekstowym portu, wpisz **80**.

    d. Wybierz **używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK** zamknąć **ustawienia sieci lokalnej (LAN)** okna dialogowego.

1. Kliknij przycisk **OK** zamknąć **Opcje internetowe** okna dialogowego.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create_aaduser_02.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-zscaler-two-test-user"></a>Tworzenie użytkownika testowego dwa rozwiązania Zscaler

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w dwa rozwiązania Zscaler. Dwa rozwiązania Zscaler obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu dwa rozwiązania Zscaler, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [dwa rozwiązania Zscaler zespołu pomocy technicznej](https://www.zscaler.com/company/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do rozwiązania Zscaler dwa.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **dwa rozwiązania Zscaler**.

    ![Konfigurowanie logowania jednokrotnego](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisk, a następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okno dialogowe, wybierz użytkownika, takie jak **Britta Simon** z listy, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. Z **wybierz rolę** okno dialogowe Wybieranie odpowiedniej roli użytkownika na liście, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka rozwiązania Zscaler dwa w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do rozwiązania Zscaler dwóch aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
