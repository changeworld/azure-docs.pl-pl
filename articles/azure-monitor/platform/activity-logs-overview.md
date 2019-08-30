---
title: Przegląd dziennika aktywności platformy Azure
description: Dowiedz się, co to jest dziennik aktywności platformy Azure i jak można go użyć do poznania zdarzeń występujących w ramach subskrypcji platformy Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fa1737a8627fe9561a2a84e7f0ef69aefb6deb14
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170618"
---
# <a name="overview-of-azure-activity-log"></a>Przegląd dziennika aktywności platformy Azure

**Dziennik aktywności platformy Azure** zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Obejmuje to szereg danych, od Azure Resource Manager dane operacyjne do aktualizacji na zdarzeniach Service Health. Dziennik aktywności był wcześniej znany jako _dzienniki inspekcji_ lub _dzienniki operacyjne_, ponieważ kategoria administracyjna zgłasza zdarzenia płaszczyzny kontroli dla subskrypcji. 

Użyj dziennika aktywności, aby określić, ktoi kiedy _mają_być wykonywane operacje zapisu (Put, post, Delete) dotyczące zasobów w ramach subskrypcji. Dodatkowo użytkownik rozumie stanu operacji i inne odpowiednie właściwości. 

Dziennik aktywności nie obejmuje operacji odczytu (GET) ani operacji dla zasobów, które korzystają z modelu klasyczny/frontonu reddog.

## <a name="comparison-to-diagnostic-logs"></a>Porównanie z dziennikami diagnostycznymi
Dla każdej subskrypcji platformy Azure istnieje pojedynczy dziennik aktywności. Zawiera dane dotyczące operacji na zasobie z zewnątrz ("płaszczyzny kontroli"). [Dzienniki diagnostyczne](diagnostic-logs-overview.md) są emitowane przez zasób i zawierają informacje dotyczące operacji tego zasobu ("płaszczyzny danych"). Należy włączyć ustawienia diagnostyczne dla każdego zasobu.

![Dzienniki aktywności w porównaniu z dziennikami diagnostycznymi](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Dziennik aktywności platformy Azure jest przeznaczony głównie dla działań, które wystąpiły w Azure Resource Manager. Nie śledzi zasobów przy użyciu modelu klasycznego/frontonu reddog. Niektóre klasyczne typy zasobów mają dostawcę zasobów serwera proxy w Azure Resource Manager (na przykład Microsoft. ClassicCompute). W przypadku korzystania z klasycznego typu zasobu za pośrednictwem Azure Resource Manager przy użyciu tych dostawców zasobów serwera proxy operacje będą widoczne w dzienniku aktywności. W przypadku korzystania z klasycznego typu zasobu poza serwerem proxy Azure Resource Manager akcje są rejestrowane tylko w dzienniku operacji. Dziennik operacji można przeglądać w osobnej sekcji portalu.

## <a name="activity-log-retention"></a>Przechowywanie dziennika aktywności
Po utworzeniu wpisy dziennika aktywności nie są modyfikowane ani usuwane przez system. Ponadto nie można zmienić ich w interfejsie ani programowo. Zdarzenia dziennika aktywności są przechowywane przez 90 dni. Aby przechowywać te dane przez dłuższy okres, [Zbierz je w Azure monitor](activity-log-collect.md) lub [wyeksportuj do magazynu lub Event Hubs](activity-log-export.md).

## <a name="view-the-activity-log"></a>Wyświetlanie dziennika aktywności
Wyświetl dziennik aktywności dla wszystkich zasobów z menu **Monitoruj** w Azure Portal. Wyświetl dziennik aktywności dla określonego zasobu z poziomu opcji **Dziennik aktywności** w menu tego zasobu. Rekordy dziennika aktywności można także pobrać przy użyciu programu PowerShell, interfejsu wiersza polecenia lub API REST.  Zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](activity-log-view.md).

![Wyświetl dziennik aktywności](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Zbierz dziennik aktywności w Azure Monitor
Zbierz dziennik aktywności do obszaru roboczego Log Analytics w Azure Monitor, aby przeanalizować go z innymi danymi monitorowania i zachować dane przez dłużej niż 90 dni. Zobacz [zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym log Analytics w Azure monitor](activity-log-collect.md).

![Zapytanie dziennika aktywności](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Eksportuj dziennik aktywności
Eksportowanie dziennika aktywności do usługi Azure Storage w celu archiwizacji lub przesyłania strumieniowego do centrum zdarzeń w celu pozyskiwania przez usługę innej firmy lub rozwiązanie do analizy niestandardowej. Zobacz [Eksportowanie dziennika aktywności platformy Azure](activity-log-export.md). Możesz również analizować zdarzenia dziennika aktywności w Power BI przy użyciu [**pakietu zawartości Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

## <a name="alert-on-activity-log"></a>Alert dotyczący dziennika aktywności
Alert można utworzyć po utworzeniu określonych zdarzeń w dzienniku aktywności przy użyciu [alertu dziennika aktywności](activity-log-alerts.md). Możesz również utworzyć alert przy użyciu [kwerendy dziennika](alerts-log-query.md) , gdy dziennik aktywności jest połączony z obszarem roboczym log Analytics, ale istnieje koszt rejestrowania alertów zapytań. Nie ma kosztu dla alertów dziennika aktywności.

## <a name="categories-in-the-activity-log"></a>Kategorie w dzienniku aktywności
Każde zdarzenie w dzienniku aktywności ma określoną kategorię, która została opisana w poniższej tabeli. Aby uzyskać szczegółowe informacje o wypełniana z tych kategorii, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md). 

| Kategoria | Opis |
|:---|:---|
| Administracyjne | Zawiera rekord wszystkich operacji tworzenia, aktualizowania, usuwania i akcji wykonywanych za pomocą Menedżer zasobów. Przykłady zdarzeń administracyjnych obejmują _Utwórz maszynę wirtualną_ i _Usuń sieciową grupę zabezpieczeń_.<br><br>Każda Akcja podejmowana przez użytkownika lub aplikację przy użyciu Menedżer zasobów jest modelowana jako operacja dla określonego typu zasobu. Jeśli typem operacji jest _zapis_, _usuwanie_lub _Akcja_, rekordy zarówno rozpoczęcia, jak i sukcesu lub niepowodzenia tej operacji są rejestrowane w kategorii administracyjnej. Zdarzenia administracyjne zawierają również wszelkie zmiany w ramach kontroli dostępu opartej na rolach w ramach subskrypcji. |
| Service Health | Zawiera rekord wszystkich zdarzeń związanych z kondycją usług, które wystąpiły na platformie Azure. Przykładem zdarzenia Service Health _SQL Azure w regionie Wschodnie stany USA występuje przestój_. <br><br>Service Health zdarzenia są dostępne w sześciu odmianach: _Wymagana akcja_, _wspomagane odzyskiwanie_, _incydent_, _konserwacja_, _informacje_lub _zabezpieczenia_. Te zdarzenia są tworzone tylko wtedy, gdy w subskrypcji znajduje się zasób, na który wpłynie zdarzenie.
| Kondycja zasobu | Zawiera rekord wszystkich zdarzeń związanych z kondycją zasobów, które wystąpiły w Twoich zasobach platformy Azure. Przykładem zdarzenia Resource Health jest _stan kondycji maszyny wirtualnej zmieniony na niedostępny_.<br><br>Zdarzenia Resource Health mogą reprezentować jeden z czterech stanów kondycji: _Dostępne_, _niedostępne_, _obniżone_i nieznane. Ponadto zdarzenia Resource Health mogą być kategoryzowane jako _zainicjowane przez platformę_ lub _zainicjowane przez użytkownika_. |
| Alerty | Zawiera rekord aktywacji dla alertów platformy Azure. Przykładem zdarzenia alertu jest _użycie procesora CPU w systemie 80 myVM w ciągu ostatnich 5 minut_.|
| Automatyczne skalowanie | Zawiera rekord wszystkich zdarzeń związanych z działaniem aparatu skalowania automatycznego na podstawie wszelkich ustawień automatycznego skalowania zdefiniowanych w ramach subskrypcji. Przykładem zdarzenia automatycznego skalowania jest _Akcja skalowania automatycznego w górę_. |
| Zalecenie | Zawiera zdarzenia rekomendacji z Azure Advisor. |
| Bezpieczeństwo | Zawiera rekord wszystkich alertów wygenerowanych przez Azure Security Center. Przykład zdarzenia zabezpieczeń to _podejrzany plik_o podwójnym rozszerzeniu. |
| Zasady | Zawiera rekordy wszystkich operacji akcji wykonywanych przez Azure Policy. Przykłady zdarzeń zasad obejmują inspekcję i _odmowę_. Wszystkie akcje podejmowane przez zasady są modelowane jako operacje na zasobach. |


## <a name="next-steps"></a>Następne kroki

* [Tworzenie profilu dziennika w celu wyeksportowania dziennika aktywności platformy Azure](activity-log-export.md)
* [Prześlij strumieniowo dziennik aktywności platformy Azure do Event Hubs](activity-logs-stream-event-hubs.md)
* [Archiwizuj dziennik aktywności platformy Azure w magazynie](archive-activity-log.md)

