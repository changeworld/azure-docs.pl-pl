---
title: 'Samouczek: Integracja usługi Azure Active Directory ze ścieżką niejawnego programu testów | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Śledź niejawnego programu testów.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 53b6a928-e8a4-4cf1-9952-50cd3f013b7c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28ea99f7e672339e45730e80294455931573d62a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170001"
---
# <a name="tutorial-azure-active-directory-integration-with-insider-track"></a>Samouczek: Integracja usługi Azure Active Directory ze ścieżką niejawnego programu testów

W tym samouczku dowiesz się, jak zintegrować śledzenie niejawnego programu testów za pomocą usługi Azure Active Directory (Azure AD).

Integracja śledzenia niejawnego programu testów z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ścieżki niejawnego programu testów.
- Użytkowników, aby automatycznie uzyskać zalogowanych do śledzenia niejawnego programu testów (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD ze ścieżką niejawnego programu testów, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Śledź niejawnego programu testów logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie śledzenia niejawnego programu testów z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-insider-track-from-the-gallery"></a>Dodawanie śledzenia niejawnego programu testów z galerii
Aby skonfigurować integrację prowadnicy niejawnego programu testów w usłudze Azure AD, należy dodać ścieżkę niejawnego programu testów z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ścieżkę niejawnego programu testów z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **śledzenie niejawnego programu testów**, wybierz opcję **śledzenie niejawnego programu testów** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Śledź niejawnego programu testów na liście wyników](./media/insidertrack-tutorial/tutorial_insidertrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą śledzenia niejawnego programu testów w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ścieżce niejawnego programu testów do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ścieżce niejawnego programu testów musi zostać ustanowione.

W ścieżce niejawnego programu testów, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu ścieżki niejawnego programu testów, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego śledzenie niejawnego programu testów](#create-an-insider-track-test-user)**  — aby odpowiednikiem Britta Simon w ścieżce niejawnego programu testów, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji śledzenie niejawnego programu testów.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z niejawnego programu testów, śledzenie, wykonaj następujące czynności:**

1. W witrynie Azure portal na **śledzenie niejawnego programu testów** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/insidertrack-tutorial/tutorial_insidertrack_samlbase.png)

3. Na **niejawnego programu testów śledzenie domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Niejawnego programu testów śledzenie domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/insidertrack-tutorial/tutorial_insidertrack_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>/InsiderTrack.Portal.<companyname>/Sso/`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwe. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta śledzenie niejawnego programu testów](https://cytecsolutions.com/contact/) aby zyskać tę wartość.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/insidertrack-tutorial/tutorial_insidertrack_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/insidertrack-tutorial/tutorial_general_400.png)

6. Na **konfiguracji śledzenia niejawnego programu testów** kliknij **skonfigurować śledzenie niejawnego programu testów** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja śledzenie niejawnego programu testów](./media/insidertrack-tutorial/tutorial_insidertrack_configure.png) 

7. Aby skonfigurować logowanie jednokrotne na **śledzenie niejawnego programu testów** stronie, musisz wysłać pobrany **XML metadanych, adres URL wylogowania, identyfikator jednostki SAML,** i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [Śledzenie niejawnego programu testów zespołu pomocy technicznej](https://cytecsolutions.com/contact/). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/insidertrack-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/insidertrack-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/insidertrack-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/insidertrack-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-insider-track-test-user"></a>Tworzenie użytkownika testowego śledzenie niejawnego programu testów

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ścieżce niejawnego programu testów. Praca z [śledzenie niejawnego programu testów zespołu pomocy technicznej](https://cytecsolutions.com/contact/) Aby dodać użytkowników na platformie śledzenie niejawnego programu testów. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do ścieżki niejawnego programu testów.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do śledzenia niejawnego programu testów, należy wykonać następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **śledzenie niejawnego programu testów**.

    ![Połącz śledzenie niejawnego programu testów na liście aplikacji](./media/insidertrack-tutorial/tutorial_insidertrack_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka śledzenie niejawnego programu testów, w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji śledzenie niejawnego programu testów.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insidertrack-tutorial/tutorial_general_01.png
[2]: ./media/insidertrack-tutorial/tutorial_general_02.png
[3]: ./media/insidertrack-tutorial/tutorial_general_03.png
[4]: ./media/insidertrack-tutorial/tutorial_general_04.png

[100]: ./media/insidertrack-tutorial/tutorial_general_100.png

[200]: ./media/insidertrack-tutorial/tutorial_general_200.png
[201]: ./media/insidertrack-tutorial/tutorial_general_201.png
[202]: ./media/insidertrack-tutorial/tutorial_general_202.png
[203]: ./media/insidertrack-tutorial/tutorial_general_203.png

