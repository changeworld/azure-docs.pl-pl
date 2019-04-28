---
title: 'Samouczek: Integracja usługi Azure Active Directory z TOPdesk - Public | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i TOPdesk — publiczny.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89412040fdea32746574d8ae5bada9c017617b80
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129298"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą TOPdesk — publiczny

W tym samouczku dowiesz się, jak zintegrować TOPdesk - publicznej za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie TOPdesk - publicznej z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do TOPdesk - publicznego.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do TOPdesk — publiczny (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z TOPdesk - publiczny, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- TOPdesk — publiczny logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie TOPdesk - publicznego z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-topdesk---public-from-the-gallery"></a>Dodawanie TOPdesk - publicznego z galerii
Aby skonfigurować integrację TOPdesk - publicznego w usłudze Azure AD, należy dodać TOPdesk - publicznego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać TOPdesk - publicznego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **TOPdesk — publiczny**, wybierz opcję **TOPdesk — publiczny** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TOPdesk - publiczny, na liście wyników](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą TOPdesk — publiczny w oparciu o nazwie "Britta Simon" użytkownika testowego.

Logowanie jednokrotne do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w TOPdesk — publiczny jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w TOPdesk — publiczny musi można ustanowić.

W TOPdesk — publiczna, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą TOPdesk - publiczny, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Utwórz TOPdesk - użytkownika testowego publicznych](#create-a-topdesk---public-test-user)**  — aby odpowiednikiem Britta Simon w TOPdesk - publiczny, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w sieci TOPdesk - publicznych aplikacji.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z TOPdesk — publiczna, wykonaj następujące czynności:**

1. W witrynie Azure portal na **TOPdesk - Public** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. Na **TOPdesk — domeny publicznej i adresów URL** sekcji, wykonaj następujące czynności:

    ![TOPdesk — domeny publicznej i adresów URL pojedynczy informacje logowania jednokrotnego](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net`
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Adres URL odpowiedzi to explaned w dalszej części samouczka. Skontaktuj się z pomocą [TOPdesk - zespołem pomocy technicznej publicznych klienta](https://help.topdesk.com/saas/enterprise/user/) do uzyskania tych wartości.  

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. Na **TOPdesk — Konfiguracja publiczna** , kliknij przycisk **skonfigurować TOPdesk - Public** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![TOPdesk — Konfiguracja publiczna](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Zaloguj się na swoje **TOPdesk - Public** witryny firmy jako administrator.

1. W menu **TOPdesk** kliknij pozycję **Settings** (Ustawienia).
   
    ![Ustawienia](./media/topdesk-public-tutorial/ic790598.png "Ustawienia")

1. Kliknij pozycję **Login Settings** (Ustawienia logowania).
   
    ![Ustawienia logowania](./media/topdesk-public-tutorial/ic790599.png "Ustawienia logowania")

1. Rozwiń menu **Login Settings** (Ustawienia logowania), a następnie kliknij pozycję **General** (Ogólne).
   
    ![Ogólne](./media/topdesk-public-tutorial/ic790600.png "Ogólne")

1. W **publicznych** części **logowania języka SAML** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia techniczne](./media/topdesk-public-tutorial/ic790601.png "Ustawienia techniczne")
   
    a. Kliknij pozycję **Download** (Pobierz), aby pobrać publiczny plik metadanych, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz plik metadanych pobrany, a następnie zlokalizuj **AssertionConsumerService** węzła.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopiuj **AssertionConsumerService** wartość, wklej tę wartość w **adres URL odpowiedzi** polu tekstowym w **TOPdesk — domeny publicznej i adresów URL** sekcji.      
   
1. Aby utworzyć plik certyfikatu, wykonaj następujące kroki:
    
    ![Certyfikat](./media/topdesk-public-tutorial/ic790606.png "Certyfikat")
    
    a. Otwórz plik metadanych pobrany w witrynie Azure Portal.
    
    b. Rozwiń węzeł **RoleDescriptor**, w którym element **xsi:type** ma wartość **fed:ApplicationServiceType**.
    
    c. Skopiuj wartość węzła**X509Certificate**.
    
    d. Zapisz skopiowaną wartość **X509Certificate** lokalnie na komputerze w pliku.

1. W sekcji **Public** (Publiczne) kliknij przycisk **Add** (Dodaj).
    
    ![Logowanie SAML](./media/topdesk-public-tutorial/ic790625.png "Logowanie SAML")

1. W oknie dialogowym **SAML configuration assistant** (Asystent konfiguracji SAML) wykonaj następujące kroki:
    
    ![Asystent konfiguracji SAML](./media/topdesk-public-tutorial/ic790608.png "Asystent konfiguracji SAML")
    
    a. Aby przekazać plik metadanych pobrany w witrynie Azure Portal, w obszarze **Federation Metadata** (Metadane federacji) kliknij przycisk **Browse** (Przeglądaj).

    b. Aby przekazać plik certyfikatu, w obszarze **Certificate (RSA)** (Certyfikat — RSA) kliknij przycisk **Browse** (Przeglądaj).

    c. Aby przekazać plik logo uzyskany od zespołu pomocy technicznej TOPdesk, w obszarze **Logo icon** (Ikona logo) kliknij przycisk **Browse** (Przeglądaj).

    d. W polu tekstowym **User name attribute** (Atrybut nazwy użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W polu tekstowym **Display name** (Nazwa wyświetlana) wpisz nazwę konfiguracji.

    f. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-topdesk---public-test-user"></a>Utwórz TOPdesk - użytkownika testowego publiczne

Aby umożliwić użytkownikom usługi Azure AD zalogować się do TOPdesk - publiczny, musi być obsługiwana w TOPdesk - publicznego.  
W przypadku TOPdesk - publiczny, inicjowanie obsługi administracyjnej jest zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:
1. Zaloguj się na swoje **TOPdesk - Public** witryny firmy jako administrator.

1. W menu u góry kliknij **TOPdesk \> New \> pliki obsługi \> osoby**.
   
    ![Osoby](./media/topdesk-public-tutorial/ic790628.png "osoby")

1. W oknie dialogowym nowej osoby wykonaj następujące czynności:
   
    ![Nową osobę](./media/topdesk-public-tutorial/ic790629.png "nowej osoby")
   
    a. Kliknij kartę Ogólne.

    b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon
 
    c. Wybierz **witryny** dla konta.
 
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Możesz użyć wszelkie inne TOPdesk — narzędzia do tworzenia konta użytkownika publicznego lub interfejsów API dostarczonych przez TOPdesk — publiczny można uaktywniać ich konta usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do TOPdesk - publicznego.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon TOPdesk - publiczny, należy wykonać następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **TOPdesk - Public**.

    ![TOPdesk - publicznego linku na liście aplikacji](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu TOPdesk - Public kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do Twojej TOPdesk - publicznych aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

