---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą MyWorkDrive | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: f0e2c499619df938bd6f4b05757ba607a9edf244
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183360"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą MyWorkDrive

W tym samouczku dowiesz się, jak zintegrować MyWorkDrive w usłudze Azure Active Directory (Azure AD).

Integrowanie MyWorkDrive z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do MyWorkDrive.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do MyWorkDrive (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą MyWorkDrive, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- MyWorkDrive logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie MyWorkDrive z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-myworkdrive-from-the-gallery"></a>Dodawanie MyWorkDrive z galerii

Aby skonfigurować integrację MyWorkDrive do usługi Azure AD, należy dodać MyWorkDrive z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać MyWorkDrive z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **MyWorkDrive**, wybierz opcję **MyWorkDrive** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![MyWorkDrive na liście wyników](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą MyWorkDrive, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w MyWorkDrive do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w MyWorkDrive musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą MyWorkDrive, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego MyWorkDrive](#creating-a-myworkdrive-test-user)**  — aby odpowiednikiem Britta Simon w MyWorkDrive, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji MyWorkDrive.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z MyWorkDrive, wykonaj następujące czynności:**

1. W witrynie Azure portal na **MyWorkDrive** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![MyWorkDrive domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![MyWorkDrive domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Te wartości nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości.  Wprowadź name:e.g hosta serwera MyWorkDrive własnej firmy.
    > 
    > Adres URL odpowiedzi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Adres URL logowania:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Jeśli wiesz, jak skonfigurować własną nazwę hosta i certyfikat protokołu SSL dla tych wartości, skontaktuj się z zespołem pomocy technicznej MyWorkDrive klienta.

6. Na **certyfikat podpisywania SAML** stronie **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj **ikonę** do skopiowania **adres Url metadanych Federacji aplikacji**i zapisz go na komputerze...

    ![Link do pobierania certyfikatu](./media/myworkdrive-tutorial/tutorial_myworkdrive_certificate.png)

7. W oknie przeglądarki internetowej innej, zaloguj się do MyWorkDrive jako Administrator zabezpieczeń.

8. Na serwerze MyWorkDrive w panelu administracyjnym kliknij **ENTERPRISE** i wykonaj następujące czynności:

    ![Administrator](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Włącz **logowania jednokrotnego SAML/usług AD FS**.

    b. Wybierz **SAML - usługi Azure AD**

    c. W **adres Url metadanych Federacji aplikacji Azure** pola tekstowego, Wklej wartość **adres Url metadanych Federacji aplikacji** skopiowanej w witrynie Azure portal.

    d. Kliknij pozycję **Zapisz**

    >[!NOTE]
    >Aby uzyskać dodatkowe informacje, przejrzyj [artykuł pomocy technicznej usługi Azure AD MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

### <a name="creating-a-myworkdrive-test-user"></a>Tworzenie użytkownika testowego MyWorkDrive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w MyWorkDrive. Praca z [zespołem pomocy technicznej MyWorkDrive](mailto:support@myworkdrive.com) Aby dodać użytkowników na platformie MyWorkDrive. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do MyWorkDrive.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **MyWorkDrive**.

    ![Konfigurowanie logowania jednokrotnego](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka MyWorkDrive w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji MyWorkDrive.
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
