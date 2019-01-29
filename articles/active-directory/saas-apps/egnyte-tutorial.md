---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Egnyte | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2018
ms.author: jeedes
ms.openlocfilehash: e33fc71e0e43864d7d70495fc5056a8acaf4ad56
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159016"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Egnyte

W tym samouczku dowiesz się, jak zintegrować Egnyte w usłudze Azure Active Directory (Azure AD).

Integrowanie Egnyte z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Egnyte.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Egnyte (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Egnyte, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Egnyte logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Egnyte z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-egnyte-from-the-gallery"></a>Dodawanie Egnyte z galerii

Aby skonfigurować integrację Egnyte w usłudze Azure AD, należy dodać Egnyte z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Egnyte z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Egnyte**, wybierz opcję **Egnyte** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Egnyte na liście wyników](./media/egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Egnyte w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Egnyte do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Egnyte musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Egnyte, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Egnyte](#creating-an-egnyte-test-user)**  — aby odpowiednikiem Britta Simon w Egnyte połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Egnyte.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Egnyte, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Egnyte** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial_general_301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Egnyte domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/egnyte-tutorial/tutorial_egnyte_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Egnyte](https://www.egnyte.com/corp/contact_egnyte.html) aby zyskać tę wartość. 

5. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/egnyte-tutorial/tutorial_egnyte_certificate.png) 

6. Na **Konfigurowanie Egnyte** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja Egnyte](common/configuresection.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Egnyte.

8. Kliknij pozycję **Ustawienia**.
   
    ![Ustawienia](./media/egnyte-tutorial/ic787819.png "Ustawienia")

9. W menu, kliknij **ustawienia**.

    ![Ustawienia](./media/egnyte-tutorial/ic787820.png "Ustawienia")

10. Kliknij przycisk **konfiguracji** kartę, a następnie kliknij przycisk **zabezpieczeń**.

    ![Zabezpieczenia](./media/egnyte-tutorial/ic787821.png "Zabezpieczenia")

11. W **uwierzytelniania rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:

    ![Rejestracja jednokrotna uwierzytelnianie](./media/egnyte-tutorial/ic787822.png "pojedynczego logowania dla uwierzytelniania")   
    
    a. Jako **uwierzytelniania rejestracji jednokrotnej**, wybierz opcję **SAML 2.0**.
   
    b. Jako **dostawcy tożsamości**, wybierz opcję **AzureAD**.
   
    c. Wklej **adres URL logowania** skopiowane z witryny Azure portal do **adres URL logowania dostawcy tożsamości** pola tekstowego.
   
    d. Wklej **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal do **Identyfikatora jednostki dostawcy tożsamości** pola tekstowego.
      
    e. Otwórz swój certyfikat zakodowany base-64 w Notatniku pobranego z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu dostawcy tożsamości** pola tekstowego.
   
    f. Jako **domyślne mapowanie użytkownika**, wybierz opcję **adres E-mail**.
   
    g. Jako **Użyj wartości wystawcy specyficznego dla domeny**, wybierz opcję **wyłączone**.
   
    h. Kliknij pozycję **Zapisz**.

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

### <a name="creating-an-egnyte-test-user"></a>Tworzenie użytkownika testowego Egnyte

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Egnyte, musi być obsługiwana w Egnyte. W przypadku Egnyte Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **Egnyte** witryny firmy jako administrator.

2. Przejdź do **ustawienia \> użytkownikami i grupami**.

3. Kliknij przycisk **Dodaj nowego użytkownika**, a następnie wybierz typ użytkownika, które chcesz dodać.
   
    ![Użytkownicy](./media/egnyte-tutorial/ic787824.png "Użytkownicy")

4. W **nowy użytkownik zaawansowany** sekcji, wykonaj następujące czynności:
    
    ![Nowego użytkownika standardowego](./media/egnyte-tutorial/ic787825.png "nowego użytkownika standardowego")   

    a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com**.

    b. W **Username** tekstu wprowadź nazwę użytkownika, użytkownika, takich jak **Brittasimon**.

    c. Wybierz **logowanie jednokrotne** jako **typ uwierzytelniania**.
   
    d. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Można użyć jakichkolwiek innych Egnyte użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Egnyte do aprowizacji kont użytkowników usługi AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Egnyte.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Egnyte**.

    ![Konfigurowanie logowania jednokrotnego](./media/egnyte-tutorial/tutorial_egnyte_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Egnyte w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Egnyte.
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
