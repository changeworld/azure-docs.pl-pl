---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą vxMaintain | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: d4dba956ecd88683b124d4faf997a5fde6603727
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184783"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą vxMaintain

W tym samouczku dowiesz się, jak zintegrować vxMaintain w usłudze Azure Active Directory (Azure AD).

Integracja ta zapewnia kilka istotnych korzyści. Możesz:

- Kontrolowanie w usłudze Azure AD, kto ma dostęp do vxMaintain.
- Umożliwianie użytkownikom automatyczne logowanie do vxMaintain za pomocą logowania jednokrotnego (SSO) przy użyciu ich kont usługi Azure AD.
- Zarządzanie Twoimi kontami w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą vxMaintain, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- VxMaintain subskrypcji logowanie Jednokrotne włączone

> [!NOTE]
> Podczas testowania kroki opisane w tym samouczku, zaleca się, że należy używać w środowisku produkcyjnym.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

W tym samouczku opisano scenariusz składa się z dwóch głównych bloków konstrukcyjnych:

* Dodawanie vxMaintain z galerii
* Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="add-vxmaintain-from-the-gallery"></a>Dodaj vxMaintain z galerii
Aby skonfigurować integrację vxMaintain z usługą Azure AD, należy dodać vxMaintain z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać vxMaintain z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku. 

    ![Przycisk Azure Active Directory][1]

1. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku "Aplikacje dla przedsiębiorstw"][2]
    
1. Aby dodać aplikację, w **wszystkie aplikacje** okno dialogowe, wybierz opcję **nową aplikację**.

    !["Nowa aplikacja" przycisk][3]

1. W polu wyszukiwania wpisz **vxMaintain**.

    ![Listy rozwijanej "Pojedynczy znak na tryb"](./media/vxmaintain-tutorial/tutorial_vxmaintain_search.png)

1. Na liście wyników wybierz **vxMaintain**, a następnie wybierz pozycję **Dodaj**.

    ![Łącze vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji możesz skonfigurować i przetestować logowania jednokrotnego usługi Azure AD przy użyciu vxMaintain, w oparciu o użytkownika testu o nazwie "Britta Simon."

Aby logowanie Jednokrotne do pracy usługi Azure AD musi znać vxMaintain odpowiednikiem użytkownika usługi Azure AD. Oznacza to należy ustanowić relację łącza między użytkownikiem usługi Azure AD i odpowiedniego użytkownika vxMaintain.

Do ustanawiania relacji łączy, przypisz vxMaintain **nazwa_użytkownika** wartość jako usługi Azure AD **Username** wartość.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD przy użyciu vxMaintain, należy wykonać poniższe bloki konstrukcyjne.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można zarówno Włączanie logowania jednokrotnego usługi Azure AD w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji vxMaintain, wykonując następujące czynności:

1. W witrynie Azure portal na **vxMaintain** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Polecenie "Logowanie jednokrotne"][4]

1. Aby włączyć logowanie Jednokrotne, w **tryb rejestracji jednokrotnej** listy rozwijanej wybierz **opartej na SAML logowania jednokrotnego**.
 
    ![Polecenie "opartej na SAML logowania jednokrotnego"](./media/vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

1. W obszarze **vxMaintain domena i adresy URL**, wykonaj następujące czynności:

    ![VxMaintain sekcji domena i adresy URL](./media/vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. W **identyfikator** wpisz adres URL, który ma następującą składnię: `https://<company name>.verisae.com`

    b. W **adres URL odpowiedzi** wpisz adres URL, który ma następującą składnię: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Podane wyżej wartości nie są rzeczywiste. Można aktualizować rzeczywisty identyfikator i adres URL odpowiedzi. Aby uzyskać wartości, skontaktuj się z pomocą [zespołem pomocy technicznej vxMaintain](https://www.hubspot.com/company/contact).
 
1. W obszarze **certyfikat podpisywania SAML**, wybierz opcję **XML metadanych**, a następnie zapisz plik metadanych do komputera.

    ![W sekcji "Certyfikat podpisywania SAML"](./media/vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

1. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/vxmaintain-tutorial/tutorial_general_400.png)

1. Aby skonfigurować **vxMaintain** logowania jednokrotnego, Wyślij pobrany **XML metadanych** pliku do [vxMaintain zespołem pomocy technicznej](https://www.hubspot.com/company/contact).

> [!TIP]
> Po skonfigurowaniu aplikacja może odczytywać zwięzłe wersję poprzednich instrukcji w [witryny Azure portal](https://portal.azure.com). Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **logowania jednokrotnego** kartę, a następnie przejść osadzonego Dokumentacja z **konfiguracji** sekcji. 
>
>Aby dowiedzieć się więcej na temat funkcji dokumentacja embedded, zobacz [Zarządzanie logowania jednokrotnego dla aplikacji korporacyjnych](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji utworzysz użytkownika testowego Britta Simon w witrynie Azure portal, wykonując następujące czynności:

![Usługa Azure AD użytkownika testowego][100]

1. W **witryny Azure portal**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk "Azure Active Directory"](./media/vxmaintain-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** > **wszyscy użytkownicy**.
    
    ![Link "Wszyscy użytkownicy"](./media/vxmaintain-tutorial/create_aaduser_02.png)  
    **Wszyscy użytkownicy** zostanie otwarte okno dialogowe. 

1. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Przycisk Dodaj](./media/vxmaintain-tutorial/create_aaduser_03.png) 

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:
 
    ![Okno dialogowe Użytkownik](./media/vxmaintain-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika testowego Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj tę wartość, który został wygenerowany w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-vxmaintain-test-user"></a>Tworzenie użytkownika testowego vxMaintain

W tej sekcji utworzysz należeć użytkownik testowy Britta Simon vxMaintain. Aby dodać użytkowników, na platformie vxMaintain, współpracować z [zespołem pomocy technicznej vxMaintain](https://www.hubspot.com/company/contact). Przed użyciem logowania jednokrotnego, Utwórz i Aktywuj użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika testowego Britta Simon do udzielania dostępu do vxMaintain za pomocą logowania jednokrotnego usługi Azure. Aby to zrobić, wykonaj następujące czynności:

![Testowanie użytkownika na liście Nazwa wyświetlana][200] 

1. W witrynie Azure portal **aplikacje** przejdź do widoku **katalogu** Widok > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Link "Wszystkie aplikacje"][201] 

1. W **aplikacje** listy wybierz **vxMaintain**.

    ![Łącze vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

1. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Link „Użytkownicy i grupy”][202] 

1. Wybierz **Dodaj** a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Link „Użytkownicy i grupy”][203]

1. W **użytkowników i grup** okno dialogowe, **użytkowników** listy wybierz **Britta Simon**, a następnie wybierz pozycję **wybierz** przycisku.

1. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Wybieranie **vxMaintain** kafelka w panelu dostępu należy logować Cię w aplikacji vxMaintain automatycznie.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Kolejne kroki

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/vxmaintain-tutorial/tutorial_general_203.png

