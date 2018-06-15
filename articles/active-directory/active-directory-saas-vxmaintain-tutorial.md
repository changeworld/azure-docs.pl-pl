---
title: 'Samouczek: Integracji Azure Active Directory z vxMaintain | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c30d59a2bd903a5578caf376957a22bb270455f9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346115"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Samouczek: Integracji Azure Active Directory z vxMaintain

Z tego samouczka dowiesz się integrowanie vxMaintain z usługi Azure Active Directory (Azure AD).

Integracja ta zapewnia kilka istotnych korzyści. Możesz:

- Kontrolowanie w usłudze Azure AD, który ma dostęp do vxMaintain.
- Umożliwianie użytkownikom automatycznie logować się do vxMaintain z logowaniem jednokrotnym (SSO) za pomocą ich kont usługi Azure AD.
- Zarządzanie kont w jednej centralnej lokalizacji: portalu Azure.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z vxMaintain, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- VxMaintain logowanie Jednokrotne włączone subskrypcji

> [!NOTE]
> Podczas testowania czynności w tym samouczku, firma Microsoft zaleca, nie używaj do środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, wykonaj te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. 

Scenariusz, w tym samouczku przedstawiono składa się z dwóch głównych elementów:

* Dodawanie vxMaintain z galerii
* Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="add-vxmaintain-from-the-gallery"></a>Dodaj vxMaintain z galerii
Aby skonfigurować integrację vxMaintain z usługą Azure AD, należy dodać vxMaintain z galerii do listy zarządzanych aplikacji SaaS.

Aby dodać vxMaintain z galerii, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku. 

    ![Przycisk usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![W okienku "Aplikacje przedsiębiorstwa"][2]
    
3. Aby dodać aplikację, w **wszystkie aplikacje** okno dialogowe, wybierz opcję **nowej aplikacji**.

    !["Nowa aplikacja" przycisku][3]

4. W polu wyszukiwania wpisz **vxMaintain**.

    ![Na liście rozwijanej "Pojedynczy znak na tryb"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Na liście wyników wybierz **vxMaintain**, a następnie wybierz **Dodaj**.

    ![Łącze vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej
W tej sekcji można skonfigurować i przetestować logowania jednokrotnego programu Azure AD przy użyciu vxMaintain, na podstawie użytkownika testowego, nazywany "Britta Simona".

Aby logowanie Jednokrotne do pracy usługi Azure AD musi znać vxMaintain odpowiednikiem użytkownika usługi Azure AD. Oznacza to należy ustanowić relację łącza między użytkownikiem usługi Azure AD i odpowiedniego użytkownika vxMaintain.

Do ustanawiania relacji łącza, przypisz vxMaintain **nazwy użytkownika** wartość, jak usługi Azure AD **Username** wartość.

Aby skonfigurować i przetestować logowania jednokrotnego programu Azure AD przy użyciu vxMaintain, wykonaj poniższe bloki konstrukcyjne.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego.

W tej sekcji można zarówno włączenia funkcji logowania jednokrotnego usługi Azure AD w portalu Azure i skonfigurować logowanie Jednokrotne w aplikacji vxMaintain w następujący sposób:

1. W portalu Azure na **vxMaintain** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Polecenie "Logowanie jednokrotne"][4]

2. Aby włączyć logowanie Jednokrotne, w **tryb rejestracji jednokrotnej** listy rozwijanej wybierz **na języku SAML logowania jednokrotnego**.
 
    ![Polecenie "na podstawie SAML logowania"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. W obszarze **vxMaintain domeny i adres URL**, wykonaj następujące czynności:

    ![VxMaintain sekcji domeny i adresy URL](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. W **identyfikator** wpisz adres URL, który ma następującą składnię: `https://<company name>.verisae.com`

    b. W **adres URL odpowiedzi** wpisz adres URL, który ma następującą składnię: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Poprzednie wartości nie są prawdziwe. Zaktualizuj je z rzeczywistego identyfikatora i adres URL odpowiedzi. Aby uzyskać wartości, skontaktuj się z [zespołem pomocy technicznej vxMaintain](http://www.verisae.com/contact-us).
 
4. W obszarze **certyfikat podpisywania SAML**, wybierz pozycję **XML metadanych**, a następnie zapisz plik metadanych do komputera.

    ![W sekcji "Certyfikat podpisywania SAML"](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Wybierz pozycję **Zapisz**.

    ![Przycisk Zapisz](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Aby skonfigurować **vxMaintain** logowania jednokrotnego, Wyślij pobrany **XML metadanych** pliku na [vxMaintain zespołem pomocy technicznej](http://www.verisae.com/contact-us).

> [!TIP]
> Po skonfigurowaniu aplikacji może odczytywać zwięzły wersji poprzednich instrukcji w [portalu Azure](https://portal.azure.com). Po dodaniu aplikacji z **usługi Active Directory** > **aplikacje dla przedsiębiorstw** zaznacz **rejestracji jednokrotnej** karcie i uzyskuje dostęp do osadzonego Dokumentacja z **konfiguracji** sekcji. 
>
>Aby dowiedzieć się więcej o funkcji osadzonych dokumentacji, zobacz [Zarządzanie logowanie jednokrotne dla aplikacji przedsiębiorstwa](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji możesz tworzyć użytkownika testowego Simona Britta w portalu Azure w następujący sposób:

![Dane użytkownika usługi Azure AD][100]

1. W **portalu Azure**, w okienku po lewej stronie wybierz **usługi Azure Active Directory** przycisku.

    ![Przycisk "Azure Active Directory"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** > **wszyscy użytkownicy**.
    
    ![Łącze "Wszyscy użytkownicy"](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    **Wszyscy użytkownicy** zostanie otwarte okno dialogowe. 

3. Aby otworzyć **użytkownika** okno dialogowe, wybierz opcję **Dodaj**.
 
    ![Przycisk Dodaj](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. W **użytkownika** okno dialogowe, wykonaj następujące czynności:
 
    ![Okno dialogowe użytkownika](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika testowego Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartości, który został wygenerowany w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-vxmaintain-test-user"></a>Tworzenie użytkownika testowego vxMaintain

W tej sekcji utworzysz użytkownika testowego Simona Britta w vxMaintain. Aby dodać użytkowników do platformy vxMaintain, pracy z [zespołem pomocy technicznej vxMaintain](http://www.verisae.com/contact-us). Przed użyciem logowania jednokrotnego, Utwórz i Aktywuj użytkowników.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika testowego Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do vxMaintain. Aby to zrobić, wykonaj następujące czynności:

![Użytkownik testowy na liście Nazwa wyświetlana][200] 

1. W portalu Azure **aplikacji** widok, przejdź do **katalogu** Widok > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.

    ![Łącze "Wszystkie aplikacje"][201] 

2. W **aplikacji** listy, wybierz **vxMaintain**.

    ![Łącze vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. W okienku po lewej stronie wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Wybierz **Dodaj** , a następnie w **Dodaj przydziału** okienku wybierz **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][203]

5. W **użytkowników i grup** okna dialogowego, **użytkowników** listy, wybierz **Simona Britta**, a następnie wybierz **wybierz** przycisku.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Test z usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz przetestować konfigurację programu Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Wybieranie **vxMaintain** kafelka w panelu dostępu należy logować Cię w aplikacji vxMaintain automatycznie.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Kolejne kroki

* [Lista samouczków dotyczących integracji aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

