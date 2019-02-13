---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą sposób prowadzenia | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i sposób prowadzenia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dc6d8e2cf7ac4786f30484325406a1fe696dff3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165131"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą sposób prowadzenia

W tym samouczku dowiesz się, jak zintegrować sposób prowadzenia w usłudze Azure Active Directory (Azure AD).

Integrowanie sposób możemy zrobić z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do prowadzenia sposób.
- Użytkowników, aby automatycznie uzyskać zalogowanych do sposób prowadzenia (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą sposób prowadzenia, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Sposób możemy logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie sposób prowadzenia z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-way-we-do-from-the-gallery"></a>Dodawanie sposób prowadzenia z galerii
Aby skonfigurować integrację z sposób prowadzenia w usłudze Azure AD, należy dodać sposób prowadzenia z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać sposób prowadzenia z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **sposób prowadzenia**, wybierz opcję **sposób prowadzenia** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Sposób prowadzenia na liście wyników](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą sposób prowadzenia oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w sposób prowadzenia do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w sposób prowadzenia musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą sposób możemy zrobić, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego sposób prowadzenia](#create-a-way-we-do-test-user)**  — aby odpowiednikiem Britta Simon w sposób czynność tę możemy wykonać jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji sposób prowadzenia.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne za pomocą sposób prowadzenia, wykonaj następujące czynności:**

1. W witrynie Azure portal na **sposób prowadzenia** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Na **sposób możemy zrobić domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Sposób możemy zrobić domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej w sposób możemy zrobić klienta](mailto:support@waywedo.com) do uzyskania tych wartości. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/waywedo-tutorial/tutorial_general_400.png)

6. Na **sposób możemy zrobić konfiguracji** kliknij **skonfigurować sposób prowadzenia** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Prowadzenia konfiguracji](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. W oknie przeglądarki internetowej innej, zaloguj się do sposób prowadzenia jako Administrator zabezpieczeń.

8. Kliknij przycisk **ikona osoby** w prawym górnym rogu dowolnej stronie w sposób prowadzenia kliknięcie **konta** w menu rozwijanym.

    ![Sposób prowadzenia konta](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Kliknij przycisk **ikonę menu** można otworzyć menu nawigacji wypychania i kliknij przycisk **Single Sign On**.

    ![Sposób prowadzenia pojedynczego](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Na **ustawienia rejestracji jednokrotnej** strony, wykonaj następujące czynności:

    ![Sposób prowadzenia Zapisz](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kliknij przycisk **włączyć logowanie jednokrotne** Przełącz, aby **tak** włączyć logowanie jednokrotne.

    b. W **nazwa rejestracji jednokrotnej** polu tekstowym wprowadź nazwę.

    c. W **identyfikator jednostki** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal.

    d. W **adres URL logowania jednokrotnego SAML** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    e. Przekaż certyfikat, klikając **wybierz** znajdujący się obok **certyfikatu**.

    f. **Ustawienia opcjonalne** -
    
    * Włącz haseł — Jeśli ta opcja jest wyłączona, regularne hasła funkcji dla sposób prowadzenia, dzięki czemu użytkownicy mogą tylko używać logowania jednokrotnego.

    * Włączanie automatycznego aprowizowania — Jeśli ta opcja jest włączona, adres e-mail używany do logowania jednokrotnego będą automatycznie porównywane do listy użytkowników w sposób prowadzenia. Jeśli adres e-mail nie jest zgodny z aktywnego użytkownika w sposób prowadzenia, automatycznie dodaje nowe konto użytkownika osoby, logowania, żądanie wszystkie brakujące informacje.

      > [!NOTE]
      > Użytkownicy dodani za pomocą logowania jednokrotnego są dodawane jako użytkownik ogólny i nie przypisano rolę w systemie. Administrator będzie mógł go w programie i modyfikowanie ich rola zabezpieczeń jako edytor lub administrator i można także przypisać organizacyjny jeden lub kilka ról. 

    g. Kliknij przycisk **Zapisz** można utrwalić ustawień.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/waywedo-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/waywedo-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/waywedo-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/waywedo-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-way-we-do-test-user"></a>Tworzenie użytkownika testowego sposób prowadzenia

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w sposób prowadzenia. Sposób prowadzenia obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu sposób możemy zrobić, jeśli go jeszcze nie istnieje.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej w sposób możemy zrobić klienta](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do prowadzenia sposób.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon sposób możemy zrobić, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **sposób prowadzenia**.

    ![Sposób prowadzenia łącze na liście aplikacji](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka sposób prowadzenia w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do sposób prowadzenia aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

