---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą BorrowBox | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BorrowBox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a36b87f0a440bb2bf8d8d75cf0d29dbd521ff43f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175000"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą BorrowBox

W tym samouczku dowiesz się, jak zintegrować BorrowBox w usłudze Azure Active Directory (Azure AD).

Integrowanie BorrowBox z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do BorrowBox.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do BorrowBox (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BorrowBox, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- BorrowBox logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BorrowBox z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-borrowbox-from-the-gallery"></a>Dodawanie BorrowBox z galerii
Aby skonfigurować integrację BorrowBox w usłudze Azure AD, należy dodać BorrowBox z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać BorrowBox z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./common/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./common/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![image](./common/a_new_app.png)

4. W polu wyszukiwania wpisz **BorrowBox**, wybierz opcję **BorrowBox** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą BorrowBox w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w BorrowBox do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w BorrowBox musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą BorrowBox, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego BorrowBox](#create-a-borrowbox-test-user)**  — aby odpowiednikiem Britta Simon w BorrowBox połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BorrowBox.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z BorrowBox, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **BorrowBox** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./common/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./common/b1_b2_saml_sso.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![image](./common/b1-domains_and_urlsedit.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    b. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta BorrowBox](mailto:borrowbox@bolinda.com) można uzyskać wartość.

5. Aplikacja BorrowBox oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty i oświadczenia użytkownika** na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

    ![image](./media/borrowbox-tutorial/i4-attribute.png)

6. W **oświadczenia użytkownika** sekcji na **atrybutów użytkowników i oświadczeń** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    a. Kliknij pozycję **ikonę edycji** otworzyć **Zarządzanie oświadczenia użytkownika** okna dialogowego.

    ![image](./media/borrowbox-tutorial/i2-attribute.png)

    ![image](./media/borrowbox-tutorial/i3-attribute.png)

    b. Z listy **Atrybut źródłowy** wybierz pozycję **user.mail**.

    c. Kliknij pozycję **Zapisz**. 

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać odpowiedni certyfikat zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

8. Aby skonfigurować logowanie jednokrotne na **BorrowBox** stronie, musisz wysłać certyfikatu/metadanych, który został pobrany z witryny Azure portal do [zespołem pomocy technicznej BorrowBox](mailto:borrowbox@bolinda.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![image](./common/d_users_and_groups.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![image](./common/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![image](./common/d_userproperties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-borrowbox-test-user"></a>Tworzenie użytkownika testowego BorrowBox

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w BorrowBox. BorrowBox obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu BorrowBox, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej BorrowBox](mailto:borrowbox@bolinda.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do BorrowBox.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./common/d_all_applications.png)

2. Na liście aplikacji wybierz **BorrowBox**.

    ![image](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![image](./common/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./common/d_assign_user.png)

4. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

5. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BorrowBox w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji BorrowBox.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
