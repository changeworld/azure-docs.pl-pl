---
title: Uwierzytelnianie oparte na nagłówku za pomocą usługi PingAccess dla serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Publikuj aplikacje z pingaccess i serwerem proxy aplikacji do obsługi uwierzytelniania opartego na nagłówku.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367986"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Uwierzytelnianie oparte na nagłówku dla logowania jednokrotnego za pomocą serwera proxy aplikacji i usługi PingAccess

Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) nawiązał współpracę z usługą PingAccess, dzięki czemu klienci usługi Azure AD mogą uzyskiwać dostęp do większej liczby aplikacji. PingAccess rozszerza [istniejące oferty serwera proxy aplikacji,](application-proxy.md) aby uwzględnić dostęp do logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania.

## <a name="whats-pingaccess-for-azure-ad"></a>Co to jest usługa PingAccess dla usługi Azure AD?

Za pomocą usługi PingAccess dla usługi Azure AD można przyznać użytkownikom dostęp i logowanie jednokrotne (SSO) do aplikacji, które używają nagłówków do uwierzytelniania. Serwer proxy aplikacji traktuje te aplikacje jak wszystkie inne, używając usługi Azure AD do uwierzytelniania dostępu, a następnie przekazywania ruchu za pośrednictwem usługi łącznika. PingAccess znajduje się przed aplikacjami i tłumaczy token dostępu z usługi Azure AD do nagłówka. Następnie aplikacja odbiera uwierzytelnianie w formacie, który może odczytać.

Użytkownicy nie zauważą niczego innego, gdy zalogują się, aby korzystać z aplikacji firmowych. Mogą nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera proxy aplikacji kierują ruch zdalny do wszystkich aplikacji bez względu na ich typ uwierzytelniania, dzięki czemu nadal będą równoważyć obciążenia automatycznie.

## <a name="how-do-i-get-access"></a>Jak uzyskać dostęp?

Ponieważ ten scenariusz pochodzi z partnerstwa między usługą Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure Active Directory Premium obejmują podstawową licencję PingAccess, która obejmuje maksymalnie 20 aplikacji. Jeśli chcesz opublikować więcej niż 20 aplikacji opartych na nagłówku, możesz kupić dodatkową licencję od PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikowanie aplikacji na platformie Azure

Ten artykuł jest dla osób do opublikowania aplikacji z tego scenariusza po raz pierwszy. Oprócz szczegółowości kroki publikowania, prowadzi cię w rozpoczęciu pracy z proxy aplikacji i PingAccess. Jeśli masz już skonfigurowane obie usługi, ale chcesz odświeżać kroki publikowania, przejdź do [dodaj aplikację do usługi Azure AD](#add-your-application-to-azure-ad-with-application-proxy) sekcji.

> [!NOTE]
> Ponieważ ten scenariusz jest partnerstwo między usługi Azure AD i PingAccess, niektóre instrukcje istnieją w witrynie tożsamości ping.

### <a name="install-an-application-proxy-connector"></a>Instalowanie łącznika serwera proxy aplikacji

Jeśli włączony serwer proxy aplikacji został już włączony i zainstalowany łącznik, możesz pominąć tę sekcję i przejść do [dodaj aplikację do usługi Azure AD za pomocą serwera proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy).

Łącznik serwera proxy aplikacji to usługa systemu Windows Server, która kieruje ruch pracowników zdalnych do opublikowanych aplikacji. Aby uzyskać bardziej szczegółowe instrukcje instalacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji. Zostanie wyświetlona strona **centrum administracyjnego usługi Azure Active Directory.**
1. Wybierz usługę pobierania**łącznika****serwera proxy** > aplikacji **usługi Azure Active Directory** > . Zostanie wyświetlona strona **Pobierania łącznika serwera proxy aplikacji.**

   ![Pobieranie łącznika serwera proxy aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postępuj zgodnie z instrukcjami instalacji.

Pobieranie łącznika powinno automatycznie włączyć serwer proxy aplikacji dla twojego katalogu, ale jeśli nie, można wybrać **włącz serwer proxy aplikacji**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji

Istnieją dwie akcje, które należy wykonać w witrynie Azure portal. Najpierw należy opublikować aplikację za pomocą serwera proxy aplikacji. Następnie należy zebrać pewne informacje o aplikacji, które można użyć podczas pingaccess kroki.

#### <a name="publish-your-application"></a>Publikowanie aplikacji

Najpierw musisz opublikować aplikację. Działanie to obejmuje:

- Dodawanie aplikacji lokalnej do usługi Azure AD
- Przypisywanie użytkownika do testowania aplikacji i wybieranie identyfikatora samouszczącego opartego na nagłówku
- Konfigurowanie adresu URL przekierowania aplikacji
- Przyznawanie użytkownikom i innym aplikacjom uprawnień do korzystania z aplikacji lokalnej

Aby opublikować własną aplikację lokalną:

1. Jeśli nie w ostatniej sekcji, zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz **aplikacje przedsiębiorstwa** > **Nowa aplikacja** > Dodaj aplikację**lokalną**. Zostanie wyświetlona strona **Dodaj własną aplikację lokalną.**

   ![Dodawanie własnej aplikacji lokalnej](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Wypełnij wymagane pola informacjami o nowej aplikacji. Skorzystaj z poniższych wskazówek dotyczących ustawień.

   > [!NOTE]
   > Aby uzyskać bardziej szczegółowe wskazówki dotyczące tego kroku, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD.](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)

   1. **Wewnętrzny adres URL:** zwykle podajesz adres URL, który prowadzi do strony logowania aplikacji, gdy jesteś w sieci firmowej. W tym scenariuszu łącznik musi traktować serwer proxy PingAccess jako główną stronę aplikacji. Użyj tego `https://<host name of your PingAccess server>:<port>`formatu: . Port jest domyślnie 3000, ale można go skonfigurować w PingAccess.

      > [!WARNING]
      > W przypadku logowania jednokrotnego wewnętrzny adres `https` URL musi być `http`używany i nie może go używać .

   1. **Metoda wstępnego uwierzytelniania:** wybierz pozycję **Azure Active Directory**.
   1. **Przetłumacz adres URL w nagłówkach**: Wybierz **pozycję Nie**.

   > [!NOTE]
   > Jeśli jest to twoja pierwsza aplikacja, użyj portu 3000, aby rozpocząć i wrócić, aby zaktualizować to ustawienie, jeśli zmienisz konfigurację usługi PingAccess. W przypadku kolejnych aplikacji port będzie musiał być zgodny z odbiornikiem skonfigurowanym w ucho. Dowiedz się więcej o [słuchaczach w PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Wybierz pozycję **Dodaj**. Zostanie wyświetlona strona przeglądu nowej aplikacji.

Teraz przypisz użytkownika do testowania aplikacji i wybierz logowanie jednookie i jednookie:

1. Na pasku bocznym aplikacji wybierz **pozycję Użytkownicy i grupy** > **Dodaj użytkowników** > **i grupy (Liczba\<> Wybrana).** Pojawi się lista użytkowników i grup do wyboru.

   ![Pokazuje listę użytkowników i grup](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Wybierz użytkownika do testowania aplikacji i wybierz **wybierz wybierz**. Upewnij się, że to konto testowe ma dostęp do aplikacji lokalnej.
1. Wybierz opcję **Przypisz**.
1. Na pasku bocznym aplikacji wybierz **pozycję Logowanie jednokrotne oparte na** > **nagłówku**.

   > [!TIP]
   > Jeśli po raz pierwszy używasz logowania jednokrotnego opartego na nagłówku, musisz zainstalować usługę PingAccess. Aby upewnić się, że subskrypcja platformy Azure jest automatycznie skojarzona z instalacją usługi PingAccess, użyj łącza na tej stronie logowania jednokrotnego, aby pobrać usługę PingAccess. Możesz teraz otworzyć witrynę pobierania lub wrócić do tej strony później.

   ![Pokazuje ekran logowania oparty na nagłówku i PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Wybierz **pozycję Zapisz**.

Następnie upewnij się, że adres URL przekierowania jest ustawiony na zewnętrzny adres URL:

1. Na **pasku bocznym centrum administracyjnego usługi Azure Active Directory** wybierz pozycję**Rejestracje aplikacji** **usługi Azure Active Directory** > . Pojawi się lista aplikacji.
1. Wybierz aplikację.
1. Wybierz łącze obok **pozycji Przekierowanie identyfikatorów URI**, pokazujące liczbę identyfikatorów URI przekierowania skonfigurowanych dla klientów sieci Web i klientów publicznych. Zostanie ** \<wyświetlona strona> nazwa aplikacji — uwierzytelnianie.**
1. Sprawdź, czy zewnętrzny adres URL przypisany do aplikacji wcześniej znajduje się na liście **Przekierowanie identyfikatorów URI.** Jeśli tak nie jest, dodaj teraz zewnętrzny adres URL, używając przekierowywania typu URI **sieci Web**i wybierz pozycję **Zapisz**.

Na koniec skonfiguruj aplikację lokalną, aby użytkownicy mieli dostęp do odczytu, a inne aplikacje miały dostęp do odczytu/zapisu:

1. Na pasku bocznym **rejestracji aplikacji** wybierz pozycję Uprawnienia interfejsu API Dodaj **uprawnienie** > **Add a permission** > **Interfejsy** > API firmy**Microsoft Microsoft Graph**. Zostanie **wyświetlona** strona Uprawnienia interfejsu API żądania dla programu **Microsoft Graph** zawierająca interfejsy API usługi Windows Azure Active Directory.

   ![Pokazuje stronę Żądania uprawnień interfejsu API](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Wybierz **delegowane** > uprawnienia**Użytkownik.Read****User** > .
1. Wybierz **uprawnienie** > **Application** > aplikacji**Application Application.ReadWrite.All**.
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Na stronie **Uprawnienia interfejsu API** wybierz pozycję **Udzielaj zgody administratora \<dla nazwy katalogu>**.

#### <a name="collect-information-for-the-pingaccess-steps"></a>Zbieranie informacji o krokach PingAccess

Aby skonfigurować aplikację za pomocą usługi PingAccess, należy zebrać te trzy informacje (wszystkie identyfikatory GUID):

| Nazwa pola usługi Azure AD | Nazwa pola PingAccess | Format danych |
| --- | --- | --- |
| **Identyfikator aplikacji (klienta)** | **Client ID (Identyfikator klienta)** | Identyfikator GUID |
| **Identyfikator katalogu (dzierżawcy)** | **Wystawca** | Identyfikator GUID |
| `PingAccess key` | **Client Secret (Wpis tajny klienta)** | Losowy ciąg znaków |

Aby zebrać te informacje:

1. Na **pasku bocznym centrum administracyjnego usługi Azure Active Directory** wybierz pozycję**Rejestracje aplikacji** **usługi Azure Active Directory** > . Pojawi się lista aplikacji.
1. Wybierz aplikację. Zostanie wyświetlona strona **Rejestracje** aplikacji.

   ![Przegląd rejestracji dla aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Obok wartości **identyfikatora aplikacji (klienta)** wybierz ikonę **Kopiuj do schowka,** a następnie skopiuj ją i zapisz. Tę wartość należy określić później jako identyfikator klienta usługi PingAccess.
1. Następnie wartość **identyfikatora katalogu (dzierżawy)** wybierz również **pozycję Kopiuj do schowka,** a następnie skopiuj i zapisz. Tę wartość należy określić później jako wystawca PingAccess.
1. Z paska bocznego **rejestracji aplikacji** dla aplikacji wybierz pozycję **Certyfikaty i wpisy tajne** > **Nowy klucz tajny klienta**. Zostanie wyświetlona strona **Dodaj klucz tajny klienta.**

   ![Pokazuje stronę Dodaj klucz tajny klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. W **opisie**wpisz `PingAccess key`.
1. W obszarze **Wygasa**wybierz sposób ustawiania klucza PingAccess: **W ciągu 1 roku**, W ciągu **2 lat**lub **Nigdy.**
1. Wybierz pozycję **Dodaj**. Klucz PingAccess pojawia się w tabeli wpisów tajnych klienta z losowym ciągiem, który automatycznie wypełnia się w polu **WARTOŚĆ.**
1. Obok pola **WARTOŚĆ** klucza PingAccess wybierz ikonę **Kopiuj do schowka,** a następnie skopiuj ją i zapisz. Tę wartość należy określić później jako klucz tajny klienta PingAccess.

**Zaktualizuj `acceptMappedClaims` pole:**

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz**rejestracje aplikacji** **usługi Azure Active Directory** > . Pojawi się lista aplikacji.
1. Wybierz aplikację.
1. Na pasku bocznym strony **Rejestracje aplikacji** dla aplikacji wybierz pozycję **Manifest**. Pojawi się kod JSON manifestu dla rejestracji aplikacji.
1. Wyszukaj `acceptMappedClaims` pole i zmień `True`wartość na .
1. Wybierz **pozycję Zapisz**.

### <a name="use-of-optional-claims-optional"></a>Korzystanie z oświadczeń opcjonalnych (opcjonalnie)

Oświadczenia opcjonalne umożliwia dodawanie standardowych, ale nie-zawiera domyślne oświadczenia, że każdy użytkownik i dzierżawca ma. Można skonfigurować oświadczenia opcjonalne dla aplikacji, modyfikując manifest aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł opisu manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Przykład do access_token, który będzie zużywał pingaccess:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Korzystanie z zasad mapowania oświadczeń (opcjonalnie)

[Zasady mapowania oświadczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) dla atrybutów, które nie istnieją w usłudze AzureAD. Mapowanie oświadczeń umożliwia migrację starych aplikacji przedpremierowych do chmury przez dodanie dodatkowych oświadczeń niestandardowych, które są wspierane przez usługę ADFS lub obiekty użytkownika

Aby aplikacja używała oświadczenia niestandardowego i zawierała dodatkowe pola, upewnij się, że [utworzono również niestandardowe zasady mapowania oświadczeń i przypisano je do aplikacji.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

> [!NOTE]
> Aby użyć oświadczenia niestandardowego, musisz również mieć zdefiniowane zasady niestandardowe i przypisane do aplikacji. Ta zasada powinna zawierać wszystkie wymagane atrybuty niestandardowe.
>
> Definicję i przypisanie zasad można wykonywać za pomocą programu PowerShell lub microsoft graph. Jeśli robisz je w programie PowerShell, może `New-AzureADPolicy` być konieczne najpierw użycie, `Add-AzureADServicePrincipalPolicy`a następnie przypisanie go do aplikacji za pomocą programu . Aby uzyskać więcej informacji, zobacz [Przypisywanie zasad mapowania oświadczeń](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Przykład:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Włącz funkcję PingAccess w celu używania oświadczeń niestandardowych

Włączenie funkcji PingAccess do używania oświadczeń niestandardowych jest opcjonalne, ale wymagane, jeśli oczekujesz, że aplikacja będzie korzystać z dodatkowych oświadczeń.

Podczas konfigurowania programu PingAccess w następnym kroku zostanie utworzona sesja sieci Web (Ustawienia >sesje sieci Web programu Access->) musi mieć odznaczony **profil żądania** i **odświeżanie atrybutów użytkownika ustawionych** na **Nie**

## <a name="download-pingaccess-and-configure-your-application"></a>Pobierz aplikację PingAccess i skonfiguruj aplikację

Po wykonaniu wszystkich kroków konfiguracji usługi Azure Active Directory można przejść do konfigurowania usługi PingAccess.

Szczegółowe kroki dla PingAcces część tego scenariusza nadal w dokumentacji tożsamości ping. Postępuj zgodnie z instrukcjami w [konfigurowanie usługi PingAccess dla usługi Azure AD, aby chronić aplikacje opublikowane przy użyciu serwera proxy aplikacji usługi Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) w witrynie sieci Web tożsamości ping.

Te kroki pomagają zainstalować PingAccess i skonfigurować konto PingAccess (jeśli jeszcze go nie masz). Następnie, aby utworzyć połączenie usługi Azure AD OpenID Connect (OIDC), należy skonfigurować dostawcę tokenu o wartości **identyfikatora katalogu (dzierżawy),** który został skopiowany z portalu usługi Azure AD. Następnie, aby utworzyć sesję sieci web w u pingaccess, `PingAccess key` należy użyć **identyfikatora aplikacji (klienta)** i wartości. Następnie można skonfigurować mapowanie tożsamości i utworzyć wirtualny host, witrynę i aplikację.

### <a name="test-your-application"></a>Testowanie aplikacji

Po wykonaniu wszystkich tych kroków aplikacja powinna być uruchomiona. Aby go przetestować, otwórz przeglądarkę i przejdź do zewnętrznego adresu URL utworzonego podczas publikowania aplikacji na platformie Azure. Zaloguj się przy tym przy tym konto testowe przypisane do aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie usługi PingAccess dla usługi Azure AD w celu ochrony aplikacji opublikowanych przy użyciu serwera proxy aplikacji usługi Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i komunikatów o błędach](application-proxy-troubleshoot.md)
