---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą programu Marketo | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i programu Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abab9f6e38fcf69dcb04bfea0f84d883dc5267b7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199667"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą programu Marketo

W tym samouczku dowiesz się, jak zintegrować usługi Marketo za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie usługi Marketo za pomocą usługi Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do programu Marketo
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Marketo (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą programu Marketo, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Marketo logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usługi Marketo za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-marketo-from-the-gallery"></a>Dodawanie usługi Marketo za pomocą galerii
Aby skonfigurować integrację usługi Marketo w usłudze Azure AD, należy dodać Marketo za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługi Marketo za pomocą galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Marketo**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/tutorial_marketo_search.png)

1. W panelu wyników wybierz **Marketo**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Marketo, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Marketo dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Marketo musi można ustanowić.

W programu Marketo, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą programu Marketo, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Marketo](#creating-a-marketo-test-user)**  — aby odpowiednikiem Britta Simon w Marketo, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji programu Marketo.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą programu Marketo, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Marketo** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

1. Na **programu Marketo, domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://saml.marketo.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej usługi Marketo](http://investors.marketo.com/contactus.cfm) do uzyskania tych wartości.
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_general_400.png)

1. Na **konfiguracji usługi Marketo** , kliknij przycisk **skonfigurować Marketo** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_configure.png) 

1. Aby uzyskać identyfikator Munchkin aplikacji, zaloguj się przy użyciu poświadczeń administratora usługi Marketo i wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji, a następnie kliknij przycisk **łącze Munchkin**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Skopiuj identyfikator Munchkin wyświetlone na ekranie, a następnie wykonaj adres URL odpowiedzi, w Kreatorze konfiguracji usługi Azure AD.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_12.png) 

1. Aby skonfigurować logowanie Jednokrotne w aplikacji, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do menu integracji, a następnie kliknij przycisk **Single Sign On**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Aby włączyć ustawienia protokołu SAML, kliknij przycisk **Edytuj** przycisku.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Włączone** ustawień logowania jednokrotnego.
   
    f. Wklej **identyfikator jednostki SAML**w **identyfikator wystawcy** pola tekstowego.
   
    g. W **identyfikator jednostki** polu tekstowym wprowadź adres URL w postaci `http://saml.marketo.com/sp`.
   
    h. Wybierz lokalizację identyfikator użytkownika jako **identyfikator nazwy elementu**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Jeśli Twojego identyfikatora użytkownika nie ma wartości nazwy UPN, a następnie zmień wartość na karcie atrybutu.
   
    i. Przekaż certyfikat, który został pobrany z Kreatora konfiguracji usługi Azure AD. **Zapisz** ustawienia.
   
    j. Edytowanie ustawień stron przekierowania.
   
    k. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** w **adres URL logowania** pola tekstowego.
   
    l. Wklej **adres URL wylogowania** w **adres URL wylogowania** pola tekstowego.
   
    m. W **błędny adres URL**, kopiowania usługi **adres URL wystąpienia usługi Marketo** i kliknij przycisk **Zapisz** przycisk, aby zapisać ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_10.png)

1. Aby włączyć logowanie Jednokrotne dla użytkowników, wykonaj następujące czynności:
   
    a. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.
   
    b. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Przejdź do **zabezpieczeń** menu i kliknij przycisk **ustawienia logowania**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Sprawdź **wymagają logowania jednokrotnego** opcji i **Zapisz** ustawienia.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/marketo-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-marketo-test-user"></a>Tworzenie użytkownika testowego programu Marketo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Marketo. wykonaj następujące kroki, aby utworzyć użytkownika na platformie programu Marketo.

1. Zaloguj się do aplikacji programu Marketo przy użyciu poświadczeń administratora.

1. Kliknij przycisk **administratora** przycisku w górnym okienku nawigacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_06.png) 

1. Przejdź do **zabezpieczeń** menu i kliknij przycisk **użytkowników i ról**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_19.png)  

1. Kliknij przycisk **Zaproś nowego użytkownika** link na karcie Użytkownicy
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_15.png) 

1. W Kreatorze Zaproś nowego użytkownika, wprowadź następujące informacje
   
    a. Wprowadź nazwę użytkownika **E-mail** adres w polu tekstowym
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Wprowadź **imię** w polu tekstowym
   
    c. Wprowadź **nazwisko** w polu tekstowym
   
    d. Kliknij przycisk **Dalej**

1. W **uprawnienia** zaznacz **userRoles** i kliknij przycisk **dalej**
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_17.png)
1. Kliknij przycisk **wysyłania** przycisk, aby wysłać zaproszenia do użytkownika
   
    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_18.png)

1. Użytkownik otrzymuje powiadomienie e-mail i kliknij łącze i zmienić hasło, aby aktywować konto. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Marketo.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon programu Marketo, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Marketo**.

    ![Konfigurowanie logowania jednokrotnego](./media/marketo-tutorial/tutorial_marketo_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Marketo w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji programu Marketo.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

