---
title: 'Samouczek: Integracja usługi Azure Active Directory z firmy DigiCert | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i firmy DigiCert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 646f3129-aa67-4875-9073-1d0b6a3173d9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 428878d3b1a8a369b58b045544f034eb4235c74c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047872"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>Samouczek: Integracja usługi Azure Active Directory z firmy DigiCert

W tym samouczku dowiesz się, jak zintegrować DigiCert w usłudze Azure Active Directory (Azure AD).

Integrowanie DigiCert z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do firmy DigiCert
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do firmy DigiCert (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą DigiCert, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Firmy DigiCert logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie DigiCert z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-digicert-from-the-gallery"></a>Dodawanie DigiCert z galerii
Aby skonfigurować integrację DigiCert w usłudze Azure AD, należy dodać DigiCert z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać DigiCert z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **DigiCert**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/tutorial_digicert_search.png)

5. W panelu wyników wybierz **DigiCert**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/tutorial_digicert_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą DigiCert w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w DigiCert do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w DigiCert musi można ustanowić.

W DigiCert, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z firmy DigiCert, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego DigiCert](#creating-a-digicert-test-user)**  — aby odpowiednikiem Britta Simon w DigiCert połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji firmy DigiCert.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z firmy DigiCert, wykonaj następujące czynności:**

1. W witrynie Azure portal na **DigiCert** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_digicert_samlbase.png)

3. Na **DigiCert domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_digicert_url.png)
    
    W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.digicert.com/sso`

4. Aplikacja firmy DigiCert oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład w przypadku tej konfiguracji. 

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_digicert_attributes.png)
    
5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ------------------- | -------------------- |    
    | Firmy | < companycode > |
    | digicertrole | CanAccessCertCentral |

    > [!Note]
    > Wartość **firmy** atrybut nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu kodu rzeczywistego firmy. Aby uzyskać wartość **firmy** atrybutu skontaktuj się z pomocą [zespołem pomocy technicznej firmy DigiCert](mailto:support@digicert.com).

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **OK**. 

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_digicert_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_general_400.png)

8. Aby skonfigurować logowanie jednokrotne na **DigiCert** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej firmy DigiCert](mailto:support@digicert.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/digicert-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-digicert-test-user"></a>Tworzenie użytkownika testowego DigiCert

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w firmy DigiCert. Skontaktuj się z [zespołem pomocy technicznej firmy DigiCert](mailto:support@digicert.com) Aby dodać użytkowników w firmy DigiCert.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do firmy DigiCert.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon DigiCert, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **DigiCert**.

    ![Konfigurowanie logowania jednokrotnego](./media/digicert-tutorial/tutorial_digicert_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DigiCert w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji DeigiCert.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/digicert-tutorial/tutorial_general_01.png
[2]: ./media/digicert-tutorial/tutorial_general_02.png
[3]: ./media/digicert-tutorial/tutorial_general_03.png
[4]: ./media/digicert-tutorial/tutorial_general_04.png

[100]: ./media/digicert-tutorial/tutorial_general_100.png

[200]: ./media/digicert-tutorial/tutorial_general_200.png
[201]: ./media/digicert-tutorial/tutorial_general_201.png
[202]: ./media/digicert-tutorial/tutorial_general_202.png
[203]: ./media/digicert-tutorial/tutorial_general_203.png

