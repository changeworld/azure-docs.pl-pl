---
title: 'Samouczek: Integracja usługi Azure Active Directory z NetSuite | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 5a20af1130d50209b29ad44195c14f30cba30c43
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051857"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Samouczek: Integracja usługi Azure Active Directory z NetSuite

W tym samouczku dowiesz się, jak zintegrować NetSuite w usłudze Azure Active Directory (Azure AD).

Integrowanie NetSuite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do NetSuite
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do NetSuite (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą NetSuite, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- NetSuite logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie NetSuite z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-netsuite-from-the-gallery"></a>Dodawanie NetSuite z galerii
Aby skonfigurować integrację NetSuite w usłudze Azure AD, należy dodać NetSuite z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać NetSuite z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Kliknij przycisk **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **NetSuite**, wybierz opcję **NetSuite** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![NetSuite na liście wyników](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą NetSuite w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w NetSuite to dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w NetSuite musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w NetSuite.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą NetSuite, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego NetSuite](#creating-a-netsuite-test-user)**  — aby odpowiednikiem Britta Simon w NetSuite połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji NetSuite.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z NetSuite, wykonaj następujące czynności:**

1. W witrynie Azure portal na **NetSuite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

3. Na **NetSuite domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Nie są to rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej NetSuite](http://www.NetSuite.com/portal/services/support.shtml) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_general_400.png)

6. Na **konfiguracji NetSuite** , kliknij przycisk **skonfigurować NetSuite** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

7. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy NetSuite jako administrator.

8. Na pasku narzędzi w górnej części strony kliknij **instalacji**, a następnie przejdź do **firmy** i kliknij przycisk **Włączanie funkcji**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setupsaml.png)

9. Na pasku narzędzi w środkowej części strony kliknij **SuiteCloud**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-suitecloud.png)

10. W obszarze **Zarządzanie uwierzytelniania** zaznacz **SAML logowanie Jednokrotne** Aby włączyć opcję SAML logowanie Jednokrotne w NetSuite.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-ticksaml.png)

11. Na pasku narzędzi w górnej części strony kliknij **Instalatora**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

12. Z **zadań dotyczących jej konfiguracji** kliknij **integracji**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-integration.png)

13. W **Zarządzanie uwierzytelniania** kliknij **SAML logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml.png)

14. Na **konfiguracji SAML** w obszarze **konfiguracji NetSuite** sekcji należy wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Wybierz **podstawową METODĘ uwierzytelniania**.

    b. Pola oznaczone **METADANYCH dostawcy tożsamości SAMLV2**, wybierz opcję **PRZEKAŻ plik METADANYCH dostawcy tożsamości**. Następnie kliknij przycisk **Przeglądaj** można przekazać plik metadanych, który został pobrany z witryny Azure portal.

    c. Kliknij przycisk **przesłać**.

15. W usłudze Azure AD, kliknij pozycję **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pole wyboru i dodać atrybut.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-attributes.png)

16. Aby uzyskać **nazwa atrybutu** wpisz w `account`. Aby uzyskać **wartość atrybutu** wpisz w Twojego identyfikatora konta NetSuite. Ta wartość jest stała i zmian przy użyciu konta. Poniżej znajdują się instrukcje na temat znajdowania Twój identyfikator konta:

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. NetSuite, kliknij **instalacji** a następnie przejdź do **firmy** i kliknij przycisk **informacje o firmie** w górnym menu nawigacji.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-account-id.png)

    b. W **informacje o firmie** strony na kopii kolumna po prawej stronie **Accountid**.

    c. Wklej **Accountid** który skopiowano z konta NetSuite go do **wartość atrybutu** pola w usłudze Azure AD. 

17. Zanim użytkownicy mogą wykonywać logowanie jednokrotne do NetSuite, ich należy przypisać odpowiednie uprawnienia w NetSuite. Postępuj zgodnie z poniższymi instrukcjami, aby przypisać te uprawnienia.

    a. W menu górnym menu nawigacyjnym kliknij **Instalatora**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    b. W menu nawigacji po lewej stronie wybierz **użytkowników/role**, następnie kliknij przycisk **Zarządzanie rolami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Kliknij przycisk **nową rolę**.

    d. Wpisz **nazwa** dla nowej roli.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kliknij pozycję **Zapisz**.

    f. W menu u góry kliknij **uprawnienia**. Następnie kliknij przycisk **Instalatora**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-sso.png)

    g. Wybierz **SAML logowania jednokrotnego**, a następnie kliknij przycisk **Dodaj**.

    h. Kliknij pozycję **Zapisz**.

    i. W menu górnym menu nawigacyjnym kliknij **instalacji**, następnie kliknij przycisk **Menedżera instalacji**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-setup.png)

    j. W menu nawigacji po lewej stronie wybierz **użytkowników/role**, następnie kliknij przycisk **Zarządzanie użytkownikami**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wybierz użytkownika testowego. Następnie kliknij przycisk **Edytuj** , a następnie przejdź do **dostępu** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-edit-user.png)

    l. W oknie dialogowym role przypisać odpowiednie role, które zostały utworzone.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kliknij pozycję **Zapisz**.
 
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/NetSuite-tutorial/create_aaduser_01.png) 

2.  Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/NetSuite-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/NetSuite-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 

### <a name="creating-a-netsuite-test-user"></a>Tworzenie użytkownika testowego NetSuite

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w NetSuite. NetSuite obsługę just-in-time, który jest domyślnie włączona.
Brak elementu akcji dla Ciebie w tej sekcji. Jeśli użytkownik jeszcze nie istnieje w NetSuite, nowy katalog jest tworzony podczas próby uzyskania dostępu NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do NetSuite.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon NetSuite, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **NetSuite**.

    ![Konfigurowanie logowania jednokrotnego](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Aby przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu w [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), zaloguj się do konta testowego, a kliknij **NetSuite**.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

