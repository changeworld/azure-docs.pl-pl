---
title: Dzienniki usług IIS w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Internetowe usługi informacyjne (IIS) przechowują aktywność użytkownika w plikach dziennika, które mogą być zbierane przez usługę Azure Monitor.  W tym artykule opisano sposób konfigurowania kolekcji dzienników usług IIS i szczegóły rekordów, które tworzą w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 1b3ae6295a639c3d59643b106b920cb606572e0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670580"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>Zbieranie dzienników usług IIS w usłudze Azure Monitor
Internetowe usługi informacyjne (IIS) przechowują aktywność użytkowników w plikach dziennika, które mogą być zbierane przez usługę Azure Monitor i przechowywane jako [dane dziennika.](data-platform.md)

![Dzienniki usług IIS](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>Konfigurowanie dzienników usługi IIS
Usługa Azure Monitor zbiera wpisy z plików dziennika utworzonych przez usługi IIS, dlatego należy [skonfigurować usługi IIS do rejestrowania.](https://technet.microsoft.com/library/hh831775.aspx)

Usługa Azure Monitor obsługuje tylko pliki dziennika usług IIS przechowywane w formacie W3C i nie obsługuje pól niestandardowych ani zaawansowanego rejestrowania usług IIS. Nie zbiera dzienników w formacie macierzystym NCSA lub IIS.

Konfigurowanie dzienników usług IIS w usłudze Azure Monitor z [menu Ustawienia zaawansowane](agent-data-sources.md#configuring-data-sources).  Nie jest wymagana konfiguracja inna niż wybranie **plików dziennika usługi IIS w formacie Collect W3C**.


## <a name="data-collection"></a>Zbieranie danych
Usługa Azure Monitor zbiera wpisy dziennika usług IIS od każdego agenta za każdym razem, gdy zmienia się sygnatura czasowa dziennika. Dziennik jest odczytywany co **5 minut.** Jeśli z jakiegokolwiek powodu usługi IIS nie zaktualizują sygnatury czasowej przed czasem narzutu podczas tworzenia nowego pliku, wpisy zostaną zebrane po utworzeniu nowego pliku. Częstotliwość tworzenia nowego pliku jest kontrolowana przez ustawienie **Harmonogram narzucenia plików dziennika** dla witryny IIS, która domyślnie jest raz dziennie. Jeśli ustawienie jest **co godzinę,** usługa Azure Monitor zbiera dziennik co godzinę. Jeśli ustawienie jest **codziennie,** usługa Azure Monitor zbiera dziennik co 24 godziny.


## <a name="iis-log-record-properties"></a>Właściwości rekordu usług IIS
Rekordy dziennika usług IIS mają typ **W3CIISLog** i mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--- |:--- |
| Computer (Komputer) |Nazwa komputera, z których pobrano zdarzenie. |
| Cip |Adres IP klienta. |
| csMetod |Metoda żądania, takich jak GET lub POST. |
| csReferer (niem. |Witryna, z której użytkownik podążał za łączem do bieżącej witryny. |
| csUserAgent |Typ przeglądarki klienta. |
| nazwa csUser |Nazwa uwierzytelnionego użytkownika, który uzyskał dostęp do serwera. Użytkownicy anonimowi są wskazywani przez łącznik. |
| csUriStem ( csUriStem ) |Miejsce docelowe żądania, takie jak strona internetowa. |
| csUriQuery (kw. |Kwerenda, jeśli istnieje, że klient próbował wykonać. |
| ManagementGroupName |Nazwa grupy zarządzania agentami programu Operations Manager.  W przypadku innych agentów jest\<to identyfikator obszaru roboczego AOI\> |
| RemoteIPCountry |Kraj/region adresu IP klienta. |
| RemoteIPLatitude |Szerokość geograficzna adresu IP klienta. |
| RemoteIPLongitude |Długość geograficzna adresu IP klienta. |
| scStatus (stan sc) |Kod stanu HTTP. |
| scSubStatus (stan scSub) |Kod błędu podstatu. |
| scWin32Status |Kod stanu systemu Windows. |
| Sip |Adres IP serwera www. |
| SourceSystem |Opsmgr |
| Sport |Port na serwerze, z który klient jest połączony. |
| SNameite |Nazwa witryny usługi IIS. |
| TimeGenerated |Data i godzina zarejestrowania wpisu. |
| CzasTaken |Czas przetwarzania żądania w milisekundach. |

## <a name="log-queries-with-iis-logs"></a>Rejestrowanie zapytań za pomocą dzienników IIS
Poniższa tabela zawiera różne przykłady zapytań dziennika, które pobierają rekordy dziennika usług IIS.

| Zapytanie | Opis |
|:--- |:--- |
| Dziennik W3CIIS |Wszystkie rekordy dziennika usługi IIS. |
| W3CIISLog &#124; gdzie scStatus==500 |Wszystkie rekordy dziennika IIS o stanie zwrotu 500. |
| W3CIISLog &#124; podsumować count() przez cIP |Liczba wpisów dziennika IIS według adresu IP klienta. |
| W3CIISLog &#124; gdzie csHost=="www\.contoso.com" &#124; podsumowują count() przez csUriStem |Liczba wpisów dziennika IIS według adresu\.URL dla contoso.com hosta www. |
| W3CIISLog &#124; suma (csBytes) przez komputer &#124; podjęcia 500000 |Całkowita liczba bajtów odebranych przez każdy komputer usługi IIS. |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj usługę Azure Monitor do zbierania innych [źródeł danych](agent-data-sources.md) do analizy.
* Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md) do analizowania danych zebranych ze źródeł danych i rozwiązań.
