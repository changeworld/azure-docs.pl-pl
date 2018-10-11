---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą połączenia protokołu SAML środowiska wirtualnego ON24 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i połączenia ON24 wirtualnego środowiska języka SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071242"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą połączenia ON24 wirtualnego środowiska języka SAML

W tym samouczku dowiesz się, jak zintegrować ON24 wirtualnego środowiska języka SAML połączenia z usługą Azure Active Directory (Azure AD).

Integrowanie ON24 wirtualnego środowiska języka SAML połączenia z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do połączenia ON24 wirtualnego środowiska języka SAML.
- Użytkowników, aby automatycznie uzyskać zalogowanych do ON24 wirtualnego środowiska języka SAML połączenia (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Połączenia ON24 wirtualnego środowiska języka SAML logowania jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ON24 środowiska SAML połączeń wirtualnych z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Dodawanie ON24 środowiska SAML połączeń wirtualnych z galerii
Aby skonfigurować integrację ON24 wirtualnego środowiska języka SAML połączenia w usłudze Azure AD, należy dodać ON24 wirtualnego środowiska języka SAML połączenia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ON24 wirtualnego środowiska języka SAML połączenia z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/on24-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **ON24 wirtualnego środowiska języka SAML połączenia**, wybierz opcję **ON24 wirtualnego środowiska języka SAML połączenia** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ON24 wirtualnego środowiska języka SAML połączenia na podstawie użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika ON24 wirtualnego środowiska języka SAML połączenie do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika ON24 wirtualnego środowiska języka SAML połączenia musi nawiązać.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego połączenia SAML środowiska wirtualnego ON24](#create-an-on24-virtual-environment-saml-connection-test-user)**  — aby odpowiednikiem Britta Simon w ON24 wirtualnego SAML połączenia ze środowiskiem połączonym z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji połączenia ON24 wirtualnego środowiska języka SAML.

**Aby skonfigurować usługi Azure AD logowania jednokrotnego przy użyciu połączenia ON24 wirtualnego środowiska języka SAML, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **ON24 wirtualnego środowiska języka SAML połączenia** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Kliknij przycisk **zmiana jednego tryb logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji należy wykonać następujące czynności, jeśli chcesz skonfigurować aplikację w **tożsamości** tryb intiated:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL:

     **Adres URL środowiska produkcyjnego**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **Adres URL środowiska pytań i odpowiedzi**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL:
    
     **Adres URL środowiska produkcyjnego**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **Adres URL środowiska pytań i odpowiedzi**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Kliknij przycisk **Ustaw dodatkowe adresy URL**. 

    d. W **tan przekaźnika** pole tekstowe, wpisz adres URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Jeśli chcesz skonfigurować aplikację w **SP** tryb intiated w **adres URL logowania** pole tekstowe, wpisz adres URL: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać odpowiedni certyfikat zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Aby skonfigurować logowanie jednokrotne na **ON24 wirtualnego środowiska języka SAML połączenia** stronie, musisz wysłać certyfikatu/metadanych, który został pobrany z witryny Azure portal do [ON24 wirtualnego środowiska języka SAML połączenia zespołu pomocy technicznej](https://www.on24.com/about-us/support/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/on24-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Tworzenie użytkownika testowego połączenia ON24 wirtualnego środowiska języka SAML

W tej sekcji utworzysz użytkownika o nazwie Britta Simon ON24 wirtualnego środowiska języka SAML połączenia. Praca z [zespołem pomocy technicznej ON24 wirtualnego środowiska języka SAML połączenia](https://www.on24.com/about-us/support/) Aby dodać użytkowników na platformie ON24 wirtualnego środowiska języka SAML połączenia. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do połączenia ON24 wirtualnego środowiska języka SAML.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **ON24 wirtualnego środowiska języka SAML połączenia**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ON24 wirtualnego środowiska języka SAML połączenia w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ON24 wirtualnego środowiska języka SAML połączenia.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

