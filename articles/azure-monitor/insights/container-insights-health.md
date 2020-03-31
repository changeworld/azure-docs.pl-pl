---
title: Monitorowanie kondycji klastra kubernetes za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak można wyświetlać i analizować kondycję klastrów AKS i innych niż AKS za pomocą usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843994"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Poznaj kondycję klastra usługi Kubernetes za pomocą usługi Azure Monitor dla kontenerów

Dzięki usłudze Azure Monitor dla kontenerów monitoruje i raportuje stan kondycji składników infrastruktury zarządzanej i wszystkich węzłów uruchomionych w dowolnym klastrze usługi Kubernetes obsługiwanym przez usługę Azure Monitor dla kontenerów. To środowisko wykracza poza stan kondycji klastra obliczony i zgłoszony w [widoku wieloas klastrowym,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)gdzie teraz można zrozumieć, czy jeden lub więcej węzłów w klastrze jest ograniczonych zasobami lub węzeł lub zasobnik jest niedostępny, co może mieć wpływ na uruchomioną aplikację w klastrze na podstawie wyselekcjonowanych metryk.

>[!NOTE]
>Funkcja Kondycja jest w publicznej wersji zapoznawczej w tej chwili.
>

Aby uzyskać informacje dotyczące włączania usługi Azure Monitor dla kontenerów, zobacz [Wbudowany monitor Azure Dla kontenerów.](container-insights-onboard.md)

>[!NOTE]
>Aby obsługiwać klastry aparatu AKS, sprawdź, czy spełnia następujące wymagania:
>- Korzysta z najnowszej wersji [klienta HELM.](https://helm.sh/docs/using_helm/)
>- Konteneryzowana wersja agenta to *microsoft/oms:ciprod11012019*. Aby uaktualnić agenta, zobacz [uaktualnianie agenta w klastrze Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Omówienie

W usłudze Azure Monitor dla kontenerów funkcja kondycji (wersja zapoznawcza) zapewnia proaktywne monitorowanie kondycji klastra usługi Kubernetes, aby ułatwić identyfikowanie i diagnozowanie problemów. To daje możliwość wyświetlania znaczących problemów wykrytych. Monitoruje ocenę kondycji klastra uruchamianego na konteneryzowanym agencie w klastrze, a dane kondycji są zapisywane w tabeli **KubeHealth** w obszarze roboczym usługi Log Analytics. 

Kondycja klastra usługi Kubernetes jest oparta na wielu scenariuszach monitorowania zorganizowanych przez następujące obiekty i abstrakcje programu Kubernetes:

- Infrastruktura Kubernetes — udostępnia pakiet zbiorczy serwera interfejsu API usługi Kubernetes, zestawów replik i zestawów demonów uruchomionych w węzłach wdrożonych w klastrze przez ocenę wykorzystania procesora i pamięci oraz dostępność zasobników

    ![Widok zestawienia kondycji infrastruktury Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Węzły — zapewnia zestawienie pul węzłów i stan poszczególnych węzłów w każdej puli, oceniając wykorzystanie procesora CPU i pamięci oraz stan węzła zgłoszony przez kubernetes.

    ![Widok zestawienia kondycji węzłów](./media/container-insights-health/health-view-nodes-01.png)

Obecnie obsługiwany jest tylko stan wirtualnego kubelet. Stan kondycji procesora CPU i wykorzystania pamięci wirtualnych węzłów kublet jest zgłaszany jako **Nieznany,** ponieważ sygnał nie jest odbierany od nich.

Wszystkie monitory są wyświetlane w układzie hierarchicznym w okienku Hierarchia kondycji, gdzie monitor agregacji reprezentujący obiekt lub abstrakcję Kubernetes (czyli infrastrukturę kubernetes lub węzły) są najbardziej najważniejszym monitorem odzwierciedlającym połączony stan zdrowia wszystkich monitorów podrzędnych. Kluczowe scenariusze monitorowania używane do wyprowadzania kondycji to:

* Oceń wykorzystanie procesora CPU z węzła i kontenera.
* Oceń wykorzystanie pamięci z węzła i kontenera.
* Stan zasobników i węzłów na podstawie obliczania ich stanu gotowości zgłoszonych przez kubernetes.

Do wskazywania stanu używane są następujące ikony:

|Ikona|Znaczenie|  
|--------|-----------|  
|![Zielona ikona znacznika wyboru oznacza dobrą kondycję](./media/container-insights-health/healthyicon.png)|Sukces, kondycja OK (zielony)|  
|![Żółty trójkąt i wykrzyknik oznaczają ostrzeżenie](./media/container-insights-health/warningicon.png)|Ostrzeżenie (żółty)|  
|![Czerwony przycisk z białym znakiem X oznacza stan krytyczny](./media/container-insights-health/criticalicon.png)|Krytyczny (czerwony)|  
|![Ikona wyszarzone](./media/container-insights-health/grayicon.png)|Nieznany (szary)|  

## <a name="monitor-configuration"></a>Konfiguracja monitora

Aby zrozumieć zachowanie i konfigurację każdego monitora obsługującego usługę Azure Monitor dla funkcji kondycji kontenerów, zobacz [Przewodnik konfiguracji monitora kondycji.](container-insights-health-monitors-config.md)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Wyświetlanie kondycji klastra AKS lub innego niż AKS

Dostęp do funkcji Usługi Azure Monitor dla kontenerów Kondycja (wersja zapoznawcza) jest dostępny bezpośrednio z klastra AKS, wybierając **pozycję Insights** z lewego okienka w witrynie Azure portal. W sekcji **Insights** wybierz pozycję **Kontenery**. 

Aby wyświetlić kondycję z klastra innego niż AKS, który jest klastrem aparatu AKS hostowanym lokalnie lub w usłudze Azure Stack, wybierz **usługę Azure Monitor** z lewego okienka w witrynie Azure portal. W sekcji **Insights** wybierz pozycję **Kontenery**.  Na stronie z wieloma klastrami wybierz klaster nienawiązywny z listy.

W usłudze Azure Monitor dla kontenerów na stronie **Klaster** wybierz pozycję **Kondycja**.

![Przykład pulpitu nawigacyjnego kondycji klastra](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Przeglądanie kondycji klastra

Po otwarciu strony Kondycja domyślnie wybrano **opcję Infrastruktura kubernetes** w siatce **Aspekt kondycji.**  Siatka podsumowuje bieżący stan zestawienia kondycji infrastruktury kubernetes i węzłów klastra. Wybranie obu aspektów kondycji aktualizuje wyniki w okienku Hierarchia kondycji (czyli środkowym okienku) i pokazuje wszystkie monitory podrzędne w układzie hierarchicznym, wyświetlając ich bieżący stan kondycji. Aby wyświetlić więcej informacji o monitorze zależnym, można wybrać jeden z nich, a okienko właściwości zostanie automatycznie wyświetlone po prawej stronie strony. 

![Okienko właściwości kondycji klastra](./media/container-insights-health/health-view-property-pane.png)

W okienku właściwości dowiesz się, co następuje:

- Na **karcie Przegląd** pokazuje bieżący stan wybranego monitora, kiedy monitor został ostatnio obliczony i kiedy wystąpiła ostatnia zmiana stanu. Dodatkowe informacje są wyświetlane w zależności od typu monitora wybranego w hierarchii.

    Jeśli wybierzesz monitor agregacji w okienku Hierarchia kondycji, na karcie **Przegląd** w okienku właściwości znajduje się zestawienie całkowitej liczby monitorów podrzędnych w hierarchii oraz liczby monitorów agregacji w stanie krytycznym, ostrzegawczym i zdrowym. 

    ![Karta Przegląd okienka właściwości kondycji dla monitora agregacji](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Jeśli wybierzesz monitor jednostki w okienku Hierarchia kondycji, pokazuje również w obszarze **Ostatni stan zmienić** poprzednie próbki obliczone i zgłoszone przez agenta konteneryzowanego w ciągu ostatnich czterech godzin. Jest to oparte na obliczeniach monitorów jednostki w celu porównania kilku kolejnych wartości w celu określenia jego stanu. Na przykład, jeśli wybrano monitor jednostki *stanu Pod gotowy,* pokazuje ostatnie dwie próbki kontrolowane przez parametr *ConsecutiveSamplesForStateTransition*. Aby uzyskać więcej informacji, zobacz szczegółowy opis [monitorów jednostek](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Karta Przegląd okienka właściwości kondycji](./media/container-insights-health/health-overview-unit-monitor.png)

    Jeśli czas zgłoszony przez **ostatnią zmianę stanu** jest dzień lub starszy, jest wynikiem żadnych zmian w stanie monitora. Jednak jeśli ostatnia próbka odebrana dla monitora jednostki ma więcej niż cztery godziny, prawdopodobnie oznacza to, że konteneryzowany agent nie wysyłał danych. Jeśli agent wie, że określony zasób istnieje, na przykład węzeł, ale nie otrzymał danych z monitorów wykorzystania procesora lub pamięci węzła (na przykład), stan kondycji monitora jest ustawiony na **Nieznany**.  

- Na karcie**Konfiguracja** pokazuje domyślne ustawienia parametrów konfiguracji (tylko dla monitorów jednostek, a nie monitorów agregujących) i ich wartości.
- Na **wiedzy** kartę zawiera informacje wyjaśniające zachowanie monitora i jak ocenia stan złej kondycji.

Monitorowanie danych na tej stronie nie odświeża się automatycznie i należy wybrać **odświeżyć** w górnej części strony, aby zobaczyć najnowszy stan kondycji odebrany z klastra.

## <a name="next-steps"></a>Następne kroki

Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do oceny lub dostosowania do alertów, wizualizacji lub analizowania klastrów.
