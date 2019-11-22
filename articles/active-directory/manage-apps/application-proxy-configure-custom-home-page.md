---
title: Niestandardowa strona główna dla opublikowanych aplikacji — serwer proxy aplikacji usługi Azure AD platformy Azure
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD
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
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275594"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ustaw niestandardową stronę główną dla opublikowanych aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób konfigurowania aplikacji w celu skierowania użytkownika do niestandardowej strony głównej. W przypadku publikowania aplikacji za pomocą serwera proxy aplikacji należy ustawić wewnętrzny adres URL, ale czasami nie jest to strona, która powinna być widoczna w pierwszej kolejności. Ustaw niestandardową stronę główną, aby użytkownik otrzymywał prawą stronę podczas uzyskiwania dostępu do aplikacji. Użytkownik zobaczy niestandardową stronę główną, bez względu na to, czy uzyskują dostęp do aplikacji z poziomu panelu dostępu Azure Active Directory lub uruchamiania aplikacji pakietu Office 365.

Gdy użytkownik uruchomi aplikację, jest domyślnie skierowana do adresu URL domeny głównej dla opublikowanej aplikacji. Strona docelowa jest zwykle ustawiana jako adres URL strony głównej. Przy użyciu modułu Azure AD PowerShell Zdefiniuj niestandardowy adres URL strony głównej, jeśli chcesz, aby użytkownik aplikacji znalazł się na określonej stronie w aplikacji.

Poniżej przedstawiono scenariusz, w którym wyjaśniono, dlaczego firma ustawi niestandardową stronę główną:

- W sieci firmowej użytkownik przechodzi do `https://ExpenseApp/login/login.aspx`, aby zalogować się i uzyskać dostęp do aplikacji.
- Ponieważ istnieją inne zasoby (takie jak obrazy), których serwer proxy aplikacji musi uzyskać dostęp na najwyższym poziomie struktury folderów, należy opublikować aplikację przy użyciu `https://ExpenseApp` jako wewnętrznego adresu URL.
- Domyślny zewnętrzny adres URL to `https://ExpenseApp-contoso.msappproxy.net`, który nie pobiera zewnętrznego użytkownika do strony logowania.
- Zamiast tego chcesz ustawić `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` jako adres URL strony głównej, dlatego użytkownik zewnętrzny zobaczy najpierw stronę logowania.

> [!NOTE]
> Po zapewnieniu użytkownikom dostępu do opublikowanych aplikacji, aplikacje są wyświetlane w [panelu dostępu usługi Azure AD](../user-help/my-apps-portal-end-user-access.md) i [uruchamianie aplikacji usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed ustawieniem adres URL strony głównej, należy przestrzegać następujących wymagań:

- Określona ścieżka musi być ścieżką poddomenową adresu URL domeny głównej.

  Na przykład jeśli adres URL domeny głównej jest `https://apps.contoso.com/app1/`, konfigurowany adres URL strony głównej musi rozpoczynać się od `https://apps.contoso.com/app1/`.

- Jeśli wprowadzisz zmiany w opublikowanej aplikacji, zmiana może zresetować wartość adres URL strony głównej. Po zaktualizowaniu aplikacji w przyszłości, należy przeprowadzić ponowne sprawdzenie i, jeśli to konieczne, zaktualizuj adres URL strony głównej.

Możesz ustawić adres URL strony głównej za pośrednictwem Azure Portal lub przy użyciu programu PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Zmień stronę główną w witrynie Azure portal

Aby zmienić adres URL strony głównej aplikacji za pomocą portalu usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) jako administrator.
1. Wybierz pozycję **Azure Active Directory**, a następnie **rejestracje aplikacji**. Zostanie wyświetlona lista zarejestrowanych aplikacji.
1. Wybierz aplikację z listy. Zostanie wyświetlona strona zawierająca szczegóły zarejestrowanej aplikacji.
1. W obszarze **Zarządzaj**wybierz opcję **znakowanie**.
1. Zaktualizuj **adres URL strony głównej** za pomocą nowej ścieżki.

   ![Strona znakowania dla zarejestrowanej aplikacji wyświetlającej pole adresu URL strony głównej](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Wybierz pozycję **Zapisz**.

## <a name="change-the-home-page-with-powershell"></a>Zmień stronę główną przy użyciu programu PowerShell

Aby skonfigurować stronę główną aplikacji przy użyciu programu PowerShell, należy wykonać następujące działania:

1. Zainstaluj moduł Azure AD PowerShell.
1. Znajdź wartość ObjectId aplikacji.
1. Zaktualizuj adres URL strony głównej aplikacji za pomocą poleceń programu PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu Azure AD PowerShell

Przed zdefiniowaniem adres URL niestandardowej strony głównej przy użyciu programu PowerShell, należy zainstalować moduł programu PowerShell usługi Azure AD. Pakiet można pobrać z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), który używa punktu końcowego interfejs API programu Graph.

Aby zainstalować pakiet, wykonaj następujące kroki:

1. Otwórz okno programu PowerShell standardowe, a następnie uruchom następujące polecenie:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Jeśli używasz polecenia jako użytkownik inny niż administrator, należy użyć `-scope currentuser` opcji.

1. Podczas instalacji wybierz pozycję **Y** , aby zainstalować dwa pakiety z NuGet.org. Oba pakiety są wymagane.

### <a name="find-the-objectid-of-the-app"></a>Znajdowanie identyfikatora obiektu aplikacji

Uzyskasz identyfikator ObjectId aplikacji, wyszukując aplikację według nazwy wyświetlanej lub strony głównej.

1. W tym samym oknie programu PowerShell Zaimportuj moduł programu Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Zaloguj się do modułu usługi Azure AD jako administrator dzierżawy.

   ```powershell
   Connect-AzureAD
   ```

1. Znajdź aplikację. Ten przykład używa programu PowerShell, aby znaleźć obiekt ObjectId, wyszukując aplikację przy użyciu nazwy wyświetlanej `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Powinna pojawić się wynik podobny do pokazanego poniżej. Skopiuj identyfikator GUID identyfikatora obiektu do użycia w następnej sekcji.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternatywnie można po prostu ściągnąć listę wszystkich aplikacji, przeszukać listę aplikacji z określoną nazwą wyświetlaną lub stroną główną, a następnie skopiować identyfikator ObjectId aplikacji po znalezieniu aplikacji.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Zaktualizuj adres URL strony głównej

Utwórz adres URL strony głównej i zaktualizuj aplikację przy użyciu tej wartości. Kontynuuj korzystanie z tego samego okna programu PowerShell lub jeśli używasz nowego okna programu PowerShell, zaloguj się ponownie do modułu usługi Azure AD przy użyciu `Connect-AzureAD`. Następnie wykonaj następujące kroki:

1. Utwórz zmienną do przechowywania wartości ObjectId skopiowanej w poprzedniej sekcji. (Zastąp wartość ObjectId użytą dla tego przykładu programu SharePoint wartością identyfikatora obiektu aplikacji).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Upewnij się, że masz poprawną aplikację, uruchamiając następujące polecenie. Dane wyjściowe powinny być identyczne z danymi wyjściowymi wyprowadzonymi w poprzedniej sekcji ([Znajdowanie identyfikatora objectid aplikacji](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Utwórz obiekt pusta aplikacja do przechowywania, które chcesz wprowadzić zmiany.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Ustaw adres URL strony głównej na żądaną wartość. Wartość musi być ścieżką poddomeny opublikowanej aplikacji. Na przykład, jeśli zmienisz adres URL strony głównej z `https://sharepoint-iddemo.msappproxy.net/` do `https://sharepoint-iddemo.msappproxy.net/hybrid/`, użytkownicy aplikacji przejść bezpośrednio do niestandardowej strony głównej.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Wprowadź aktualizację strony głównej.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Aby potwierdzić, że zmiana zakończyła się pomyślnie, uruchom ponownie następujące polecenie z kroku 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   W naszym przykładzie dane wyjściowe powinny teraz wyglądać następująco:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Uruchom ponownie aplikację, aby upewnić się, że Strona główna jest wyświetlana jako pierwszy ekran zgodnie z oczekiwaniami.

> [!NOTE]
> Wszelkie zmiany wprowadzone do aplikacji może zresetować adres URL strony głównej. Jeśli adres URL strony głównej resetuje, powtórz kroki opisane w tej sekcji, aby ustawić go ponownie.

## <a name="next-steps"></a>Następne kroki

- [Zdalny dostęp do programu SharePoint przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Samouczek: Dodawanie aplikacji lokalnej dla dostępu zdalnego przy użyciu serwera proxy aplikacji w Azure Active Directory](application-proxy-add-on-premises-application.md)