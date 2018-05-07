---
title: Rozwiązywanie problemów ze spadkiem stanu w usłudze Azure Traffic Manager
description: Jak rozwiązywać problemy z profilów usługi Traffic Manager będzie wyświetlana jako znacznie mniej wydajna stanu.
services: traffic-manager
documentationcenter: ''
author: chadmath
manager: cshepard
ms.assetid: 8af0433d-e61b-4761-adcc-7bc9b8142fc6
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: genli
ms.openlocfilehash: f5b2f471b13db67411f15f32abad5afc644c04ba
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Rozwiązywanie problemów ze stanem obniżonej wydajności w usłudze Azure Traffic Manager

W tym artykule opisano, jak rozwiązywać problemy z profilem usługi Azure Traffic Manager, który jest wyświetlany stan awaryjnych. W tym scenariuszu należy wziąć pod uwagę skonfigurowano profil Menedżera ruchu wskazujący cloudapp.net hostowanych usług. Jeśli wyświetlany kondycji z Menedżera ruchu **obniżony** stan, a następnie stan jeden lub więcej punktów końcowych może być **obniżony**:

![Stan punktu końcowego obniżeniem](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Jeśli wyświetlany kondycji z Menedżera ruchu **nieaktywne** stan, a następnie oba punkty końcowe mogą być **wyłączone**:

![Stan nieaktywny Menedżera ruchu](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Sondy opis Menedżera ruchu

* Menedżer ruchu uwzględnia punktu końcowego można ONLINE tylko wtedy, gdy sondy odbiera odpowiedź 200 protokołu HTTP z ścieżka sondowania. Druga odpowiedź – 200 jest błąd.
* Przekierowanie 30 x nie powiedzie się, nawet jeśli 200 zwraca adresu URL.
* Dla sondy HTTPs błędów certyfikatów są ignorowane.
* Rzeczywista zawartość ścieżce badania nie ma znaczenia, tak długo, jak jest zwracany 200. Adres URL do niektórych zawartości statycznej, takie jak sondowanie "/ favicon.ico" to technika wspólnej. Zawartość dynamiczna, takich jak strony ASP może nie zawsze zwrócić 200, nawet wtedy, gdy aplikacja jest w dobrej kondycji.
* Najlepszym rozwiązaniem jest, aby ustawić ścieżkę sondy do zasobu, który ma za mało logika do określenia, że lokacja jest w górę lub w dół. W poprzednim przykładzie, ustawiając dla ścieżki "/ favicon.ico", tylko testowania tego w3wp.exe odpowiada. To sondowanie może nie wskazywać aplikacji sieci web jest w dobrej kondycji. Lepszym rozwiązaniem byłoby takich jak ustawić ścieżkę do elementu "/ Probe.aspx" mający logika do określenia kondycji lokacji. Na przykład można użyć liczników wydajności do wykorzystania procesora CPU lub mierzenia liczby żądań zakończonych niepowodzeniem. Lub może próbować zasobów bazy danych i stanu sesji, aby upewnić się, że aplikacja sieci web działa.
* Jeśli wszystkie punkty końcowe w profilu są ograniczone, następnie Menedżera ruchu traktuje wszystkie punkty końcowe w dobrej kondycji i tras ruchu do wszystkich punktów końcowych. Takie zachowanie gwarantuje, że problemy z mechanizmu sondowania spowoduje zakończenie przestoju usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby rozwiązać błąd sondowania, należy narzędzia, który zawiera kod stanu HTTP zwrotu z adresem URL badania. Istnieje wiele narzędzi przedstawiających raw odpowiedzi HTTP.

* [Fiddler](http://www.telerik.com/fiddler)
* [Narzędzie curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Ponadto służy karcie sieciowej F12 narzędzi debugowania w programie Internet Explorer do przeglądania odpowiedzi HTTP.

W tym przykładzie, jeśli chcesz zobaczyć odpowiedzi z adresu URL naszej sondy: http://watestsdp2008r2.cloudapp.net:80/Probe. W poniższym przykładzie programu PowerShell zilustrowano problem.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Przykładowe dane wyjściowe:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Zwróć uwagę, że Odebraliśmy odpowiedź przekierowania. Jak wspomniano wcześniej, StatusCode żadnych innych niż 200 oznaczają niepowodzenie. Menedżer ruchu zmieni stan punktu końcowego na Offline. Aby rozwiązać ten problem, sprawdź konfigurację witryny sieci Web, aby upewnić się, że odpowiednie StatusCode może być zwracany w ścieżce badania. Skonfiguruj ponownie sondowania Menedżera ruchu wskaż ścieżkę, która zwraca 200.

Sonda sieci używa protokołu HTTPS, może być konieczne wyłączenie sprawdzania, aby uniknąć błędów protokołu SSL/TLS podczas testu certyfikatu. Poniższe instrukcje PowerShell Wyłącz sprawdzanie poprawności certyfikatu dla bieżącej sesji programu PowerShell:

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

[O metodach routingu ruchu Menedżera ruchu](traffic-manager-routing-methods.md)

[Co to jest Menedżera ruchu](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Polecenia cmdlet systemu Azure Traffic Manager][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
