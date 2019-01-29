---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą amplitudy | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między amplitudy i usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeedes
ms.openlocfilehash: 5a83dc0b7d40291cd38c276b4e6c0437e38a0e15
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192319"
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą amplitudy

W tym samouczku dowiesz się, jak zintegrować amplitudy w usłudze Azure Active Directory (Azure AD).

Integrowanie amplitudy z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, który ma dostęp do amplitudy.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do amplitudy (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą amplitudy, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Amplitudy logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie amplitudy z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-amplitude-from-the-gallery"></a>Dodawanie amplitudy z galerii
Aby skonfigurować integrację amplitudy w usłudze Azure AD, należy dodać amplitudy z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać amplitudy z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **amplitudy**, wybierz opcję **amplitudy** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Amplitudy na liście wyników](./media/amplitude-tutorial/tutorial_amplitude_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą amplitudę, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w amplitudy do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w amplitudy musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą amplitudę, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego amplitudy](#create-an-amplitude-test-user)**  — aby odpowiednikiem Britta Simon w amplitudy połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji amplitudy.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z amplitudę, wykonaj następujące czynności:**

1. W witrynie Azure portal na **amplitudy** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/amplitude-tutorial/tutorial_amplitude_samlbase.png)

3. Na **amplitudy domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Amplitudy domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/amplitude-tutorial/tutorial_amplitude_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://amplitude.com/saml/sso/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. W dalszej części tego samouczka będzie przechowywana wartość adresu URL odpowiedzi.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Amplitudy domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/amplitude-tutorial/tutorial_amplitude_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://analytics.amplitude.com/sso`

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/amplitude-tutorial/tutorial_amplitude_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/amplitude-tutorial/tutorial_general_400.png)
    
7. Zaloguj się do amplitudy firmowa witryna jako administrator.

8. Kliknij pozycję **planowanie administratora** na pasku nawigacyjnym po lewej stronie.

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure1.png)

9. Wybierz **platformy Microsoft Azure Active Directory metadanych** z **integracja logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure2.png)

10. Na **Ustaw się logowanie jednokrotne** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/amplitude-tutorial/configure3.png)

    a. Otwórz pobrany **Xml metadanych** z witryny Azure portal w programie Notatnik, Wklej zawartość **Microsoft Azure Active Directory Metadata** pola tekstowego.

    b. Kopiuj **adres URL odpowiedzi (ACS)** wartość i wklej go w **adres URL odpowiedzi** textbox sekcji amplitudy domena i adresy URL w witrynie Azure portal.

    c. Kliknij pozycję **Zapisz**

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/amplitude-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/amplitude-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/amplitude-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/amplitude-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-amplitude-test-user"></a>Tworzenie użytkownika testowego amplitudy

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w amplitudy. Amplitudy obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu amplitudę, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej amplitudy](https://amplitude.zendesk.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do amplitudy.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon amplitudę, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **amplitudy**.

    ![Link amplitudy na liście aplikacji](./media/amplitude-tutorial/tutorial_amplitude_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka amplitudy w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do amplitudy aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/amplitude-tutorial/tutorial_general_01.png
[2]: ./media/amplitude-tutorial/tutorial_general_02.png
[3]: ./media/amplitude-tutorial/tutorial_general_03.png
[4]: ./media/amplitude-tutorial/tutorial_general_04.png

[100]: ./media/amplitude-tutorial/tutorial_general_100.png

[200]: ./media/amplitude-tutorial/tutorial_general_200.png
[201]: ./media/amplitude-tutorial/tutorial_general_201.png
[202]: ./media/amplitude-tutorial/tutorial_general_202.png
[203]: ./media/amplitude-tutorial/tutorial_general_203.png

