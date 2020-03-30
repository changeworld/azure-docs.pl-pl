---
title: Globalna sieć firmy Microsoft — Platforma Azure
description: W tym artykule opisano, jak firma Microsoft tworzy szybką i niezawodną sieć globalną
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453071"
---
# <a name="microsoft-global-network"></a>Globalna sieć firmy Microsoft

Microsoft jest właścicielem i operatorem jednej z największych sieci szkieletowych na świecie. Ta globalna i wyrafinowana architektura, obejmująca ponad 100 000 mil, łączy nasze centra danych i klientów. 
 
Każdego dnia klienci na całym świecie łączą się i przekazują biliony żądań do platformy Microsoft Azure, Bing, Dynamics 365, Office 365, XBox i wielu innych. Niezależnie od typu, klienci oczekują natychmiastowej niezawodności i responsywności naszych usług. 
 
[Globalna sieć firmy Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) jest centralną częścią zapewniania wspaniałych rażeń z chmury. Łącząc nasze [centra danych](https://azure.microsoft.com/global-infrastructure/) firmy Microsoft w 54 regionach platformy Azure i dużej siatce węzłów brzegowych strategicznie rozmieszczonych na całym świecie, nasza globalna sieć oferuje zarówno dostępność, pojemność, jak i elastyczność, aby sprostać każdemu zapotrzebowaniu.

![Globalna sieć firmy Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Pobierz sieć chmurową premium
 
Wybór [najlepszego możliwego środowiska](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) jest łatwy w korzystaniu z chmury firmy Microsoft. Od momentu, gdy ruch klientów wchodzi do naszej globalnej sieci za pośrednictwem naszych strategicznie rozmieszczonych węzłów brzegowych, Twoje dane przemieszczają się po zoptymalizowanych trasach z prędkością zbliżoną do światła. Zapewnia to optymalne opóźnienie dla najlepszej wydajności. Te węzły brzegowe, połączone z ponad 3500 unikalnymi partnerami internetowymi (rówieśnikami) za pośrednictwem tysięcy połączeń w ponad 145 lokalizacjach, stanowią podstawę naszej strategii połączeń wzajemnych. 
 
Niezależnie od tego, czy chodzi o połączenie z Londynu do Tokio, czy z Waszyngtonu do Los Angeles, wydajność sieci jest określana ilościowo i ma na to wpływ takie rzeczy, jak opóźnienie, jitter, utrata pakietów i przepustowość.  W firmie Microsoft preferujemy bezpośrednie połączenia między sobą w przeciwieństwie do łączy tranzytowych, dzięki czemu ruch odpowiedzi jest symetryczny i pomaga utrzymać przeskoki, strony równorzędne i ścieżki tak krótkie i proste, jak to możliwe. 

Na przykład, jeśli użytkownik w Londynie próbuje uzyskać dostęp do usługi w Tokio, a następnie ruch internetowy wchodzi na jedną z naszych krawędzi w Londynie, przechodzi przez Microsoft WAN przez Francję, nasze ścieżki Trans-Arabia między Europą i Indiami, a następnie do Japonii, gdzie usługa jest hostowana. Ruch odpowiedzi jest symetryczny. Jest to czasami określane jako [routing zimnych ziemniaków,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) co oznacza, że ruch pozostaje w sieci Microsoft tak długo, jak to możliwe, zanim go oddamy.  
  
Tak, to oznacza, że każdy i cały ruch podczas korzystania z usług firmy Microsoft? Tak, wszelki ruch między centrami danych, w ramach platformy Microsoft Azure lub między usługami firmy Microsoft, takimi jak maszyny wirtualne, Office 365, XBox, DC SQL, Magazyn i sieci wirtualne, jest kierowany w naszej globalnej sieci i nigdy przez publiczny Internet, aby zapewnić optymalny wydajności i integralności.  
 
Ogromne inwestycje w przepustowość światłowodów i różnorodność w ścieżkach metra, lądowych i podwodnych mają kluczowe znaczenie dla utrzymania spójnego i wysokiego poziomu usług, a jednocześnie napędzają ekstremalny rozwój naszych usług w chmurze i online. Najnowsze dodatki do naszej globalnej sieci to kabel podmorski [MAREA,](https://www.submarinecablemap.com/#/submarine-cable/marea) pierwszy w branży Open Line System (OLS) nad podmorską, między Bilbao, Hiszpanią i Virginia Beach, Virginia, USA, a także [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) między Nowym Jorkiem, USA i Dublinem, Irlandią i [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) między Tokio, Japonią i Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Nasza sieć to Twoja sieć

Włożyliśmy dwie dekady doświadczenia, wraz z ogromnymi inwestycjami w sieć, aby zapewnić optymalną wydajność przez cały czas. Firmy mogą w pełni korzystać z naszych zasobów sieciowych i tworzyć zaawansowane architektury nakładek na wierzchu. 
 
Platforma Microsoft Azure oferuje najbogatsze portfolio usług i możliwości, umożliwiając klientom szybkie i łatwe tworzenie, rozszerzanie i spełnianie wymagań sieciowych w dowolnym miejscu. Nasza rodzina usług łączności obejmuje komunikację równorzędną sieci wirtualnych między regionami, hybrydową i w chmurze architekturę typu "punkt-lokacja" i lokacja, a także globalne scenariusze tranzytu adresów IP.  W przypadku przedsiębiorstw, które chcą połączyć własne centrum danych lub sieć z platformą Azure lub klientów z ogromnymi potrzebami pozyskiwania lub przesyłania danych, [usługi ExpressRoute](../expressroute/expressroute-introduction.md)i [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) udostępniają opcje przepustowości do 100 Gb/s bezpośrednio do globalnej sieci firmy Microsoft w lokalizacjach komunikacji równorzędnej na całym świecie.  
 
[Usługa ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) została zaprojektowana w taki sposób, aby uzupełnić implementację sieci WAN dostawcy usług i połączyć witryny lokalne na całym świecie. Jeśli prowadzisz operację globalną, możesz użyć usługi ExpressRoute Global Reach w połączeniu z preferowanymi i lokalnymi dostawcami usług, aby połączyć wszystkie witryny globalne za pomocą sieci globalnej firmy Microsoft. Rozszerzenie nowej sieci w chmurze (WAN) o dużą liczbę lokacji w oddziałach można osiągnąć za pośrednictwem wirtualnej sieci WAN platformy Azure, która umożliwia bezproblemowe łączenie oddziałów z globalną siecią firmy Microsoft za pomocą urządzenia SDWAN & VPN (czyli sprzętu lokalnego klienta lub CPE) z wbudowaną łatwością użycia oraz automatyczną łącznością i zarządzaniem konfiguracją. 
 
[Globalna komunikacja równorzędna sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) umożliwia klientom bezproblemowe łączenie dwóch lub więcej sieci wirtualnych platformy Azure w różnych regionach. Po wzniebowtęglowane sieci wirtualne są wyświetlane jako jeden. Ruch między maszynami wirtualnymi w sieciach wirtualnych równorzędnych jest kierowany za pośrednictwem infrastruktury szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej — tylko za pośrednictwem prywatnych adresów IP. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Dobrze zarządzane przy użyciu innowacji definiowanych programowo

Firma Microsoft, prowadząc jedną z wiodących chmur na świecie, zdobyła wiele wglądu i doświadczenia w budowaniu i zarządzaniu wydajną infrastrukturą globalną.  
 
Przestrzegamy solidnych zasad operacyjnych: 
 
- Użyj najlepszego w swojej klasie sprzętu przełączającego w różnych warstwach sieci.  
- Wdrażanie nowych funkcji bez wpływu na użytkowników końcowych.  
- Aktualizacje są dostępne w bezpieczny i niezawodny sposób w całej flocie, tak szybko, jak to możliwe. Godziny zamiast tygodni.  
- Korzystaj z głębokich danych telemetrycznych w skali chmury i w pełni zautomatyzowanego ograniczania błędów.  
- Użyj ujednoliconej i zdefiniowanej programowo technologii sieciowej, aby sterować wszystkimi elementami sprzętowymi w sieci.  Eliminacja powielania działań i zmniejsza liczbę awarii. 
 
Zasady te mają zastosowanie do wszystkich warstw sieci: od hosta Interfejsu Sieciowego, platformy przełączania, funkcji sieciowych w centrum danych, takich jak moduły równoważenia obciążenia, aż po sieć WAN z naszą platformą inżynierii ruchu i naszymi sieciami optycznymi.  
 
Wykładniczy rozwój platformy Azure i jej sieci osiągnął punkt, w którym ostatecznie zdaliśmy sobie sprawę, że nie można już polegać na ludzkiej intuicji do zarządzania globalnymi operacjami sieciowymi. Aby spełnić potrzebę weryfikacji długich, średnich i krótkoterminowych zmian w sieci, opracowaliśmy platformę do syntetycznego odzwierciedlania i emulowania naszej sieci produkcyjnej. Możliwość tworzenia lustrzanych środowisk i uruchamiania milionów symulacji, pozwala nam przetestować zmiany oprogramowania i sprzętu oraz ich wpływ, zanim zdążymy je na naszą platformę produkcyjną i sieć. 

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o usługach sieciowych dostarczanych na platformie Azure](https://azure.microsoft.com/product-categories/networking/)
