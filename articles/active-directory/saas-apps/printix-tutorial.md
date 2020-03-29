---
title: 'Samouczek: Integracja usługi Azure Active Directory z printix | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e883833f7998c073b574c892ed5c7777e01faab4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62111459"
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Samouczek: Integracja usługi Azure Active Directory z printixem

W tym samouczku dowiesz się, jak zintegrować Printix z usługą Azure Active Directory (Azure AD).

Integracja printix z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, który ma dostęp do Printix
- Możesz włączyć użytkownikom automatyczne logowanie do Printix (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD
- Możesz zarządzać kontami w jednej centralnej lokalizacji - witrynie Azure portal

Jeśli chcesz dowiedzieć się więcej szczegółów na temat integracji aplikacji SaaS z usługą Azure AD, zobacz, [co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z printix, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Subskrypcja z obsługą logowania jednokrotnego Printix

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego do testowania czynności opisanych w tym samouczku.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska testowego usługi Azure AD, możesz uzyskać miesięczną wersję próbną [tutaj.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku testujesz logowanie jednokrotne usługi Azure AD w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Printix z galerii
1. Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

## <a name="adding-printix-from-the-gallery"></a>Dodawanie Printix z galerii
Aby skonfigurować integrację Printix z usługą Azure AD, należy dodać Printix z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Printix z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacji Enterprise**. Następnie przejdź do **wszystkich aplikacji**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Printix**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/tutorial_printix_search.png)

1. W panelu wyników wybierz pozycję **Printix**, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD
W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Printix na podstawie użytkownika testowego o nazwie "Britta Simon".

Aby usługa Azure AD działała z logiem pojedynczym, musi wiedzieć, czym jest użytkownik w programie Printix dla użytkownika w usłudze Azure AD. Innymi słowy należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w printix.

W Printix przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość nazwy **użytkownika,** aby ustanowić relację łącza.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą funkcji Printix, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configuring-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)** — do testowania logowania jednokrotnego usługi Azure AD za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Printix](#creating-a-printix-test-user)** — aby mieć odpowiednik Britta Simon w Printix, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)** — aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal i konfigurujesz logowanie jednokrotne w aplikacji Printix.

**Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą funkcji Printix, wykonaj następujące czynności:**

1. W witrynie Azure portal na stronie integracji aplikacji **Printix** kliknij pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. W oknie dialogowym **logowania jednokrotnego** wybierz **opcję Tryb** jako **logowanie oparte na SAML,** aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_samlbase.png)

1. W sekcji **Domeny i adresy URL Printix** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_url.png)

    W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.printix.net`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Printix,](mailto:support@printix.net) aby uzyskać wartość. 
 
1. W sekcji **Certyfikat podpisywania SAML** kliknij pozycję **Metadane XML,** a następnie zapisz plik metadanych na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_general_400.png)

1. Zaloguj się do dzierżawy Printix jako administrator.

1. W menu u góry kliknij ikonę w prawym górnym rogu i wybierz "**Uwierzytelnianie**".
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_06.png)

1. Na karcie **Ustawienia** wybierz pozycję **Włącz uwierzytelnianie platformy Azure/Usługi Office 365**
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_07.png)

1. Na karcie **Azure** wprowadź adres URL metadanych federacji do pola tekstowego "**Dokument metadanych federacji**". 

    Dołącz plik xml metadanych pobrany z usługi Azure AD do [zespołu pomocy technicznej Printix](mailto:support@printix.net). Następnie przekazują plik xml i podają adres URL metadanych federacji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_08.png)
   
1. Kliknij przycisk **"Testuj"** i kliknij przycisk "**OK**", jeśli test się powiódł.
   
     Strona usługi Azure active directory zostanie wyświetlona po kliknięciu przycisku **testu.** "Test zakończył się pomyślnie" w tym miejscu oznacza, że po wprowadzeniu poświadczeń konta testowego platformy Azure pojawi się komunikat "Ustawienia przetestowane OK". Następnie kliknij przycisk **OK.**
   
    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_09.png)

1. Kliknij przycisk **Zapisz** na stronie **"Uwierzytelnianie".**


> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji ** Konfiguracja** w dolnej części strony. Więcej informacji na temat osadzonej dokumentacji można znaleźć tutaj: [Dokumentacja osadzona usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Tworzenie użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące kroki:**

1. W **witrynie Azure portal**w lewym okienku nawigacji kliknij ikonę **usługi Azure Active Directory.**

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **pozycję Użytkownicy i grupy** oraz kliknij pozycję Wszyscy **użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_02.png) 

1. Aby otworzyć okno dialogowe **Użytkownik,** kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_03.png) 

1. Na stronie Okno dialogowe **Użytkownik** wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/printix-tutorial/create_aaduser_04.png) 

    a. W polach tekstowych **Nazwa** wpisz **BrittaSimon**.

    b. W polach tekstowych **Nazwa użytkownika** wpisz **adres e-mail** brittaSimon.

    d. Wybierz **pozycję Pokaż hasło** i zapisz wartość **hasła**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-printix-test-user"></a>Tworzenie użytkownika testowego Printix

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w Printix. Printix obsługuje just-in-time inicjowania obsługi administracyjnej, która jest domyślnie włączona.

W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do Printix, jeśli jeszcze nie istnieje. 

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, należy skontaktować się z [zespołem pomocy technicznej Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Printix.

![Przypisywanie użytkownika][200] 

**Aby przypisać Britta Simon do Printix, wykonaj następujące czynności:**

1. W witrynie Azure portal otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacji przedsiębiorstwa,** a następnie kliknij pozycję **Wszystkie aplikacje**.

    ![Przypisywanie użytkownika][201] 

1. Na liście aplikacji wybierz **printix**.

    ![Konfigurowanie logowania jednokrotnego](./media/printix-tutorial/tutorial_printix_app.png) 

1. W menu po lewej stronie kliknij pozycję **Użytkownicy i grupy**.

    ![Przypisywanie użytkownika][202] 

1. Kliknij przycisk **Add** (Dodaj). Następnie wybierz **pozycję Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Przypisywanie użytkownika][203]

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **Wybierz** w oknie dialogowym **Użytkownicy i grupy.**

1. Kliknij przycisk **Przypisz** w oknie dialogowym **Dodawanie przydziału.**
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Printix w Panelu dostępu należy automatycznie zalogować się do aplikacji Printix.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/printix-tutorial/tutorial_general_01.png
[2]: ./media/printix-tutorial/tutorial_general_02.png
[3]: ./media/printix-tutorial/tutorial_general_03.png
[4]: ./media/printix-tutorial/tutorial_general_04.png

[100]: ./media/printix-tutorial/tutorial_general_100.png

[200]: ./media/printix-tutorial/tutorial_general_200.png
[201]: ./media/printix-tutorial/tutorial_general_201.png
[202]: ./media/printix-tutorial/tutorial_general_202.png
[203]: ./media/printix-tutorial/tutorial_general_203.png

