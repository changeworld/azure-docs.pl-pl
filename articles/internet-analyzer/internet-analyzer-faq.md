---
title: Analizator internetowy Często zadawane pytania | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące analizatora internetowego platformy Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184251"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Często zadawane pytania dotyczące analizatora internetu platformy Azure (wersja zapoznawcza)

Jest to często zadawane pytania dotyczące analizatora internetowego platformy Azure — jeśli masz dodatkowe pytania, przejdź na [forum opinii](https://aka.ms/internetAnalyzerFeedbackForum) i opublikuj swoje pytanie. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

## <a name="how-do-i-participate-in-the-preview"></a>Jak mogę wziąć udział w wersji zapoznawczej?

Podgląd jest dostępny dla wybranych klientów. Jeśli jesteś zainteresowany dołączeniem do wersji zapoznawczej, wykonaj następujące czynności:

1. Zaloguj się do [Portalu Azure](https://ms.portal.azure.com).
2. Przejdź do strony **Subskrypcje.**
3. Kliknij subskrypcję platformy Azure, z którą zamierzasz korzystać z analizatora internetowego.
4. Przejdź do ustawień **dostawców zasobów** dla subskrypcji.
5. Wyszukaj **witrynę Microsoft.Network** i kliknij przycisk **Zarejestruj się** (lub **Zarejestruj się ponownie).**
![żądanie dostępu](./media/ia-faq/request-preview-access.png)

6. [Poproś o zatwierdzenie,](https://aka.ms/internetAnalyzerContact) podając nam swój adres e-mail i identyfikator subskrypcji platformy Azure, który został użyty do wysłania żądania dostępu.
7. Po zatwierdzeniu żądania otrzymasz potwierdzenie e-mail i będzie można tworzyć/aktualizować/modyfikować zasoby analizatora internetowego z nowo dozwolonej subskrypcji platformy Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Czy muszę osadzić klienta, aby uruchomić test?

Tak, klient analizatora internetowego musi być zainstalowany w aplikacji, aby zbierać metryki specyficzne dla użytkowników. [Dowiedz się, jak zainstalować klienta.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Czy otrzymuję naliczone naliczanie ekscesów za udział w wersji zapoznawczej?
Nie, azure internet analizator jest wolny do użycia w wersji zapoznawczej. Podczas wersji zapoznawczej nie ma umowy dotyczącej poziomu usług.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Jakie scenariusze ma zaradzić analizatorowi internetoweemu?

Analizator internetowy został zaprojektowany, aby zapewnić szczegółowe informacje o wydajności sieci na podstawie populacji użytkowników. Aby ułatwić podejmowanie najlepszych decyzji dotyczących wydajności dla użytkowników, analizator internetowy porównuje wydajność dwóch punktów końcowych Internetu przy użyciu odrębnej populacji użytkowników. Podczas gdy Internet Analyzer może odpowiedzieć na wiele pytań, niektóre z najczęstszych są:

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

## <a name="which-tests-can-i-run-in-preview"></a>Jakie testy można uruchomić w wersji zapoznawczej?

Każdy test utworzony w analizatorze internetowym składa się z dwóch punktów końcowych — punktu końcowego A i punktu końcowego B. Dowolną z następujących kombinacji można uruchomić jako testy:  
* Dwa wstępnie skonfigurowane punkty końcowe,
* Jeden niestandardowy i jeden wstępnie skonfigurowany punkt końcowy, lub
* Dwa [niestandardowe punkty końcowe](internet-analyzer-custom-endpoint.md) (jeden niestandardowy punkt końcowy musi znajdować się na platformie Azure).

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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Czym różni się analizator internetowy od innych usług monitorowania świadczonych przez platformę Azure?

Analizator internetowy pomaga zrozumieć wydajność użytkowników końcowych i pomaga w podejmowaniu decyzji w celu poprawy ich wydajności. Podczas gdy inne narzędzia do monitorowania platformy Azure zapewniają wgląd w usługi platformy Azure, analizator internetowy koncentruje się na mierzeniu wydajności Internetu end-to-end dla użytkowników.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>W jaki sposób dane pomiarowe są obsługiwane przez analizator internetowy?

Platforma Azure ma [silne procesy zabezpieczeń i spełnia szeroki zakres standardów zgodności.](https://azure.microsoft.com/support/trust-center/) Tylko Ty i Twój wyznaczony zespół macie dostęp do Twoich danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do niego tylko w określonych ograniczonych okolicznościach, o których wiadomo. Jest szyfrowany podczas transportu i w spoczynku.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się [z omówieniem analizatora internetowego.](internet-analyzer-overview.md)
