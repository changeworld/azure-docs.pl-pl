---
title: Rozwiązywanie problemów z negatywny wpływ na dostępność stanu w usłudze Azure Traffic Manager
description: Jak rozwiązywać problemy z profilami usługi Traffic Manager, gdy jest on wyświetlany jako negatywny wpływ na dostępność stanu.
services: traffic-manager
documentationcenter: ''
author: chadmath
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: f01dfe78d5d5e322258b0ee98cec314f9afe33c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60329755"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Rozwiązywanie problemów ze stanem obniżonej wydajności w usłudze Azure Traffic Manager

W tym artykule opisano, jak rozwiązywać problemy z profilem usługi Azure Traffic Manager, który jest wyświetlany stan obniżonej wydajności. W tym scenariuszu należy wziąć pod uwagę skonfigurowano profil usługi Traffic Manager, wskazując cloudapp.net hostowanych usług. Wyświetlaniem kondycji usługi Traffic Manager **obniżony** stanu, a następnie stan co najmniej jeden punkt końcowy może być **obniżony**:

![Stan punktu końcowego o obniżonym poziomie](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Wyświetlaniem kondycji usługi Traffic Manager **nieaktywny** stanu, a następnie obie punktów końcowych może być **wyłączone**:

![Nieaktywne stan usługi Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Sondy usługi Traffic Manager opis

* Usługa Traffic Manager uważa się usługi ONLINE, tylko wtedy, gdy sondy odbiera odpowiedź HTTP 200 powrót po awarii z ścieżka sondy punktu końcowego. Druga odpowiedź – 200 jest błąd.
* Przekierowanie 30 x kończy się niepowodzeniem, nawet jeśli adresu URL zwraca wartość 200.
* Dla sondy protokołu HTTPs błędy certyfikatów są ignorowane.
* Rzeczywista zawartość ścieżka sondy nie ma znaczenia, tak długo, jak zwracany jest 200. Sondowanie adres URL do zawartości statycznej takich jak "/ favicon.ico" jest to typowa technika. Zawartość dynamiczna, takich jak strony ASP może nie zawsze zwrócić 200, nawet wtedy, gdy aplikacja jest w dobrej kondycji.
* Najlepszym rozwiązaniem jest, aby ustawić ścieżkę sondowania coś, co ma za mało logikę w celu określenia, że witryna jest w górę lub w dół. W poprzednim przykładzie, ustawiając dla ścieżki "/ favicon.ico", obejmuje tylko testowanie tym w3wp.exe odpowiada. To sondowanie nie może wskazywać, czy aplikacja sieci web jest w dobrej kondycji. Lepszym rozwiązaniem byłoby ustawić ścieżkę na coś, takie jak "/ Probe.aspx" zawierający logikę w celu określenia kondycji lokacji. Na przykład można użyć liczników wydajności do wykorzystania procesora CPU lub mierzenia liczby żądań zakończonych niepowodzeniem. Lub może próbować uzyskać dostęp do zasobów bazy danych lub stanu sesji, aby upewnić się, że działa aplikacja sieci web.
* Jeśli wszystkie punkty końcowe w profilu są ograniczone, usługa Traffic Manager traktuje wszystkie punkty końcowe jako w dobrej kondycji i kieruje ruch do wszystkich punktów końcowych. Takie zachowanie gwarantuje, że problemy z mechanizmu sondowania spowoduje pełną awarii usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby rozwiązać błąd sondowania, należy to narzędzie, które zawiera kod stanu HTTP zwracany z adresem URL badania. Istnieje wiele narzędzi, które pokazują nieprzetworzona odpowiedź HTTP.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Ponadto możesz użyć karcie sieciowej narzędzi debugowania F12 w przeglądarce Internet Explorer do wyświetlania odpowiedzi HTTP.

W tym przykładzie chcemy, aby zobaczyć odpowiedź z naszej adresu URL sondy: http:\//watestsdp2008r2.cloudapp.net:80/Probe. Poniższy przykład programu PowerShell ilustruje ten problem.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Przykładowe dane wyjściowe:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Należy zauważyć, że odebraliśmy odpowiedzi przekierowania. Jak wspomniano wcześniej, StatusCode wszelkie inne niż 200 jest uznawany za błąd. Usługa Traffic Manager zmiany stanu punktu końcowego Offline. Aby rozwiązać ten problem, sprawdź konfigurację witryny sieci Web, aby upewnić się, że odpowiednie StatusCode mogą być zwrócone ze ścieżki sondy. Skonfiguruj ponownie sondy usługi Traffic Manager, aby wskazywała ścieżkę, która zwraca wartość 200.

Twoje sondy jest przy użyciu protokołu HTTPS, może być konieczne wyłączenie sprawdzania, aby uniknąć błędów certyfikatu SSL/TLS w trakcie testu certyfikatu. Poniższe instrukcje PowerShell wyłączyć weryfikację certyfikatów dla bieżącej sesji programu PowerShell:

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

[Metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md)

[Co to jest usługa Traffic Manager](traffic-manager-overview.md)

[Cloud Services](https://go.microsoft.com/fwlink/?LinkId=314074)

[Usługa Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet usługi Azure Traffic Manager][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
