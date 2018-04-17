---
title: Skalowanie funkcji platformy Azure i hosting | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać planowania użycia funkcji Azure i plan usługi aplikacji.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkcje, funkcje, użycie planu, plan usługi aplikacji, przetwarzania zdarzeń, elementów webhook, dynamiczne obliczeń, architektura niekorzystającą
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4c43477a28efe01fd197a0c09afadb338638036
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="azure-functions-scale-and-hosting"></a>Skala funkcji platformy Azure i hostingu

Można uruchomić usługi Azure Functions w dwóch różnych trybach: plan zużycia i plan usługi aplikacji Azure. Plan zużycie automatycznie przydzieli moc obliczeniową, gdy kod działa, skaluje się wymagane do obsługi obciążenia, a następnie skalowany w dół, gdy kodu nie jest uruchomiona. Nie trzeba płacić za maszyny wirtualne w stanie bezczynności i nie trzeba wydajność rezerwowa z wyprzedzeniem. Ten artykuł skupia się na plan zużycie [niekorzystającą](https://azure.microsoft.com/overview/serverless-computing/) modelu aplikacji. Aby uzyskać szczegółowe informacje dotyczące sposobu działania plan usługi aplikacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

>[!NOTE]  
> [Linux hosting](functions-create-first-azure-function-azure-cli-linux.md) jest obecnie dostępny tylko na plan usługi aplikacji.

Jeśli nie znasz usługi Azure Functions, zobacz [Azure Functions — omówienie](functions-overview.md).

Podczas tworzenia aplikacji funkcji, należy skonfigurować plan hostingu dla funkcji, które zawiera aplikację. W trybie wystąpienia *hosta usługi Azure Functions* wykonuje funkcje. Typ kontrolki plan:

* Jak wystąpień hosta jest skalowana w poziomie.
* Zasoby, które są dostępne dla każdego hosta.

Obecnie musisz wybrać typ plan hostingu podczas tworzenia aplikacji funkcji. Nie można zmienić go później. 

W planie usługi aplikacji można skalować między warstwami przydzielić różnych ilości zasobów. W planie użycia usługi Azure Functions obsługuje automatycznie alokacji zasobów.

## <a name="consumption-plan"></a>Plan Zużycie

Podczas korzystania z planu zużycia, wystąpienia usługi Azure Functions hosta są dynamicznie dodawane i usuwane zależności od liczby zdarzeń przychodzących. Ten plan skaluje automatycznie, a użytkownik naliczane są opłaty za zasoby obliczeniowe tylko wtedy, gdy są uruchomione funkcji. Na plan użycie funkcji można uruchomić maksymalnie 10 minut. 

> [!NOTE]
> Domyślny limit czasu dla funkcji w planie zużycie wynosi 5 minut. Wartość można zwiększyć do 10 minut dla aplikacji funkcja, zmieniając właściwość `functionTimeout` w [host.json](functions-host-json.md#functiontimeout) pliku projektu.

Karta jest oparta na liczbę wykonaniami, czas wykonywania i używanej pamięci. Karta jest zagregowane we wszystkich funkcji w aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [usługi Azure Functions cennikiem].

Plan zużycie jest domyślny plan hostingu i oferuje następujące korzyści:
- Płać tylko wtedy, gdy uruchomione są funkcji.
- Skalowanie w poziomie automatycznie, nawet w okresie wysoki obciążenia.

## <a name="app-service-plan"></a>Plan usługi App Service

W planie usługi aplikacji — warstwa aplikacji funkcji Uruchom na dedykowanych maszynach wirtualnych na Basic, Standard, Premium i izolowane jednostki SKU, podobnie jak aplikacje sieci Web, aplikacje interfejsu API i aplikacje mobilne. Dedykowanych maszyn wirtualnych są przydzielone do aplikacji usługi aplikacji, co oznacza, że na hoście funkcji zawsze działa. Plany usługi App Service obsługuje Linux.

Należy wziąć pod uwagę plan usługi aplikacji w następujących przypadkach:
- Masz istniejący, niedostatecznie maszyn wirtualnych, które zostały już uruchomione inne wystąpienia usługi aplikacji.
- Spodziewasz się funkcji aplikacji do uruchamiania stale lub prawie w sposób ciągły. W takim przypadku planu usługi App Service może być bardziej ekonomiczne rozwiązanie.
- Potrzebujesz więcej opcji procesora CPU lub pamięci niż jest podana w planie zużycia.
- Musisz uruchomić dłużej niż maksymalny czas wykonywania dozwolone na użycie planu (10 minut).
- Wymagane funkcje, które są dostępne tylko na plan usługi aplikacji, takie jak obsługa środowiska usługi aplikacji, łączność w sieci Wirtualnej lub sieć VPN i większych rozmiarów maszyn wirtualnych. 
- Aby uruchomić aplikację funkcji w systemie Linux, lub chcesz wyrazić niestandardowego obrazu, na którym należy uruchamiać swoje funkcje.

Maszyna wirtualna oddziela kosztów od liczby wykonaniami, czas wykonywania i używanej pamięci. W związku z tym nie będzie płacisz więcej niż koszt wystąpienia maszyny Wirtualnej, które zostało przydzielone. Aby uzyskać szczegółowe informacje dotyczące sposobu działania plan usługi aplikacji, zobacz [szczegółowe omówienie planów usługi aplikacji Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Plan usługi aplikacji można ręcznie skalować w poziomie przez dodanie więcej wystąpień maszyny Wirtualnej, lub można włączyć automatycznego skalowania. Aby uzyskać więcej informacji, zobacz [skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Można także skalowanie w górę, wybierając inny plan usługi aplikacji. Aby uzyskać więcej informacji, zobacz [skalowanie w górę aplikacji na platformie Azure](../app-service/web-sites-scale.md). 

Jeśli planujesz uruchamianie funkcji JavaScript na plan usługi aplikacji, należy wybrać plan, który ma mniej Vcpu. Aby uzyskać więcej informacji, zobacz [wybierz planów usługi App Service jednordzeniowy](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
### Zawsze włączone

Jeśli zostanie uruchomione na plan usługi aplikacji, należy włączyć **zawsze na** tak, aby aplikacja funkcja działa poprawnie. W planie usługi aplikacji — środowisko uruchomieniowe functions przejdzie bezczynności, po upływie kilku minut braku aktywności, tak tylko wyzwalaczy HTTP "wzbudzania" funkcji. Efekt jest podobny do sposobu zadania Webjob musi mieć zawsze włączone. 

Zawsze włączone jest dostępna tylko na plan usługi aplikacji. Na plan zużycie platformy aktywuje automatycznie funkcji aplikacji.

## <a name="storage-account-requirements"></a>Wymagania konta magazynu

Plan zużycia lub plan usługi aplikacji aplikacji funkcji wymaga ogólne konto magazynu Azure obsługuje magazynu obiektów Blob platformy Azure, kolejki, pliki i tabeli. Wewnętrznie usługi Azure Functions używa usługi Azure Storage dla operacji, takich jak zarządzanie wyzwalaczy i rejestrowanie wykonania funkcji. Niektóre konta magazynu nie obsługują kolejek i tabel, takich jak konta magazynu tylko do obiektów blob (w tym magazyn w warstwie premium) i kont magazynu ogólnego przeznaczenia za pomocą replikacji magazyn strefowo nadmiarowy. Te konta są filtrowane z **konta magazynu** bloku podczas tworzenia aplikacji funkcji.

<!-- JH: Does using a PRemium Storage account improve perf? -->

Aby dowiedzieć się więcej na temat typów kont magazynu, zobacz [wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Jak działa planu zużycie

Kontroler skali w planie zużycie jest automatycznie skalowany zasobów Procesora i pamięci przez dodanie dodatkowych wystąpień funkcje hosta, na podstawie liczby zdarzeń, które funkcje są uruchamiane na. Każde wystąpienie hosta funkcji jest ograniczona do 1,5 GB pamięci.  Wystąpienie hosta to funkcja aplikacji, co oznacza wszystkie funkcje w funkcji aplikacji współużytkowanie zasobów w ramach wystąpienia i skali, w tym samym czasie.

Użycie zużycie plan hostingu, funkcja kodu pliki są przechowywane w udziałach plików Azure na koncie magazynu głównego funkcji. Podczas usuwania konta magazynu głównego aplikacji funkcji plików kodu funkcji zostaną usunięte i nie może zostać odzyskany.

> [!NOTE]
> Podczas korzystania z wyzwalacza obiektu blob w planie zużycia, może istnieć maksymalnie 10-minutowych opóźnienia w przetwarzaniu nowe obiekty BLOB, jeśli aplikacja funkcji przeszedł bezczynności. Po uruchomieniu aplikacji funkcja obiekty BLOB są przetwarzane natychmiast. Aby uniknąć tego opóźnienia początkowej, weź pod uwagę jedną z następujących opcji:
> - Host aplikacji funkcji na plan usługi aplikacji z na zawsze włączone.
> - Użyj innego mechanizmu wyzwalanie blob przetwarzania, takich jak subskrypcja siatki zdarzeń lub komunikat z kolejki, zawierający nazwę obiektu blob. Na przykład zobacz [przykłady dla obiektu blob danych wejściowych powiązania](functions-bindings-storage-blob.md#input---example).

### <a name="runtime-scaling"></a>Skalowanie środowiska wykonawczego

Środowisko Azure Functions używa składnik o nazwie *kontrolera skali* do monitorowania częstość zdarzeń i określanie, czy do skalowania w poziomie oraz skalowanie w. Kontroler skali korzysta z algorytmów heurystycznych dla każdego typu wyzwalacza. Na przykład gdy używasz wyzwalacz magazynu kolejek Azure skaluje na podstawie długość kolejki i wiek najstarsze komunikatu w kolejce.

Jednostka skalowania jest aplikacja funkcji. Kiedy funkcja aplikacji jest skalowana w poziomie, dodatkowe zasoby są przydzielane do uruchomienia wielu wystąpień hosta usługi Azure Functions. Z drugiej strony jak żądanie zostanie zmniejszona obliczeń, kontrolera skali usuwa wystąpienia hosta funkcji. Liczba wystąpień jest ostatecznie skalowany w dół od zera uruchomionej żadnych funkcji w funkcji aplikacji.

![Kontroler skali monitorowanie zdarzeń i tworzenie wystąpień](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Opis zachowania skalowania

Skalowanie może się różnić w przypadku wielu czynników, a inaczej w zależności od wyzwalacza i językiem wybranym skali. Istnieją jednak kilka aspektów skalowania, która istnieje obecnie w systemie:
* Aplikacji jednej funkcji tylko będzie skalowana do maksymalnie 200 wystąpieniami. Pojedyncze wystąpienie może przetwarzać więcej niż jednego komunikatu lub żądania w czasie, więc nie ma ustawiony limit liczby współbieżnych wykonaniami.
* Co najwyżej raz na 10 sekund tylko zostaną przydzielone nowe wystąpienia.

Wyzwalacze różnych mogą także mieć różne ograniczonym zakresie, a także udokumentowane poniżej:

* [Centrum zdarzeń](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Najlepsze rozwiązania i wzorce dla skalowalnych aplikacji

Istnieje wiele aspektów aplikacji funkcji, które mają wpływ na skuteczność go są skalowane, łącznie z konfiguracji hosta, środowiska uruchomieniowego rozmiaru i wydajności zasobów.  Widok [skalowalności w dalszej części artykułu zagadnienia dotyczące wydajności](functions-best-practices.md#scalability-best-practices) Aby uzyskać więcej informacji.

### <a name="billing-model"></a>Model rozliczania

Rozliczeń dla planu zużycie opisano szczegółowo w [usługi Azure Functions cennikiem]. Użycie jest agregowana na poziomie aplikacji funkcji i liczby tylko wykonywany jest kod w funkcji czasu. Jednostki do rozliczeń są następujące: 
* **Zużycie zasobów w ciągu sekund na gigabajtów (GB-s)**. Obliczony jako połączenie rozmiar pamięci i czasu wykonywania dla wszystkich funkcji w funkcji aplikacji. 
* **Wykonaniami**. Zliczane każdym razem, gdy funkcja jest wykonywana w odpowiedzi na zdarzenia wyzwalacza.

[usługi Azure Functions cennikiem]: https://azure.microsoft.com/pricing/details/functions
