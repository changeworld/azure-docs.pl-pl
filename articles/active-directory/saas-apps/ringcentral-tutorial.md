---
title: 'Samouczek: Integracja usługi Azure Active Directory z RingCentral | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 35033e52fb54177428f8869ebcc462bd9465ad4c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871629"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Samouczek: Integracja usługi Azure Active Directory z RingCentral

W tym samouczku dowiesz się, jak zintegrować RingCentral w usłudze Azure Active Directory (Azure AD).

Integrowanie RingCentral z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do RingCentral.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do RingCentral (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RingCentral, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- RingCentral logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie RingCentral z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ringcentral-from-the-gallery"></a>Dodawanie RingCentral z galerii
Aby skonfigurować integrację RingCentral w usłudze Azure AD, należy dodać RingCentral z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać RingCentral z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **RingCentral**, wybierz opcję **RingCentral** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w RingCentral do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w RingCentral musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego RingCentral](#create-a-ringcentral-test-user)**  — aby odpowiednikiem Britta Simon w RingCentral połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji RingCentral.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z RingCentral, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **RingCentral** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **zmiana jednego tryb logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    a. Kliknij przycisk **przekazywania pliku metadanych**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Kliknij pozycję **logo folderu** wybierz plik metadanych, a następnie kliknij przycisk **przekazywanie**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji pola tekstowego, jak pokazano poniżej :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. W **adres URL logowania** pole tekstowe, wpisz adres URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Możesz uzyskać **plik metadanych usługodawcy** na stronie konfiguracji logowania jednokrotnego RingCentral, które zostało wyjaśnione w dalszej części tego samouczka.

6. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. W **identyfikator** pole tekstowe, wpisz adres URL:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać certyfikatu z danymi opcjami zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. W oknie przeglądarki internetowej innej, zaloguj się do RingCentral jako Administrator zabezpieczeń.

9. Na górze, wybierz polecenie **narzędzia**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Przejdź do **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Na **logowania jednokrotnego** w obszarze **konfiguracji logowania jednokrotnego** sekcji z **kroku 1** kliknij **Edytuj** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Na **Konfigurowanie logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kliknij przycisk **Przeglądaj** można przekazać pliku metadanych, który został pobrany z witryny Azure portal.

    b. Po przekazaniu metadanych wartości pobrać, automatycznie wypełnione w **logowania jednokrotnego ogólne informacje o** sekcji.

    c. W obszarze **mapowanie atrybutu** zaznacz **mapy atrybut poczty E-mail, aby** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij pozycję **Zapisz**.

    e. Z **krok 2** kliknij **Pobierz** można pobrać **plik metadanych usługodawcy** i przekaż go w **podstawową konfigurację protokołu SAML** sekcji automatyczne polulate **identyfikator** i **adres URL odpowiedzi** wartości w witrynie Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na tej samej stronie, przejdź do **włączyć logowanie Jednokrotne** sekcji, a następnie wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Wybierz **Włącz usługę logowania jednokrotnego**.
    
    b. Wybierz **umożliwiają użytkownikom logowanie się przy użyciu poświadczeń logowania jednokrotnego lub RingCentral**.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-ringcentral-test-user"></a>Tworzenie użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Praca z [zespołem pomocy technicznej klienta RingCentral](https://success.ringcentral.com/RCContactSupp) Aby dodać użytkowników na platformie RingCentral. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do RingCentral.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka RingCentral w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji RingCentral.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

