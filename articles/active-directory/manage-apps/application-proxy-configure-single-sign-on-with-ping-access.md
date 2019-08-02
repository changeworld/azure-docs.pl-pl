---
title: Uwierzytelnianie oparte na nagłówka z usługą PingAccess dla serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Publikowanie aplikacji z usługą PingAccess i serwera Proxy aplikacji obsługują uwierzytelnianie oparte na nagłówka.
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
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0544ed0ff217b6e37cca22a1fc1e0048b30da462
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694219"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Na podstawie nagłówka uwierzytelniania dla logowania jednokrotnego przy użyciu serwera Proxy aplikacji i PingAccess

Serwer proxy aplikacji usługi Azure Active Directory (Azure AD) został partner PingAccess, dzięki czemu klienci usługi Azure AD mogą uzyskiwać dostęp do większej liczby aplikacji. Rozwija PingAccess [istniejące oferty serwera Proxy aplikacji](application-proxy.md) obejmujący dostęp pojedynczego logowania jednokrotnego do aplikacji, które używające nagłówków na potrzeby uwierzytelniania.

## <a name="whats-pingaccess-for-azure-ad"></a>Co to jest PingAccess dla usługi Azure AD?

Dzięki usłudze PingAccess dla usługi Azure AD możesz udzielić użytkownikom dostępu i logowania jednokrotnego do aplikacji, które używają nagłówków do uwierzytelniania. Serwer Proxy aplikacji traktuje te aplikacje, takie jak każdy inny, za pomocą usługi Azure AD do uwierzytelniania dostępu i następnie przekazywanie ruchu za pośrednictwem usługi łącznika. PingAccess znajduje się przed aplikacjami i tłumaczy token dostępu z usługi Azure AD na nagłówek. Następnie aplikacja odbiera uwierzytelnianie w formacie, który może odczytać.

Użytkownicy nie będą zauważyć nic innego po zalogowaniu się do użycia aplikacje firmowe. One nadal pracować z dowolnego miejsca na dowolnym urządzeniu. Łączniki serwera proxy aplikacji kierują ruch zdalny do wszystkich aplikacji bez względu na ich typ uwierzytelniania, dzięki czemu będą nadal zrównoważać obciążenia automatycznie.

## <a name="how-do-i-get-access"></a>Jak uzyskać dostęp?

Ponieważ ten scenariusz pochodzi z partnerstwa między Azure Active Directory i PingAccess, potrzebujesz licencji dla obu usług. Jednak subskrypcje usługi Azure Active Directory — wersja Premium obejmują licencję PingAccess podstawowe, który obejmuje maksymalnie 20 aplikacji. Jeśli trzeba opublikować więcej niż 20 aplikacji opartej na nagłówkach, możesz kupić dodatkową licencję z usługą PingAccess.

Aby uzyskać więcej informacji, zobacz [Wersje usługi Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikuj swoje aplikacje na platformie Azure

Ten artykuł ma na celu opublikowanie aplikacji w tym scenariuszu po raz pierwszy. Oprócz szczegółów dotyczących etapów publikowania Przewodnik po wprowadzeniu do serwera proxy aplikacji i PingAccess. Jeśli skonfigurowano już obie usługi, ale chcesz, aby odświeżyć procedurę publikowania, przejdź do sekcji [Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy) .

> [!NOTE]
> Ponieważ ten scenariusz jest partnerstwa między usługą Azure AD i PingAccess, istnieją niektóre instrukcje w witrynie Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Zainstaluj łącznik serwera Proxy aplikacji

Jeśli włączono serwer proxy aplikacji włączony i został już zainstalowany łącznik, możesz pominąć tę sekcję i przejść do sekcji [Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji](#add-your-application-to-azure-ad-with-application-proxy).

Łącznik serwera proxy aplikacji to usługa systemu Windows Server, która kieruje ruch od pracowników zdalnych do opublikowanych aplikacji. Aby uzyskać bardziej szczegółowe instrukcje instalacji, [zobacz Samouczek: Dodaj aplikację lokalną dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md).

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji. Zostanie wyświetlona strona **Centrum administracyjne Azure Active Directory** .
1. Wybierz pozycję**Usługa łącznika pobierania** **serwera proxy** > aplikacji **Azure Active Directory** > . Zostanie wyświetlona strona **pobierania łącznika serwera proxy aplikacji** .

   ![Pobieranie łącznika serwera proxy aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Postępuj zgodnie z instrukcjami instalacji.

Pobranie łącznika powinno automatycznie włączyć serwer proxy aplikacji dla katalogu, ale jeśli nie, możesz wybrać opcję **Włącz serwer proxy aplikacji**.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Dodawanie aplikacji do usługi Azure AD za pomocą serwera proxy aplikacji

Istnieją dwie akcje, które należy wykonać w witrynie Azure portal. Najpierw musisz opublikować aplikację przy użyciu serwera Proxy aplikacji. Następnie należy zebrać pewne informacje dotyczące aplikacji, których można użyć podczas kroków PingAccess.

#### <a name="publish-your-application"></a>Publikowanie aplikacji

Najpierw musisz opublikować aplikację. Ta akcja obejmuje:

- Dodawanie aplikacji lokalnej do usługi Azure AD
- Przypisywanie użytkownika na potrzeby testowania aplikacji i wybierania rejestracji jednokrotnej opartej na nagłówkach
- Konfigurowanie adresu URL przekierowania aplikacji
- Przyznawanie uprawnień użytkownikom i innym aplikacjom do korzystania z aplikacji lokalnej

Aby opublikować własną aplikację lokalną:

1. Jeśli nie ma w ostatniej sekcji, zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz **pozycję aplikacje** > dla przedsiębiorstw**Nowa** > aplikacja**lokalna**aplikacji. Zostanie wyświetlona strona **Dodawanie własnej aplikacji lokalnej** .

   ![Dodaj własną aplikację lokalną](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Wypełnij pola wymagane informacjami o nowej aplikacji. Skorzystaj z poniższych wskazówek dotyczących ustawień.

   > [!NOTE]
   > Aby zapoznać się z bardziej szczegółowym opisem tego kroku, zobacz [Dodawanie aplikacji lokalnej do usługi Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Wewnętrzny adres URL**: Zazwyczaj podajesz adres URL, który przeprowadzi Cię do strony logowania aplikacji, gdy jesteś w sieci firmowej. W tym scenariuszu łącznik musi traktować serwer proxy PingAccess jako stronę frontonu aplikacji. Użyj tego formatu: `https://<host name of your PingAccess server>:<port>`. Numer portu to 3000 domyślnie, ale można go skonfigurować w PingAccess.

      > [!WARNING]
      > W przypadku tego typu logowania jednokrotnego wewnętrzny adres URL musi być używany `https` i nie może `http`używać.

   1. **Metoda uwierzytelniania wstępnego**: Wybierz **Azure Active Directory**.
   1. **Tłumaczenie adresu URL w nagłówkach**: Wybierz pozycję **nie**.

   > [!NOTE]
   > Jeśli jest to swoją pierwszą aplikację, należy użyć portu 3000 do uruchamiania i wróć, aby zaktualizować to ustawienie, jeśli zmienisz konfigurację PingAccess. W przypadku kolejnych aplikacji port będzie musiał odpowiadać odbiornikowi skonfigurowanemu w PingAccess. Dowiedz się więcej o [słuchaczy w PingAccess](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html).

1. Wybierz pozycję **Dodaj**. Zostanie wyświetlona strona przegląd nowej aplikacji.

Teraz Przypisz użytkownika do testowania aplikacji i wybierz pozycję Logowanie jednokrotne oparte na nagłówkach:

1. Na pasku bocznym aplikacji wybierz pozycję **Użytkownicy i grupy** > **Dodaj** > użytkowników**i grupę użytkowników\<(liczba > zaznaczona)** . Zostanie wyświetlona lista użytkowników i grup do wyboru.

   ![Pokazuje listę użytkowników i grup](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Wybierz użytkownika do testowania aplikacji, a następnie wybierz pozycję **Wybierz**. Upewnij się, że to konto testu ma dostęp do aplikacji w środowisku lokalnym.
1. Wybierz opcję **Przypisz**.
1. Na pasku bocznym aplikacji wybierz pozycję Rejestracja jednokrotna **na** > **podstawie nagłówka**.

   > [!TIP]
   > Jeśli po raz pierwszy przy użyciu opartej na nagłówkach logowania jednokrotnego, musisz zainstalować oprogramowanie PingAccess. Aby upewnić się, że Twoja subskrypcja platformy Azure jest automatycznie kojarzony z instalacji oprogramowanie PingAccess, użyj łącza w tym — strona logowanie jednokrotne można pobrać oprogramowanie PingAccess. Możesz teraz otworzyć witryny pobierania lub późniejszy powrót do tej strony.

   ![Pokazuje ekran logowania na podstawie nagłówka i PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Wybierz pozycję **Zapisz**.

Upewnij się, że adres URL przekierowania jest ustawiony na zewnętrzny adres URL:

1. Na pasku bocznym **Centrum administracyjnego Azure Active Directory** wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Wybierz łącze obok **identyfikatorów URI przekierowania**, pokazując liczbę identyfikatorów URI przekierowania skonfigurowanych dla klientów sieci Web i publicznych. Zostanie wyświetlona strona **nazwa aplikacji>—uwierzytelnianie.\<**
1. Sprawdź, czy zewnętrzny adres URL przypisany do aplikacji znajduje się na liście **identyfikatorów URI przekierowania** . Jeśli tak nie jest, Dodaj teraz zewnętrzny adres URL, używając typu identyfikatora URI przekierowania dla **sieci Web**, a następnie wybierz pozycję **Zapisz**.

Na koniec Skonfiguruj swoją aplikację lokalną, aby użytkownicy mieli dostęp do odczytu i do nich dostęp do odczytu i zapisu:

1. Na pasku bocznym **rejestracje aplikacji** aplikacji wybierz pozycję >  >  **uprawnienia interfejsu API** **Dodaj uprawnienia do** **Microsoft Graph** **interfejsów API** > firmy Microsoft. Zostanie wyświetlona strona **uprawnienia interfejsu API żądania** dla **Microsoft Graph** , która zawiera interfejsy api dla systemu Windows Azure Active Directory.

   ![Pokazuje stronę uprawnień interfejsu API żądania](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Wybierz pozycję **uprawnienia** > delegowane użytkownik**użytkownik** >  **. Odczytaj**.
1. Wybierz pozycję **uprawnienia** > aplikacjiaplikacjaaplikacja **. ReadWrite. wszystkie.**  > 
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Na stronie **uprawnienia interfejsu API** wybierz opcję **Przyznaj zgodę \<administratora na nazwę katalogu >** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Gromadzenie informacji o krokach PingAccess

Należy zebrać te trzy informacje (wszystkie identyfikatory GUID) w celu skonfigurowania aplikacji za pomocą PingAccess:

| Nazwa pola usługi Azure AD | Nazwa pola PingAccess | Format danych |
| --- | --- | --- |
| **Identyfikator aplikacji (klienta)** | **Identyfikator klienta** | Identyfikator GUID |
| **Identyfikator katalogu (dzierżawcy)** | **Issuer** | Identyfikator GUID |
| `PingAccess key` | **Client Secret (Wpis tajny klienta)** | Ciąg losowy |

Aby zebrać te informacje:

1. Na pasku bocznym **Centrum administracyjnego Azure Active Directory** wybierz pozycję **Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację. Zostanie wyświetlona strona **rejestracje aplikacji** aplikacji.

   ![Przegląd rejestracji dla aplikacji](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Obok pozycji wartość **identyfikatora aplikacji (klienta)** wybierz ikonę **Kopiuj do schowka** , a następnie skopiuj i Zapisz. Tę wartość można określić później jako identyfikator klienta PingAccess.
1. Następnie wybierz pozycję **Kopiuj do schowka**, a następnie skopiuj i Zapisz. Tę wartość można określić później jako wystawca PingAccess.
1. Na pasku bocznym **rejestracje aplikacji** aplikacji wybierz pozycję **Certyfikaty** > i wpisy tajne**Nowy wpis tajny klienta**. Zostanie wyświetlona strona **Dodawanie wpisu tajnego klienta** .

   ![Pokazuje stronę Dodawanie wpisu tajnego klienta](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. W polu **Opis**wpisz `PingAccess key`.
1. Wobszarze wygaśnięcie wybierz sposób ustawienia klucza PingAccess: **Za 1 rok**, **w ciągu 2 lat**lub **nigdy**.
1. Wybierz pozycję **Dodaj**. Klucz PingAccess jest wyświetlany w tabeli wpisów tajnych klienta z losowym ciągiem, który jest wypełniany w polu **wartość** .
1. Obok pola **wartość** klucza PingAccess wybierz ikonę **Kopiuj do schowka** , a następnie skopiuj i Zapisz. Tę wartość można określić później jako klucz tajny klienta PingAccess.

### <a name="update-graphapi-to-send-custom-fields-optional"></a>Aktualizacja GraphAPI do wysyłania pól niestandardowych (opcjonalnie)

Jeśli potrzebujesz niestandardowego żądania, które wysyła inne tokeny w ramach access_token używanych przez PingAccess, należy ustawić `acceptMappedClaims` pole aplikacji na `True`. Aby wprowadzić tę zmianę, można użyć Eksploratora grafów lub manifestu aplikacji portalu usługi Azure AD.

**W tym przykładzie użyto Eksploratora wykresów:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**W tym przykładzie do aktualizowania `acceptMappedClaims` pola służy [Portal Azure Active Directory](https://aad.portal.azure.com/) :**

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) jako administrator aplikacji.
1. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji**. Zostanie wyświetlona lista aplikacji.
1. Wybierz aplikację.
1. Na pasku bocznym strony **rejestracje aplikacji** aplikacji wybierz pozycję **manifest**. Zostanie wyświetlony kod JSON manifestu dla rejestracji aplikacji.
1. Wyszukaj pole i zmień wartość na `True`. `acceptMappedClaims`
1. Wybierz pozycję **Zapisz**.

### <a name="use-of-optional-claims-optional"></a>Użycie opcjonalnych oświadczeń (opcjonalnie)

Opcjonalne oświadczenia umożliwiają dodawanie niestandardowych oświadczeń, które nie są uwzględniane przez wszystkich użytkowników i dzierżawców. Można skonfigurować opcjonalne oświadczenia dla aplikacji, modyfikując manifest aplikacji. Aby uzyskać więcej informacji, zobacz [artykuł Omówienie manifestu aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Przykład, aby uwzględnić adres e-mail w access_token, który będzie zużywał PingAccess:
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

### <a name="use-of-claims-mapping-policy-optional"></a>Użycie zasad mapowania oświadczeń (opcjonalnie)

[Zasady mapowania oświadczeń (wersja zapoznawcza)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) dla atrybutów, które nie istnieją w AzureAD. Mapowanie oświadczeń umożliwia migrowanie starych aplikacji Premium do chmury przez dodawanie dodatkowych oświadczeń niestandardowych, które są obsługiwane przez usługi ADFS lub obiekty użytkownika

Aby aplikacja korzystała z oświadczenia niestandardowego i zawierać dodatkowe pola, upewnij się, że utworzono również [niestandardowe zasady mapowania oświadczeń i przypisano je do aplikacji](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Aby użyć oświadczenia niestandardowego, musi również mieć zasad niestandardowych, zdefiniowanych i przypisane do aplikacji. Ta zasada powinna zawierać wszystkie wymagane atrybutów niestandardowych.
>
> Możesz wykonywać definicje zasad i przypisywać je za pomocą programu PowerShell, Eksploratora Azure AD Graph lub Microsoft Graph. Jeśli wykonujesz je w programie PowerShell, możesz najpierw użyć `New-AzureADPolicy` , a następnie przypisać go do aplikacji za pomocą `Add-AzureADServicePrincipalPolicy`polecenia. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad mapowania oświadczeń](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

Przykład:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Włącz PingAccess do korzystania z oświadczeń niestandardowych

Włączenie PingAccess do korzystania z oświadczeń niestandardowych jest opcjonalne, ale wymagane, jeśli oczekujesz, że aplikacja korzysta z dodatkowych oświadczeń.

Gdy skonfigurujesz PingAccess w poniższym kroku, sesja sieci Web, którą utworzysz (Ustawienia > dostępu > sesji sieci Web) musi mieć **profil żądania** , a następnie **Odśwież atrybuty użytkownika** ustawione na wartość **nie**

## <a name="download-pingaccess-and-configure-your-application"></a>Pobieranie PingAccess i Konfigurowanie aplikacji

Teraz, że ukończono wszystkie kroki konfiguracji usługi Azure Active Directory, możesz przejść do konfigurowania PingAccess.

Szczegółowe instrukcje dotyczące części PingAccess tego scenariusza są kontynuowane w dokumentacji ping Identity. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie PingAccess dla usługi Azure AD, aby chronić aplikacje opublikowane przy użyciu Microsoft Azure AD serwera proxy aplikacji](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) w witrynie sieci Web usługi ping Identity.

Te kroki ułatwiają zainstalowanie PingAccess i skonfigurowanie konta PingAccess (jeśli jeszcze go nie masz). Następnie w celu utworzenia połączenia usługi Azure AD OpenID Connect Connect (OIDC) można skonfigurować dostawcę tokenów z wartością **identyfikatora katalogu (dzierżawa)** skopiowaną z portalu usługi Azure AD. Następnie, aby utworzyć sesję sieci Web w programie PingAccess, należy użyć **identyfikatora aplikacji** i `PingAccess key` wartości. Po tym można Konfigurowanie mapowania tożsamości i utworzyć hostów wirtualnych, witryn i aplikacji.

### <a name="test-your-application"></a>Testowanie aplikacji

Po wykonaniu wszystkich powyższych czynności aplikacja powinna być uruchomiona. Aby go przetestować, Otwórz przeglądarkę i przejdź do zewnętrznego adresu URL, który został utworzony podczas publikowania aplikacji na platformie Azure. Zaloguj się przy użyciu konta testowego, które zostało przypisane do aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie PingAccess usługi Azure AD do ochrony aplikacji publikowanych za pomocą serwera proxy aplikacji Microsoft Azure AD](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](what-is-single-sign-on.md)
- [Rozwiązywanie problemów z serwera Proxy aplikacji i komunikaty o błędach](application-proxy-troubleshoot.md)
