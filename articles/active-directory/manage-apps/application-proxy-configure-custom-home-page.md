---
title: Niestandardowa strona główna opublikowanych aplikacji — serwer proxy aplikacji usługi Azure AD
description: Obejmuje podstawowe informacje o łącznikach serwera proxy aplikacji usługi Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275594"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Ustawianie niestandardowej strony głównej dla opublikowanych aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD

W tym artykule omówiono sposób konfigurowania aplikacji w celu kierowania użytkownika do niestandardowej strony głównej. Podczas publikowania aplikacji za pomocą serwera proxy aplikacji, można ustawić wewnętrzny adres URL, ale czasami nie jest to strona użytkownik powinien zobaczyć jako pierwszy. Ustaw niestandardową stronę główną, aby użytkownik pobierał właściwą stronę podczas uzyskiwania dostępu do aplikacji. Użytkownik zobaczy ustawioną niestandardową stronę główną, niezależnie od tego, czy uzyskuje dostęp do aplikacji z Panelu dostępu usługi Azure Active Directory, czy z programu Uruchamianie aplikacji usługi Office 365.

Gdy użytkownik uruchomi aplikację, jest on domyślnie kierowany do adresu URL domeny głównej opublikowanej aplikacji. Strona docelowa jest zazwyczaj ustawiona jako adres URL strony głównej. Użyj modułu programu Azure AD PowerShell, aby zdefiniować niestandardowy adres URL strony głównej, gdy chcesz, aby użytkownik aplikacji wylądował na określonej stronie w aplikacji.

Oto jeden scenariusz, który wyjaśnia, dlaczego Twoja firma ustawi niestandardową stronę główną:

- W sieci firmowej użytkownik `https://ExpenseApp/login/login.aspx` przechodzi do logowania się i uzyskiwania dostępu do aplikacji.
- Ponieważ masz inne zasoby (takie jak obrazy), które serwer proxy aplikacji musi uzyskać dostęp na `https://ExpenseApp` najwyższym poziomie struktury folderów, publikujesz aplikację jako wewnętrzny adres URL.
- Domyślnym zewnętrznym `https://ExpenseApp-contoso.msappproxy.net`adresem URL jest adres , który nie zabiera użytkownika zewnętrznego na stronę logowania.
- Zamiast tego `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` chcesz ustawić jako adres URL strony głównej, aby użytkownik zewnętrzny najpierw zobaczył stronę logowania.

> [!NOTE]
> Gdy przyznasz użytkownikom dostęp do opublikowanych aplikacji, aplikacje są wyświetlane w [Panelu dostępu usługi Azure AD](../user-help/my-apps-portal-end-user-access.md) i w programie Office [365 .](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed ustawieniem adresu URL strony głównej należy pamiętać o następujących wymaganiach:

- Określona ścieżka musi być ścieżką poddomeny adresu URL domeny głównej.

  Jeśli na przykład adres URL `https://apps.contoso.com/app1/`domeny głównej to adres URL strony `https://apps.contoso.com/app1/`głównej, który konfigurujesz, musi zaczynać się od pliku .

- Jeśli zmienisz opublikowaną aplikację, zmiana może zresetować wartość adresu URL strony głównej. Po zaktualizowaniu aplikacji w przyszłości należy ponownie sprawdzić i, jeśli to konieczne, zaktualizować adres URL strony głównej.

Adres URL strony głównej można ustawić za pośrednictwem witryny Azure portal lub za pomocą programu PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Zmienianie strony głównej w witrynie Azure Portal

Aby zmienić adres URL strony głównej aplikacji za pośrednictwem portalu usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator.
1. Wybierz **pozycję Azure Active Directory**, a następnie **rejestracje aplikacji**. Pojawi się lista zarejestrowanych aplikacji.
1. Wybierz aplikację z listy. Zostanie wyświetlona strona ze szczegółami zarejestrowanej aplikacji.
1. W obszarze **Zarządzaj**wybierz pozycję **Znakowanie**.
1. Zaktualizuj **adres URL strony głównej** o nową ścieżkę.

   ![Strona znakowania zarejestrowanej aplikacji z polem URL strony głównej](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Wybierz **pozycję Zapisz**.

## <a name="change-the-home-page-with-powershell"></a>Zmienianie strony głównej za pomocą programu PowerShell

Aby skonfigurować stronę główną aplikacji przy użyciu programu PowerShell, należy:

1. Zainstaluj moduł programu Azure AD PowerShell.
1. Znajdź objectid wartość aplikacji.
1. Zaktualizuj adres URL strony głównej aplikacji za pomocą poleceń programu PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu programu Azure AD PowerShell

Przed zdefiniowaniem niestandardowego adresu URL strony głównej przy użyciu programu PowerShell należy zainstalować moduł programu Azure AD PowerShell.Pakiet można pobrać z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), która używa punktu końcowego interfejsu API wykresu.

Aby zainstalować pakiet, wykonaj następujące kroki:

1. Otwórz standardowe okno programu PowerShell, a następnie uruchom następujące polecenie:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Jeśli używasz polecenia jako nie-admin, użyj `-scope currentuser` tej opcji.

1. Podczas instalacji wybierz **opcję Y,** aby zainstalować dwa pakiety z Nuget.org. Oba pakiety są wymagane.

### <a name="find-the-objectid-of-the-app"></a>Znajdowanie identyfikatora obiektu aplikacji

Otrzymasz ObjectId aplikacji, wyszukując aplikację według jej nazwy wyświetlanej lub strony głównej.

1. W tym samym oknie programu PowerShell zaimportuj moduł usługi Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

1. Zaloguj się do modułu usługi Azure AD jako administrator dzierżawy.

   ```powershell
   Connect-AzureAD
   ```

1. Znajdź aplikację. W tym przykładzie program PowerShell służy do znajdowania objectidu, wyszukując aplikację o nazwie wyświetlanej `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Powinieneś uzyskać wynik podobny do pokazanego tutaj. Skopiuj identyfikator GUID objectid do użycia w następnej sekcji.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Alternatywnie można po prostu wyciągnąć listę wszystkich aplikacji, przeszukać listę aplikacji o określonej nazwie wyświetlanej lub stronie głównej i skopiować objectid aplikacji po znalezieniu aplikacji.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Aktualizowanie adresu URL strony głównej

Utwórz adres URL strony głównej i zaktualizuj aplikację o tę wartość. Kontynuuj korzystanie z tego samego okna programu PowerShell lub jeśli używasz nowego okna programu `Connect-AzureAD`PowerShell, zaloguj się ponownie do modułu usługi Azure AD przy użyciu . Następnie wykonaj poniższe czynności:

1. Utwórz zmienną do przechowywania wartości ObjectId skopiowanej w poprzedniej sekcji. (Zastąp wartość ObjectId używaną w tym przykładzie programu SharePoint wartością ObjectId aplikacji).

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Upewnij się, że masz poprawną aplikację, uruchamiając następujące polecenie. Dane wyjściowe powinny być identyczne z wyjściem, które widziałeś w poprzedniej sekcji ([Znajdź ObjectId aplikacji](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Utwórz pusty obiekt aplikacji, aby zatrzymać zmiany, które chcesz wprowadzić.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Ustaw adres URL strony głównej na żądaną wartość. Wartość musi być ścieżką poddomeny opublikowanej aplikacji. Jeśli na przykład zmienisz adres `https://sharepoint-iddemo.msappproxy.net/` URL `https://sharepoint-iddemo.msappproxy.net/hybrid/`strony głównej na , użytkownicy aplikacji przejdą bezpośrednio do niestandardowej strony głównej.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Dokonaj aktualizacji strony głównej.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Aby potwierdzić, że zmiana powiodła się, uruchom ponownie następujące polecenie z kroku 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   W naszym przykładzie dane wyjściowe powinny teraz być wyświetlane w następujący sposób:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Uruchom ponownie aplikację, aby potwierdzić, że strona główna jest wyświetlana jako pierwszy ekran, zgodnie z oczekiwaniami.

> [!NOTE]
> Wszelkie zmiany wprowadzone w aplikacji mogą zresetować adres URL strony głównej. Jeśli adres URL strony głównej zostanie zresetowany, powtórz czynności opisane w tej sekcji, aby go przywrócić.

## <a name="next-steps"></a>Następne kroki

- [Włączanie dostępu zdalnego do programu SharePoint przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji w usłudze Azure Active Directory](application-proxy-add-on-premises-application.md)