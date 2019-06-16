---
title: Dzienniki usług IIS, w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Internet Information Services (IIS) aktywności użytkownika są przechowywane w plikach dziennika, które można zbierać z usługi Azure Monitor.  W tym artykule opisano sposób konfigurowania zbierania dzienników usług IIS i szczegóły rekordów, utworzonego przez siebie w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: 5843ee11a615a2780e9fea2d89f7b18fb45706d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65604363"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Zbieraj dzienniki usług IIS w usłudze Azure Monitor
Aktywności użytkownika w Internet Information Services (IIS) są przechowywane w plikach dziennika, które mogą być zbierane przez usługi Azure Monitor i przechowywane jako [dane dziennika](data-platform.md).

![Dzienniki usług IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurowanie usług IIS dzienników
Usługa Azure Monitor umożliwia zbieranie informacji o wpisów z pliki dziennika utworzone przez usługi IIS, więc musisz [skonfigurowania usług IIS do rejestrowania](https://technet.microsoft.com/library/hh831775.aspx).

Usługa Azure Monitor tylko obsługuje przechowywane w formacie W3C plików dziennika usług IIS i nie obsługuje niestandardowych pól lub Zaawansowane rejestrowanie programu IIS. Dzienniki w formacie natywnym NCSA lub IIS nie są zbierane.

Skonfiguruj dzienniki usług IIS w usłudze Azure Monitor z [menu Zaawansowane ustawienia](agent-data-sources.md#configuring-data-sources).  Jest wymagana żadna konfiguracja, inne niż wybieranie **pliki dziennika usług IIS w formacie W3C zbieranie**.


## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor umożliwia zbieranie informacji o wpisów dziennika IIS z każdego agenta w każdym razem, gdy dziennik jest zamknięty i nowego jest tworzony. Tę częstotliwość jest kontrolowana przez **harmonogram Przerzucanie pliku dziennika** ustawienia witryny usług IIS, która jest raz dziennie domyślnie. Na przykład, jeśli to ustawienie **godzinowe**, a następnie usługa Azure Monitor będzie zbierać dziennika co godzinę.  Jeśli to ustawienie ma **codzienne**, a następnie usługa Azure Monitor będzie zbierać dziennika co 24 godziny.


## <a name="iis-log-record-properties"></a>Właściwości rekordu dziennika usług IIS
Rekordy dziennika usług IIS mają typ **W3CIISLog** i mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, na którym zostały zebrane zdarzenia. |
| cIP |Adres IP klienta. |
| csMethod |Metoda żądania, takich jak GET lub POST. |
| csReferer |Witryna, że użytkownik, a następnie łącze z do bieżącej lokacji. |
| csUserAgent |Typ przeglądarki klienta. |
| csUserName |Nazwa uwierzytelnionego użytkownika, który uzyskał dostęp do serwera. Użytkownicy anonimowi są wskazywani przez łącznik. |
| csUriStem |Obiekt docelowy żądaniem, takiego jak strony sieci web. |
| csUriQuery |Zapytania, jeśli istnieje, czy klient próbował wykonać. |
| ManagementGroupName |Nazwa grupy zarządzania agentów programu Operations Manager.  Dla innych agentów jest to AOI -\<identyfikator obszaru roboczego\> |
| RemoteIPCountry |Kraj/region adresu IP klienta. |
| RemoteIPLatitude |Szerokość adresu IP klienta. |
| RemoteIPLongitude |Długość geograficzna adresu IP klienta. |
| scStatus |Kod stanu HTTP. |
| scSubStatus |Kod błędu: Substatus. |
| scWin32Status |Kod stanu Windows. |
| sIP |Adres IP serwera sieci web. |
| SourceSystem |OpsMgr |
| sPort |Port, na serwerze klient połączony. |
| sSiteName |Nazwa witryny usług IIS. |
| TimeGenerated |Data i godzina zarejestrowania wpis. |
| Właściwość timeTaken |Długość czasu na przetworzenie żądania w milisekundach. |

## <a name="log-queries-with-iis-logs"></a>Dziennik zapytań przy użyciu dzienników usług IIS
Poniższa tabela zawiera przykłady różnych zapytań dziennika, które pobierają rekordy dziennika usług IIS.

| Zapytanie | Opis |
|:--- |:--- |
| W3CIISLog |Wszystkie rekordy dziennika usług IIS. |
| W3CIISLog &#124; gdzie scStatus == 500 |Wszystkie rekordy dziennika usług IIS przy użyciu status powrotu 500. |
| W3CIISLog &#124; podsumowania count() przez cIP |Liczba usług IIS wpisy Zaloguj się przy użyciu adresu IP klienta. |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |Wpisy dziennika liczba usług IIS według adresu URL dla hosta www\.contoso.com. |
| W3CIISLog &#124; Podsumuj sum(csBytes) przez komputer &#124; zająć 500000 |Suma bajtów odebranych przez każdy komputer IIS. |

## <a name="next-steps"></a>Kolejne kroki
* Konfigurowanie usługi Azure Monitor do gromadzenia innych [źródeł danych](agent-data-sources.md) do analizy.
* Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md) analizować dane zbierane z innych źródeł danych i rozwiązań.