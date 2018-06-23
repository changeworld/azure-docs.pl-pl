---
title: 'Samouczek: Integracji Azure Active Directory z iPass SmartConnect | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i iPass SmartConnect.
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
ms.openlocfilehash: 5ae600142f7e90a7f6185c3a948a4174ce4c7797
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320527"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Samouczek: Integracji Azure Active Directory z iPass SmartConnect

Z tego samouczka dowiesz się integrowanie iPass SmartConnect z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD iPass SmartConnect zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do iPass SmartConnect.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do iPass SmartConnect (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z iPass SmartConnect, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- IPass SmartConnect logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie iPass SmartConnect z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Dodawanie iPass SmartConnect z galerii
Aby skonfigurować integrację usługi Azure AD iPass SmartConnect, należy dodać iPass SmartConnect z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać iPass SmartConnect z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **iPass SmartConnect**, wybierz pozycję **iPass SmartConnect** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![iPass SmartConnect na liście wyników](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iPass SmartConnect oparta na koncie użytkownika testu o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, jaki użytkownik odpowiednikiem w iPass SmartConnect jest użytkownikiem w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w iPass SmartConnect musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z iPass SmartConnect, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego SmartConnect iPass](#create-an-ipass-smartconnect-test-user)**  — mają odpowiednika Simona Britta w iPass SmartConnect połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w Twojej aplikacji SmartConnect iPass.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z iPass SmartConnect, wykonaj następujące czynności:**

1. W portalu Azure na **iPass SmartConnect** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

3. Na **iPass SmartConnect domeny i adres URL** sekcji, jeśli chcesz skonfigurować aplikację w **IDP** tryb inicjowane, nie trzeba wykonywać żadnych czynności.

    ![informacje logowania z jednym iPass SmartConnect domeny i adres URL](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

4. Sprawdź, Pokaż zaawansowane ustawienia adresu URL i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![informacje logowania z jednym iPass SmartConnect domeny i adres URL](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    W polu tekstowym adres URL logowania wpisz adres URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

5. iPass SmartConnect aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ipasssmartconnect-tutorial/attribute.png)

6. Kliknij przycisk **widoku i edytować wszystkie atrybuty użytkowników** checkbox w **atrybuty użytkownika** sekcji, aby rozwinąć atrybutów. Wykonaj poniższe kroki na każdym z atrybutów wyświetlanych-

    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace|
    | ---------------| --------------- |----------------|
    | Imię | user.givenname |   |
    | Nazwisko | user.surname | |
    | wyślij wiadomość e-mail | user.userprincipalname | |
    | nazwa użytkownika | user.userprincipalname | |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Należy zachować wartość przestrzeni nazw puste dla danego wiersza.

    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

9. Aby skonfigurować logowanie jednokrotne w **iPass SmartConnect** stronie, musisz wysłać pobrany **XML metadanych** i **nazwy domeny** do [iPass SmartConnect obsługuje zespołu](mailto:help@ipass.com). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Tworzenie użytkownika testowego SmartConnect iPass

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w iPass SmartConnect. Praca z [iPass SmartConnect obsługuje zespołu](mailto:help@ipass.com) do dodawania użytkowników lub domeny, w których są potrzebne, aby być białej platformy SmartConnect iPass. Jeśli domena jest dodawany przez zespół, użytkownicy będą uzyskać automatycznie przygotowana do platformy SmartConnect iPass. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udzielanie dostępu do iPass SmartConnect.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta iPass SmartConnect, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **iPass SmartConnect**.

    ![IPass SmartConnect łącza na liście aplikacji](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

**Aby przetestować aplikację w przepływie SP inicjowane, wykonaj następujące czynności:**

a. Pobierz iPass windows klienta SmartConnect [tutaj](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect łącza na liście aplikacji](./media/ipasssmartconnect-tutorial/testing3.png)

b. Zainstaluj klienta, a następnie uruchom.

c. Polecenie **wprowadzenie**.

![IPass SmartConnect łącza na liście aplikacji](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Wprowadź nazwę użytkownika platformy Azure z domeną. Polecenie **kontynuować**. To nastąpi przekierowanie do strony logowania do systemu Azure

![IPass SmartConnect łącza na liście aplikacji](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Po pomyślnym uwierzytelnieniu Aktywacja klienta zostanie uruchomiony. Klient będzie pobrać aktywowany.

**Aby przetestować aplikację w przepływie IdP inicjowane, wykonaj następujące czynności:**

a. Zaloguj się do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Polecenie iPass SmartConnect aplikacji.

c. Jest uruchamiany SSA strony, kliknij pozycję **pobierania aplikacji dla systemu Windows** do zainstalowania iPass SmartConnect klienta.

![IPass SmartConnect łącza na liście aplikacji](./media/ipasssmartconnect-tutorial/testing4.png)

d. Po instalacji klienta przy pierwszym uruchomieniu zostanie automatycznie uruchamia aktywacji po zaakceptowaniu warunków i postanowień.

e. Jeśli aktywacja nie zostanie uruchomiony, kliknij przycisk Aktywuj na stronie SSA, aby rozpocząć aktywację.

f. Klient będzie pobrać aktywowany.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

