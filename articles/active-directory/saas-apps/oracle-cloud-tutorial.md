---
title: 'Samouczek: Integracja usługi Azure Active Directory z konsolą Oracle Cloud Infrastructure Console | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a konsolą Oracle Cloud Infrastructure Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289105"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Samouczek: Integracja konsoli infrastruktury oracle w chmurze z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować oracle cloud infrastructure console z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Oracle Cloud Infrastructure Console z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Oracle Cloud Infrastructure Console.
* Włącz automatyczne logowanie użytkowników do konsoli Oracle Cloud Infrastructure Console za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego (SSO) oracle Cloud Infrastructure Console.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Oracle Cloud Infrastructure Console obsługuje sso inicjowane przez usługę **SP.**
* Po skonfigurowaniu konsoli Oracle Cloud Infrastructure Console można wymusić kontrole sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Formanty sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Dodawanie konsoli Oracle Cloud Infrastructure Console z galerii

Aby skonfigurować integrację oracle cloud infrastructure console z usługą Azure AD, musisz dodać Oracle Cloud Infrastructure Console z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Oracle Cloud Infrastructure Console** w polu wyszukiwania.
1. Wybierz **Oracle Cloud Infrastructure Console** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą konsoli Oracle Cloud Infrastructure Console przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w konsoli Oracle Cloud Infrastructure Console.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą konsoli Oracle Cloud Infrastructure Console, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Skonfiguruj oracle cloud infrastructure console,](#configure-oracle-cloud-infrastructure-console)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Oracle Cloud Infrastructure Console,](#create-oracle-cloud-infrastructure-console-test-user)** aby mieć odpowiednik B. Simona w Oracle Cloud Infrastructure Console, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Oracle Cloud Infrastructure Console** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

   > [!NOTE]
   > Plik metadanych dostawcy usług otrzymasz z sekcji **Konfigurowanie logowania jednokrotnego konsoli infrastruktury Oracle Cloud Console** w samouczku.
    
   1. Kliknij pozycję **Przekaż plik metadanych**.

   1. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

   1. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** zostaną automatycznie wypełnione w polu tekstowym sekcji **Podstawowa konfiguracja SAML.**
    
      > [!NOTE]
      > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

      W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta konsoli infrastruktury Oracle Cloud Infrastructure,](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

1. Aplikacja Oracle Cloud Infrastructure Console oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

   ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Oracle Cloud Infrastructure Console oczekuje, że kilka więcej atrybutów zostanie przekazanych z powrotem w odpowiedzi SAML. W sekcji **Atrybuty & oświadczenia użytkownika** w oknie dialogowym **Oświadczenia grupy (wersja zapoznawcza)** wykonaj następujące czynności:

   1. Kliknij **pióro** obok **pozycji Nazwa wartości identyfikatora**.

   1. Wybierz **opcję Trwałe** jako Wybierz format **identyfikatora nazwy**.
 
   1. Kliknij przycisk **Zapisz**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Kliknij **pióro** obok pozycji **Grupy zwrócone w zgłęceniu**.

   1. Wybierz **pozycję Grupy zabezpieczeń** z listy radiowej.

   1. Wybierz **atrybut źródłowy** identyfikatora **grupy**.

   1. Zaznacz **pozycję Dostosuj nazwę oświadczenia grupy**.

   1. W polu tekstowym **Nazwa** wpisz polecenie **nazwa grupy**.

   1. W polu **tekstowym Obszar nazw (opcjonalnie)** wpisz polecenie `https://auth.oraclecloud.com/saml/claims`.

   1. Kliknij przycisk **Zapisz**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. W sekcji **Konfigurowanie konsoli infrastruktury Oracle Cloud** Cloud skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Oracle Cloud Infrastructure Console.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Oracle Cloud Infrastructure Console**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

   ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurowanie konsoli infrastruktury Oracle Cloud Infrastructure

1. W innym oknie przeglądarki internetowej zaloguj się do Oracle Cloud Infrastructure Console jako administrator.

1. Kliknij po lewej stronie menu i kliknij **tożsamość,** a następnie przejdź do **Federacji**.

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config01.png)

1. Zapisz **plik metadanych usługodawcy,** klikając łącze **Pobierz ten dokument** i prześlij go do sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal, a następnie kliknij **pozycję Dodaj dostawcę tożsamości**.

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config02.png)

1. W wyskakującym okienku **Dodaj dostawcę tożsamości** wykonaj następujące czynności:

   ![Konfigurowanie](./media/oracle-cloud-tutorial/config03.png)

   1. W polu **tekstowym NAZWA** wprowadź swoje imię i nazwisko.

   1. W polu tekstowym **OPIS** wprowadź opis.

   1. Wybierz **pozycję MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) lub SAML 2.0 COMPLIANT IDENTITY PROVIDER** as **TYPE**.

   1. Kliknij **przycisk Przeglądaj,** aby przekazać kod XML metadanych federacji, który został pobrany z witryny Azure portal.

   1. Kliknij **przycisk Kontynuuj** i w sekcji Edytuj **dostawcę tożsamości** wykonaj następujące czynności:

      ![Konfigurowanie](./media/oracle-cloud-tutorial/config09.png)

   1. Grupa **dostawców tożsamości** powinna zostać wybrana jako grupa niestandardowa. Identyfikator GRUPY powinien być identyfikatorem GUID grupy z usługi Azure Active Directory. Grupa musi być mapowana z odpowiednią grupą w polu **OCI GROUP.**

   1. Możesz mapować wiele grup zgodnie z konfiguracją w witrynie Azure portal i potrzebie organizacji. Kliknij **+ Dodaj mapowanie,** aby dodać dowolną liczbę grup.

   1. Kliknij **przycisk Prześlij**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Tworzenie użytkownika testowego oracle cloud infrastructure console

 Oracle Cloud Infrastructure Console obsługuje just-in-time inicjowania obsługi administracyjnej, która jest domyślnie. W tej sekcji nie musisz niczego robić. Nowy użytkownik nie zostanie utworzony podczas próby dostępu, a także nie ma potrzeby tworzenia użytkownika.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka konsoli infrastruktury Oracle Cloud w Panelu dostępu nastąpi przekierowanie do strony logowania konsoli Oracle Cloud Infrastructure Console. Wybierz **dostawcę tożsamości** z menu rozwijanego i kliknij przycisk **Kontynuuj,** jak pokazano poniżej, aby się zalogować. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Konfigurowanie](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Jak chronić Oracle Cloud Infrastructure Console dzięki zaawansowanej widoczności i sterowaniu](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
