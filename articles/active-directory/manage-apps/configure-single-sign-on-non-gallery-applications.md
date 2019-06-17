---
title: Logowanie jednokrotne — aplikacje spoza galerii — platforma tożsamości firmy Microsoft | Dokumentacja firmy Microsoft
description: Konfigurowanie logowania jednokrotnego (SSO) do aplikacji spoza galerii w platformie tożsamości firmy Microsoft (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34e6257b4800387470cdc1b7d624bf3ebd1d3e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65989238"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Konfigurowanie logowania jednokrotnego do aplikacji spoza galerii w platformie tożsamości firmy Microsoft

Ten artykuł dotyczy funkcja, która umożliwia administratorom skonfigurowanie logowania jednokrotnego dla aplikacji w galerii aplikacji platformy tożsamości firmy Microsoft *bez konieczności pisania kodu*.

Jeśli zamiast tego szukasz wskazówki dla deweloperów o tym, jak zintegrować aplikacje niestandardowe z usługą Azure AD za pomocą kodu, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md).

Galerii aplikacji platformy tożsamości firmy Microsoft zawiera listę aplikacji, które są znane formularz logowania jednokrotnego z platformą Microsoft identity, pomocy technicznej zgodnie z opisem w [w tym artykule](what-is-single-sign-on.md). Po (jako IT specjalista lub system integratora w swojej organizacji) znalezieniu aplikacji, którą chcesz się połączyć, możesz rozpocząć zgodnie z instrukcjami krok po kroku przedstawiony w witrynie Azure portal umożliwia logowanie jednokrotne.

Następujące funkcje są również dostępne, zgodnie z umowy licencyjnej. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/active-directory/).

- Samoobsługowej integracji aplikacji, które używają nowoczesnego protokołu, takich jak [OpenId Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) do uwierzytelniania użytkowników i uzyskiwanie tokenów dla [programu Microsoft Graph](https://graph.microsoft.com).
- Samoobsługowa integracja dowolnej aplikacji, która obsługuje [Assertion Markup języka SAML (Security) w wersji 2.0](https://wikipedia.org/wiki/SAML_2.0) dostawcy tożsamości (zainicjowanego przez dostawcę usług lub inicjowane przez dostawcę tożsamości)
- Samoobsługowa integracja dowolnej aplikacji sieci web, który jest oparty na języku HTML strony logowania za pomocą [SSO oparte na hasłach](what-is-single-sign-on.md#password-based-sso)
- Samoobsługowe połączenia aplikacji używających [systemu dla protokołu Standard międzydomenowe Identity Management (SCIM) przypisywanie użytkowników](use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania łączy do dowolnej aplikacji w [uruchamianie aplikacji usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panelu dostępu usługi Azure AD](what-is-single-sign-on.md#linked-sign-on)

Te możliwości mogą obejmować samoobsługowa integracja oprogramowania jako aplikacja usługi (SaaS), którego używasz, nawet wtedy, gdy nikt nie ma dołączone jeszcze aplikacji w galerii aplikacji usługi Azure AD. Możliwość inną jest samoobsługowa integracja aplikacji sieci web innych firm, który Twoja organizacja została wdrożona do serwerów, które możesz kontrolować, zarówno w chmurze lub lokalnie.

Nazywane również *szablony integracji aplikacji*, te funkcje zapewniają punkty połączeń opartych na standardach aplikacji obsługujących SAML, Standard SCIM lub uwierzytelnianie oparte na formularzach. Możliwości obejmują elastyczne opcje i ustawienia dla zachowania zgodności z szerokim liczby aplikacji.

## <a name="adding-an-unlisted-application"></a>Dodawanie aplikacji nieznajdujących się na liście

Platforma tożsamości firmy Microsoft zapewnia dwa mechanizmy do rejestrowania aplikacji.

Aplikacja, która korzysta z nowoczesnego protokołu, takich jak [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) uwierzytelniać swoich użytkowników jest zarejestrowana przy użyciu [portalu rejestracji aplikacji](../develop/quickstart-register-app.md).

Do zarejestrowania aplikacji przy użyciu wszystkich pozostałych rodzajów [obsługiwanych mechanizmów uwierzytelniania](what-is-single-sign-on.md), takiej jak [SAML](../develop/single-sign-on-saml-protocol.md) protokołu, należy użyć **aplikacje dla przedsiębiorstw** bloku, aby połączyć je z platformą Microsoft identity.

Łączenie aplikacji nieznajdujących się na liście przy użyciu szablonu usługi integracji aplikacji, wykonaj następujące kroki:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora usługi Microsoft platformy tożsamości.
2. Wybierz **aplikacje dla przedsiębiorstw** > **nową aplikację**.
3. (Opcjonalne, ale zalecane) W **Dodaj z galerii** polu wyszukiwania, wprowadź nazwę wyświetlaną aplikacji. Jeśli aplikacja zostanie wyświetlona w wynikach wyszukiwania, zaznacz ją i Pomiń pozostałą część tej procedury.
4. Wybierz **aplikacji spoza galerii**. **Dodaj własną aplikację** zostanie wyświetlona strona.

   ![Dodawanie aplikacji](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Wprowadź nazwę wyświetlaną dla nowej aplikacji.
6. Wybierz pozycję **Dodaj**.

Dodawanie aplikacji w ten sposób, zapewnia podobne możliwości dostępne niż ten, który wstępnie zintegrowanych aplikacji. Najpierw wybierz **logowanie jednokrotne** z paska bocznego w aplikacji. Następnej strony (**wybierz jedną metodę logowania jednokrotnego**) przedstawia opcje dotyczące konfigurowania logowania jednokrotnego:

- **SAML**
- **Oparte na hasłach**
- **Połączone**

![Wybierz jedną metodę logowania jednokrotnego](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Aby uzyskać więcej informacji o tych opcjach zobacz następujące sekcje tego artykułu.

## <a name="saml-based-single-sign-on"></a>Opartej na SAML logowania jednokrotnego

Wybierz **SAML** opcję, aby skonfigurować uwierzytelnianie oparte na protokole SAML dla aplikacji. (Ta opcja wymaga, że aplikacja obsługuje SAML 2.0). **Ustaw się logowanie jednokrotne z SAML** zostanie wyświetlona strona.

![Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Ta strona zawiera pięć różnych nagłówki:

| Numer pozycji | Nazwa nagłówka | Aby uzyskać podsumowanie tej pozycji zobacz: |
| --- | --- | --- |
| 1 | **Konfiguracja podstawowa SAML** | [Wprowadź podstawową konfigurację protokołu SAML](#enter-basic-saml-configuration) |
| 2 | **Atrybuty użytkowników i oświadczeń** | [Przeglądanie lub dostosowywanie oświadczeń wystawionych w tokenie języka SAML](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **Certyfikat podpisywania SAML** | [Data wygaśnięcia certyfikatu przeglądu, stan i powiadomienia e-mail](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **Konfigurowanie \<Nazwa aplikacji >** | [Konfigurowanie aplikacji docelowej](#set-up-target-application) |
| 5 | **Testowanie logowania jednokrotnego przy użyciu \<Nazwa aplikacji >** | [Testowanie aplikacji SAML](#test-the-saml-application) |

Teraz można zbierać informacje dotyczące sposobu używania funkcji SAML aplikacji przed kontynuowaniem. Wykonaj poniższe sekcje, aby skonfigurować logowanie Jednokrotne między aplikacją i usługi Azure AD.

### <a name="enter-basic-saml-configuration"></a>Wprowadź podstawową konfigurację protokołu SAML

Aby skonfigurować usługę Azure AD, przejdź do **podstawową konfigurację protokołu SAML** nagłówek i wybierz jego **Edytuj** ikonę (ołówka). Można ręcznie wprowadzić wartości lub Przekaż plik metadanych do wyodrębnienia wartości pól.

![Podstawowa konfiguracja SAML](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Wymagane są następujące dwa pola:

- **Identyfikator**. Ta wartość musi jednoznacznie wskazywać na aplikację, dla których logowanie jednokrotne jest konfigurowane. Możesz odnaleźć tę wartość jako **wystawcy** element **AuthnRequest** (żądaniu SAML) wysłana przez aplikację. Ta wartość pojawia się również jako **identyfikator jednostki** w żadnych metadanych SAML udostępniany przez aplikację. W dokumentacji aplikacji SAML szczegółowe informacje na temat co jego **identyfikator jednostki** lub **odbiorców** wartość.

  Poniższy kod przedstawia sposób, w jaki **identyfikator** lub **wystawcy** pojawia się w żądaniu języka SAML, która aplikacja wysyła do usługi Azure AD:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **Adres URL odpowiedzi**. Adres URL odpowiedzi to, gdzie aplikacja oczekuje otrzymać SAML token. Ten adres URL jest również określany jako adres URL asercji klienta service (ACS). Sprawdź dokumentacji języka SAML aplikacji, aby uzyskać szczegółowe informacje na temat odpowiedzi tokenu SAML adres URL lub adres URL usługi ACS.

  Aby skonfigurować wiele adresów URL odpowiedzi, służy poniższy skrypt programu PowerShell.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Następujące trzy pola są opcjonalne:

- **Adres URL logowania (zainicjowanego przez dostawcę usług tylko)** . Ta wartość wskazuje, gdzie użytkownik przechodzi do logowania się do tej aplikacji. Jeśli aplikacja wykonuje zainicjowanego przez dostawcę usług rejestracji Jednokrotnej, następnie po użytkownik przejdzie do tego adresu URL PS nie konieczne przekierowanie do usługi Azure AD do uwierzytelniania i zalogować użytkownika. Jeśli określisz to pole, usługa Azure AD używa tego adresu URL do uruchomienia aplikacji z usługi Office 365 i stronie panelu dostępu usługi Azure AD. Jeśli to pole zostanie pominięty, usługi Azure AD zamiast tego wykonuje inicjowane przez dostawcę tożsamości logowania podczas uruchomień aplikacji z usługi Office 365, Panel dostępu usługi Azure AD lub adres URL logowania jednokrotnego usługi Azure AD (które można skopiować z **pulpit nawigacyjny** strony).

- **Stan przekazywania**. Stan przekazywania można określić w SAML w celu poinstruowania dokąd przekierowywać użytkowników po uwierzytelnieniu w aplikacji. Wartością jest zwykle adres URL lub adres URL ścieżki, która powoduje przejście do określonej lokalizacji w aplikacji.

- **Adres URL wylogowania**. Ta wartość jest używana do wysyłania odpowiedzi wylogowania protokołu SAML do aplikacji.

Aby uzyskać więcej informacji, zobacz [pojedynczego logowania jednokrotnego protokołu SAML](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Przeglądanie lub dostosowywanie oświadczeń wystawionych w tokenie języka SAML

Po użytkownik uwierzytelnia się do aplikacji, usługa Azure AD wystawia aplikacji tokenu SAML informacji (lub oświadczenia) o użytkowniku, który unikatowo identyfikuje je. Domyślnie informacje te obejmują nazwy użytkownika, adres e-mail, imię i nazwisko użytkownika.

Aby wyświetlić lub edytować oświadczenia wysyłane w tokenie języka SAML do aplikacji:

- Przejdź do **atrybutów użytkowników i oświadczeń** nagłówek i wybierz **Edytuj** ikony. **Atrybutów użytkowników i oświadczeń** zostanie wyświetlona strona.

![Atrybuty użytkowników i oświadczeń](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Może być konieczne edytowanie oświadczeń wystawionych w tokenie SAML dwóch powodów:

- Aplikacja wymaga innego zestawu identyfikatorów URI lub wartości oświadczeń.
- Aplikacja wymaga **nazwę wartości identyfikatora** uważają się coś innego niż nazwa użytkownika (znany także jako nazwa główna użytkownika) przechowywane na platformie tożsamości firmy Microsoft.

Aby uzyskać więcej informacji, zobacz [jak: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji korporacyjnych](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Data wygaśnięcia certyfikatu przeglądu, stan i powiadomienia e-mail

Po utworzeniu aplikacji spoza galerii lub w galerii usługi Azure AD tworzy certyfikat specyficzne dla aplikacji, która wygaśnie po trzech lat od daty utworzenia. Potrzebujesz tego certyfikatu do skonfigurowania zaufania między usługą Azure AD i aplikacji. Aby uzyskać szczegółowe informacje na temat formatu certyfikatu w dokumentacji aplikacji SAML.

Z usługi Azure AD, możesz pobrać aktywnego certyfikatu w formacie Base64 lub Raw bezpośrednio z poziomu głównego **Ustaw się logowanie jednokrotne z SAML** strony. Możesz też uzyskać aktywnego certyfikatu przez pobranie pliku XML metadanych aplikacji lub za pomocą adresu URL metadanych Federacji aplikacji.

Aby wyświetlić, utworzyć lub pobrać certyfikaty (aktywne lub nieaktywne), przejdź do **certyfikat podpisywania SAML** nagłówek i wybierz **Edytuj** ikony. **Certyfikat podpisywania SAML** pojawia się.

![Certyfikat podpisywania SAML](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Sprawdź, czy certyfikat ma:

- *Data wygaśnięcia żądaną.* Można skonfigurować datę wygaśnięcia do trzech lat w przyszłości.
- *Stan aktywny dla żądanego certyfikatu.* Jeśli stan jest **nieaktywny**, Zmień stan na **Active**. Aby zmienić stan, kliknij prawym przyciskiem myszy żądany certyfikat wiersza, a następnie wybierz **uaktywnić certyfikat**.
- *Właściwa opcja podpisywania i algorytm.*
- *Powiadomienie prawidłowe adresy e-mail.* Gdy aktywnego certyfikatu zbliża się data wygaśnięcia, usługi Azure AD wysyła powiadomienie na adres e-mail skonfigurowany w tym polu.  

Aby uzyskać więcej informacji, zobacz [zarządzać certyfikatami federacyjnego logowania jednokrotnego](manage-certificates-for-federated-single-sign-on.md) i [zaawansowane opcje w tokenie SAML podpisywanie certyfikatów](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Konfigurowanie aplikacji docelowej

Aby skonfigurować aplikację do logowania jednokrotnego, zlokalizuj dokumentacji aplikacji. Aby znaleźć w dokumentacji, przejdź do **Konfigurowanie \<Nazwa aplikacji >** nagłówek i wybierz **wyświetlić instrukcje krok po kroku**. Dokumentacja wyświetlana w **Konfigurowanie logowania jednokrotnego** strony. Ta strona zawiera wytyczne odnośnie wypełniania **adres URL logowania**, **usługi Azure AD identyfikator**, i **adres URL wylogowania** wartości w **Konfigurowanie \<nazwy aplikacji >** nagłówka.

Wymagane wartości różny w zależności od aplikacji. Aby uzyskać szczegółowe informacje Zobacz dokumentacji języka SAML aplikacji. **Adres URL logowania** i **adres URL wylogowania** wartości zarówno prowadzić do tego samego punktu końcowego, który jest punktem końcowym obsługiwanie żądań SAML dla swojego wystąpienia usługi Azure AD. **Usługi Azure AD identyfikator** jest wartością **wystawcy** w tokenie języka SAML wystawionym dla aplikacji.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Przypisywanie użytkowników i grup do aplikacji SAML

Po skonfigurowaniu aplikacji do użycia usługi Azure AD jako dostawcy tożsamości opartej na SAML jest już prawie gotowe do testowania. Jako formant zabezpieczeń usługi Azure AD tylko wystawia token użytkownika do logowania się do aplikacji, jeśli usługa Azure AD udzielił dostępu dla użytkownika. Użytkownicy mogą uzyskać dostęp, bezpośrednio lub za pośrednictwem członkostwa w grupie.

Aby przypisać nowego użytkownika lub grupy do aplikacji:

1. Na pasku bocznym aplikacji wybierz **użytkowników i grup**. **\<Nazwa aplikacji >-Użytkownicy i grupy** zostanie wyświetlona strona, która pokazuje bieżącą listę przypisanych użytkowników i grup.
2. Wybierz **dodawania użytkowników**. **Dodać przypisania** zostanie wyświetlona strona.
3. Wybierz **użytkowników i grup (\<numer > wybrany)** . **Użytkowników i grup** zostanie wyświetlona strona, wyświetlanie listy dostępnych użytkowników i grup.
4. Typ lub przewijania, aby znaleźć użytkownika lub grupę, którą chcesz przypisać z listy.
5. Wybierz użytkowników lub grupy, którą chcesz dodać, a następnie wybierz **wybierz** przycisku. **Użytkowników i grup** strony zniknie.
6. W **dodać przypisania** wybierz opcję **przypisać**. **<application name> — Użytkownicy i grupy** innym użytkownikom wyświetlanych na liście zostanie wyświetlona strona.

   ![Użytkownicy aplikacji i grupy](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

Z tej listy możesz wykonywać następujące czynności:

- Usuwa użytkownika.
- Edytuj ich roli.
- Zaktualizuj swoje poświadczenia (nazwę użytkownika i hasło), aby użytkownik mógł uwierzytelniać się w aplikacji z poziomu panelu dostępu użytkownika.

Może edytować lub usunąć wielu użytkowników lub grup w danym momencie.

Przypisanie użytkownika umożliwia usłudze Azure AD wystawić token użytkownika. Powoduje również kafelka dla tej aplikacji, które będą wyświetlane na panelu dostępu użytkownika. Kafelek aplikacji pojawia się również w module uruchamiania aplikacji usługi Office 365, jeśli użytkownik korzysta z usługi Office 365.

> [!NOTE]
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji.

### <a name="test-the-saml-application"></a>Testowanie aplikacji SAML

Przed testowaniem aplikacji SAML, konieczne jest posiadanie już skonfigurować aplikację w usłudze Azure AD oraz przypisać użytkowników lub grup do aplikacji. Aby przetestować aplikację SAML, wybierz pozycję **logowanie jednokrotne**, który wraca do **opartej na SAML logowania jednokrotnego** strony. (Jeśli inną metodę logowania jednokrotnego w praktyce, wybierz **zmianę trybów rejestracji jednokrotnej** > **SAML** zbyt.) Następnie w **przetestować logowanie jednokrotne za pomocą \<Nazwa aplikacji >** nagłówka, wybierz **testu**. Aby uzyskać więcej informacji, zobacz [opartej na SAML debugować logowanie jednokrotne do aplikacji w usłudze Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Hasło logowania jednokrotnego

Wybierz tę opcję, aby skonfigurować [opartego na hasłach logowania jednokrotnego](what-is-single-sign-on.md) dla aplikacji sieci web za pomocą strony logowania HTML. Logowanie Jednokrotne oparte na hasłach, określane również jako hasło vaulting, pozwala na zarządzanie dostępem użytkowników i hasła do aplikacji sieci web, które nie obsługują federacji tożsamości. Jest również przydatne w scenariuszach, w której kilka użytkownicy potrzebują do udostępnienia jednego konta, takie jak do kont aplikacji mediów społecznościowych organizacji.

Po wybraniu **opartego na hasłach**, zostanie wyświetlony monit wprowadź adres URL aplikacji sieci web stronę logowania.

![Na podstawie hasła logowania jednokrotnego](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Następnie wykonaj następujące kroki:

1. Wprowadź adres URL. Ten ciąg musi być stronie, która zawiera pole wprowadzania nazwy użytkownika.
2. Wybierz pozycję **Zapisz**. Usługa Azure AD próbuje przetworzyć strony logowania wprowadź nazwę użytkownika i hasło z danych wejściowych.
3. Usługi Azure AD przez analizowanie próba zakończy się niepowodzeniem, wybierz opcję **Konfiguruj \<Nazwa aplikacji > Ustawienia jednego hasła logowania** do wyświetlenia **Konfigurowanie logowania jednokrotnego** strony. (Jeśli próba zakończy się powodzeniem, można pominąć pozostałą część tej procedury).
4. Wybierz **ręcznie Wykryj pola logowania**. Dodatkowe instrukcje ręczne wykrywanie pól logowania są wyświetlane.

   ![Ręczne konfigurowanie opartego na hasłach logowania jednokrotnego](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Wybierz **Przechwyć pola logowania**. Zostanie otwarta strona stanu przechwytywania w nowej karcie, przedstawiający komunikat **Przechwytywanie metadanych jest obecnie w toku**.
6. Jeśli **wymagany do rozszerzenia dostęp do panelu** pole pojawia się w nowej karcie, wybierz **Zainstaluj teraz** zainstalował **Moje zabezpieczenia aplikacji logowania rozszerzenia** rozszerzenia przeglądarki. (Wymaga rozszerzenia przeglądarki Microsoft Edge, Chrome lub Firefox.) Następnie zainstaluj, uruchomić, włącz rozszerzenie i Odśwież stronę Przechwytywanie stanu.

   Rozszerzenie przeglądarki następnie otworzy inną kartę, która wyświetla wprowadzony adres URL.
7. Na karcie za pomocą wprowadzonego adresu URL przejść przez proces logowania. Wypełnij pola Nazwa użytkownika i hasło i spróbuj zalogować się. (Nie trzeba wprowadzić poprawne hasło).

   Wyświetlony monit z pytaniem, monit o zapisanie przechwycone pola logowania.
8. Kliknij przycisk **OK**. Zamknięcie karty, rozszerzenie przeglądarki aktualizacji na stronie stanu przechwytywania z komunikatem **metadane zostały zaktualizowane dla aplikacji**, a tej przeglądarki, na karcie również zostanie zamknięty.
9. W usłudze Azure AD **Konfigurowanie logowania jednokrotnego** wybierz opcję **dobrze, udało mi się zaloguj się na aplikację pomyślnym**.
10. Kliknij przycisk **OK**.

Po przechwytywania strony logowania, może przypisywać użytkowników i grup, a następnie możesz skonfigurować zasady poświadczeń, podobnie jak zwykłych [hasło logowania jednokrotnego aplikacji](what-is-single-sign-on.md).

> [!NOTE]
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji.

## <a name="existing-single-sign-on"></a>Istniejące logowanie jednokrotne

Wybierz tę opcję, aby dodać łącze do aplikacji w portalu panelu dostępu usługi Azure AD lub Office 365 Twojej organizacji. Można użyć tej metody można dodać łącza do aplikacji niestandardowe sieci web, które obecnie używają usług Active Directory Federation Services (lub innej usługi federacyjnej), zamiast usługi Azure AD do uwierzytelniania. Lub można dodać linków bezpośrednich do określonej strony programu SharePoint lub innych stron sieci web, które mają być wyświetlane w panelach dostępu użytkowników.

Po wybraniu **połączonej**, zostanie wyświetlony monit wpisz adres URL aplikacji, aby połączyć. Wpisz adres URL, a następnie wybierz pozycję **Zapisz**. Może przypisać użytkowników i grup do aplikacji, co powoduje, że są wyświetlane w aplikacji [uruchamianie aplikacji usługi Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) lub [panelu dostępu usługi Azure AD](end-user-experiences.md) dla tych użytkowników.

> [!NOTE]
> Możesz przekazać logo kafelka na potrzeby aplikacji przy użyciu **Przekaż Logo** znajdujący się na **Konfiguruj** kartę dla aplikacji.

## <a name="related-articles"></a>Pokrewne artykuły:

- [Instrukcje: Dostosowywanie oświadczeń wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw](../develop/active-directory-saml-claims-customization.md)
- [Debugowanie opartej na SAML logowania jednokrotnego do aplikacji w usłudze Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Platforma tożsamości firmy Microsoft (dawniej Azure Active Directory dla deweloperów)](../develop/index.yml)
