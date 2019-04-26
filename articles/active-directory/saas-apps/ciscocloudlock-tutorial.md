---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu sieci szkieletowej zabezpieczeń chmury | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i sieci szkieletowej zabezpieczeń chmury.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a556b38ca4947b71555ba7b023607b392900bdaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60429519"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu sieci szkieletowej zabezpieczeń chmury

W tym samouczku dowiesz się, jak zintegrować sieci szkieletowej zabezpieczeń chmury z usługi Azure Active Directory (Azure AD).

Integrowanie sieci szkieletowej zabezpieczeń chmury z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do sieci szkieletowej zabezpieczeń chmury.
- Użytkowników, aby automatycznie uzyskać zalogowanych do sieci szkieletowej zabezpieczeń chmury (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu sieci szkieletowej zabezpieczeń chmury, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Sieci szkieletowej zabezpieczeń chmury logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie zabezpieczeń sieci szkieletowej chmury z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Dodawanie zabezpieczeń sieci szkieletowej chmury z galerii
Aby skonfigurować integrację z sieci szkieletowej zabezpieczeń chmury w usłudze Azure AD, należy dodać sieci szkieletowej zabezpieczeń chmury z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać sieci szkieletowej zabezpieczeń chmury z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **sieci szkieletowej zabezpieczeń chmury**, wybierz opcję **sieci szkieletowej zabezpieczeń chmury** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Sieci szkieletowej zabezpieczeń chmury na liście wyników](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą zabezpieczeń sieci szkieletowej chmury oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w sieci szkieletowej zabezpieczeń chmury dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w sieci szkieletowej zabezpieczeń chmury musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z sieci szkieletowej zabezpieczeń chmury, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego sieci szkieletowej zabezpieczeń chmury](#create-a-the-cloud-security-fabric-test-user)**  — aby odpowiednikiem Britta Simon w chmurze zabezpieczeń sieci szkieletowej jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Fabric zabezpieczeń chmury.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z sieci szkieletowej zabezpieczeń chmury, wykonaj następujące czynności:**

1. W witrynie Azure portal na **sieci szkieletowej zabezpieczeń chmury** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

1. Na **Cloud Security Service Fabric domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Cloud Security Service Fabric domena i adresy URL pojedynczego logowania jednokrotnego informacje](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj wartość za pomocą rzeczywisty identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta sieci szkieletowej zabezpieczeń chmury](mailto:support@cloudlock.com) można uzyskać wartość. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **sieci szkieletowej zabezpieczeń chmury** stronie, musisz wysłać pobrany **XML metadanych** do [zespołem pomocy technicznej w sieci szkieletowej zabezpieczeń chmury](mailto:support@cloudlock.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Tworzenie użytkownika testowego sieci szkieletowej zabezpieczeń chmury

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w sieci szkieletowej zabezpieczeń chmury. Praca z [zespołem pomocy technicznej w sieci szkieletowej zabezpieczeń chmury](mailto:support@cloudlock.com) Aby dodać użytkowników w sieci szkieletowej zabezpieczeń chmury platformy. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do sieci szkieletowej zabezpieczeń chmury.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon do sieci szkieletowej zabezpieczeń chmury, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **sieci szkieletowej zabezpieczeń chmury**.

    ![Link sieci szkieletowej zabezpieczeń chmury na liście aplikacji](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka sieci szkieletowej zabezpieczeń chmury na panelu dostępu, możesz powinien pobrać automatycznie zalogowanych do sieci szkieletowej zabezpieczeń chmury aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
