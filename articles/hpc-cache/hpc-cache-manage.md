---
title: Zarządzanie i aktualizowanie pamięci podręcznej platformy Azure HPC (wersja zapoznawcza)
description: Jak zarządzać i aktualizować pamięć podręczną Azure HPC przy użyciu Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: rohogue
ms.openlocfilehash: b5607f1d97e4e240a86ba8e7800af7cb21083217
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952019"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Zarządzanie pamięcią podręczną przy użyciu Azure Portal

Na stronie Przegląd pamięci podręcznej w Azure Portal są wyświetlane szczegóły projektu, stan pamięci podręcznej i podstawowe statystyki pamięci podręcznej. Ma także kontrolki do usuwania pamięci podręcznej, opróżniania danych do długoterminowego przechowywania lub aktualizacji oprogramowania.

Aby otworzyć stronę przegląd, wybierz zasób pamięci podręcznej w Azure Portal. Na przykład Załaduj stronę **wszystkie zasoby** i kliknij nazwę pamięci podręcznej.

![zrzut ekranu przedstawiający stronę omówienia wystąpienia pamięci podręcznej platformy Azure HPC](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

Przyciski w górnej części strony mogą pomóc w zarządzaniu pamięcią podręczną:

* [**Opróżnij**](#flush-cached-data) — zapisuje wszystkie dane w pamięci podręcznej do celów magazynu
* [**Upgrade**](#upgrade-cache-software) — aktualizuje oprogramowanie pamięci podręcznej
* **Refresh** -ponownie ładuje stronę przeglądu
* [**Usuń**](#delete-the-cache) — trwale niszczy pamięć podręczną

Przeczytaj więcej na temat tych opcji poniżej.

## <a name="flush-cached-data"></a>Opróżnij buforowane dane

Przycisk **opróżniania** na stronie Przegląd informuje pamięć podręczną, aby natychmiast napisać wszystkie zmienione dane przechowywane w pamięci podręcznej do obiektów docelowych magazynu zaplecza. Pamięć podręczna zapisuje w sposób rutynowy dane do miejsc docelowych magazynu, dlatego nie trzeba tego robić ręcznie, chyba że chcesz upewnić się, że system przechowywania zaplecza jest aktualny. Można na przykład użyć operacji **opróżniania** przed wykonaniem migawki magazynu lub sprawdzaniem rozmiaru zestawu danych.

> [!NOTE]
> W procesie opróżniania pamięć podręczna nie może udostępniać żądań klientów. Dostęp do pamięci podręcznej jest zawieszony i wznawiany po zakończeniu operacji.

Po uruchomieniu operacji opróżniania pamięci podręcznej pamięć podręczna nie akceptuje żądań klientów, a stan pamięci podręcznej na stronie Przegląd zmieni się na wartość **opróżniania**.

Dane w pamięci podręcznej są zapisywane do odpowiednich obiektów docelowych magazynu. Proces może potrwać kilka minut lub może potrwać godzinę lub dłużej, w zależności od ilości danych, które zostały ostatnio zapisaną w pamięci podręcznej.

Po zapisaniu wszystkich danych w celu przechowania pamięci podręcznej automatycznie zaczynają ponownie żądania klientów. Stan pamięci podręcznej powraca do stanu **dobrej kondycji**.

## <a name="upgrade-cache-software"></a>Uaktualnij oprogramowanie pamięci podręcznej

Jeśli dostępna jest nowa wersja oprogramowania, przycisk **Uaktualnij** zostanie uaktywniony. W górnej części strony może być również wyświetlany komunikat o aktualizowaniu oprogramowania.

![zrzut ekranu przedstawiający górny wiersz przycisków z włączonym przyciskiem Uaktualnij](media/hpc-cache-upgrade-button.png)

Dostęp klienta nie zostanie przerwany podczas uaktualniania oprogramowania, ale wydajność pamięci podręcznej jest niska. Zaplanuj uaktualnienie oprogramowania w godzinach użycia poza szczytem lub w planowanym okresie konserwacji.

Aktualizacja oprogramowania może potrwać kilka godzin. W przypadku pamięci podręcznych skonfigurowanych do większej przepływności trwa dłużej niż w przypadku pamięci podręcznych o mniejszych wartościach o najwyższej wydajności.

Gdy uaktualnienie oprogramowania jest dostępne, masz kilka dni, aby zastosować je ręcznie. Data końcowa jest wyświetlana w komunikacie uaktualniania. Jeśli w tym czasie nie zostanie uaktualniony, platforma Azure automatycznie zastosuje aktualizację do pamięci podręcznej. Nie można skonfigurować chronometrażu automatycznego uaktualniania. Jeśli chodzi o wpływ na wydajność pamięci podręcznej, należy uaktualnić oprogramowanie samodzielnie przed upływem czasu.

Kliknij przycisk **Uaktualnij** , aby rozpocząć aktualizację oprogramowania. Stan pamięci podręcznej zmieni się na **uaktualnienie** do momentu zakończenia operacji.

## <a name="delete-the-cache"></a>Usuń pamięć podręczną

Przycisk **Usuń** niszczy pamięć podręczną. Po usunięciu pamięci podręcznej wszystkie jej zasoby zostaną zniszczone i nie będą już naliczane opłaty za konto.

Usunięcie pamięci podręcznej nie ma żadnych zmian w magazynie docelowym. Możesz dodać je do przyszłej pamięci podręcznej później lub zlikwidować je osobno.

Pamięć podręczna automatycznie opróżnia wszystkie niezapisane dane do miejsc docelowych magazynu w ramach końcowego zamknięcia.

## <a name="cache-metrics-and-monitoring"></a>Metryki pamięci podręcznej i monitorowanie

Na stronie Przegląd przedstawiono wykresy dla niektórych podstawowych statystyk pamięci podręcznej — przepływność pamięci podręcznej, operacje na sekundę i opóźnienie.

![zrzut ekranu przedstawiający trzy wykresy liniowe pokazujący wymienione powyżej dane statystyczne dla przykładowej pamięci podręcznej](media/hpc-cache-overview-stats.png)

Te wykresy są częścią wbudowanych narzędzi do monitorowania i analizowania danych platformy Azure. Dodatkowe narzędzia i alerty są dostępne na stronach pod nagłówkiem **monitorowanie** na pasku bocznym portalu. Więcej informacji znajduje się w sekcji Portal dokumentacji dotyczącej [monitorowania platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

<!-- * Learn more about metrics and statistics for hpc cache -->
* Dowiedz się więcej o [narzędziach metryk i statystyk dotyczących platformy Azure](../azure-monitor/index.yml)
* Uzyskaj [Pomoc dotyczącą pamięci podręcznej platformy Azure HPC](hpc-cache-support-ticket.md)
