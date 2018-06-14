---
title: Omówienie planu usługi Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak planów usługi App Service dla pracy w usłudze Azure App Service i korzyści do środowiska zarządzania.
keywords: usługi aplikacji, usługi azure app service, skalowania, skalowalna, skalowalność, plan usługi app service, koszt usługi aplikacji
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
ms.openlocfilehash: 268844eae8dc06937529e79d52515cad2f6da3f4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862363"
---
# <a name="azure-app-service-plan-overview"></a>Omówienie usługi Azure planu usługi aplikacji

W usłudze App Service, aplikacja jest uruchamiana w _planu usługi aplikacji_. Plan usługi aplikacji definiuje zestaw zasoby obliczeniowe dla aplikacji sieci web do uruchomienia. Obliczeniowe te zasoby są odpowiednikiem [ _farmy serwerów_ ](https://wikipedia.org/wiki/Server_farm) w hostingu z konwencjonalnej sieci web. Co najmniej jedną aplikację można skonfigurować do uruchamiania na tym samym zasobów obliczeniowych (lub w tym samym planie usługi aplikacji).

Po utworzeniu planu usługi App Service w danym regionie (na przykład, Europa Zachodnia) zestaw zasobów obliczeniowych jest tworzony dla tego planu, w tym regionie. Niezależnie od aplikacji umieścić w tym planie usługi aplikacji, uruchom na te zasoby obliczeniowe, zgodnie z definicją planu usługi aplikacji. Definiuje każdy plan usługi aplikacji:

- Region (zachodnie stany USA, wschodnie stany USA, itp.)
- Liczba wystąpień maszyn wirtualnych
- Liczba wystąpień maszyn wirtualnych (mały, Średni, duże)
- Warstwa cenowa (wolne, współużytkowane, Basic, Standard, Premium, PremiumV2, izolowany i zużycia)

_Warstwy cenowej_ usługi App Service plan określa, jakie funkcje usługi App Service, możesz uzyskać i ile płacisz planu. Istnieje kilka kategorii warstw cenowych:

- **Udostępnione obliczeń**: **wolne** i **Shared**, dwóch podstawowych warstw, działa jako aplikacja na tej samej maszyny Wirtualnej Azure, jak inne aplikacje usługi aplikacji, w tym aplikacje innych klientów. Te warstwy przydzielić przydziałów procesora CPU do poszczególnych aplikacji, działającą z udostępnionymi zasobami i zasobów nie można skalować w poziomie.
- **Dedykowany obliczeń**: **podstawowe**, **standardowe**, **Premium**, i **PremiumV2** warstw uruchamiania aplikacji na platformie Azure dedykowane Maszyny wirtualne. Tylko aplikacje w tym samym planie usługi aplikacji udostępnianie tych samych zasobów obliczeniowych. Wyższego poziomu, więcej wystąpień maszyny Wirtualnej są dostępne dla skalowalnego w poziomie.
- **Izolowane**: Ta warstwa działa dedykowanych maszynach wirtualnych platformy Azure na dedykowanych sieci wirtualnych Azure, która zapewnia izolację sieci na górze izolacja obliczeń do aplikacji. Zapewnia maksymalną możliwości skalowania w poziomie.
- **Zużycie**: Ta warstwa jest dostępna tylko dla [funkcji aplikacji](../azure-functions/functions-overview.md). Skaluje się funkcje dynamicznie w zależności od obciążenia. Aby uzyskać więcej informacji, zobacz [hosting usługi Azure Functions plany porównanie](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Każda warstwa także konkretnego podzestawu funkcji usługi aplikacji. Te funkcje obejmują domeny niestandardowe i certyfikatów SSL, skalowanie automatyczne, miejsc wdrożenia, kopie zapasowe, integracji usługi Traffic Manager i inne. Im wyższa warstwy więcej funkcje są dostępne. Aby dowiedzieć się, które funkcje są obsługiwane w każdej warstwy cenowej, zobacz [szczegóły planu usługi aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Nowy **PremiumV2** warstwa cenowa oferuje [Dv2 serii maszyn wirtualnych](../virtual-machines/windows/sizes-general.md#dv2-series) szybkich procesorów, pamięci masowej SSD i podwójne współczynnik pamięci core w porównaniu do **standardowe** warstwy. **PremiumV2** obsługuje również zwiększenia skali za pomocą wystąpienia zwiększenie liczby jednocześnie nadal zapewniając z zaawansowanych możliwości w planie Standard. Wszystkie funkcje dostępne w istniejących **Premium** warstwy znajdują się w **PremiumV2**.
>
> Podobnie jak inne dedykowane warstw, maszyny Wirtualnej są dostępne trzy rozmiary dla tej warstwy:
>
> - Mała liczba godzin (jeden rdzeń procesora CPU, 3.5 GiB pamięci) 
> - Średnia liczba godzin (dwie rdzeni Procesora, 7 GiB pamięci) 
> - Duży (4 rdzeni Procesora, 14 GiB pamięci)  
>
> Aby uzyskać **PremiumV2** uzyskać informacje o cenach, zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/).
>
> Aby rozpocząć pracę z nowym **PremiumV2** warstwy cenowej, zobacz [warstwy PremiumV2 Konfigurowanie aplikacji usługi](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Jak Moja aplikacja uruchamiania i skalowania?

W **wolne** i **Shared** warstw, aplikacja odbiera minut procesora CPU dla udostępnionego wystąpienia maszyny Wirtualnej i nie można skalować w poziomie. W innej warstwy aplikacji działa i skaluje w następujący sposób.

Po utworzeniu aplikacji w usłudze App Service jest umieszczany w planie usługi aplikacji. Po uruchomieniu aplikacji, jest uruchamiana na wszystkich wystąpień maszyn wirtualnych skonfigurowanych w planie usługi aplikacji. W przypadku wielu aplikacji w tym samym planie usługi aplikacji, wszystkie mają tego samego wystąpienia maszyny Wirtualnej. Jeśli masz wiele miejsc wdrożenia dla aplikacji, wszystkich miejsc wdrożenia również uruchomić na tym samym wystąpień maszyn wirtualnych. Włączanie dzienników diagnostycznych, wykonywanie kopii zapasowych, czy uruchamiania zadań Webjob, również korzystają cykle procesora CPU i pamięci w tych wystąpień maszyn wirtualnych.

W ten sposób plan usługi aplikacji jest jednostka skalowania aplikacji usługi aplikacji. Jeśli plan jest skonfigurowana do uruchamiania pięciu wystąpień maszyny Wirtualnej, a następnie uruchom wszystkie aplikacje w planie we wszystkich wystąpieniach pięć. Jeśli plan jest skonfigurowany dla Skalowanie automatyczne, a następnie wszystkie aplikacje w planie są ze sobą skalowanie zgodnie z ustawieniami automatycznego skalowania.

Aby uzyskać informacji na temat skalowania aplikacji, zobacz [skalowanie liczby wystąpień ręcznie lub automatycznie](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Ile kosztuje planu usługi aplikacji?

W tej sekcji opisano, jak są rozliczane aplikacji usługi App Service. Szczegółowe określonego regionu cenową informacji, zobacz [App Service — ceny](https://azure.microsoft.com/pricing/details/app-service/).

Z wyjątkiem **wolne** warstwy, plan usługi aplikacji prowadzi dodatkowy co godzinę wykorzystuje zasoby obliczeniowe.

- W **Shared** każdej aplikacji warstwy odbiera przydziału procesora CPU minut, więc _każdej aplikacji_ rozliczany co godzinę dla limitu przydziału procesora CPU.
- W dedykowanym obliczeniowe warstw (**podstawowe**, **standardowe**, **Premium**, **PremiumV2**), planu usługi aplikacji definiuje liczbę maszyn wirtualnych wystąpienia, które aplikacje są skalowane, więc _każde wystąpienie maszyny Wirtualnej_ w usłudze App Service plan ma dodatkowy co godzinę. Te wystąpienia maszyny Wirtualnej są naliczane tych samych niezależnie od tego, jak wiele aplikacji są uruchomione na nich. Aby uniknąć nieoczekiwanego opłat, zobacz [wyczyścić plan usługi aplikacji](app-service-plan-manage.md#delete).
- W **izolowany** warstwy, środowiska usługi aplikacji definiuje liczbę izolowanego procesów roboczych uruchamianych aplikacji, a _każdy pracownik_ jest rozliczana co godzinę. Ponadto istnieje co godzinę opłata podstawowa dla pracy samego środowiska usługi aplikacji. 
- (Tylko w przypadku funkcji platformy azure) **Zużycie** warstwy dynamicznie przydziela wystąpień maszyn wirtualnych do obsługi obciążenia aplikacji funkcji i rozliczany dynamicznie na sekundę przez platformę Azure. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Nie zostały naliczone opłaty dotyczące korzystania z funkcji usług aplikacji, które są dostępne dla Ciebie (Konfigurowanie domen niestandardowych certyfikatów SSL, miejsc wdrożenia, kopie zapasowe, itp.). Dostępne są następujące wyjątki:

- Domenami usługi aplikacji — płać przy zakupie jednej platformie Azure i po odnowieniu go każdego roku.
- Certyfikaty usługi aplikacji — płać przy zakupie jednej platformie Azure i po odnowieniu go każdego roku.
- Opartych na protokole IP połączeń SSL - miejsca przez godzinę bezpłatnie dla każdego połączenia SSL opartego na protokole IP, ale niektóre **standardowe** warstwę lub nowszych udostępnia jedno połączenie SSL opartego na protokole IP bezpłatnie. Mogą na podstawie SNI połączeń SSL.

> [!NOTE]
> Usługi aplikacji jest zintegrowane z innej usługi Azure, może być konieczne należy wziąć pod uwagę opłaty od tych innych usług. Na przykład jeśli używasz usługi Azure Traffic Manager geograficznie, skalowanie aplikacji usługi Azure Traffic Manager również opłaty należy na podstawie użycia. Aby oszacować koszt między usługami na platformie Azure, zobacz [Kalkulator cennik](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Co zrobić, jeśli Moja aplikacja potrzebuje więcej możliwości i funkcje?

Plan usługi aplikacji mogą być skalowane w górę i w dół w dowolnym momencie. Jest tak proste, jak zmiana warstwy cenowej planu. Można wybrać dolnej warstwy cenowej na początku i skalowanie w górę później Jeśli potrzebujesz więcej funkcji usługi aplikacji.

Na przykład można uruchomić testów aplikacji sieci web w **wolne** usługi aplikacji — planowanie i zapłać nothing. Jeśli chcesz dodać Twojego [niestandardową nazwę DNS](app-service-web-tutorial-custom-domain.md) do aplikacji sieci web, wystarczy skalowanie planu do **Shared** warstwy. Później, gdy chcesz dodać [niestandardowego certyfikatu SSL](app-service-web-tutorial-custom-ssl.md), skalowanie do planu **podstawowe** warstwy. Jeśli chcesz, aby [środowiska przejściowe](web-sites-staged-publishing.md), skalowanie do **standardowe** warstwy. Jeśli potrzebujesz więcej rdzeni, pamięcią lub magazynem, skalowanie w górę do większy rozmiar maszyny Wirtualnej w tej samej warstwie.

Taka sama działa w odwrotnym kierunku. Jeśli uważasz, że użytkownik nie będzie potrzebować możliwości lub funkcji wyższego poziomu, można skalować w dół do dolnej warstwy, co pozwala na oszczędność pieniędzy.

Aby uzyskać informacje na temat skalowania w górę plan usługi aplikacji, zobacz [skalowanie w górę aplikacji na platformie Azure](web-sites-scale.md).

Jeśli Twoja aplikacja znajduje się w tym samym planie usługi aplikacji w innych aplikacjach, można zwiększyć wydajność aplikacji przez izolowanie zasoby obliczeniowe. Możesz zrobić to przez przenoszenie aplikacji do osobnych planu usługi aplikacji. Aby uzyskać więcej informacji, zobacz [przenieść aplikację do innego planu usługi aplikacji](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Aplikację należy umieścić w plan nowy lub istniejący plan?

Ponieważ płacić za zasoby obliczeniowe planu usługi aplikacji przydziela (zobacz [ile kosztuje moich kosztów planu usługi aplikacji?](#cost)), można potencjalnie zaoszczędzić, ustawiając dla wielu aplikacji w jeden plan usługi aplikacji. Możesz kontynuować dodawanie aplikacji do istniejącego planu tak długo, jak plan ma za mało zasobów, aby obsłużyć obciążenia. Jednak należy pamiętać, aplikacje, w tym samym planie usługi aplikacji się, że wszystkie mają takie same zasoby obliczeniowe. Aby ustalić, czy zasoby niezbędne znajdują się w nowej aplikacji, należy zrozumieć pojemność istniejący plan usługi aplikacji i oczekiwanego obciążenia dla nowej aplikacji. Przeciążanie plan usługi aplikacji może powodować przestoje dla nowych i istniejących aplikacji.

Isolate aplikację do nowej usługi aplikacji podczas planowania:

- Aplikacja jest obciążający zasoby.
- Chcesz skalować istniejącego planu aplikacji niezależnie od innych aplikacji.
- Aplikacja potrzebuje zasobów w innym regionie geograficznym.

W ten sposób można przydzielić nowego zestawu zasobów dla aplikacji i uzyskać większą kontrolę nad aplikacjami.

## <a name="manage-an-app-service-plan"></a>Zarządzanie plan usługi aplikacji

> [!div class="nextstepaction"]
> [Zarządzanie plan usługi aplikacji](app-service-plan-manage.md)
