---
title: Monitorowanie aplikacji
description: Dowiedz się, jak monitorować aplikacje w usłudze Azure App Service przy użyciu witryny Azure portal. Opis przydziałów i metryk, które są zgłaszane.
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: d84340730391abd7dba4d13202503d37941c09b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500433"
---
# <a name="monitor-apps-in-azure-app-service"></a>Monitorowanie aplikacji w usłudze Azure App Service
[Usługa Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) udostępnia wbudowane funkcje monitorowania aplikacji sieci web, aplikacji mobilnych i aplikacji interfejsu API w [witrynie Azure portal.](https://portal.azure.com)

W witrynie Azure portal można przeglądać *przydziały* i *metryki* dla aplikacji i planu usługi App Service oraz *konfigurowanie alertów* i metryk opartych na regułach *skalowania automatycznego.*

## <a name="understand-quotas"></a>Zrozumienie przydziałów

Aplikacje hostowane w usłudze App Service podlegają pewnym ograniczeniom zasobów, z których mogą korzystać. Limity są definiowane przez plan usługi aplikacji, który jest skojarzony z aplikacją.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Jeśli aplikacja jest hostowana w planie *bezpłatnym* lub *udostępnionym,* limity zasobów, z których aplikacja może korzystać, są definiowane przez przydziały.

Jeśli aplikacja jest hostowana w planie *Podstawowym,* *Standard*lub *Premium,* limity zasobów, których mogą używać, są ustawiane przez *rozmiar* (Mały, Średni, Duży) i *liczba wystąpień* (1, 2, 3 itd.) planu usługi app service.

Przydziały dla aplikacji bezpłatnych lub udostępnionych to:

| Limit przydziału | Opis |
| --- | --- |
| **Procesor (krótki)** | Ilość procesora CPU dozwolona dla tej aplikacji w odstępie 5 minut. Ten przydział jest resetowany co pięć minut. |
| **Procesor (dzień)** | Całkowita ilość procesora CPU dozwolone dla tej aplikacji w ciągu dnia. Ten przydział jest resetowany co 24 godziny o północy czasu UTC. |
| **Pamięci** | Całkowita ilość pamięci dozwolona dla tej aplikacji. |
| **Przepustowość** | Całkowita przepustowość wychodząca dozwolona dla tej aplikacji w ciągu dnia. Ten przydział jest resetowany co 24 godziny o północy czasu UTC. |
| **Filesystem** | Całkowita dozwolona ilość miejsca do magazynowania. |

Jedynym przydziałem mającym zastosowanie do aplikacji hostowanych w *językach Basic,* *Standard*i *Premium* jest system plików.

Aby uzyskać więcej informacji na temat określonych przydziałów, limitów i funkcji dostępnych dla różnych jednostek SKU usługi App Service, zobacz [Limity usług subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

### <a name="quota-enforcement"></a>Egzekwowanie kwot

Jeśli aplikacja przekroczy *procesor (krótki)*, *PROCESOR (dzień)* lub przydział *przepustowości,* aplikacja zostanie zatrzymana, dopóki przydział nie zostanie zresetowany. W tym czasie wszystkie przychodzące żądania powodują błąd HTTP 403.

![Komunikat o błędzie 403][http403]

Jeśli limit pamięci aplikacji zostanie przekroczony, aplikacja zostanie ponownie uruchomiona.

Jeśli przydział systemu plików zostanie przekroczony, każda operacja zapisu zakończy się niepowodzeniem. Błędy operacji zapisu obejmują wszelkie zapisy w dziennikach.

Możesz zwiększyć lub usunąć przydziały z aplikacji, uaktualniając plan usługi App Service.

## <a name="understand-metrics"></a>Zrozumienie danych

> [!NOTE]
> **Użycie systemu plików** to nowa metryka wdrażana globalnie, nie oczekuje się żadnych danych, chyba że zostałasz na białej liście do prywatnej wersji zapoznawczej.
> 

> [!IMPORTANT]
> **Średni czas odpowiedzi** zostanie przestarzały, aby uniknąć pomyłek z agregacjami metryk. Użyj **czasu odpowiedzi** jako zamiennika.

Metryki zawierają informacje o aplikacji lub zachowanie planu usługi app service.

W przypadku aplikacji dostępne dane to:

| Metryka | Opis |
| --- | --- |
| **Czas reakcji** | Czas przeznaczony dla aplikacji do obsługi żądań w sekundach. |
| **Średni czas odpowiedzi (przestarzały)** | Średni czas wyświetlania żądań przez aplikację w sekundach. |
| **Zestaw pracy średniej pamięci** | Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB). |
| **Połączenia** | Liczba gniazd powiązanych istniejących w piaskownicy (w3wp.exe i jego procesy podrzędne).  Gniazdo powiązane jest tworzone przez wywoływanie interfejsów API bind()/connect() i pozostaje do momentu zamknięcia wspomnianego gniazda za pomocą CloseHandle()/closesocket(). |
| **Czas procesora** | Ilość procesora CPU zużywanego przez aplikację w ciągu kilku sekund. Aby uzyskać więcej informacji na temat tej metryki, zobacz [czas procesora cpu a procent procesora .](#cpu-time-vs-cpu-percentage) |
| **Bieżące złożenia** | Bieżąca liczba zestawów załadowany przez wszystkie AppDomains w tej aplikacji. |
| **Dane w** | Ilość przychodzącej przepustowości zużywanej przez aplikację w MiB. |
| **Wyjście danych** | Ilość wychodzącej przepustowości zużywanej przez aplikację w MiB. |
| **Użycie systemu plików** | Procent przydziału systemu plików zużytego przez aplikację. |
| **Odpady odśmieskowe Gen 0** | Liczba razy generacji 0 obiekty są śmieci zebrane od początku procesu aplikacji. Kontrolery nowej generacji obejmują wszystkie dc niższej generacji.|
| **Wyrzucanie elementów bezużytecznych gen 1** | Liczba razy generacji 1 obiekty są śmieci zebrane od początku procesu aplikacji. Kontrolery nowej generacji obejmują wszystkie dc niższej generacji.|
| **Odpady odśmieskowe gen 2** | Liczba razy generacji 2 obiekty są śmieci zebrane od początku procesu aplikacji.|
| **Liczba dojść** | Całkowita liczba dojść aktualnie otwartych przez proces aplikacji.|
| **Http 2xx** | Liczba żądań powodujących kod stanu HTTP ≥ 200, ale < 300. |
| **Http 3xx** | Liczba żądań powodujących kod stanu HTTP ≥ 300, ale < 400. |
| **Http 401** | Liczba żądań powodujących kod stanu HTTP 401. |
| **Http 403** | Liczba żądań powodujących kod stanu HTTP 403. |
| **Http 404** | Liczba żądań powodujących kod stanu HTTP 404. |
| **Http 406** | Liczba żądań powodujących kod stanu HTTP 406. |
| **Http 4xx** | Liczba żądań powodujących kod stanu HTTP ≥ 400, ale < 500. |
| **Błędy serwera http** | Liczba żądań powodujących kod stanu HTTP ≥ 500, ale < 600. |
| **IO inne bajty na sekundę** | Szybkość, z jaką proces aplikacji jest wydawanie bajtów do operacji we/wy, które nie obejmują danych, takich jak operacje kontroli.|
| **Inne operacje we/wy na sekundę** | Szybkość, z jaką proces aplikacji jest wystawianie operacji we/wy, które nie są operacje odczytu lub zapisu.|
| **Bajty odczytu we/wy na sekundę** | Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|
| **Operacje odczytu we/wy na sekundę** | Szybkość, z jaką proces aplikacji jest wydawanie operacji we/wy odczytu.|
| **Bajty zapisu we/wy na sekundę** | Szybkość, z jaką proces aplikacji jest zapisywanie bajtów do operacji we/wy.|
| **Operacje zapisu we/wy na sekundę** | Szybkość, z jaką proces aplikacji jest wydawanie operacji we/wy zapisu.|
| **Zestaw roboczy pamięci** | Bieżąca ilość pamięci używanej przez aplikację w MiB. |
| **Bajty prywatne** | Bajty prywatne to bieżący rozmiar w bajtach pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|
| **Żądania** | Całkowita liczba żądań niezależnie od ich wynikowego kodu stanu HTTP. |
| **Żądania w kolejce aplikacji** | Liczba żądań w kolejce żądań aplikacji.|
| **Liczba wątków** | Liczba wątków aktualnie aktywnych w procesie aplikacji.|
| **Łączna liczba domen aplikacji** | Bieżąca liczba AppDomains załadowany w tej aplikacji.|
| **Całkowita liczba niezaładowanych domen aplikacji** | Całkowita liczba AppDomains zwolniony od początku aplikacji.|


W przypadku planu usługi app service dostępne są następujące dane:

> [!NOTE]
> Metryki planu usługi app service są dostępne tylko dla planów w warstwach *Podstawowe,* *Standardowe*i *Premium.*
> 

| Metryka | Opis |
| --- | --- |
| **Procent procesora** | Średni procesor cpu używany we wszystkich wystąpieniach planu. |
| **Procent pamięci** | Średnia pamięć używana we wszystkich wystąpieniach planu. |
| **Dane w** | Średnia przepustowość przychodząca używana we wszystkich wystąpieniach planu. |
| **Wyjście danych** | Średnia przepustowość wychodząca używana we wszystkich wystąpieniach planu. |
| **Długość kolejki dysku** | Średnia liczba żądań odczytu i zapisu, które zostały umieszczone w kolejce w magazynie. Wysoka długość kolejki dysku jest wskazaniem aplikacji, która może spowalniać z powodu nadmiernego we/wy dysku. |
| **Długość kolejki http** | Średnia liczba żądań HTTP, które musiały siedzieć w kolejce przed ich spełnieniem. Wysoka lub rosnąca długość kolejki HTTP jest objawem planu pod dużym obciążeniem. |

### <a name="cpu-time-vs-cpu-percentage"></a>Czas procesora a procent procesora
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Istnieją dwa metryki, które odzwierciedlają użycie procesora:

**Czas procesora:** Przydatne dla aplikacji hostowanych w planach wolnych lub udostępnionych, ponieważ jeden z ich przydziałów jest zdefiniowany w minutach procesora używanego przez aplikację.

**Procent procesora:** przydatne w przypadku aplikacji hostowanych w planach podstawowych, standardowych i premium, ponieważ można je skalować w poziomie. Procent procesora CPU jest dobrym wskaźnikiem ogólnego użycia we wszystkich wystąpieniach.

## <a name="metrics-granularity-and-retention-policy"></a>Szczegółowe metryki i zasady przechowywania
Metryki planu usługi aplikacji i aplikacji są rejestrowane i agregowane przez usługę, z następującymi szczegółowościami i zasadami przechowywania:

* **Metryki** ziarnistości minut są przechowywane przez 30 godzin.
* **Metryki** ziarnistości godzin są przechowywane przez 30 dni.
* Wskaźniki ziarnistości **dziennej** są przechowywane przez 30 dni.

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Monitorowanie przydziałów i metryk w witrynie Azure portal
Aby przejrzeć stan różnych przydziałów i metryk, które mają wpływ na aplikację, przejdź do [witryny Azure portal](https://portal.azure.com).

![Wykres przydziałów w witrynie Azure portal][quotas]

Aby znaleźć przydziały, wybierz **pozycję Ustawienia** > **Przydziały**. Na wykresie możesz przejrzeć: 
1. Nazwa przydziału.
1. Jego interwał resetowania.
1. Jego obecny limit.
1. Jego bieżąca wartość.

![Wykres metrykowy w][metrics] witrynie Azure portal Można uzyskać dostęp do metryk bezpośrednio ze strony **Przegląd** zasobu. Tutaj zobaczysz wykresy przedstawiające niektóre dane aplikacji.

Kliknięcie dowolnego z tych wykresów spowoduje wyświetlenie widoku metryk, w którym można tworzyć wykresy niestandardowe, wyszukiwać różne metryki i wiele więcej. 

Aby dowiedzieć się więcej o metrykach, zobacz [Monitorowanie metryk usługi](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Alerty i skalowanie automatyczne
Metryki dla aplikacji lub planu usługi aplikacji można podłączyć do alertów. Aby uzyskać więcej informacji, zobacz [Receive alert notifications](../monitoring-and-diagnostics/insights-alerts-portal.md) (Otrzymywanie powiadomień o alertach).

Aplikacje usługi App Service hostowane w planach usługi App Service w trybie podstawowym lub wyższym obsługują skalowanie automatyczne. Za pomocą skalowania automatycznego można skonfigurować reguły monitorujące metryki planu usługi App Service. Reguły można zwiększyć lub zmniejszyć liczbę wystąpień, które mogą zapewnić dodatkowe zasoby w razie potrzeby. Reguły mogą również pomóc zaoszczędzić pieniądze, gdy aplikacja jest nadmiernie aprowizacji.

Aby uzyskać więcej informacji na temat skalowania automatycznego, zobacz [Jak skalować](../monitoring-and-diagnostics/insights-how-to-scale.md) i [najważniejsze wskazówki dotyczące skalowania automatycznego usługi Azure Monitor](../azure-monitor/platform/autoscale-best-practices.md).

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png