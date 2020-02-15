---
title: Zarządzanie i aktualizowanie pamięci podręcznej platformy Azure HPC
description: Jak zarządzać i aktualizować pamięć podręczną Azure HPC przy użyciu Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252046"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Zarządzanie pamięcią podręczną przy użyciu Azure Portal

Na stronie Przegląd pamięci podręcznej w Azure Portal są wyświetlane szczegóły projektu, stan pamięci podręcznej i podstawowe statystyki pamięci podręcznej. Ma także kontrolki do zatrzymywania lub uruchamiania pamięci podręcznej, usuwania pamięci podręcznej, opróżniania danych do długoterminowego przechowywania i aktualizacji oprogramowania.

Aby otworzyć stronę przegląd, wybierz zasób pamięci podręcznej w Azure Portal. Na przykład Załaduj stronę **wszystkie zasoby** i kliknij nazwę pamięci podręcznej.

![zrzut ekranu przedstawiający stronę omówienia wystąpienia pamięci podręcznej platformy Azure HPC](media/hpc-cache-overview.png)

Przyciski w górnej części strony mogą pomóc w zarządzaniu pamięcią podręczną:

* **Uruchamianie** i [**Zatrzymywanie**](#stop-the-cache) — wstrzymywanie operacji pamięci podręcznej
* [**Opróżnianie**](#flush-cached-data) — zapisuje zmienione dane w celu przechowywania
* [**Upgrade**](#upgrade-cache-software) — aktualizuje oprogramowanie pamięci podręcznej
* **Refresh** -ponownie ładuje stronę przeglądu
* [**Usuń**](#delete-the-cache) — trwale niszczy pamięć podręczną

Przeczytaj więcej na temat tych opcji poniżej.

## <a name="stop-the-cache"></a>Zatrzymaj pamięć podręczną

Można zatrzymać pamięć podręczną, aby zmniejszyć koszty w nieaktywnym okresie. Nie są naliczane opłaty za czas przestoju, gdy pamięć podręczna jest zatrzymana, ale opłaty są naliczane za przydzieloną pamięć podręczną. (Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem](https://aka.ms/hpc-cache-pricing) ).

Zatrzymana pamięć podręczna nie odpowiada na żądania klientów. Przed zatrzymaniem pamięci podręcznej należy odinstalować klientów.

Przycisk **Zatrzymaj** wstrzymuje aktywną pamięć podręczną. Przycisk **Zatrzymaj** jest dostępny, gdy stan pamięci podręcznej jest w **dobrej kondycji** lub ma negatywny wpływ na **wydajność**.

![zrzut ekranu górnych przycisków z wyróżnioną pozycją Zatrzymaj i podręczny komunikat opisujący akcję zatrzymania i pytanie "czy chcesz kontynuować?" z opcją Yes (domyślnie) i bez przycisków](media/stop-cache.png)

Po kliknięciu przycisku tak, aby potwierdzić zatrzymywanie pamięci podręcznej, pamięć podręczna automatycznie opróżni swoją zawartość do miejsc docelowych magazynu. Ten proces może zająć trochę czasu, ale zapewnia spójność danych. Na koniec stan pamięci podręcznej zmieni się na **zatrzymany**.

Aby ponownie uaktywnić zatrzymaną pamięć podręczną, kliknij przycisk **Uruchom** . Nie jest wymagany żaden monit.

![zrzut ekranu górnych przycisków z wyróżnioną pozycją Start](media/start-cache.png)

## <a name="flush-cached-data"></a>Opróżnij buforowane dane

Przycisk **opróżniania** na stronie Przegląd informuje pamięć podręczną, aby natychmiast napisać wszystkie zmienione dane przechowywane w pamięci podręcznej do obiektów docelowych magazynu zaplecza. Pamięć podręczna zapisuje w sposób rutynowy dane do miejsc docelowych magazynu, dlatego nie trzeba tego robić ręcznie, chyba że chcesz upewnić się, że system przechowywania zaplecza jest aktualny. Można na przykład użyć operacji **opróżniania** przed wykonaniem migawki magazynu lub sprawdzaniem rozmiaru zestawu danych.

> [!NOTE]
> W procesie opróżniania pamięć podręczna nie może udostępniać żądań klientów. Dostęp do pamięci podręcznej jest zawieszony i wznawiany po zakończeniu operacji.

![zrzut ekranu górnych przycisków z wyróżnioną opcją Opróżnij i podręczny komunikat opisujący akcję opróżniania i pytanie "czy chcesz kontynuować?" z opcją Yes (domyślnie) i bez przycisków](media/hpc-cache-flush.png)

Po uruchomieniu operacji opróżniania pamięci podręcznej pamięć podręczna nie akceptuje żądań klientów, a stan pamięci podręcznej na stronie Przegląd zmieni się na wartość **opróżniania**.

Dane w pamięci podręcznej są zapisywane do odpowiednich obiektów docelowych magazynu. W zależności od ilości danych, które muszą zostać opróżnione, proces może potrwać kilka minut lub za godzinę.

Po zapisaniu wszystkich danych w celu przechowania pamięci podręcznej automatycznie zaczynają ponownie żądania klientów. Stan pamięci podręcznej powraca do stanu **dobrej kondycji**.

## <a name="upgrade-cache-software"></a>Uaktualnij oprogramowanie pamięci podręcznej

Jeśli dostępna jest nowa wersja oprogramowania, przycisk **Uaktualnij** zostanie uaktywniony. W górnej części strony pojawi się również komunikat o aktualizowaniu oprogramowania.

![zrzut ekranu przedstawiający górny wiersz przycisków z włączonym przyciskiem Uaktualnij](media/hpc-cache-upgrade-button.png)

Dostęp klienta nie zostanie przerwany podczas uaktualniania oprogramowania, ale wydajność pamięci podręcznej jest niska. Zaplanuj uaktualnienie oprogramowania w godzinach użycia poza szczytem lub w planowanym okresie konserwacji.

Aktualizacja oprogramowania może potrwać kilka godzin. W przypadku pamięci podręcznych skonfigurowanych do większej przepływności trwa dłużej niż w przypadku pamięci podręcznych o mniejszych wartościach o najwyższej wydajności.

Po udostępnieniu uaktualnienia oprogramowania użytkownik będzie miał tydzień lub powinien go zastosować ręcznie. Data końcowa jest wyświetlana w komunikacie uaktualniania. Jeśli w tym czasie nie zostanie uaktualniony, platforma Azure automatycznie zastosuje aktualizację do pamięci podręcznej. Nie można skonfigurować chronometrażu automatycznego uaktualniania. Jeśli chodzi o wpływ na wydajność pamięci podręcznej, należy uaktualnić oprogramowanie samodzielnie przed upływem czasu.

Jeśli pamięć podręczna zostanie zatrzymana po upływie daty zakończenia, pamięć podręczna będzie automatycznie uaktualniać oprogramowanie przy kolejnym uruchomieniu. (Aktualizacja może nie zacząć od razu, ale rozpocznie się w ciągu pierwszej godziny).

Kliknij przycisk **Uaktualnij** , aby rozpocząć aktualizację oprogramowania. Stan pamięci podręcznej zmieni się na **uaktualnienie** do momentu zakończenia operacji.

## <a name="delete-the-cache"></a>Usuń pamięć podręczną

Przycisk **Usuń** niszczy pamięć podręczną. Po usunięciu pamięci podręcznej wszystkie jej zasoby zostaną zniszczone i nie będą już naliczane opłaty za konto.

W przypadku usunięcia pamięci podręcznej nie ma to żadnego oddziaływania na woluminy magazynu zaplecza używane jako cele magazynu. Możesz dodać je do przyszłej pamięci podręcznej później lub zlikwidować je osobno.

> [!NOTE]
> Pamięć podręczna Azure HPC nie zapisuje automatycznie zmienionych danych z pamięci podręcznej w systemach magazynu zaplecza przed usunięciem pamięci podręcznej.
>
> Aby upewnić się, że wszystkie dane w pamięci podręcznej zostały zapisaną do magazynu długoterminowego, [Zatrzymaj pamięć podręczną](#stop-the-cache) przed jego usunięciem. Upewnij się, że stan **został zatrzymany** przed kliknięciem przycisku Usuń.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Metryki pamięci podręcznej i monitorowanie

Na stronie Przegląd przedstawiono wykresy dla niektórych podstawowych statystyk pamięci podręcznej — przepływność pamięci podręcznej, operacje na sekundę i opóźnienie.

![zrzut ekranu przedstawiający trzy wykresy liniowe pokazujący wymienione powyżej dane statystyczne dla przykładowej pamięci podręcznej](media/hpc-cache-overview-stats.png)

Te wykresy są częścią wbudowanych narzędzi do monitorowania i analizowania danych platformy Azure. Dodatkowe narzędzia i alerty są dostępne na stronach pod nagłówkiem **monitorowanie** na pasku bocznym portalu. Więcej informacji znajduje się w sekcji Portal dokumentacji dotyczącej [monitorowania platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

<!-- * Learn more about metrics and statistics for hpc cache -->
* Dowiedz się więcej o [narzędziach metryk i statystyk dotyczących platformy Azure](../azure-monitor/index.yml)
* Uzyskaj [Pomoc dotyczącą pamięci podręcznej platformy Azure HPC](hpc-cache-support-ticket.md)
