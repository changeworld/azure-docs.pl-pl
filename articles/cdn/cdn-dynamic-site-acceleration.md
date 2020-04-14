---
title: Dynamiczne przyspieszanie witryny za pośrednictwem usługi Azure CDN
description: Usługa Azure CDN obsługuje optymalizację dynamicznego przyspieszania witryny (DSA) dla plików z zawartością dynamiczną.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 26559adf183a5e008d77b87654a1bd4dabebbca0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253836"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Dynamiczne przyspieszanie witryny za pośrednictwem usługi Azure CDN

Wraz z eksplozją mediów społecznościowych, handlu elektronicznego i hiperspersonalizowanej sieci, szybko rosnący odsetek treści przekazywałych użytkownikom końcowym jest generowany w czasie rzeczywistym. Użytkownicy oczekują szybkiego, niezawodnego i spersonalizowanego środowiska sieciowego, niezależnie od przeglądarki, lokalizacji, urządzenia lub sieci. Jednak te właśnie innowacje, które sprawiają, że te doświadczenia są tak angażujące, również spowalniają pobieranie stron i zagrażają jakości doświadczeń konsumentów. 

Standardowa sieć dostarczania zawartości (CDN) obejmuje możliwość buforowania plików bliżej użytkowników końcowych, aby przyspieszyć dostarczanie plików statycznych. Jednak w przypadku dynamicznych aplikacji sieci web buforowanie tej zawartości w lokalizacjach brzegowych nie jest możliwe, ponieważ serwer generuje zawartość w odpowiedzi na zachowanie użytkownika. Przyspieszenie dostarczania takich treści jest bardziej złożone niż tradycyjne buforowanie krawędzi i wymaga kompleksowego rozwiązania, które precyzyjnie dostraja każdy element wzdłuż całej ścieżki danych od momentu powstania do dostarczenia. Dzięki optymalizacji dynamicznego przyspieszania witryny usługi Azure CDN (DSA) wydajność stron internetowych z zawartością dynamiczną jest wymiernie lepsza.

**Usługa Azure CDN firmy Akamai** i **azure CDN firmy Verizon** oferują optymalizację DSA za pośrednictwem menu **Zoptymalizowane pod kątem** podczas tworzenia punktu końcowego. Dynamiczne przyspieszanie witryny firmy Microsoft jest oferowane za pośrednictwem [usługi Azure Front Door Service.](https://docs.microsoft.com/azure/frontdoor/front-door-overview)

> [!Important]
> W przypadku **usługi Azure CDN z profilów Akamai** można zmienić optymalizację punktu końcowego sieci CDN po jego utworzeniu.
>   
> W przypadku profili usługi **Azure CDN from Verizon** nie można zmienić optymalizacji punktu końcowego usługi CDN po jego utworzeniu.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfiguracja punktu końcowego sieci CDN w celu przyspieszenia dostarczania plików dynamicznych

Aby skonfigurować punkt końcowy usługi CDN w celu optymalizacji dostarczania plików dynamicznych, można użyć witryny Azure portal, interfejsów API REST lub dowolnego z zestawów SDK klienta, aby zrobić to samo programowo. 

**Aby skonfigurować punkt końcowy usługi CDN dla optymalizacji DSA przy użyciu witryny Azure portal:**

1. Na stronie **profilu sieci CDN** wybierz pozycję **Punkt końcowy**.

   ![Dodawanie nowego punktu końcowego sieci CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Zostanie wyświetlone okienko **Dodawanie punktu końcowego**.

2. W obszarze **Zoptymalizowane pod kątem**wybierz pozycję **Dynamiczne przyspieszanie witryny**.

    ![Tworzenie nowego punktu końcowego sieci CDN za pomocą dsa](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. W polu **Ścieżka sondy**wprowadź prawidłową ścieżkę do pliku.

    Ścieżka sondy jest obiektem specyficznym dla DSA i do utworzenia jest wymagana prawidłowa ścieżka. DSA używa pliku ścieżki małej *sondy* umieszczonego na serwerze pochodzenia w celu optymalizacji konfiguracji routingu sieciowego dla sieci CDN. W przypadku pliku ścieżki sondy można pobrać i przekazać przykładowy plik do witryny lub użyć istniejącego zasobu w źródle pochodzenia o rozmiarze około 10 KB.

4. Wprowadź inne wymagane opcje punktu końcowego (aby uzyskać więcej informacji, zobacz [Tworzenie nowego punktu końcowego sieci CDN),](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)a następnie wybierz pozycję **Dodaj**.

   Po utworzeniu punktu końcowego sieci CDN stosuje optymalizacje DSA dla wszystkich plików, które odpowiadają określonym kryteriom. 


**Aby skonfigurować istniejący punkt końcowy dla DSA (usługa Azure CDN tylko z profilów Akamai):**

1. Na stronie **profilu sieci CDN** wybierz punkt końcowy, który chcesz zmodyfikować.

2. W lewym okienku wybierz pozycję **Optymalizacja**. 

   Zostanie wyświetlona strona **Optymalizacja.**

3. W obszarze **Zoptymalizowane dla**wybierz pozycję **Dynamiczne przyspieszanie witryny**, a następnie wybierz pozycję **Zapisz**.

> [!Note]
> DSA wiąże się z dodatkowymi opłatami. Aby uzyskać więcej informacji, zobacz [Ceny sieci dostarczania zawartości](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optymalizacja DSA przy użyciu usługi Azure CDN

Dynamiczne przyspieszanie witryny w usłudze Azure CDN przyspiesza dostarczanie zasobów dynamicznych przy użyciu następujących technik:

-   [Optymalizacja trasy](#route-optimization)
-   [Optymalizacje TCP](#tcp-optimizations)
-   [Wstępne wciąciecz przednastawieniem obiektu (tylko usługa Azure CDN firmy Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Adaptacyjna kompresja obrazu (tylko usługa Azure CDN firmy Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optymalizacja trasy

Optymalizacja trasy jest ważna, ponieważ Internet jest dynamicznym miejscem, w którym ruch i tymczasowe przerwy w działach stale zmieniają topologię sieci. Protokół Brama graniczna (BGP) jest protokołem routingu w Internecie, ale mogą istnieć szybsze trasy za pośrednictwem pośredniczących serwerów Punktu Obecności (PoP). 

Optymalizacja trasy wybiera najbardziej optymalną ścieżkę do źródła, dzięki czemu witryna jest stale dostępna, a zawartość dynamiczna jest dostarczana użytkownikom końcowym za pośrednictwem najszybszej i najbardziej niezawodnej trasy. 

Sieć Akamai wykorzystuje techniki do zbierania danych w czasie rzeczywistym i porównywania różnych ścieżek przez różne węzły na serwerze Akamai, a także domyślnej trasy BGP w otwartym Internecie, aby określić najszybszą trasę między źródłem a krawędzią CDN. Techniki te unikają punktów zatłoczenia Internetu i długich tras. 

Podobnie sieć Verizon używa kombinacji Anycast DNS, obsługa dużej pojemności PoPs i kontroli kondycji, aby określić najlepsze bramy do najlepszych danych trasy z klienta do źródła.
 
W rezultacie w pełni dynamiczna i transakcyjna zawartość jest dostarczana szybciej i bardziej niezawodnie użytkownikom końcowym, nawet jeśli jest niedościwidylna. 

### <a name="tcp-optimizations"></a>Optymalizacje TCP

Protokół TCP (Transmission Control Protocol) to standard pakietu protokołów internetowych używany do dostarczania informacji między aplikacjami w sieci IP.  Domyślnie do skonfigurowania połączenia TCP wymagane jest kilka żądań typu back-and-forth, a także ograniczenia w celu uniknięcia przeciążenia sieci, co powoduje nieefektywność na dużą skalę. **Usługa Azure CDN firmy Akamai** obsługuje ten problem, optymalizując w trzech obszarach: 

 - [Eliminacja powolnego uruchamiania TCP](#eliminating-tcp-slow-start)
 - [Wykorzystanie połączeń trwałych](#leveraging-persistent-connections)
 - [Dostrajanie parametrów pakietu TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminacja powolnego uruchamiania TCP

TCP *slow start* to algorytm protokołu TCP, który zapobiega przeciążeniu sieci, ograniczając ilość danych przesyłanych przez sieć. Zaczyna się od małych przeciążeń rozmiarów okien między nadawcą a odbiorcą, dopóki nie zostanie osiągnięta maksymalna lub zostanie wykryta utrata pakietów.

 Zarówno **usługa Azure CDN firmy Akamai,** jak i **usługa Azure CDN z** profili Verizon eliminują powolny start protokołu TCP z następującymi trzema krokami:

1. Monitorowanie kondycji i przepustowości służy do pomiaru przepustowości połączeń między serwerami PoP brzegowej.
    
2. Metryki są współużytkowane między serwerami PoP brzegowej, dzięki czemu każdy serwer jest świadomy warunków sieciowych i kondycji serwera innych punktów dostępowych wokół nich.  
    
3. Serwery krawędzi cdn zakładają niektóre parametry transmisji, takie jak optymalny rozmiar okna podczas komunikowania się z innymi serwerami krawędzi CDN w pobliżu. Ten krok oznacza, że początkowy rozmiar okna przeciążenia można zwiększyć, jeśli kondycja połączenia między serwerami brzegowymi sieci CDN jest w stanie zwiększyć transfer danych pakietów.  

#### <a name="leveraging-persistent-connections"></a>Wykorzystanie połączeń trwałych

Przy użyciu sieci CDN mniej unikatowych maszyn łączy się bezpośrednio z serwerem pochodzenia w porównaniu z użytkownikami łączącymi się bezpośrednio z twoim źródłem. Usługa Azure CDN również łączenia żądań użytkowników razem, aby ustanowić mniej połączeń ze źródłem.

Jak wcześniej wspomniano, kilka żądań uzgadniania są wymagane do ustanowienia połączenia TCP. Połączenia trwałe, które są implementowane przez nagłówek `Keep-Alive` HTTP, ponownie korzystają z istniejących połączeń TCP dla wielu żądań HTTP, aby zapisać czas podróży w obie strony i przyspieszyć dostarczanie. 

**Usługa Azure CDN firmy Verizon** wysyła również okresowe pakiety keep-alive za pomocą połączenia TCP, aby zapobiec zamknięciu otwartego połączenia.

#### <a name="tuning-tcp-packet-parameters"></a>Dostrajanie parametrów pakietu TCP

**Usługa Azure CDN firmy Akamai** dostraja parametry regulujące połączenia między serwerem i zmniejsza liczbę długodystansowych podróży w obie strony wymaganych do pobierania zawartości osadzonej w lokacji przy użyciu następujących technik:

- Zwiększenie początkowego okna przeciążenia, tak aby można było wysyłać więcej pakietów bez oczekiwania na potwierdzenie.
- Zmniejszanie początkowego limitu czasu ponownego transmisji, tak aby wykryto stratę, a retransmisja odbywa się szybciej.
- Zmniejszenie minimalnego i maksymalnego limitu czasu ponownego przesyłania w celu skrócenia czasu oczekiwania przed przy założeniu, że pakiety zostały utracone podczas transmisji.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Wstępne wciąciecz przednastawieniem obiektu (tylko usługa Azure CDN firmy Akamai)

Większość witryn sieci Web składa się ze strony HTML, która odwołuje się do różnych innych zasobów, takich jak obrazy i skrypty. Zazwyczaj gdy klient żąda strony sieci Web, przeglądarka najpierw pobiera i analizuje obiekt HTML, a następnie wykonuje dodatkowe żądania do połączonych zasobów, które są wymagane do pełnego załadowania strony. 

*Pobieranie wstępne* to technika pobierania obrazów i skryptów osadzonych na stronie HTML, podczas gdy kod HTML jest obsługiwany w przeglądarce, a zanim przeglądarka nawet wysunie te żądania obiektu. 

Po włączeniu opcji wstępnego klucza w momencie, gdy sieć CDN obsługuje stronę podstawową HTML do przeglądarki klienta, sieć CDN analizuje plik HTML i żąda dodatkowych połączonych zasobów i przechowuje go w pamięci podręcznej. Gdy klient składa żądania dla połączonych zasobów, serwer brzegowy sieci CDN ma już żądane obiekty i może obsługiwać je natychmiast bez podróży w obie strony do źródła. Ta optymalizacja korzysta zarówno z zawartości kryjówkowanej, jak i nieskrywnej.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Adaptacyjna kompresja obrazu (tylko usługa Azure CDN firmy Akamai)

Niektóre urządzenia, zwłaszcza mobilne, od czasu do czasu mają mniejszą prędkość sieci. W tych scenariuszach jest bardziej korzystne dla użytkownika, aby otrzymywać mniejsze obrazy na swojej stronie sieci Web szybciej niż długo czekać na obrazy w pełnej rozdzielczości.

Ta funkcja automatycznie monitoruje jakość sieci i wykorzystuje standardowe metody kompresji JPEG, gdy szybkość sieci jest mniejsza, aby skrócić czas dostawy.

Adaptacyjna kompresja obrazu | Rozszerzenia plików  
--- | ---  
Kompresja JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Buforowanie

W przypadku dsa buforowanie jest domyślnie wyłączone w sieci CDN, nawet jeśli źródło zawiera `Cache-Control` lub `Expires` nagłówki w odpowiedzi. DSA jest zwykle używany dla zasobów dynamicznych, które nie powinny być buforowane, ponieważ są one unikatowe dla każdego klienta. Buforowanie może przerwać to zachowanie.

Jeśli masz stronę internetową z mieszanką statycznych i dynamicznych zasobów, najlepiej jest przyjąć podejście hybrydowe, aby uzyskać najlepszą wydajność. 

W przypadku **usługi Azure CDN Standard firmy Verizon** i Azure **CDN Standard z profilów Akamai** można włączyć buforowanie określonych punktów końcowych DSA przy użyciu [reguł buforowania](cdn-caching-rules.md).

Aby uzyskać dostęp do reguł buforowania:

1. Na stronie **profilu sieci CDN** w obszarze Ustawienia wybierz pozycję **Reguły buforowania**.  
    
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    Zostanie otwarta strona **reguł buforowania.**

2. Utwórz globalną lub niestandardową regułę buforowania, aby włączyć buforowanie dla punktu końcowego DSA. 

Tylko w przypadku profilów **usługi Azure CDN Premium z** usług Verizon można włączyć buforowanie określonych punktów końcowych DSA przy użyciu aparatu [reguł](cdn-rules-engine.md). Wszystkie reguły, które są tworzone mają wpływ tylko te punkty końcowe profilu, które są zoptymalizowane pod kątem DSA. 

Aby uzyskać dostęp do silnika reguł:
    
1. Na stronie **profilu sieci CDN** wybierz pozycję **Zarządzaj**.  
    
    ![Przycisk zarządzania profilem CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Zostanie otwarty portal zarządzania siecią CDN.

2. W portalu zarządzania siecią CDN wybierz pozycję **ADN**, a następnie wybierz pozycję **Aparat reguł**. 

    ![Aparat reguł dla DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatywnie można użyć dwóch punktów końcowych usługi CDN: jednego punktu końcowego zoptymalizowanego za pomocą dsa w celu dostarczania zasobów dynamicznych, a innego punktu końcowego zoptymalizowanego za pomocą statycznego typu optymalizacji, takiego jak ogólne dostarczanie w sieci Web, do dostarczania zasobów buforowanych. Zmodyfikuj adresy URL stron sieci Web, aby połączyć się bezpośrednio z zasobem w punkcie końcowym sieci CDN, którego zamierzasz użyć. 

Na przykład: `mydynamic.azureedge.net/index.html` jest stroną dynamiczną i jest ładowany z punktu końcowego DSA.Strona html odwołuje się do wielu zasobów statycznych, takich jak biblioteki JavaScript lub obrazy `mystatic.azureedge.net/banner.jpg` ładowane ze statycznego punktu końcowego sieci CDN, takich jak i `mystatic.azureedge.net/scripts.js`. 



