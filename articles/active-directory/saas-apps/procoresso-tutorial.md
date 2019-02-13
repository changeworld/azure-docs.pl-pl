---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Procore logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d72f4ac39ac992d0386b3971cd4d3bc6616f0a28
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186459"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore

W tym samouczku dowiesz się, jak zintegrować Procore logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).

Integrowanie Procore logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Procore logowania jednokrotnego.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Procore SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Procore, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Procore logowanie Jednokrotne logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Procore logowania jednokrotnego za pomocą galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie Procore logowania jednokrotnego za pomocą galerii

Aby skonfigurować integrację Procore logowania jednokrotnego w usłudze Azure AD, należy dodać Procore logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Procore logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Procore logowania jednokrotnego**, wybierz opcję **Procore logowania jednokrotnego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Procore logowania jednokrotnego na liście wyników](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Procore Usługa rejestracji Jednokrotnej w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Procore logowania jednokrotnego do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Procore logowania jednokrotnego musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Procore logowania jednokrotnego](#creating-a-procore-sso-test-user)**  — aby odpowiednikiem Britta Simon w Procore logowanie Jednokrotne połączonej z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Procore logowania jednokrotnego.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Procore logowania jednokrotnego** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Procore domena logowania jednokrotnego i adresy URL pojedynczy informacje logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link do pobierania certyfikatu](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. Na **skonfigurować logowanie Jednokrotne Procore** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja procore logowania jednokrotnego](common/configuresection.png)

7. Aby skonfigurować logowanie jednokrotne na **Procore logowania jednokrotnego** strona, zaloguj się do witryny firmy procore jako administrator.

8. Z listy przybornika w dół, kliknij pozycję **administratora** o otwarcie strony ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_tool_admin.png)

9. Wklej wartość w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. W **pojedynczy znak na adres URL wystawcy** tekstu pole, Wklej wartość **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.

    b. W **SAML logowania na docelowy adres URL** pole, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. Teraz Otwórz **XML metadanych Federacji** pobrane powyżej w witrynie Azure portal i skopiować certyfikat w tagu o nazwie **X509Certificate**. Wklej skopiowany wartość do **certyfikatu logowania jednokrotnego x509** pole.

10. Kliknij przycisk **Zapisz zmiany**.

11. Po tych ustawień musi wysyłać **nazwy domeny** (np. **contoso.com**) za pomocą którego logujesz się do Procore do [zespołem pomocy technicznej Procore](https://support.procore.com/) i będą oni mogli Aktywuj federacyjnego logowania jednokrotnego dla tej domeny.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

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

### <a name="creating-a-procore-sso-test-user"></a>Tworzenie użytkownika testowego Procore logowania jednokrotnego

Wykonaj poniższe kroki, aby utworzyć użytkownika testowego Procore stronie Procore SSOc.

1. Zaloguj się do witryny firmy procore jako administrator.  

2. Z listy przybornika w dół, kliknij pozycję **katalogu** o otwarcie strony katalogu firmy.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kliknij pozycję **dodać osobę** opcję, aby otworzyć formularz, a następnie wprowadź wykonywać następujące opcje —

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_add.png)

    a. W **imię** pola tekstowego, imię typ użytkownika, takich jak **Britta**.

    b. W **nazwisko** pola tekstowego, nazwisko typ użytkownika, takich jak **Simon**.

    c. W **adres E-mail** , adres e-mail użytkownika typu pole tekstowe, takich jak **BrittaSimon@contoso.com**.

    d. Wybierz **szablon uprawnień** jako **Zastosuj szablon uprawnień później**.

    e. Kliknij pozycję **Utwórz**.

4. Sprawdź i aktualizowanie szczegółów dla nowo dodanego kontaktu.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_check.png)

5. Kliknij pozycję **zapisywanie i wysyłanie Invitiation** (Jeśli wymagane jest za pośrednictwem wiadomości e-mail) lub **Zapisz** (zapisane bezpośrednio), aby ukończyć rejestrację użytkownika.
    
    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Procore logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Procore logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Procore logowania jednokrotnego w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Procore logowania jednokrotnego.
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
