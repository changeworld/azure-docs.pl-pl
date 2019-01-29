---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Evernote | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 4ad45757aaa64652c0f9ad06c785b2749a4620a5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171545"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Evernote

W tym samouczku dowiesz się, jak zintegrować Evernote w usłudze Azure Active Directory (Azure AD).

Integrowanie Evernote z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do połączenia z usługą Evernote.
- Aby umożliwić użytkownikom automatycznie pobrać podpisane w przypadku połączenia z usługą Evernote (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Evernote, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Evernote logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Evernote z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie Evernote z galerii
Aby skonfigurować integrację Evernote w usłudze Azure AD, należy dodać Evernote z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Evernote z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Evernote**, wybierz opcję **Evernote** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Evernote na liście wyników](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Evernote w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Evernote do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Evernote musi można ustanowić.

W Evernote, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Evernote, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Evernote](#create-an-evernote-test-user)**  — aby odpowiednikiem Britta Simon w Evernote połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Evernote.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Evernote, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Evernote** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. Na **Evernote domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w trybie zainicjował dostawcy tożsamości należy wykonać następujące czynności:

    ![Evernote domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/evernote-tutorial/tutorial_evernote_url.png)

    W **identyfikator** pole tekstowe, wpisz adres URL: `https://www.evernote.com/saml2`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Evernote domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/evernote-tutorial/tutorial_evernote_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://www.evernote.com/Login.action`   

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/evernote-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Evernote** , kliknij przycisk **skonfigurować Evernote** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Evernote](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Evernote, jako administrator.

1. Przejdź do **"Konsola administracyjna"**

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **"Konsola administracyjna"**, przejdź do **"Zabezpieczenia"** i wybierz **"Logowanie jednokrotne"**

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włączanie logowania jednokrotnego:** Usługa rejestracji Jednokrotnej jest domyślnie włączona (kliknij **Wyłącz logowanie jednokrotne** Aby usunąć wymaganie logowania jednokrotnego)

    b. Wklej **SAML logowania jednokrotnego adres URL usługi** wartości, które zostały skopiowane z witryny Azure portal do **adresu URL żądania HTTP SAML** pola tekstowego.

    c. Otwórz certyfikat pobrany z usługi Azure AD w programie Notatnik i skopiuj zawartość, w tym "BEGIN CERTIFICATE" a "END CERTIFICATE" i wklej go w **certyfikat X.509** pola tekstowego. 

    d.Click **Zapisz zmiany**

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/evernote-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/evernote-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/evernote-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/evernote-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-evernote-test-user"></a>Tworzenie użytkownika testowego Evernote

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Evernote, musi być obsługiwana w Evernote.  
W przypadku Evernote Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Evernote jako administrator.

1. Kliknij przycisk **"Konsola administracyjna"**.

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. Z **"Konsola administracyjna"**, przejdź do **"Dodawanie użytkowników"**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. **Dodawanie członków zespołu** w **E-mail** polu tekstowym wpisz adres e-mail konta użytkownika i kliknij przycisk **zaprosić.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. Po wysłaniu zaproszenia, właściciel konta usługi Azure Active Directory zostanie wysłana wiadomość e-mail o zaakceptowanie zaproszenia.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu z usługą Evernote.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Evernote, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Evernote**.

    ![Link Evernote na liście aplikacji](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote w panelu dostępu, użytkownik powinien uzyskać zalogowanych do aplikacji Evernote. Można będzie można logować się jako organizacji konta jednak trzeba zalogować się przy użyciu konta osobistego. 

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png

