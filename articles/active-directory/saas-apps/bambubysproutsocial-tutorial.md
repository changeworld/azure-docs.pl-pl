---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Bambu przez Sprout społecznościowych | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Bambu przez Sprout społecznościowych.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2b9ddbc-cab7-40d6-aca1-5b171cab4199
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: aa995693f1b6b970c29c3c807667c7f730210fda
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173653"
---
# <a name="tutorial-azure-active-directory-integration-with-bambu-by-sprout-social"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Bambu przez Sprout społecznościowych

W tym samouczku dowiesz się, jak zintegrować Bambu przez Sprout społecznościowych z usługi Azure Active Directory (Azure AD).

Integrowanie Bambu przez Sprout społecznościowych z usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Bambu przez Sprout społecznościowych
- Użytkowników, aby automatycznie uzyskać zalogowanych do Bambu przez Sprout społecznościowych (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z Bambu przez Sprout społecznościowych, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Bambu przez Sprout społecznościowych logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Bambu przez Sprout społecznościowych z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bambu-by-sprout-social-from-the-gallery"></a>Dodawanie Bambu przez Sprout społecznościowych z galerii
Aby skonfigurować integrację Bambu przez Sprout społecznościowych w usłudze Azure AD, należy dodać Bambu przez Sprout społecznościowych z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Bambu przez Sprout społecznościowych z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure Portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Kliknij przycisk **nową aplikację** przycisk u góry okna dialogowego, aby dodać nową aplikację.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Bambu przez Sprout społecznościowych**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_search.png)

1. W panelu wyników wybierz **Bambu przez Sprout społecznościowych**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Bambu przez Sprout społecznościowych w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Bambu przez Sprout społecznościowych dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Bambu przez Sprout społecznościowych musi nawiązać.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Bambu przez Sprout społecznościowych.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Bambu przez Sprout społecznościowych, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie Bambu przez użytkownika testowego Sprout społecznościowych](#creating-a-bambu-by-sprout-social-test-user)**  — aby odpowiednikiem Britta Simon w Bambu przez Sprout społecznościowych połączonego z usługi Azure AD reprezentacja jej.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal, a podczas konfigurowania logowania jednokrotnego w sieci Bambu Sprout społecznościowych aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Bambu przez Sprout społecznościowych, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Bambu przez Sprout społecznościowych** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, jako **tryb** wybierz **opartej na SAML logowania jednokrotnego** na włączanie logowania jednokrotnego.
 
    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_samlbase.png)

1. Na **Bambu Sprout społecznościowych domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure. 

    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_url.png)

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_general_400.png)
    
1. Na **Bambu Sprout społecznościowych konfiguracji** , kliknij przycisk **skonfigurować Bambu przez Sprout społecznościowych** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **Bambu przez Sprout społecznościowych** stronie, musisz wysłać pobrany **XML metadanych** i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [ Bambu przez dział pomocy technicznej Sprout społecznościowych](mailto:support@getbambu.com). One będzie wybrać tę opcję, aby mogła mieć ustawione prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** sekcji, wystarczy kliknąć **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

<!--### Next steps

To ensure users can sign-in to Bambu by Sprout Social after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Bambu by Sprout Social prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Bambu by Sprout Social in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Bambu by Sprout Social users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/create_aaduser_01.png) 

1. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlania listy użytkowników.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/create_aaduser_02.png) 

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bambubysproutsocial-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **Britta Simon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** obiektu Britta Simon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-bambu-by-sprout-social-test-user"></a>Tworzenie Bambu przez Sprout społecznościowych użytkownika testowego

Aplikacja obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy zostaną automatycznie utworzeni w aplikacji.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej Bambu przez Sprout społecznościowych.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Bambu przez Sprout społecznościowych, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Bambu przez Sprout społecznościowych**.

    ![Konfigurowanie logowania jednokrotnego](./media/bambubysproutsocial-tutorial/tutorial_bambubysproutsocial_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Kliknięcie Bambu Sprout społecznościowych kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do Twojej Bambu przez aplikację Sprout społecznościowych. Aby uzyskać więcej szczegółów na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bambubysproutsocial-tutorial/tutorial_general_01.png
[2]: ./media/bambubysproutsocial-tutorial/tutorial_general_02.png
[3]: ./media/bambubysproutsocial-tutorial/tutorial_general_03.png
[4]: ./media/bambubysproutsocial-tutorial/tutorial_general_04.png

[100]: ./media/bambubysproutsocial-tutorial/tutorial_general_100.png

[200]: ./media/bambubysproutsocial-tutorial/tutorial_general_200.png
[201]: ./media/bambubysproutsocial-tutorial/tutorial_general_201.png
[202]: ./media/bambubysproutsocial-tutorial/tutorial_general_202.png
[203]: ./media/bambubysproutsocial-tutorial/tutorial_general_203.png
