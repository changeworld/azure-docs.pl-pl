---
title: Rozwiązywanie problemów ze stanem awaryjnym w usłudze Azure Traffic Manager
description: Jak rozwiązywać problemy z profilami usługi Traffic Manager, gdy jest wyświetlany jako stan obniżony.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938361"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Azure Traffic Manager

W tym artykule opisano sposób rozwiązywania problemów z profilem usługi Azure Traffic Manager, który jest wyświetlany stan obniżony. Pierwszym krokiem w rozwiązywaniu problemów ze stanem awaryjnym usługi Azure Traffic Manager jest włączenie rejestrowania diagnostycznego.  Aby uzyskać więcej informacji, zobacz [Włączanie dzienników diagnostycznych.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) W tym scenariuszu należy wziąć pod uwagę, że skonfigurowano profil usługi Traffic Manager wskazując niektóre z cloudapp.net usług hostowanych. Jeśli stan urządzenia Traffic Manager jest wyświetlany w stanie **Obniżona,** stan jednego lub kilku punktów końcowych może ulec **pogorszeniu:**

![stan zdegradowanego punktu końcowego](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Jeśli kondycja Menedżera ruchu wyświetla stan **nieaktywny,** oba punkty końcowe mogą być **wyłączone:**

![Nieaktywny komunikat o ruchu drogowym](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Opis sond menedżera ruchu

* Usługa Traffic Manager uważa, że punkt końcowy jest online tylko wtedy, gdy sonda odbiera odpowiedź HTTP 200 z powrotem ze ścieżki sondy. Jeśli aplikacja zwraca inny kod odpowiedzi HTTP należy dodać ten kod odpowiedzi do [oczekiwanych zakresów kodu stanu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) profilu usługi Traffic Manager.
* 30-krotna odpowiedź przekierowania jest traktowana jako błąd, chyba że określono to jako prawidłowy kod odpowiedzi w [zakresach kodu stanu oczekiwanego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) profilu usługi Traffic Manager. Usługa Traffic Manager nie sonduje celu przekierowania.
* W przypadku sond HTTPs błędy certyfikatów są ignorowane.
* Rzeczywista zawartość ścieżki sondy nie ma znaczenia, tak długo, jak 200 jest zwracany. Sondowanie adresu URL niektórych treści statycznych, takich jak "/favicon.ico" jest powszechną techniką. Zawartość dynamiczna, taka jak strony ASP, może nie zawsze zwracać 200, nawet jeśli aplikacja jest w dobrej kondycji.
* Najlepszym rozwiązaniem jest ustawienie ścieżki sondy do czegoś, co ma wystarczającą logikę, aby ustalić, że witryna jest w górę lub w dół. W poprzednim przykładzie, ustawiając ścieżkę do "/favicon.ico", testujesz tylko, że w3wp.exe odpowiada. Ta sonda może nie wskazywać, że aplikacja sieci web jest w dobrej kondycji. Lepszym rozwiązaniem byłoby ustawienie ścieżki do czegoś takiego jak "/Probe.aspx", który ma logikę do określenia kondycji witryny. Na przykład można użyć liczników wydajności do wykorzystania procesora CPU lub zmierzyć liczbę żądań nie powiodło się. Możesz też spróbować uzyskać dostęp do zasobów bazy danych lub stanu sesji, aby upewnić się, że aplikacja sieci web działa.
* Jeśli wszystkie punkty końcowe w profilu są zdegradowane, usługa Traffic Manager traktuje wszystkie punkty końcowe jako w dobrej kondycji i kieruje ruch do wszystkich punktów końcowych. To zachowanie gwarantuje, że problemy z mechanizmem sondowania nie powodują całkowitej awarii usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby rozwiązać problem błędu sondy, potrzebujesz narzędzia, które pokazuje kod stanu HTTP zwracany z adresu URL sondy. Dostępnych jest wiele narzędzi, które pokazują nieprzetworzoną odpowiedź HTTP.

* [Fiddler](https://www.telerik.com/fiddler)
* [Curl](https://curl.haxx.se/)
* [Wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Ponadto można użyć karty Sieć narzędzi debugowania F12 w programie Internet Explorer, aby wyświetlić odpowiedzi HTTP.

W tym przykładzie chcemy zobaczyć odpowiedź z\/naszej sondy URL: http: /watestsdp2008r2.cloudapp.net:80/Probe. Poniższy przykład programu PowerShell ilustruje problem.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Przykładowe dane wyjściowe:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Zauważ, że otrzymaliśmy odpowiedź przekierowania. Jak wspomniano wcześniej, każdy Kod stanu inny niż 200 jest uważany za błąd. Usługa Traffic Manager zmienia stan punktu końcowego na offline. Aby rozwiązać ten problem, sprawdź konfigurację witryny sieci Web, aby upewnić się, że odpowiedni kod stanu może zostać zwrócony ze ścieżki sondy. Ponownie skonfiguruj sondę Menedżera ruchu, aby wskazać ścieżkę, która zwraca 200.

Jeśli sonda używa protokołu HTTPS, może być konieczne wyłączenie sprawdzania certyfikatów, aby uniknąć błędów SSL/TLS podczas testu. Następujące instrukcje programu PowerShell wyłączają sprawdzanie poprawności certyfikatu dla bieżącej sesji programu PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Następne kroki

[Informacje o metodach routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md)

[Co to jest Traffic Manager](traffic-manager-overview.md)

[Usługi w chmurze](https://go.microsoft.com/fwlink/?LinkId=314074)

[Usługa aplikacji platformy Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet usługi Azure Traffic Manager][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
