---
title: Konfiguracja monitorów kondycji usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: Ten artykuł zawiera zawartość opisującą szczegółową konfigurację monitorów kondycji w usłudze Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055697"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Przewodnik konfiguracji monitora kondycji usługi Azure Monitor dla kontenerów

Monitory są podstawowym elementem pomiaru kondycji i wykrywania błędów w usłudze Azure Monitor dla kontenerów. Ten artykuł ułatwia zrozumienie pojęć, jak kondycja jest mierzona i elementy, które składają się na model kondycji do monitorowania i raportowania kondycji klastra Kubernetes z funkcji [kondycji (wersja zapoznawcza).](container-insights-health.md)

>[!NOTE]
>Funkcja Kondycja jest w publicznej wersji zapoznawczej w tej chwili.
>

## <a name="monitors"></a>Monitory

Monitor mierzy kondycję niektórych aspektów obiektu zarządzanego. Monitory każdy ma dwa lub trzy stany kondycji. Monitor będzie w jednym i tylko jednym z jego potencjalnych stanów w danym momencie. Po załadowaniu monitora przez konteneryzowany agent jest inicjowany do stanu dobrej kondycji. Stan zmienia się tylko wtedy, gdy zostaną wykryte określone warunki dla innego stanu.

Ogólny stan zdrowia określonego obiektu jest określany na podstawie kondycji każdego z jego monitorów. Ta hierarchia jest zilustrowana w okienku hierarchii kondycji w usłudze Azure Monitor dla kontenerów. Zasady dotyczące sposobu rzutowania kondycji jest częścią konfiguracji monitorów agregacji.

## <a name="types-of-monitors"></a>Rodzaje monitorów

|Monitorowanie | Opis | 
|--------|-------------|
| Monitor jednostki |Monitor jednostki mierzy pewien aspekt zasobu lub aplikacji. Może to być sprawdzanie licznika wydajności w celu określenia wydajności zasobu lub jego dostępności. |
|Monitor agregatu | Monitory agregowane grupują wiele monitorów, aby zapewnić jeden stan kondycji zagregowanej kondycji kondycji. Monitory jednostek są zazwyczaj konfigurowane w określonym monitorze agregatu. Na przykład monitor agregacji węzła zestawia stan wykorzystania procesora CPU węzła, wykorzystania pamięci i stanu węzła.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Zagregowane zasady zbiorczego monitorowania kondycji

Każdy monitor agregacji definiuje zasady zestawienia kondycji, która jest logika, która jest używana do określenia kondycji monitora agregacji na podstawie kondycji monitorów pod nim. Możliwe zasady zestawienia kondycji dla monitora zbiorczego są następujące:

#### <a name="worst-state-policy"></a>Najgorsza polityka państwa

Stan monitora agregacji odpowiada stan monitora podrzędnego z najgorszym stanem kondycji. Jest to najbardziej typowe zasady używane przez monitory agregacji.

![Przykład zagregowanego stanu zestawienia monitora](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Zasady procentowe

Obiekt źródłowy pasuje do najgorszego stanu pojedynczego elementu członkowskiego określonego procentu obiektów docelowych w najlepszym stanie. Ta zasada jest używana, gdy określony procent obiektów docelowych musi być w dobrej kondycji dla obiektu docelowego, który ma być uważany za zdrowy. Zasady procentowe sortuje monitory w kolejności malejącej ważności stanu, a stan monitora agregowanego jest obliczany jako najgorszy stan N% (N jest podyktowane parametrem konfiguracji *StateThresholdPercentage).*

Załóżmy na przykład, że istnieje pięć wystąpień kontenera obrazu kontenera, a ich poszczególne stany to **Krytyczne**, **Ostrzeżenie,** **Zdrowy,** **Zdrowy,** **Zdrowy**.  Stan monitora wykorzystania procesora CPU kontenera będzie **krytyczny,** ponieważ najgorszy stan 90% kontenerów jest **krytyczny** podczas sortowania w malejącej kolejności ważności.

## <a name="understand-the-monitoring-configuration"></a>Opis konfiguracji monitorowania

Usługa Azure Monitor dla kontenerów zawiera szereg scenariuszy monitorowania kluczy, które są skonfigurowane w następujący sposób.

### <a name="unit-monitors"></a>Monitory jednostek

|**Nazwa monitora** | Typ monitora | **Opis** | **Parametr** | **Wartość** |
|-----------------|--------------|-----------------|---------------|-----------|
|Wykorzystanie pamięci węzła |Monitor jednostki |Ten monitor ocenia wykorzystanie pamięci węzła co minutę, przy użyciu danych zgłoszonych przez cadvisor. |KolejnepróbyForStateTransition<br> FailIfGreaterThanPercentage (FailIfGreaterThanPercentage)<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Wykorzystanie procesora CPU węzła |Monitor jednostki |Ten monitor sprawdza wykorzystanie procesora CPU węzła co minutę, przy użyciu danych zgłoszonych przez cadvisor. | KolejnepróbyForStateTransition<br> FailIfGreaterThanPercentage (FailIfGreaterThanPercentage)<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Stan węzła |Monitor jednostki |Ten monitor sprawdza warunki węzła zgłaszane przez kubernetes.<br> Obecnie sprawdzane są następujące warunki węzła: Ciśnienie dysku, Ciśnienie pamięci, Ciśnienie PID, Brak dysku, Sieć niedostępna, Stan gotowości dla węzła.<br> Z powyższych warunków, jeśli jest **spełniony**brak *dysku* lub niedostępny w *sieci,* monitor zmieni stan **krytyczny.**<br> Jeśli inne warunki równe **true**, inne niż **Stan Gotowy,** monitor zmienia się w stan **ostrzeżenie.** | NodeConditionTypeForFailedState (Typami dla węzłów) | outofdisk, siećniedostępna ||
|Wykorzystanie pamięci kontenera |Monitor jednostki |Ten monitor raportuje połączony stan kondycji wykorzystania pamięci (RSS) wystąpień kontenera.<br> Wykonuje proste porównanie, które porównuje każdą próbkę do pojedynczego progu i jest określone przez parametr konfiguracji **ConsecutiveSamplesForStateTransition**.<br> Jego stan jest obliczany jako najgorszy stan 90% wystąpień kontenera (StateThresholdPercentage), sortowane w kolejności malejącej ważności stanu kondycji kontenera (czyli Krytyczne, Ostrzeżenie, Zdrowy).<br> Jeśli żaden rekord nie zostanie odebrany z wystąpienia kontenera, stan kondycji wystąpienia kontenera jest zgłaszany jako **Nieznany**i ma wyższy priorytet w kolejności sortowania nad **stanem krytycznym.**<br> Stan każdego wystąpienia kontenera jest obliczany przy użyciu progów określonych w konfiguracji. Jeśli użycie przekracza próg krytyczny (90%), wystąpienie jest w stanie **krytycznym,** jeśli jest mniejsze niż próg krytyczny (90%) ale większa niż próg ostrzeżenia (80%), a następnie wystąpienie jest w stanie **ostrzeżenie.** W przeciwnym razie jest w stanie **zdrowym.** |KolejnepróbyForStateTransition<br> FailIfLessThanPercentage (FailIfLessThanPercentage)<br> StanThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Wykorzystanie procesora kontenerowego |Monitor jednostki |Ten monitor raportuje połączony stan kondycji wykorzystania procesora CPU wystąpień kontenera.<br> Wykonuje proste porównanie, które porównuje każdą próbkę do pojedynczego progu i jest określone przez parametr konfiguracji **ConsecutiveSamplesForStateTransition**.<br> Jego stan jest obliczany jako najgorszy stan 90% wystąpień kontenera (StateThresholdPercentage), sortowane w kolejności malejącej ważności stanu kondycji kontenera (czyli Krytyczne, Ostrzeżenie, Zdrowy).<br> Jeśli żaden rekord nie zostanie odebrany z wystąpienia kontenera, stan kondycji wystąpienia kontenera jest zgłaszany jako **Nieznany**i ma wyższy priorytet w kolejności sortowania nad **stanem krytycznym.**<br> Stan każdego wystąpienia kontenera jest obliczany przy użyciu progów określonych w konfiguracji. Jeśli użycie przekracza próg krytyczny (90%), wystąpienie jest w stanie **krytycznym,** jeśli jest mniejsze niż próg krytyczny (90%) ale większa niż próg ostrzeżenia (80%), a następnie wystąpienie jest w stanie **ostrzeżenie.** W przeciwnym razie jest w stanie **zdrowym.** |KolejnepróbyForStateTransition<br> FailIfLessThanPercentage (FailIfLessThanPercentage)<br> StanThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Gotowe zasobniki obciążenia systemu |Monitor jednostki |Ten monitor raportuje stan na podstawie procentu zasobników w stanie gotowości w danym obciążeniu. Jego stan jest ustawiony na **Krytyczny,** jeśli mniej niż 100% strąków jest w stanie **zdrowym** |KolejnepróbyForStateTransition<br> FailIfLessThanPercentage (FailIfLessThanPercentage) |2<br> 100 ||
|Stan interfejsu API kube |Monitor jednostki |Ten monitor raportuje stan usługi Kube API. Monitor jest w stanie krytycznym w przypadku, gdy punkt końcowy interfejsu API kube jest niedostępny. Dla tego konkretnego monitora stan jest określany przez wykonanie kwerendy do punktu końcowego "węzły" dla serwera kube-api. Wszystko inne niż kod odpowiedzi OK zmienia monitora w stanie **krytycznym.** | Brak właściwości konfiguracji |||

### <a name="aggregate-monitors"></a>Monitory agregowane

|**Nazwa monitora** | **Opis** | **Algorytm** |
|-----------------|-----------------|---------------|
|Węzeł |Ten monitor jest agregatem wszystkich monitorów węzłów. Pasuje do stanu monitora podrzędnego z najgorszym stanem kondycji:<br> Wykorzystanie procesora w węźle<br> Wykorzystanie pamięci węzła<br> Stan węzła | Najgorsze z|
|Pula węzłów |Ten monitor raportuje połączony stan kondycji wszystkich węzłów w *puli agentów puli węzłów*. Jest to monitor trzech stanów, którego stan jest oparty na najgorszym stanie 80% węzłów w puli węzłów, posortowane w kolejności malejącej ważności stanów węzłów (czyli Krytyczne, Ostrzeżenie, Zdrowy).|Procentowe |
|Węzły (nadrzędny puli węzłów) |Jest to monitor agregacji wszystkich pul węzłów. Jego stan jest oparty na najgorszym stanie monitorów podrzędnych (czyli pul węzłów znajdujących się w klastrze). |Najgorsze z |
|Klaster (element nadrzędny węzłów/<br> infrastruktury Kubernetes) |Jest to monitor nadrzędny, który pasuje do stanu monitora podrzędnego z najgorszym stanem kondycji, czyli infrastrukturą i węzłami kubernetes. |Najgorsze z |
|Infrastruktura Kubernetes |Ten monitor raportuje połączony stan kondycji składników infrastruktury zarządzanej klastra. jego stan jest obliczany jako "najgorszy" jego podrzędny monitor stanów, czyli obciążeń systemu kube i statusu serwera API. |Najgorsze z|
|Obciążenie systemu |Ten monitor raportuje stan kondycji obciążenia systemu kube. Ten monitor pasuje do stanu monitora podrzędnego z najgorszym stanem kondycji, czyli **zasobników w stanie gotowości** (monitor i kontenery w obciążeniu). |Najgorsze z |
|Kontener |Ten monitor raportuje ogólny stan kondycji kontenera w danym obciążeniu. Ten monitor dopasowuje stan monitora podrzędnego do stanu najgorszego stanu kondycji, czyli **monitorów wykorzystania procesora CPU** i **wykorzystania pamięci.** |Najgorsze z |

## <a name="next-steps"></a>Następne kroki

Wyświetl [kondycję klastra monitora,](container-insights-health.md) aby dowiedzieć się więcej o wyświetlaniu stanu kondycji klastra usługi Kubernetes.
