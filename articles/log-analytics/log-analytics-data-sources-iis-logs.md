---
title: Rejestrowania przez usługi IIS w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Internet Information Services (IIS) są przechowywane działań użytkownika w plikach dziennika, które mogą zostać zebrane przez analizy dzienników.  W tym artykule opisano sposób konfigurowania zbierania dzienników usług IIS i szczegóły rekordów tworzonych w obszarze roboczym analizy dzienników.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: na
ms.openlocfilehash: 65320e7d3cc97a3d53fd1a00fbbeab5559c02fce
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133058"
---
# <a name="iis-logs-in-log-analytics"></a>Rejestrowania przez usługi IIS w analizy dzienników
Internet Information Services (IIS) są przechowywane działań użytkownika w plikach dziennika, które mogą zostać zebrane przez analizy dzienników.  

![Dzienniki usług IIS](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurowanie usług IIS dzienników
Analiza dzienników zbiera wpisy z pliki dziennika utworzone przez usługi IIS, więc należy [konfigurowania usług IIS do rejestrowania](https://technet.microsoft.com/library/hh831775.aspx).

Analiza dzienników tylko obsługuje przechowywane w formacie W3C plików dziennika usług IIS i nie obsługuje pól niestandardowych lub zaawansowanych rejestrowanie programu IIS.  
Analiza dzienników nie zbiera dzienniki w formacie native NCSA lub usług IIS.

Konfigurowanie dzienników usług IIS w analizy dzienników z [danych menu Ustawienia usługi Analiza dzienników](log-analytics-data-sources.md#configuring-data-sources).  Jest wymagana żadna konfiguracja innych niż wybranie **pliki dziennika w formacie W3C zbieranie IIS**.


## <a name="data-collection"></a>Zbieranie danych
Analiza dzienników zbiera wpisy dziennika usług IIS z każdego agenta, w każdym razem, gdy dziennik jest zamknięty, a nowy jest tworzony. Steruje tym częstotliwość **harmonogram przerzucania pliku dziennika** ustawienia witryny usług IIS, która jest raz dziennie domyślnie. Na przykład w przypadku ustawienia **co godzinę**, a następnie analizy dzienników zbierze dziennika co godzinę.  Jeśli ustawienie jest **codzienne**, następnie analizy dzienników zbierze dziennika co 24 godziny.


## <a name="iis-log-record-properties"></a>Właściwości rekordu dziennika usług IIS
Rekordy dziennika usług IIS zawiera typu **W3CIISLog** i mieć właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, na którym zostały zebrane zdarzenia. |
| cIP |Adres IP klienta. |
| csMethod |Metoda żądania, takich jak GET lub POST. |
| csReferer |Lokacja, że użytkownik, a następnie z link do bieżącej lokacji. |
| csUserAgent |Typ przeglądarki klienta. |
| csUserName |Nazwa uwierzytelnionego użytkownika, który uzyskał dostęp do serwera. Użytkownicy anonimowi są wskazywani przez łącznik. |
| csUriStem |Obiekt docelowy żądaniem, takiego jak strony sieci web. |
| csUriQuery |Zapytania, jeśli istnieją, które klient próbował wykonać. |
| ManagementGroupName |Nazwa grupy zarządzania agenty programu Operations Manager.  W przypadku innych agentów jest AOI -\<identyfikator obszaru roboczego\> |
| RemoteIPCountry |Kraj adres IP klienta. |
| RemoteIPLatitude |Szerokość geograficzna adresu IP klienta. |
| RemoteIPLongitude |Długość geograficzna adresu IP klienta. |
| scStatus |Kod stanu HTTP. |
| scSubStatus |Kod błędu podstanu. |
| scWin32Status |Kod stanu systemu Windows. |
| sIP |Adres IP serwera sieci web. |
| SourceSystem |Program Operations Manager |
| sPort |Port na serwerze klient połączony. |
| sSiteName |Nazwa witryny usług IIS. |
| TimeGenerated |Data i godzina zarejestrowania wpisu. |
| Właściwość TimeTaken |Długość czasu na przetwarzanie żądania w milisekundach. |

## <a name="log-searches-with-iis-logs"></a>Dziennik wyszukiwania przy użyciu dzienników usług IIS
Poniższa tabela zawiera przykłady różnych dziennika zapytań, które pobierają rekordy dziennika usług IIS.

| Zapytanie | Opis |
|:--- |:--- |
| W3CIISLog |Wszystkie rekordy dziennika usług IIS. |
| W3CIISLog &#124; gdzie scStatus == 500 |Wszystkie rekordy dziennika usług IIS o stanie zwracanych 500. |
| W3CIISLog &#124; podsumowania count() przez cIP |Liczba usług IIS pozycje dziennika za pomocą adresu IP klienta. |
| W3CIISLog &#124; gdzie csHost == "www.contoso.com" &#124; podsumowania count() przez csUriStem |Liczba usług IIS dziennika wpisy przez adres URL dla www.contoso.com hosta. |
| W3CIISLog &#124; Podsumuj sum(csBytes) przez komputer &#124; zająć 500000 |Całkowita liczba bajtów odebranych przez każdy komputer usług IIS. |

## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie analizy dzienników do gromadzenia innych [źródeł danych](log-analytics-data-sources.md) do analizy.
* Dowiedz się więcej o [dziennika wyszukiwania](log-analytics-log-searches.md) analizować dane zebrane ze źródeł danych i rozwiązania.
* Konfigurowanie alertów w analizy dzienników do aktywnego powiadomienia ważne warunków znaleziono w dziennikach usług IIS.
