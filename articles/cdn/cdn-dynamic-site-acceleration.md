---
title: Akceleracja dynamiczne witryny za pomocą usługi Azure CDN
description: Usługi Azure CDN obsługuje lokacji dynamicznej optymalizacji acceleration (DSA) dla plików z dynamiczną zawartością.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: rli; v-deasim
ms.openlocfilehash: 66032a68634be5b52bbfa544d0a3bcd3d1090652
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359989"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Akceleracja dynamiczne witryny za pomocą usługi Azure CDN

Z rozłożenie mediów społecznościowych, handlu elektronicznego i web spersonalizowanych funkcji hyper szybko rośnie odsetek obsługiwanej użytkownikom końcowym zawartości jest generowany w czasie rzeczywistym. Użytkownicy oczekują web szybkie, niezawodne i spersonalizowanego doświadczenia, niezależnie od przeglądarki, lokalizacji, urządzenia lub sieci. Jednak bardzo innowacje, wchodzące w tych środowisk, dlatego współpraca również powolna strona pliki do pobrania i zagrożenie jakości obsługi klienta. 

Standardowa dostarczania zawartości (CDN) do sieci obejmuje możliwość przyspieszenia dostarczanie plików statycznych do pamięci podręcznej plików bliżej dla użytkowników końcowych. Jednak z dynamicznych aplikacji sieci web, buforowanie tej zawartości w lokalizacjach edge nie jest możliwe, ponieważ serwer generuje zawartość w odpowiedzi na zachowanie użytkownika. Przyspieszenia dostarczania zawartości takie jest bardziej złożone niż tradycyjne krawędzi buforowanie i wymaga precyzyjne Dostraja każdego elementu w ścieżce danych od chwili rozpoczęcia dostarczać rozwiązanie end-to-end. Z usługi Azure CDN optymalizacji acceleration (DSA) dynamiczne witryny widoczny jest zwiększenie wydajności wieku sieci web z zawartością dynamiczną.

**Azure CDN from Akamai** i **Azure CDN from Verizon** zawierają DSA optymalizacji za pośrednictwem **zoptymalizowane pod kątem** menu podczas tworzenia punktu końcowego.

> [!Important]
> Aby uzyskać **Azure CDN from Akamai** profile, możesz zmienić optymalizacji punktu końcowego usługi CDN, po jego utworzeniu.
>   
> Aby uzyskać **Azure CDN from Verizon** profile, nie można zmienić optymalizacji punktu końcowego usługi CDN po jego utworzeniu.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfiguracja punktu końcowego sieci CDN w celu przyspieszenia dostarczania plików dynamicznych

Aby skonfigurować punkt końcowy usługi CDN, aby zoptymalizować dostarczania plików dynamicznych, albo można portalu Azure, interfejsów API REST lub zestawów SDK klienta tak samo postąpić programowo. 

**Aby skonfigurować punktu końcowego usługi CDN optymalizacji DSA przy użyciu portalu Azure:**

1. W **profilu CDN** wybierz pozycję **punktu końcowego**.

   ![Dodaj nowy punkt końcowy CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Zostanie wyświetlone okienko **Dodawanie punktu końcowego**.

2. W obszarze **zoptymalizowane pod kątem**, wybierz pozycję **przyspieszenie dynamiczne witryny**.

    ![Utwórz nowy punkt końcowy CDN z DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Aby uzyskać **ścieżki sondowania**, wprowadź prawidłową ścieżkę do pliku.

    Ścieżka sondy jest funkcja specyficzna dla DSA, a wymagane do tworzenia jest prawidłowa ścieżka. DSA używa małą *ścieżki sondowania* pliku umieszczone na serwerze źródłowym w celu optymalizacji sieci konfiguracji routingu dla sieci CDN. Dla sondowania ścieżki pliku możesz pobrać i Przekaż przykładowy plik do witryny lub użyć istniejącego zasobu DS, który jest o rozmiarze około 10 KB.

4. Wprowadź inne opcje wymaganego punktu końcowego (Aby uzyskać więcej informacji, zobacz [utworzyć nowy punkt końcowy CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), a następnie wybierz pozycję **Dodaj**.

   Po utworzeniu punktu końcowego CDN ma to zastosowanie optymalizacje DSA dla wszystkich plików spełniających określone kryteria. 


**Aby skonfigurować istniejący punkt końcowy dla DSA (Azure CDN from Akamai tylko profilów):**

1. W **profilu CDN** wybierz punkt końcowy, który chcesz zmodyfikować.

2. W okienku po lewej stronie wybierz **optymalizacji**. 

   **Optymalizacji** zostanie wyświetlona strona.

3. W obszarze **zoptymalizowane pod kątem**, wybierz pozycję **przyspieszenie dynamiczne witryny**, a następnie wybierz pozycję **zapisać**.

> [!Note]
> DSA wiąże się z dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [cennik Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optymalizacja DSA za pomocą usługi Azure CDN

Dynamiczne przyspieszenie witryny na platformie Azure CDN przyspiesza dostarczania dynamicznych zasobów przy użyciu następujących metod:

-   [Optymalizacja trasy](#route-optimization)
-   [Optymalizacje TCP](#tcp-optimizations)
-   [Pobieranie z wyprzedzeniem obiektów (Azure CDN from Akamai tylko)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Kompresja adaptacyjną obrazu (Azure CDN from Akamai tylko)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optymalizacja trasy

Optymalizacja trasy jest ważne, ponieważ Internet jest dynamiczne miejsce, w którym ruch i tymczasowo awarie są ciągle zmieniana topologii sieci. Protokół BGP (Border Gateway) jest protokołem routingu z Internetu, ale może to być szybsze tras za pomocą pośredniczącej serwerów punktu obecności (PoP). 

Optymalizacja trasy wybiera optymalny ścieżka do źródła, aby witryna jest stale dostępny i dynamicznej zawartości jest dostarczany do użytkowników końcowych za pomocą trasy najszybsze i najbardziej niezawodnym możliwe. 

Sieć Akamai używa techniki do zbierania danych w czasie rzeczywistym i porównanie różnych ścieżek za pomocą różnych węzłach w serwerze Akamai, a także trasy protokołu BGP domyślnej różnych Otwórz Internet w celu określenia najszybszy trasy od źródła i krawędzi sieci CDN. Te techniki uniknąć internetowych punktów przeciążenia i długi trasy. 

Podobnie sieci Verizon używa kombinacji emisji DNS, dużej pojemności obsługuje POP i kontroli kondycji ustalenie najlepszych bramy do najważniejsze dane trasy z klienta do źródła.
 
W związku z tym pełni dynamicznych i transakcyjnego dostarczania zawartości szybciej i bardziej niezawodnie dla użytkowników końcowych, nawet wtedy, gdy jest uncacheable. 

### <a name="tcp-optimizations"></a>Optymalizacje TCP

Transmission Control Protocol (TCP) jest standardem zestawu protokołów internetowych, używanych do ich dostarczania informacji między aplikacjami sieci IP.  Domyślnie kilka żądań Wstecz i w przód są wymagane do konfigurowania połączeń TCP, jak również limity w celu uniknięcia congestions sieci, które powodują wydajność na dużą skalę. **Azure CDN from Akamai** obsługi tego problemu przez optymalizacji w trzech obszarach: 

 - [Wyeliminowanie start powolne TCP](#eliminating-tcp-slow-start)
 - [Korzystanie z usług połączeń trwałych](#leveraging-persistent-connections)
 - [Dostrajanie parametrów pakietów TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Wyeliminowanie start powolne TCP

TCP *powolna start* jest algorytm protokołu TCP, który uniemożliwia przeciążenie sieci, ograniczając ilość danych przesyłanych przez sieć. Rozpoczyna się od przeciążenia mały rozmiar okna między nadawcą i odbiorcą aż do osiągnięcia maksymalnej lub wykrycia utraty pakietów.

 Zarówno **Azure CDN from Akamai** i **Azure CDN from Verizon** profile wyeliminować TCP powolne Rozpoczynanie następujące trzy kroki:

1. Przepustowość monitorowania kondycji i służy do pomiaru przepustowości połączenia między serwerami PoP krawędzi.
    
2. Metryki są współużytkowane serwery krawędzi PoP, aby każdy serwer zdaje się warunków sieciowych i kondycji serwera punkty obecności wokół nich.  
    
3. Serwery krawędzi CDN zakładają niektórych parametrów transmisji, na przykład co optymalnego rozmiaru okna powinna być podczas komunikowania się z innymi serwerami krawędzi sieci CDN w jego pobliżu. Ten krok oznacza, że przeciążenia początkowy rozmiar okna można zwiększyć, jeśli kondycję połączenia między serwerami krawędzi CDN jest w stanie wyższej transferów danych pakietów.  

#### <a name="leveraging-persistent-connections"></a>Korzystanie z usług połączeń trwałych

Za pomocą CDN, mniejszej liczby unikatowych maszyn podłączyć się do serwera pochodzenia bezpośrednio w porównaniu z użytkowników łączących się bezpośrednio do źródła. Usługi Azure CDN puli również żądań użytkowników ze sobą w celu nawiązywania połączeń mniej z punktu początkowego.

Jak wcześniej wspomniano kilka żądania uzgadniania są wymagane do nawiązania połączenia TCP. Połączenia trwałe, które są implementowane przez `Keep-Alive` nagłówka HTTP, ponowne użycie istniejących połączeń TCP dla wielu żądań HTTP zapisać czasami opóźnienia i przyspieszyć proces dostarczania. 

**Usługi Azure CDN from Verizon** wysyła również pakiety utrzymywania aktywności okresowe za pośrednictwem połączenia TCP, aby zapobiec Otwieranie połączenia z zamknięte.

#### <a name="tuning-tcp-packet-parameters"></a>Dostrajanie parametrów pakietów TCP

**Azure CDN from Akamai** Dostraja parametry połączenia do serwera i zmniejsza liczbę rund długi zasięg wymagany do pobrania zawartości osadzone w lokacji przy użyciu następujących metod:

- Zwiększanie początkowej przeciążenia okna tak, aby więcej pakietów mogą być wysyłane bez oczekiwania na potwierdzenie.
- Zmniejszenie limitu czasu początkowej retransmisji wykrycia straty, a retransmisji występuje szybciej.
- Zmniejszenie limitu czasu retransmisji minimalną i maksymalną, aby skrócić czas oczekiwania przed przy założeniu, że pakiety zostały utracone podczas transmisji.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Pobieranie z wyprzedzeniem obiektów (Azure CDN from Akamai tylko)

Większość witryn sieci Web składają się z strony HTML, który odwołuje się do różnych zasobów takich jak obrazy czy skrypty. Zazwyczaj, gdy klient zażąda strony sieci Web, przeglądarki najpierw pobiera i analizuje obiektu HTML, a następnie tworzy dodatkowych żądań do połączonych zasobów, które są wymagane do pełni załadowania strony. 

*Pobieranie z wyprzedzeniem* jest technika, którą można pobrać obrazów i skrypty osadzone w strony HTML podczas HTML jest obsługiwane w przeglądarce, ale przed przeglądarki nawet zgłasza żądania dotyczące tych obiektów. 

Opcja pobierania z wyprzedzeniem włączona w czasie, gdy CDN służy strona podstawowa HTML do przeglądarki klienta CDN analizuje pliku w formacie HTML i dodatkowych żądania dla wszystkich połączonych zasobów i zapisz go w pamięci podręcznej. Gdy klient wysyła żądania dla połączonych zasobów, serwer graniczny CDN już ma żądane obiekty i może obsługiwać je bezpośrednio bez podróż do źródła. Optymalizacja przynosi korzyści zarówno buforowalnej, jak i buforowalnej zawartości.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Kompresja adaptacyjną obrazu (Azure CDN from Akamai tylko)

Niektóre urządzenia, szczególnie przenośnych te wystąpić niższych szybkościach sieci od czasu do czasu. W tych scenariuszach jest bardziej korzystne dla użytkownika szybciej otrzymać mniejszych obrazów na ich strony sieci Web nie czekać zbyt długo dla obrazów pełnej rozdzielczości.

Funkcja ta automatycznie monitoruje jakości sieci i wykorzystuje standardowe metody kompresji JPEG, gdy szybkości sieci są wolniejszej zwiększające czas dostarczenia.

Kompresja adaptacyjną obrazu | Rozszerzenia plików  
--- | ---  
Kompresja JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Buforowanie

Z DSA, buforowanie jest domyślnie wyłączona w sieci CDN, nawet wtedy, gdy punkt początkowy obejmuje `Cache-Control` lub `Expires` nagłówków odpowiedzi. DSA jest zazwyczaj używana w przypadku dynamicznego zasoby, które nie mają być buforowane, ponieważ są one unikatowe dla każdego klienta. Buforowanie mogą być dzielone to zachowanie.

Jeśli masz witrynę z różnymi statycznych i dynamicznych zasobów, najlepiej zająć rozwiązanie hybrydowe, aby uzyskać najlepszą wydajność. 

Dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profile, można włączyć buforowanie dla określonych DSA punktów końcowych przy użyciu [buforowanie reguły](cdn-caching-rules.md).

Dostęp do reguły buforowania:

1. Z **profilu CDN** strony, w obszarze Ustawienia zaznacz **buforowanie reguły**.  
    
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Buforowanie reguły** zostanie otwarta strona.

2. Utwórz regułę buforowania globalnych lub niestandardowych włączyć buforowanie dla punktu końcowego DSA. 

Dla **Azure CDN Premium from Verizon** profilów tylko włączeniu buforowania dla określonego DSA punktów końcowych przy użyciu [aparatu reguł](cdn-rules-engine.md). Wszystkie reguły, które są tworzone wpływa na tylko te punkty końcowe profilu są zoptymalizowane pod kątem DSA. 

Aby uzyskać dostęp do aparatu reguł:
    
1. Z **profilu CDN** wybierz pozycję **Zarządzaj**.  
    
    ![Przycisk Zarządzaj profilu CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Zostanie otwarty w portalu zarządzania usługi CDN.

2. Wybierz z portalu zarządzania usługi CDN **sieci ADN**, a następnie wybierz pozycję **aparatu reguł**. 

    ![Aparat reguł dla DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatywnie można użyć dwóch punktów końcowych usługi CDN: jeden punkt końcowy zoptymalizowane z DSA dynamicznych zasobów i innym punktem końcowym zoptymalizowanych pod kątem typu statycznego optymalizacji, takie jak ogólne dostarczania sieci web, do dostarczania buforowalnej zasobów. Zmodyfikuj adresami URL strony sieci Web bezpośrednio do elementu zawartości w punkcie końcowym CDN, który ma być używany. 

Na przykład: `mydynamic.azureedge.net/index.html` stronę dynamicznego i jest załadowany z punktu końcowego DSA.  Strony html odwołuje się do wielu zasobów statycznych, takich jak biblioteki języka JavaScript lub obrazów, które są ładowane z statycznego punktu końcowego usługi CDN, takich jak `mystatic.azureedge.net/banner.jpg` i `mystatic.azureedge.net/scripts.js`. 



