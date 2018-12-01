---
title: 'Samouczek: Integracja usługi Azure Active Directory z Infor CloudSuite | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Infor CloudSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: bed29e213c3a4ed81ec8736d40f33b35d7f069a5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730180"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Samouczek: Integracja usługi Azure Active Directory z Infor CloudSuite

W tym samouczku dowiesz się, jak zintegrować Infor CloudSuite w usłudze Azure Active Directory (Azure AD).

Integrowanie Infor CloudSuite z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Infor CloudSuite.
- Użytkowników, aby automatycznie uzyskać zalogowanych do Infor CloudSuite (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Infor CloudSuite, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Infor CloudSuite logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Infor CloudSuite z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>Dodawanie Infor CloudSuite z galerii

Aby skonfigurować integrację Infor CloudSuite w usłudze Azure AD, należy dodać Infor CloudSuite z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Infor CloudSuite z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Infor CloudSuite**, wybierz opcję **Infor CloudSuite** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![infor CloudSuite na liście wyników](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji można skonfigurować, i test usługi Azure AD logowanie jednokrotne za pomocą Infor CloudSuite oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu Infor CloudSuite do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w oprogramowaniu Infor CloudSuite musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego dzięki oprogramowaniu Infor CloudSuite, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Infor CloudSuite](#creating-infor-cloudsuite-test-user)**  — aby odpowiednikiem Britta Simon w Infor CloudSuite, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Infor CloudSuite.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego, dzięki oprogramowaniu Infor CloudSuite, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Infor CloudSuite** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](common/tutorial-general-301.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](common/editconfigure.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Infor CloudSuite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url1.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL:
    
    | | |
    |-|-|
    | ` http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL:

    | | |
    |-|-|
    | ` https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Infor CloudSuite domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url2.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    
    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/ `|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`| 

    > [!NOTE]
    > Wartość adres URL logowania nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta CloudSuite Infor](mailto:support@infor.com) aby zyskać tę wartość.

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **XML metadanych Federacji** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-certificate.png)

7. Aby skonfigurować logowanie jednokrotne na **Infor CloudSuite** stronie, musisz wysłać pobrany **XML metadanych Federacji** do [zespołem pomocy technicznej Infor CloudSuite](mailto:support@infor.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create-aaduser-01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](common/create-aaduser-02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-infor-cloudsuite-test-user"></a>Tworzenie użytkownika testowego Infor CloudSuite

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w oprogramowaniu Infor CloudSuite. Infor CloudSuite obsługuje just-in-time aprowizacji, które mogą być włączone przez administratora dzierżawy. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu Infor CloudSuite, jeśli go jeszcze nie istnieje.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Infor CloudSuite](mailto:support@infor.com).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Infor CloudSuite.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Infor CloudSuite**.

    ![Konfigurowanie logowania jednokrotnego](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Infor CloudSuite w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Infor CloudSuite.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
