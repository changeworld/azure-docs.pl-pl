---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą RingCentral | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d86dc27822c91bd6b2b1cb3ec53b07978061cc7d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437364"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą RingCentral

W tym samouczku dowiesz się, jak zintegrować RingCentral w usłudze Azure Active Directory (Azure AD).

Integrowanie RingCentral z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do RingCentral.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do RingCentral (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą RingCentral, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- RingCentral logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie RingCentral z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ringcentral-from-the-gallery"></a>Dodawanie RingCentral z galerii
Aby skonfigurować integrację RingCentral w usłudze Azure AD, należy dodać RingCentral z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać RingCentral z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **RingCentral**, wybierz opcję **RingCentral** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w RingCentral do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w RingCentral musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą RingCentral, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego RingCentral](#create-a-ringcentral-test-user)**  — aby odpowiednikiem Britta Simon w RingCentral połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji RingCentral.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z RingCentral, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **RingCentral** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Kliknij przycisk **zmiana jednego tryb logowania jednokrotnego** na górze ekranu, aby wybrać **SAML** trybu.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji pola tekstowego, jak pokazano poniżej :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. W **adres URL logowania** pole tekstowe, wpisz adres URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Możesz uzyskać **plik metadanych usługodawcy** na stronie konfiguracji logowania jednokrotnego RingCentral, które zostało wyjaśnione w dalszej części tego samouczka.

6. Jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. W **identyfikator** pole tekstowe, wpisz adres URL:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** strony w **certyfikat podpisywania SAML** , kliknij przycisk **Pobierz** można pobrać certyfikatu z danymi opcjami zgodnie z wymagania i zapisz go na komputerze.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. W oknie przeglądarki internetowej innej, zaloguj się do RingCentral jako Administrator zabezpieczeń.

9. Na górze, wybierz polecenie **narzędzia**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Przejdź do **logowanie jednokrotne**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Na **logowania jednokrotnego** w obszarze **konfiguracji logowania jednokrotnego** sekcji z **kroku 1** kliknij **Edytuj** i wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Na **Konfigurowanie logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kliknij przycisk **Przeglądaj** można przekazać pliku metadanych, który został pobrany z witryny Azure portal.

    b. Po przekazaniu metadanych wartości pobrać, automatycznie wypełnione w **logowania jednokrotnego ogólne informacje o** sekcji.

    c. W obszarze **mapowanie atrybutu** zaznacz **mapy atrybut poczty E-mail, aby** jako `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kliknij pozycję **Zapisz**.

    e. Z **krok 2** kliknij **Pobierz** można pobrać **plik metadanych usługodawcy** i przekaż go w **podstawową konfigurację protokołu SAML** sekcji Aby automatycznie wypełniać **identyfikator** i **adres URL odpowiedzi** wartości w witrynie Azure portal.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na tej samej stronie, przejdź do **włączyć logowanie Jednokrotne** sekcji, a następnie wykonaj następujące czynności:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Wybierz **Włącz usługę logowania jednokrotnego**.
    
    b. Wybierz **umożliwiają użytkownikom logowanie się przy użyciu poświadczeń logowania jednokrotnego lub RingCentral**.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-ringcentral-test-user"></a>Tworzenie użytkownika testowego RingCentral

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w RingCentral. Praca z [zespołem pomocy technicznej klienta RingCentral](https://success.ringcentral.com/RCContactSupp) Aby dodać użytkowników na platformie RingCentral. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do RingCentral.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

5. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka RingCentral w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji RingCentral.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

