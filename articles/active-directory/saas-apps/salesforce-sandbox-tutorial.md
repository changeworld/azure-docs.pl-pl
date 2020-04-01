---
title: 'Samouczek: Integracja usługi Azure Active Directory z piaskownicą Salesforce | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a piaskownicą salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76290007"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z piaskownicą Salesforce

W tym samouczku dowiesz się, jak zintegrować piaskownicę Salesforce z usługą Azure Active Directory (Azure AD). Po zintegrowaniu piaskownicy Salesforce z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do piaskownicy Salesforce.
* Włącz użytkownikom automatyczne logowanie do piaskownicy Salesforce za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Salesforce Sandbox(SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Salesforce Sandbox obsługuje jednostkę SSO inicjowane przez **SP i IDP**
* Izolowanie Salesforce obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time
* Izolowanie Salesforce obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](salesforce-sandbox-provisioning-tutorial.md)
* Po skonfigurowaniu obszaru izolowania Salesforce można wymusić kontrole sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Dodawanie piaskownicy Salesforce z galerii

Aby skonfigurować integrację piaskownicy Salesforce z usługą Azure AD, należy dodać piaskownicę Salesforce z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz obszar **piaskowy Salesforce** w polu wyszukiwania.
1. Wybierz **pozycję Izolowatość Salesforce** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla piaskownicy salesforce

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu piaskownicy Salesforce przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w obszarze izolowanym Salesforce.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD sytuować przy pracy z piaskownicą salesforce, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne w programie Salesforce Sandbox](#configure-salesforce-sandbox-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Salesforce Sandbox](#create-salesforce-sandbox-test-user)** — aby mieć odpowiednik B.Simon w obszarze sandbox Salesforce, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **izolowanie Salesforce** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie inicjowanym przez **IDP,** w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    > [!NOTE]
    > Otrzymasz plik metadanych dostawcy usług z portalu administracyjnego Salesforce Sandbox, który jest wyjaśniony w dalszej części samouczka.

    d. Po pomyślnym przekazaniu pliku metadanych wartość **adresu URL odpowiedzi** zostanie automatycznie wypełniona w polu tekstowym **Odpowiedz na adres URL.**

    ![image](common/both-replyurl.png)

    > [!Note]
    > Jeśli wartość **adresu URL odpowiedzi** nie zostanie automatycznie obliczona, wypełnij tę wartość ręcznie zgodnie z wymaganiami.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie piaskownicy Salesforce** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do salesforce piaskownicy.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Izolowatość Salesforce**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-salesforce-sandbox-sso"></a>Konfigurowanie sytcha w obszarze sandbox salesforce

1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora piaskownicy Salesforce.

2. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

3. Przewiń w dół do **ustawień** w lewym okienku nawigacji, kliknij pozycję **Tożsamość,** aby rozwinąć powiązaną sekcję. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

5. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kliknij pozycję **Wybierz plik**, aby przekazać plik metadanych XML pobrany z witryny Azure Portal, i kliknij pozycję **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na stronie **Ustawienia logowania jednokrotnego SAML** pola zostaną wypełnione automatycznie. Kliknij pozycję Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Pobierz metadane,** aby pobrać plik metadanych dostawcy usług. Użyj tego pliku w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal do konfigurowania niezbędnych adresów URL, jak wyjaśniono powyżej.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure4.png)

10. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **sp,** są następujące wymagania wstępne dla tego:

    a. Powinna być zweryfikowana domena.

    b. Musisz skonfigurować i włączyć domenę w obszarze izolowanym Salesforce, kroki w tym zakresie są wyjaśnione w dalszej części tego samouczka.

    d. W witrynie Azure portal w sekcji **Podstawowa konfiguracja SAML** kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujący krok:
  
    ![Informacje o domenie piaskownicy i adresach URL Salesforce](common/both-signonurl.png)

    W polu **tekstowym Logowania adres URL** wpisz wartość, używając następującego wzorca:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ta wartość powinna zostać skopiowana z portalu izolowane usługi Salesforce po włączeniu domeny.

11. W sekcji **Certyfikat podpisywania SAML** kliknij pozycję **XML metadanych federacji,** a następnie zapisz plik xml na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

12. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora piaskownicy Salesforce.

13. Kliknij pozycję **Konfiguracja** pod **ikoną ustawień** w prawym górnym rogu strony.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure1.png)

14. Przewiń w dół do **ustawień** w lewym okienku nawigacji, kliknij pozycję **Tożsamość,** aby rozwinąć powiązaną sekcję. Następnie kliknij pozycję **Ustawienia logowania jednokrotnego**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na stronie **Ustawienia logowania jednokrotnego** kliknij przycisk **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure3.png)

16. Wybierz pozycję **Włączona obsługa protokołu SAML**, a następnie kliknij pozycję **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Aby skonfigurować ustawienia logowania jednokrotnego SAML, kliknij pozycję **Nowe z pliku metadanych**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Kliknij **pozycję Wybierz plik,** aby przekazać plik XML metadanych, a następnie kliknij przycisk **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na stronie **Ustawienia logowania jednokrotnego SAML** pola wypełniają się automatycznie, wpisz nazwę konfiguracji (na przykład: *SPSSOWAAD_Test*), w polu tekstowym **Nazwa** i kliknij przycisk Zapisz.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Aby włączyć domenę w obszarze izolowanym Salesforce, wykonaj następujące czynności:

    > [!NOTE]
    > Przed włączeniem domeny należy utworzyć to samo w piaskownicy Salesforce. Aby uzyskać więcej informacji, zobacz [Definiowanie nazwy domeny](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Po utworzeniu domeny upewnij się, że jest poprawnie skonfigurowana.

21. W lewym okienku nawigacji w obszarze izolowanym Salesforce kliknij pozycję **Ustawienia firmy,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Moja domena**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. W sekcji **Konfiguracja uwierzytelniania** kliknij pozycję **Edytuj**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. W sekcji **Konfiguracja uwierzytelniania** jako **Usługa uwierzytelniania**wybierz nazwę ustawienia logowania jednokrotnego SAML ustawionego podczas konfiguracji logowania jednokrotnego w obszarze izolowanym Salesforce i kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Utwórz użytkownika testowego Salesforce Sandbox

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Salesforce Sandbox. Salesforce Sandbox obsługuje just-in-time inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w piaskownicy Salesforce, nowy jest tworzony podczas próby uzyskania dostępu do piaskownicy Salesforce. Salesforce Sandbox obsługuje również automatyczne inicjowanie obsługi administracyjnej użytkowników, więcej informacji można znaleźć [tutaj,](salesforce-sandbox-provisioning-tutorial.md) jak skonfigurować automatyczne inicjowanie obsługi administracyjnej użytkowników.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Izolowane witryny Salesforce w Panelu dostępu należy automatycznie zalogować się do piaskownicy Salesforce, dla której skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj piaskownicę salesforce z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Konfigurowanie inicjowania obsługi administracyjnej użytkowników](salesforce-sandbox-provisioning-tutorial.md)

- [Jak chronić piaskownicę Salesforce dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
