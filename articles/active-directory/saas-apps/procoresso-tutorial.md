---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Procore logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: bd84224f4c3a8a498a296ff50190713111895472
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051619"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore

W tym samouczku dowiesz się, jak zintegrować Procore logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).

Integrowanie Procore logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Procore logowania jednokrotnego
- Użytkowników, aby automatycznie uzyskać zalogowanych do Procore SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — portalu zarządzania platformy Azure

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Procore, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Procore logowania jednokrotnego logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Procore logowania jednokrotnego za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie Procore logowania jednokrotnego za pomocą galerii
Aby skonfigurować integrację Procore logowania jednokrotnego w usłudze Azure AD, należy dodać Procore logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Procore logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania systemu Azure](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Procore logowania jednokrotnego**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/tutorial_procoresso_search.png)

5. W panelu wyników wybierz **Procore logowania jednokrotnego**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Procore Usługa rejestracji Jednokrotnej w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Procore logowania jednokrotnego do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Procore logowania jednokrotnego musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Procore logowania jednokrotnego.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Procore logowania jednokrotnego](#creating-a-procore-sso-test-user)**  — aby odpowiednikiem Britta Simon w Procore logowanie Jednokrotne połączonej z usługi Azure AD reprezentacja jej.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowanie jednokrotne w portalu zarządzania platformy Azure i konfigurowanie logowania jednokrotnego w aplikacji Procore logowania jednokrotnego.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure na **Procore logowania jednokrotnego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Na **Procore domena logowania jednokrotnego i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_url.png)

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_general_400.png)

6. Na **Procore konfiguracji logowania jednokrotnego** kliknij **Konfigurowanie logowania jednokrotnego Procore** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Aby skonfigurować logowanie jednokrotne na **Procore logowania jednokrotnego** strona, zaloguj się do witryny firmy procore jako administrator.

8. Z listy przybornika w dół, kliknij pozycję **administratora** o otwarcie strony ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_tool_admin.png)

9. Wklej wartość w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. W **pojedynczy znak na adres URL wystawcy** pole, wklej identyfikator jednostki SAML skopiowane z portalu Azure.

    b. W **SAML logowania na docelowy adres URL** pole, Wklej SAML pojedynczego logowania jednokrotnego usługi adres URL skopiowany z witryny Azure portal.

    c. Teraz Otwórz **XML metadanych** pobrane powyżej w witrynie Azure portal i skopiować rozszyfrować przy jego użyciu w tagu o nazwie **X509Certificate**. Wklej skopiowany wartość do **certyfikatu logowania jednokrotnego x509** pole.

10. Kliknij pozycję **Zapisz zmiany**.

11. Po tych ustawień musi wysyłać **nazwy domeny** (np. **contoso.com**) za pomocą którego logujesz się do Procore do [zespołem pomocy technicznej Procore](https://support.procore.com/) i będą oni mogli Aktywuj federacyjnego logowania jednokrotnego dla tej domeny.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania platformy Azure o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-procore-sso-test-user"></a>Tworzenie użytkownika testowego Procore logowania jednokrotnego

Wykonaj poniższe kroki, aby utworzyć użytkownika testowego Procore po ich stronie.

1. Zaloguj się do witryny firmy procore jako administrator.  

2. Z listy przybornika w dół, kliknij pozycję **katalogu** o otwarcie strony katalogu firmy.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kliknij pozycję **dodać osobę** opcję, aby otworzyć formularz, a następnie wprowadź wykonywać następujące opcje —

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_add.png)

    a. W **imię** pola tekstowego, imię typ użytkownika, takich jak **Britta**.

    b. W **nazwisko** pola tekstowego, nazwisko typ użytkownika, takich jak **Simon**.

    c. W **adres E-mail** , adres e-mail użytkownika typu pole tekstowe, takich jak **BrittaSimon@contoso.com**.

    d. Wybierz **szablon uprawnień** jako **Zastosuj szablon uprawnień później**.

    e. Kliknij przycisk **Utwórz**.

4. Sprawdź i aktualizowanie szczegółów dla nowo dodanego kontaktu.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_check.png)

5. Kliknij pozycję **zapisywanie i wysyłanie Invitiation** (Jeśli wymagane jest za pośrednictwem wiadomości e-mail) lub **Zapisz** (zapisane bezpośrednio), aby ukończyć rejestrację użytkownika.
    
    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej Procore logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Procore logowania jednokrotnego, wykonaj następujące czynności:**

1. W portalu zarządzania platformy Azure powoduje ono otwarcie widoku aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Procore logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Jeśli chcesz przetestować pojedynczego ustawienia logowania jednokrotnego, otwórz Panel dostępu. Aby uzyskać więcej szczegółów na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). Po kliknięciu kafelka Procore logowania jednokrotnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Procore logowania jednokrotnego.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

