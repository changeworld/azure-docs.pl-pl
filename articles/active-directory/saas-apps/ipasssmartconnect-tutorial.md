---
title: 'Samouczek: Integracja usługi Azure Active Directory z iPass SmartConnect | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: ecfdd3fae1d394e3b57fcd325f44cad0d1a98534
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444898"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Samouczek: Integracja usługi Azure Active Directory z iPass SmartConnect

W tym samouczku dowiesz się, jak zintegrować iPass SmartConnect w usłudze Azure Active Directory (Azure AD).

Integrowanie iPass SmartConnect z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do iPass SmartConnect.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do iPass SmartConnect (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą iPass SmartConnect, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- IPass SmartConnect logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie iPass SmartConnect z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Dodawanie iPass SmartConnect z galerii
Aby skonfigurować integrację iPass SmartConnect w usłudze Azure AD, należy dodać iPass SmartConnect z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać iPass SmartConnect z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **iPass SmartConnect**, wybierz opcję **iPass SmartConnect** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![iPass SmartConnect na liście wyników](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą iPass SmartConnect zależnie od użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, jakie użytkownik odpowiednika w iPass SmartConnect jest dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w iPass SmartConnect musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą iPass SmartConnect, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego SmartConnect iPass](#create-an-ipass-smartconnect-test-user)**  — aby mają odpowiednika w pozycji Britta simon w iPass SmartConnect, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w Twojej aplikacji SmartConnect iPass.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z iPass SmartConnect, wykonaj następujące czynności:**

1. W witrynie Azure portal na **iPass SmartConnect** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Na **iPass SmartConnect domena i adresy URL** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, nie trzeba wykonywać żadnych czynności.

    ![iPass SmartConnect domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Sprawdź, Pokaż zaawansowane ustawienia adresu URL i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![iPass SmartConnect domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    W polu tekstowym adres URL logowania wpisz adres URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. iPass SmartConnect aplikacja oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego](./media/ipasssmartconnect-tutorial/attribute.png)

1. Kliknij przycisk **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** pola wyboru w **atrybutów użytkownika** sekcji, aby rozwinąć atrybutów. Wykonaj następujące czynności na każdym z atrybutów wyświetlanych-

    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace|
    | ---------------| --------------- |----------------|
    | Imię | user.givenname |   |
    | Nazwisko | user.surname | |
    | e-mail | user.userprincipalname | |
    | nazwa użytkownika | user.userprincipalname | |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Pozostaw to pole puste, dla tego wiersza wartości przestrzeni nazw.

    e. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie jednokrotne na **iPass SmartConnect** stronie, musisz wysłać pobrany **XML metadanych** i **nazwy domeny** do [iPass SmartConnect zespołu pomocy technicznej](mailto:help@ipass.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Tworzenie użytkownika testowego SmartConnect iPass

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w iPass SmartConnect. Praca z [zespołu pomocy technicznej iPass SmartConnect](mailto:help@ipass.com) można dodać użytkowników lub domeny, który jest wymagany do listy dozwolonych na platformie SmartConnect iPass. Jeśli domena jest dodawany przez zespół, użytkownicy będą automatycznie aprowizowany platformę SmartConnect iPass. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do iPass SmartConnect.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon iPass SmartConnect, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **iPass SmartConnect**.

    ![IPass SmartConnect łącze na liście aplikacji](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

**Aby przetestować aplikację w usłudze flow SP zainicjowane, wykonaj następujące czynności:**

a. Pobierz windows iPass klienta SmartConnect [tutaj](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect łącze na liście aplikacji](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instalowanie klienta i uruchamiania.

c. Kliknij pozycję **wprowadzenie**.

![IPass SmartConnect łącze na liście aplikacji](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Wprowadź nazwę użytkownika platformy Azure z domeną. Kliknij pozycję **nadal**. To nastąpi przekierowanie do strony logowania do platformy Azure

![IPass SmartConnect łącze na liście aplikacji](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Aktywacja klienta zostanie uruchomiony po pomyślnym uwierzytelnieniu. Klient będzie aktywować.

**Aby przetestować aplikację w usłudze flow inicjowane przez dostawcę tożsamości, wykonaj następujące czynności:**

a. Zaloguj się do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Polecenie iPass SmartConnect aplikacji.

c. Uruchamia ona SSA strony, kliknij pozycję **pobrać aplikację dla Windows** zainstalował iPass SmartConnect klienta.

![IPass SmartConnect łącze na liście aplikacji](./media/ipasssmartconnect-tutorial/testing4.png)

d. Po instalacji klienta przy pierwszym uruchomieniu zostanie automatycznie uruchamia aktywacji po zaakceptowaniu warunków i postanowień.

e. Jeśli aktywacja nie zostanie uruchomiona, kliknij przycisk Aktywuj na stronie SSA, aby rozpocząć aktywację.

f. Klient będzie aktywować.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

