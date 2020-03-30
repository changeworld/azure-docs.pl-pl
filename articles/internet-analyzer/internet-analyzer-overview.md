---
title: Analizator internetu platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o analizatorze internetowym platformy Azure
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73501946"
---
# <a name="what-is-internet-analyzer-preview"></a>Co to jest analizator internetowy? (Wersja zapoznawcza)

Internet Analyzer to platforma pomiarowa po stronie klienta, która sprawdza wpływ na wydajność klientów w infrastrukturze sieciowej. Niezależnie od tego, czy migrujesz z infrastruktury lokalnej na platformę Azure, czy oceniasz nową usługę platformy Azure, usługa Internet Analyzer pozwala Ci uczyć się na podstawie danych użytkowników i bogatych danych analitycznych firmy Microsoft w celu lepszego zrozumienia i zoptymalizowania architektury sieci za pomocą platformy Azure — przed migracją.

Internet Analyzer używa małego klienta JavaScript osadzonego w aplikacji sieci Web do pomiaru opóźnienia od użytkowników końcowych do wybranego zestawu miejsc docelowych sieci, _nazywamy punkty końcowe_. Analizator internetowy umożliwia skonfigurowanie wielu testów obok siebie, co pozwala na ocenę różnych scenariuszy w miarę rozwoju infrastruktury i potrzeb klientów. Analizator internetowy udostępnia niestandardowe i wstępnie skonfigurowane punkty końcowe, zapewniając zarówno wygodę, jak i elastyczność w podejmowaniu zaufanych decyzji dotyczących wydajności dla użytkowników końcowych. 


> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Szybkie & konfigurowalne testy

Analizator internetowy rozwiązuje pytania związane z wydajnością dotyczące migracji do chmury, wdrażania w nowych lub dodatkowych regionach platformy Azure lub testowania nowych platform dostarczania aplikacji i zawartości na platformie Azure, takich jak [Usługi Azure Front Door](https://azure.microsoft.com/services/frontdoor/) i Microsoft Azure [CDN.](https://azure.microsoft.com/services/cdn/) 

Każdy test utworzony w analizatorze internetowym składa się z dwóch punktów końcowych — punktu końcowego A i punktu końcowego B. Wydajność punktu końcowego B jest analizowana względem punktu końcowego A. 

Można skonfigurować własny niestandardowy punkt końcowy lub wybrać z różnych wstępnie skonfigurowanych punktów końcowych platformy Azure. Niestandardowe punkty końcowe powinny służyć do oceny obciążeń lokalnych, wystąpień w innych dostawców chmury lub konfiguracji platformy Azure niestandardowych. Testy mogą składać się z dwóch niestandardowych punktów końcowych; jednak co najmniej jeden niestandardowy punkt końcowy musi być obsługiwany na platformie Azure. Wstępnie skonfigurowane punkty końcowe platformy Azure to szybki i łatwy sposób oceny wydajności popularnych platform sieciowych platformy Azure, takich jak Usługi Azure Front Door, Usługa Azure Traffic Manager i usługa Azure CDN. 

Podczas podglądu dostępne są następujące wstępnie skonfigurowane punkty końcowe: 

* **Regiony platformy Azure**
    * Brazylia Południowa
    * Indie Środkowe
    * Środkowe stany USA
    * Azja Wschodnia
    * Wschodnie stany USA
    * Japonia Zachodnia
    * Europa Północna
    * Republika Południowej Afryki Północ
    * Azja Południowo-Wschodnia 
    * Zjednoczone Emiraty Północne
    * Zachodnie Zjednoczone Królestwo  
    * Europa Zachodnia
    * Zachodnie stany USA 
    * Zachodnie stany USA 2
* **Wiele kombinacji regionów platformy Azure** 
    * Wschodnie Stany Zjednoczone, Brazylia Południowa 
    * Wschodnie stany USA, Azja Wschodnia 
    * Europa Zachodnia, Brazylia Południowa
    * Europa Zachodnia, Azja Południowo-Wschodnia
    * Europa Zachodnia, Zjednoczone Emiraty Zjednoczone Ameryki Północnej
    * Zachodnie stany USA, wschodnie stany USA 
    * Zachodnie stany USA, Europa Zachodnia
    * Zachodnie stany USA, Zjednoczone Emiraty Zjednoczone Ameryki Północnej
    * Europa Zachodnia, Zjednoczone Emiraty Zjednoczone Ameryki Północnej, Azji Południowo-Wschodniej
    * Zachodnie stany USA, Europa Zachodnia, Azja Wschodnia
    * Zachodnie STANY USA, Europa Północna, Azja Południowo-Wschodnia, Zjednoczone Emiraty Zjednoczone Północne, Republika Południowej Afryki 
* **Azure + Azure Front Door** — wdrożone w dowolnej pojedynczej lub wielu kombinacjach regionu platformy Azure wymienionych powyżej
* **Azure + Azure CDN firmy Microsoft** — wdrożone w dowolnej kombinacji pojedynczego regionu platformy Azure wymienionej powyżej
* **Usługa Azure + Usługa Azure Traffic Manager** — wdrożona w dowolnej kombinacji wielu regionów platformy Azure wymienionej powyżej

## <a name="suggested-test-scenarios"></a>Sugerowane scenariusze testów 

Aby ułatwić podejmowanie najlepszych decyzji dotyczących wydajności dla klientów, analizator internetowy umożliwia ocenę dwóch punktów końcowych dla określonej populacji użytkowników końcowych. 

Podczas gdy Internet Analyzer może odpowiedzieć na wiele pytań, niektóre z najczęstszych są: 
* Jaki jest wpływ migracji do chmury na wydajność? 
    * *Sugerowany test: niestandardowy (bieżąca infrastruktura lokalna) w porównaniu z platformą Azure (dowolny wstępnie skonfigurowany punkt końcowy)*
* Jaka jest wartość umieszczania moich danych na krawędzi a w centrum danych? 
    *  *Sugerowany test: usługa Azure vs. Azure Front Door, Azure vs. Azure CDN firmy Microsoft*
* Jakie są korzyści z wydajności usług Azure Front Door?
    *  *Sugerowany test: niestandardowe/ azure/ CDN vs. Usługi Azure drzwiami frontowymi*
* Jakie są korzyści z wydajności usługi Azure CDN firmy Microsoft? 
    *  *Sugerowany test: Niestandardowy/ Azure/ AFD vs. Azure CDN firmy Microsoft*
* Jak układa się w usłudze Azure CDN firmy Microsoft? 
    *  *Sugerowany test: niestandardowy (inny punkt końcowy sieci CDN) w porównaniu z usługą Azure CDN firmy Microsoft*
* Jaka jest najlepsza chmura dla populacji użytkowników końcowych w każdym regionie? 
    *  *Sugerowany test: niestandardowy (inna usługa w chmurze) w porównaniu z platformą Azure (dowolny wstępnie skonfigurowany punkt końcowy)*

## <a name="how-it-works"></a>Jak to działa

Aby użyć analizatora internetowego, skonfiguruj zasób analizatora internetowego w portalu Microsoft Azure i zainstaluj małego klienta JavaScript w aplikacji. Klient mierzy opóźnienie od użytkowników końcowych do wybranych punktów końcowych, pobierając obraz o jeden piksel za pośrednictwem protokołu HTTPS. Po zebraniu pomiarów opóźnienia klient wysyła dane pomiarowe do analizatora internetowego.

Gdy użytkownik odwiedza aplikację sieci Web, klient JavaScript wybiera dwa punkty końcowe do zmierzenia we wszystkich skonfigurowanych testach. Dla każdego punktu końcowego klient wykonuje pomiar _zimnego_ i _ciepłego._ Pomiar _zimna_ wiąże się z dodatkowym opóźnieniem obok czystego opóźnienia sieci między użytkownikiem a punktem końcowym, takim jak rozpoznawanie DNS, uzgadnianie połączenia TCP i negocjacje SSL/TLS. _Ciepły_ pomiar następuje tuż po zakończeniu pomiaru _zimna_ i korzysta z trwałego zarządzania połączeniami TCP w nowoczesnych przeglądarkach, aby uzyskać dokładną miarę opóźnienia end-to-end. Gdy jest obsługiwana przez przeglądarkę użytkownika, interfejs API chronometrażu zasobów W3C jest używany do dokładnego pomiaru czasu. Obecnie do analizy używane są tylko pomiary opóźnienia ciepła.

![architektura](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Karty wyników 

Po uruchomieniu testu dane telemetryczne są widoczne w zasobie analizatora internetowego na karcie wyników. Te dane są zawsze agregowane. Użyj następujących filtrów, aby zmienić widok widocznych danych: 

* **Badanie:** Wybierz test, dla którego chcesz wyświetlić wyniki. Dane testowe pojawiają się, gdy jest wystarczająco dużo danych, aby zakończyć analizę – w większości przypadków w ciągu 24 godzin. 
* **Okres & data zakończenia:** Analizator internetowy generuje trzy karty wyników dziennie — każda karta wyników odzwierciedla inny okres agregacji - 24 godziny przed (dzień), siedem dni przed (tydzień) i 30 dni przed (miesiąc). Użyj filtru "Data zakończenia", aby wybrać okres, który chcesz zobaczyć. 
* **Państwo:** Ten filtr służy do wyświetlania danych specyficznych dla użytkowników końcowych zamieszkałych w danym kraju. Filtr globalny pokazuje dane we wszystkich regionach geograficznych.  

Więcej informacji na temat kart wyników można znaleźć na stronie [Interpretacja karty wyników.](internet-analyzer-scorecard.md) 


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć pierwszy zasób analizatora internetowego](internet-analyzer-create-test-portal.md).
* Przeczytaj często zadawane pytania [dotyczące analizatora internetowego](internet-analyzer-faq.md). 
