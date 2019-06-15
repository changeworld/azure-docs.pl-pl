---
title: Przegląd — plan App Service, Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planów usługi App Service do pracy w usłudze Azure App Service i jakie są korzyści z używania środowiska zarządzania.
keywords: Usługa App service, usłudze azure app service, skala, skalowalny, skalowalność, plan usługi app service, koszt usługi aplikacji
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60839015"
---
# <a name="azure-app-service-plan-overview"></a>Omówienie planu usługi Azure App Service

W usłudze App Service aplikacje są uruchamiane w ramach _planu usługi App Service_. Plan usługi App Service definiuje zestaw zasobów obliczeniowych dla aplikacji internetowej używanych podczas jej uruchamiania. Te obliczenia są podobne do plików zasobów [ _farmy serwerów_ ](https://wikipedia.org/wiki/Server_farm) w sieci web są konwencjonalne funkcje hostingu. Co najmniej jedną aplikację można skonfigurować do uruchamiania na tych samych zasobów obliczeniowych (lub ten sam plan usługi App Service).

Po utworzeniu planu usługi App Service w danym regionie (na przykład, Europa Zachodnia) zestaw zasobów obliczeniowych jest tworzony dla tego planu, w tym regionie. Wszystkie aplikacje, umieść ten plan usługi App Service, uruchom na te zasoby obliczeniowe, zgodnie z definicją planu usługi App Service. Każdy plan usługi App Service definiuje:

- Region (zachodnie stany USA, wschodnie stany USA itp.)
- Liczba wystąpień maszyn wirtualnych
- Rozmiar wystąpienia maszyny Wirtualnej (mały, Średni, duży)
- Warstwa cenowa (bezpłatna, współdzielona, Basic, Standard, Premium, PremiumV2, izolowany i użycie)

_Warstwy cenowej_ usługi App Service plan określa, jakie funkcje usługi App Service, możesz uzyskać i ile płać za plan. Istnieje kilka kategorii warstwy cenowe:

- **Udostępnione obliczeń**: **Bezpłatne** i **Shared**, dwa podstawowa warstw, uruchamiany moduł aplikacji w tej samej maszyny Wirtualnej platformy Azure jako inne aplikacje usługi App Service, w tym aplikacje innych klientów. Te warstwy przydzielić przydziałów procesora CPU do każdej aplikacji, która jest uruchamiana ze współużytkowanych zasobów, a zasoby nie można skalować w poziomie.
- **Obliczeniowe w wersji dedykowanej**: **Podstawowe**, **standardowa**, **Premium**, i **PremiumV2** warstwy uruchamianie aplikacji na dedykowanych maszynach wirtualnych platformy Azure. Tylko aplikacje w ten sam plan usługi App Service udostępniać te same zasoby obliczeniowe. Im większa warstwa większej liczby wystąpień maszyn wirtualnych są dostępne dla skalowalnego w poziomie.
- **Izolowane**: Ta warstwa działa dedykowanych maszynach wirtualnych platformy Azure w dedykowanej sieci wirtualnych platformy Azure, co zapewnia izolację sieci na podstawie obliczeń izolacji aplikacji. Zapewnia maksymalną możliwości skalowania w poziomie.
- **Użycie**: Ta warstwa jest dostępna tylko dla [aplikacje funkcji](../azure-functions/functions-overview.md). Skaluje się funkcje dynamicznie w zależności od obciążenia. Aby uzyskać więcej informacji, zobacz [porównanie planów hostingu usługi Azure Functions](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każda warstwa zapewnia również określony podzbiór funkcji usługi App Service. Funkcje te obejmują domen niestandardowych i certyfikatów SSL, automatycznego skalowania, miejsc wdrożenia, kopie zapasowe, integracji usługi Traffic Manager i inne. Im większa warstwa więcej funkcji są dostępne. Aby dowiedzieć się, które funkcje są obsługiwane w przypadku każdej warstwy cenowej, zobacz [szczegóły planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nowy **PremiumV2** warstwa cenowa oferuje [maszyny wirtualne z serii Dv2](../virtual-machines/windows/sizes-general.md#dv2-series) z szybszymi procesorami, magazynem dysków SSD i współczynnikiem pamięć rdzeń dwukrotnie większym niż **standardowa** warstwy. **PremiumV2** obsługuje również wyższą skalę dzięki większej liczbie wystąpień przy zachowaniu wszystkich zaawansowanych możliwości dostępnych w planie standardowa. Wszystkie funkcje dostępne w istniejącym **Premium** warstwy są objęte **PremiumV2**.
>
> Podobnie jak w innych warstwach dedykowane, maszyn wirtualnych są dostępne trzy rozmiary dla tej warstwy:
>
> - Mała liczba godzin (jeden rdzeń procesora CPU, pamięć o pojemności 3,5 GiB) 
> - Średni (dwa rdzenie Procesora, 7 GiB pamięci) 
> - Duże (cztery rdzeni procesora CPU, pamięć o pojemności 14 GiB)  
>
> Aby uzyskać **PremiumV2** informacje o cenach, zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/).
>
> Aby rozpocząć pracę z nowym **PremiumV2** warstwy cenowej, zobacz [warstwy PremiumV2 konfigurowanie dla usługi App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak Moja aplikacja uruchamiania i skalowania?

W **bezpłatna** i **Shared** warstw, aplikacja odbiera min Procesora na udostępnionego wystąpienia maszyny Wirtualnej i nie można skalować w poziomie. W innych warstwach aplikacji działa i jest skalowana w następujący sposób.

Podczas tworzenia aplikacji w usłudze App Service, zostanie ono przełączone do planu usługi App Service. Po uruchomieniu aplikacji, jest uruchamiana na wystąpieniach maszyn wirtualnych skonfigurowanych w planie usługi App Service. W przypadku wielu aplikacji w ten sam plan usługi App Service, współużytkują one wszystkich tych samych wystąpieniach maszyn wirtualnych. Jeśli masz wielu miejsc wdrożenia dla aplikacji, wszystkie gniazda wdrożenia jest również uruchomić na tych samych wystąpieniach maszyn wirtualnych. Włączanie dzienników diagnostycznych, wykonywania kopii zapasowych lub uruchomienia zadań Webjob, również używają cykle procesora CPU i pamięci na tych wystąpieniach maszyn wirtualnych.

W ten sposób plan usługi App Service jest jednostką skalowania aplikacji usługi App Service. Jeśli plan jest skonfigurowany do uruchamiania z pięcioma wystąpieniami maszyny Wirtualnej, wszystkie aplikacje w planie będzie działać na wszystkich pięciu wystąpień. Jeśli plan jest skonfigurowany dla skalowania automatycznego, wszystkie aplikacje w planie jest skalowana w poziomie ze sobą, a następnie zgodnie z ustawieniami automatycznego skalowania.

Aby uzyskać informacji na temat skalowania aplikacji, zobacz [ręczne lub automatyczne skalowanie liczby wystąpień](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Ile kosztuje Mój plan usługi App Service

W tej sekcji opisano, jak są rozliczane aplikacje usługi App Service. Uzyskać informacje szczegółowe, specyficzne dla regionu o cenach, zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/).

Z wyjątkiem **bezpłatna** warstwy, plan usługi App Service niesie ze sobą opłata za godziny na wykorzystuje zasoby obliczeniowe.

- W **Shared** warstwy, każda aplikacja odbiera przydziału minut procesora CPU, więc _każdej aplikacji_ jest naliczana co godzinę dla limitu przydziału procesora CPU.
- W dedykowanym obliczeniowych warstwy (**podstawowe**, **standardowa**, **Premium**, **PremiumV2**), plan usługi App Service definiuje liczbę maszyn wirtualnych wystąpienia, które aplikacje są skalowane, dzięki czemu _każdego wystąpienia maszyny Wirtualnej_ w usłudze App Service plan ma opłata za godziny. Te wystąpienia maszyn wirtualnych są rozliczane według tych samych aplikacji niezależnie od tego, ile działają na nich. Aby uniknąć nieoczekiwanych opłat, zobacz [wyczyścić plan usługi App Service](app-service-plan-manage.md#delete).
- W **izolowany** warstwy, środowisko usługi App Service definiuje liczbę izolowane pracowników w swoje aplikacje i _każdemu procesowi roboczemu_ jest naliczana co godzinę. Ponadto, istnieje co godzinę opłata podstawowa za usługę App Service Environment sam. 
- (Tylko w przypadku funkcji platformy azure) **Zużycie** warstwy dynamicznie przydziela wystąpień maszyn wirtualnych do obsługi obciążenia aplikacji funkcji i jest naliczana dynamicznie na sekundę przez platformę Azure. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Nie jest naliczana za korzystanie z funkcji usługi App Service, które są dostępne dla użytkownika (Konfigurowanie domen niestandardowych, certyfikaty SSL, miejsc wdrożenia, kopie zapasowe itp.). Dostępne są następujące wyjątki:

- Domen usługi App Service — płacić przy zakupie jeden na platformie Azure i kiedy należy go odnowić każdego roku.
- Certyfikaty usługi App Service — płacić przy zakupie jeden na platformie Azure i kiedy należy go odnowić każdego roku.
- Oparte na adresie IP połączenia SSL — tam użytkownika opłata godzinowa za każde połączenie SSL oparte na protokole IP, ale niektóre **standardowa** warstwy lub nowszych zapewnia jedno połączenie SSL oparte na adresie IP za darmo. Połączenia SSL oparte na SNI są bezpłatne.

> [!NOTE]
> Usługa App Service można zintegrować z usługą inną usługę platformy Azure, może być konieczne należy wziąć pod uwagę opłat za inne usługi. Na przykład jeśli używasz usługi Azure Traffic Manager geograficznie, skalować aplikację usługi Azure Traffic Manager również opłat można na podstawie użycia. Aby oszacować koszt dla wielu usług na platformie Azure, zobacz [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co zrobić, jeśli Moja aplikacja potrzebuje więcej możliwości i funkcje?

Plan usługi App Service można skalować w górę i w dół w dowolnym momencie. Jest tak prosta jak zmiana warstwy cenowej planu. Możesz wybrać niższej warstwy cenowej na początku i skalowanie w górę później, gdy potrzebujesz więcej funkcji usługi App Service.

Na przykład, można uruchomić testów aplikacji sieci web w **bezpłatna** usługi App Service plan i opłat. Jeśli chcesz dodać swoje [niestandardową nazwę DNS](app-service-web-tutorial-custom-domain.md) do aplikacji sieci web, po prostu skalę plan do **Shared** warstwy. Później, gdy chcesz dodać [niestandardowy certyfikat protokołu SSL](app-service-web-tutorial-custom-ssl.md), Skaluj plan do **podstawowe** warstwy. Jeśli chcesz mieć [przejściowe](deploy-staging-slots.md), skalę do **standardowa** warstwy. Jeśli potrzebujesz więcej rdzeni, pamięć lub magazynu, skalowanie w górę do większego rozmiaru maszyny Wirtualnej w tej samej warstwie.

Taka sama działa w odwrotnym kierunku. Jeśli uważasz, że użytkownik nie jest już potrzebują możliwości lub funkcji wyższego poziomu, możesz skalować w dół do niższej warstwy, która pozwala Ci oszczędzać pieniądze.

Aby uzyskać informacje na temat skalowania w górę plan usługi App Service, zobacz [skalowanie aplikacji na platformie Azure](web-sites-scale.md).

Jeśli aplikacja znajduje się w ten sam plan usługi App Service z innymi aplikacjami, możesz zwiększyć wydajność aplikacji przez izolowanie zasobów obliczeniowych. Należy go przez przenoszenie aplikacji do osobnych planu usługi App Service. Aby uzyskać więcej informacji, zobacz [przenieść aplikację do innego planu usługi App Service](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>W nowym planie lub istniejący plan należy umieścić aplikację?

Ponieważ płacisz za zasoby obliczeniowe planu usługi App Service przydziela (zobacz [jaki jest Mój koszt planu usługi App Service?](#cost)), może potencjalnie oszczędzasz pieniądze, umieszczając wiele aplikacji w jeden plan usługi App Service. Można nadal dodawać aplikacje do istniejącego planu, tak długo, jak plan ma za mało zasobów do obsługi obciążenia. Jednak należy pamiętać o tym, aplikacji w ten sam plan usługi App Service, że wszystkie współużytkować ten sam zasobów obliczeniowych. Aby ustalić, czy nowa aplikacja ma niezbędne zasoby, należy zrozumieć pojemność istniejącego planu usługi App Service i oczekiwanego obciążenia dla nowej aplikacji. Przeciążanie plan usługi App Service może teoretycznie spowodować przestój dla nowych i istniejących aplikacji.

Odizolować podczas planowania aplikacji do nowej usługi App Service:

- Aplikacja jest dużej ilości zasobów.
- Chcesz skalowanie aplikacji niezależnie od innych aplikacji istniejącego planu.
- Aplikacja musi zasobów w innym regionie geograficznym.

W ten sposób można przydzielić nowy zestaw zasobów dla aplikacji i zyskać większą kontrolę nad swoje aplikacje.

## <a name="manage-an-app-service-plan"></a>Zarządzanie plan usługi App Service

> [!div class="nextstepaction"]
> [Zarządzanie plan usługi App Service](app-service-plan-manage.md)
