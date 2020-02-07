---
title: Konfigurowanie logowania jednokrotnego — Azure Active Directory | Microsoft Docs
description: W tym samouczku użyto witryny Azure Portal do skonfigurowania logowania jednokrotnego opartego na języku SAML dla aplikacji w ramach usługi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: a415ac749d0d322bc2f71f64d4bec6e32ad1f12e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063496"
---
# <a name="how-to-configure-saml-based-single-sign-on"></a>Jak skonfigurować Logowanie jednokrotne oparte na języku SAML

Po dodaniu aplikacji do aplikacji usługi Azure AD Enterprise Skonfiguruj ustawienia logowania jednokrotnego. W tym artykule opisano sposób konfigurowania logowania jednokrotnego opartego na protokole SAML dla aplikacji spoza galerii. 

> [!NOTE]
> Dodajesz aplikację z galerii? Instrukcje dotyczące instalacji krok po kroku znajdują się na [liście samouczków aplikacji SaaS](../saas-apps/tutorial-list.md)

Aby skonfigurować Logowanie jednokrotne dla aplikacji spoza galerii *bez pisania kodu*, musisz mieć subskrypcję lub Azure AD — wersja Premium, a aplikacja musi obsługiwać SAML 2,0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Jeśli aplikacja nie została dodana do dzierżawy usługi Azure AD, zobacz [Dodawanie aplikacji galerii](add-gallery-app.md) lub Dodawanie aplikacji niebędącej [galerią](add-non-gallery-app.md).
- Skontaktuj się z dostawcą aplikacji, aby uzyskać prawidłowe informacje dotyczące następujących ustawień:

    | Podstawowe ustawienia konfiguracji SAML | Zainicjowane przez dostawcę usługi | Zainicjowane przez dostawcę tożsamości | Opis |
    |:--|:--|:--|:--|
    | Identyfikator (identyfikator jednostki) | Wymagane w przypadku niektórych aplikacji | Wymagane w przypadku niektórych aplikacji | Jednoznacznie identyfikuje aplikację, dla której logowanie jednokrotne jest konfigurowane. Usługa Azure AD wysyła identyfikator do aplikacji jako parametr odbiorców tokenu SAML. Aplikacja powinna go zweryfikować. Ta wartość jest widoczna również jako identyfikator jednostki w dowolnych metadanych SAML udostępnianych przez aplikację. *Tę wartość można znaleźć jako element **Issuer** w **AuthnRequest** (żądanie SAML) wysyłanej przez aplikację.* |
    | Adres URL odpowiedzi | Optional (Opcjonalność) | Wymagany | Określa miejsce, w którym aplikacja oczekuje otrzymać token języka SAML. Adres URL odpowiedzi jest również nazywany adresem URL usługi Assertion Consumer Service (ACS). |
    | Adres URL logowania | Wymagany | Nie podawaj | Gdy użytkownik otwiera ten adres URL, dostawca usługi przekierowuje go do usługi Azure AD w celu uwierzytelnienia i zalogowania. Usługa Azure AD używa adresu URL do uruchomienia aplikacji z usługi Office 365 lub panelu dostępu usługi Azure AD. Gdy ta wartość jest pusta, usługa Azure AD korzysta od dostawcy tożsamości w celu uruchomienia logowania jednokrotnego, gdy użytkownik uruchomi aplikację.|
    | Stan przekaźnika | Optional (Opcjonalność) | Optional (Opcjonalność) | Określa aplikacji, dokąd przekierować użytkownika po zakończeniu uwierzytelniania. Zazwyczaj wartość jest prawidłowym adresem URL dla aplikacji. Jednak niektóre aplikacje używają tego pola inaczej. Aby uzyskać więcej informacji, skontaktuj się z dostawcą aplikacji.
    | Adres URL wylogowywania | Optional (Opcjonalność) | Optional (Opcjonalność) | Służy do wysyłania odpowiedzi na wylogowanie SAML z powrotem do aplikacji.

## <a name="step-1-edit-the-basic-saml-configuration"></a>Krok 1. Edytuj podstawową konfigurację języka SAML

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator aplikacji w chmurze lub administrator aplikacji dla dzierżawy usługi Azure AD.

1. Przejdź do **Azure Active Directory** > **aplikacje dla przedsiębiorstw** i wybierz aplikację z listy. 
   
   - Aby wyszukać aplikację, w menu **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**. Wprowadź nazwę aplikacji w polu wyszukiwania, a następnie wybierz aplikację z wyników.

1. W sekcji **Zarządzanie** wybierz pozycję **Logowanie jednokrotne**. 

1. Wybierz pozycję **SAML**. Zostanie wyświetlona strona **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML-Preview** .

1. Aby edytować podstawowe opcje konfiguracji SAML, wybierz ikonę **edycji** (ołówek) w prawym górnym rogu sekcji **Podstawowa konfiguracja SAML** .

     ![Konfigurowanie certyfikatów](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

1. W odpowiednich polach Wprowadź informacje opisane w sekcji [przed rozpoczęciem](#before-you-begin) .

1. W górnej części strony wybierz pozycję **Zapisz**.

## <a name="step-2-configure-user-attributes-and-claims"></a>Krok 2. Konfigurowanie atrybutów użytkownika i oświadczeń 

Aplikacja może wymagać określonych atrybutów użytkownika lub oświadczeń w tokenie SAML otrzymywanych z usługi Azure AD po zalogowaniu się użytkownika. Na przykład mogą być wymagane określone identyfikatory URI lub wartości zgłoszeń, a **Nazwa** może być inna niż nazwa użytkownika przechowywana na platformie tożsamości firmy Microsoft. Wymagania dotyczące aplikacji galerii są opisane w [samouczkach specyficznych dla aplikacji](../saas-apps/tutorial-list.md). można też polecić dostawcę aplikacji. Poniżej przedstawiono ogólne kroki konfigurowania atrybutów użytkownika i oświadczeń.

1. W sekcji **atrybuty użytkownika i oświadczenia** wybierz ikonę **Edytuj** (ołówek) w prawym górnym rogu.

1. Sprawdź **wartość identyfikatora nazwy**. Wartość domyślna to *User. PrincipalName*. Identyfikator użytkownika jednoznacznie identyfikuje każdego użytkownika w aplikacji. Jeśli na przykład adresem e-mail jest zarówno nazwa użytkownika, jak i unikatowy identyfikator, ustaw wartość *user.mail*.

1. Aby zmodyfikować **wartość identyfikatora nazwy**, wybierz ikonę **edycji** (ołówek) dla pola **wartość identyfikatora nazwy** . Wprowadź odpowiednie zmiany w formacie i źródle identyfikatora, zgodnie z potrzebami. Aby uzyskać szczegółowe informacje, zobacz [Edytowanie NameID](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#editing-nameid). Zapisz zmiany po zakończeniu. 
 
1. Aby skonfigurować oświadczenia grupy, wybierz ikonę **edycji** dla **grup zwracanych w polu oświadczenia** . Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie oświadczeń grupy](../hybrid/how-to-connect-fed-group-claims.md).

3. Aby dodać zastrzeżenie, wybierz pozycję **Dodaj nowe zastrzeżenie** w górnej części strony. Wprowadź **nazwę** i wybierz odpowiednie źródło. W przypadku wybrania źródła **atrybutów** należy wybrać **atrybut źródłowy** , który ma być używany. W przypadku wybrania źródła **tłumaczenia** należy wybrać **transformację** i **parametr 1** , które mają być używane. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie oświadczeń specyficznych dla aplikacji](https://docs.microsoft.com/azure/active-directory//develop/active-directory-saml-claims-customization#adding-application-specific-claims). Zapisz zmiany po zakończeniu. 

4. Wybierz pozycję **Zapisz**. Nowe zgłoszenie pojawi się w tabeli.

   > [!NOTE]
   > Dodatkowe sposoby dostosowywania tokenu SAML z usługi Azure AD do aplikacji można znaleźć w następujących zasobach.
   >- Aby utworzyć role niestandardowe za pośrednictwem Azure Portal, zobacz [Konfigurowanie oświadczeń ról](../develop/active-directory-enterprise-app-role-management.md).
   >- Aby dostosować oświadczenia za pomocą programu PowerShell, zobacz [Dostosowywanie oświadczeń — PowerShell](../develop/active-directory-claims-mapping.md).
   >- Aby zmodyfikować manifest aplikacji w celu skonfigurowania opcjonalnych oświadczeń dla aplikacji, zobacz [Konfigurowanie opcjonalnych oświadczeń](../develop/active-directory-optional-claims.md).
   >- Aby ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów sesji i tokenów identyfikatorów, zobacz [Konfigurowanie okresów istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md). Aby ograniczyć sesje uwierzytelniania za pośrednictwem dostępu warunkowego usługi Azure AD, zobacz [możliwości zarządzania sesjami uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106).

## <a name="step-3-manage-the-saml-signing-certificate"></a>Krok 3. Zarządzanie certyfikatem podpisywania SAML

Usługa Azure AD używa certyfikatu do podpisywania tokenów SAML wysyłanych do aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** można wyświetlić lub pobrać aktywny certyfikat. Możesz również zaktualizować, utworzyć lub zaimportować certyfikat. W przypadku aplikacji galerii szczegółowe informacje o formacie certyfikatu są dostępne w dokumentacji języka SAML aplikacji (zobacz [samouczki specyficzne dla aplikacji](../saas-apps/tutorial-list.md)). 

1. Przejdź do sekcji **certyfikat podpisywania SAML** . W zależności od typu aplikacji zobaczysz opcje pobierania certyfikatu w formacie Base64, Raw lub XML metadanych Federacji. Usługa Azure AD udostępnia również **adres URL metadanych federacji aplikacji** , w którym można uzyskać dostęp do metadanych specyficznych dla aplikacji w formacie `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

1. Aby zarządzać, utworzyć lub zaimportować certyfikat, wybierz ikonę **edycji** (ołówek) w prawym górnym rogu sekcji **certyfikat podpisywania SAML** , a następnie wykonaj jedną z następujących czynności:

   - Aby utworzyć nowy certyfikat, wybierz pozycję **nowy certyfikat**, wybierz **datę wygaśnięcia**, a następnie wybierz pozycję **Zapisz**. Aby uaktywnić certyfikat, wybierz menu kontekstowe ( **...** ) i wybierz pozycję **Ustaw certyfikat jako aktywny**.
   - Aby przekazać certyfikat z kluczem prywatnym i poświadczeniami PFX, wybierz pozycję **Importuj certyfikat** i przejdź do certyfikatu. Wprowadź **hasło PFX**, a następnie wybierz pozycję **Dodaj**.  
   - Aby skonfigurować zaawansowane opcje podpisywania certyfikatu, należy użyć następujących opcji. Opisy tych opcji można znaleźć w artykule [Zaawansowane opcje podpisywania certyfikatu](certificate-signing-options.md) .
      - Z listy rozwijanej **Opcja podpisywania** wybierz pozycję **Podpisz odpowiedź SAML**, **Podpisz potwierdzenie SAML**lub **Podpisz odpowiedź i potwierdzenie protokołu SAML**.
      - Z listy rozwijanej **algorytm podpisywania** wybierz pozycję **SHA-1** lub **SHA-256**.
   - Aby powiadomić dodatkowe osoby, gdy aktywny certyfikat zbliża się do daty wygaśnięcia, wprowadź adresy e-mail w polach **powiadomienia e-mail adresy** .

1. Wybierz pozycję **Zapisz** w górnej części sekcji **certyfikat podpisywania SAML** . 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>Krok 4. Konfigurowanie aplikacji do korzystania z usługi Azure AD

Sekcja **konfigurowanie \<applicationName >** zawiera listę wartości, które należy skonfigurować w aplikacji, aby używały usługi Azure AD jako dostawcy tożsamości SAML. Wymagane wartości różnią się w zależności od aplikacji. Aby uzyskać szczegółowe informacje, zobacz dokumentację języka SAML aplikacji.

1. Przewiń w dół do sekcji **konfigurowanie \<applicationName >** . 
2. Skopiuj wartość z każdego wiersza w tej sekcji zgodnie z wymaganiami i postępuj zgodnie z instrukcjami dla aplikacji dotyczącymi dodawania wartości do aplikacji. W przypadku aplikacji galerii można wyświetlić dokumentację, wybierając opcję **Wyświetl instrukcje krok po kroku**. 
   - Wartości **adresu URL logowania** i **wylogowania** są rozpoznawane w tym samym punkcie końcowym, który jest punktem końcowym obsługi żądania SAML dla wystąpienia usługi Azure AD. 
   - **Identyfikator usługi Azure AD** jest wartością **wystawcy** w tokenie SAML wystawionym dla aplikacji.
1. Po wklejeniu wszystkich wartości do odpowiednich pól wybierz pozycję **Zapisz**.

## <a name="step-5-validate-single-sign-on"></a>Krok 5. Weryfikuj Logowanie jednokrotne

Wszystko jest gotowe do przetestowania ustawień, aby sprawdzić, czy logowanie jednokrotne jest obsługiwane przez administratora.  

1. Otwórz ustawienia logowania jednokrotnego dla aplikacji. 
2. Przewiń do sekcji **Weryfikowanie logowania jednokrotnego przy użyciu <applicationName>** . W tym samouczku Ta sekcja jest nazywana **konfigurowaniem usługi GitHub-test**.
3. Wybierz pozycję **Testuj**. Zostaną wyświetlone opcje testowania.
4. Wybierz opcję **Zaloguj się jako bieżący użytkownik**. 

Jeśli logowanie powiedzie się, możesz przydzielić użytkowników i grupy do aplikacji SAML.
Jeśli zostanie wyświetlony komunikat o błędzie, wykonaj następujące czynności:

1. Skopiuj szczegóły i wklej je w polu **Jak wygląda błąd?** .

    ![Aby uzyskać wskazówki dotyczące rozwiązywania problemów, użyj pola "jak wygląda błąd"](media/configure-single-sign-on-portal/error-guidance.png)

1. Wybierz pozycję **Uzyskaj wskazówki dotyczące rozpoznawania**. Zostaną wyświetlone główna przyczyna i wskazówki dotyczące rozwiązywania.  W tym przykładzie użytkownik nie został przypisany do aplikacji.
1. Zapoznaj się ze wskazówkami dotyczącymi rozdzielczości, a następnie, jeśli to możliwe, usuń problem.
1. Uruchamiaj test ponownie, dopóki nie zostanie pomyślnie zakończony.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkowników lub grup do aplikacji](methods-for-assigning-users-and-groups.md)
- [Konfigurowanie automatycznego inicjowania obsługi konta użytkownika](../app-provisioning/configure-automatic-user-provisioning-portal.md)
