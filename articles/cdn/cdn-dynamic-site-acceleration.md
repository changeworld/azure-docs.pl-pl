---
title: Przyspieszanie witryn dynamicznych za pomocą usługi Azure CDN
description: Usługa Azure CDN obsługuje optymalizacji przyspieszanie (witryn dynamicznych DSA) witryn dynamicznych dla plików z zawartością dynamiczną.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 08e705d3c3623d4d02ccaea609eb0555aa1c8e33
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593918"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Przyspieszanie witryn dynamicznych za pomocą usługi Azure CDN

Za pomocą rozłożenie mediów społecznościowych, handlu elektronicznego i hyper spersonalizowane w sieci web coraz procent obsługiwanej użytkownikom końcowym zawartości jest generowany w czasie rzeczywistym. Użytkownicy oczekują web szybkie, niezawodne i spersonalizowane środowisko, niezależnie od ich przeglądarki, lokalizacji, urządzeń lub sieci. Jednak bardzo innowacje, składających się z tych środowisk, więc interesujące także spowolnić strony plików do pobrania i stanowić zagrożenie dla jakości obsługi klienta. 

Standardowa dostarczania zawartości (CDN) do sieci obejmuje możliwość plików w pamięci podręcznej bliżej użytkowników końcowych, aby przyspieszyć dostarczanie plików statycznych. Jednak przy użyciu dynamicznych aplikacji sieci web, buforowanie tę zawartość w lokalizacjach nie jest możliwe, ponieważ serwer generuje zawartość w odpowiedzi na zachowania użytkowników. Przyspieszania dostarczania zawartości jest bardziej skomplikowane niż tradycyjne krawędzi buforowania i wymaga rozwiązania end-to-end, który dostrajać każdego elementu w ścieżce danych całej od powstania dostarczania. Z optymalizacją przyspieszanie (witryn dynamicznych DSA) witryn dynamicznych usługi Azure CDN znaczącym ulepszaniu warunków życia zwiększona wydajność stron sieci web z zawartością dynamiczną.

**Usługa Azure CDN from Akamai** i **Azure CDN from Verizon** oba programy oferują DSA optymalizacji za pośrednictwem **zoptymalizowane pod kątem** menu podczas tworzenia punktu końcowego. Przyspieszanie witryn dynamicznych od firmy Microsoft jest oferowana za pośrednictwem [usługi Azure Service drzwiami frontowymi](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> Aby uzyskać **Azure CDN from Akamai** profile, możesz zmienić optymalizacji punktu końcowego usługi CDN, po jego utworzeniu.
>   
> W przypadku profili usługi **Azure CDN from Verizon** nie można zmienić optymalizacji punktu końcowego usługi CDN po jego utworzeniu.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Konfiguracja punktu końcowego usługi CDN na szybszym dostarczaniu dynamiczne pliki

Aby skonfigurować punkt końcowy usługi CDN w celu optymalizacji dostarczania plików dynamicznych, możesz użyć witryny Azure portal, interfejsów API REST lub zestawy SDK klientów na te same czynności wykonasz programowo. 

**Aby skonfigurować punktu końcowego usługi CDN w celu optymalizacji DSA przy użyciu witryny Azure portal:**

1. W **profil CDN** wybierz opcję **punktu końcowego**.

   ![Dodaj nowy punkt końcowy CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   Zostanie wyświetlone okienko **Dodawanie punktu końcowego**.

2. W obszarze **zoptymalizowane pod kątem**, wybierz opcję **przyspieszanie witryn dynamicznych**.

    ![Utwórz nowy punkt końcowy usługi CDN za pomocą DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Aby uzyskać **ścieżka sondy**, wprowadź prawidłową ścieżkę do pliku.

    Ścieżka sondy jest funkcja specyficzna dla DSA, a prawidłowa ścieżka jest wymagana do utworzenia. DSA używa małego *ścieżka sondy* umieszczony plik na serwerze źródłowym, aby zoptymalizować routing konfiguracji sieci CDN. Dla ścieżki pliku sondowania można pobierać i przekaż ten przykładowy plik do witryny lub użyć istniejącego zasobu w źródle użytkownika, który wynosi około 10 KB, rozmiar.

4. Wprowadź inne opcje wymaganego punktu końcowego (Aby uzyskać więcej informacji, zobacz [utworzyć nowy punkt końcowy CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), a następnie wybierz **Dodaj**.

   Po utworzeniu punktu końcowego usługi CDN ma zastosowanie optymalizacje DSA dla wszystkich plików spełniających określone kryteria. 


**Aby skonfigurować istniejącego punktu końcowego dla DSA (Azure CDN from Akamai profilów tylko):**

1. W **profil CDN** wybierz punkt końcowy, który chcesz zmodyfikować.

2. W okienku po lewej stronie wybierz **optymalizacji**. 

   **Optymalizacji** zostanie wyświetlona strona.

3. W obszarze **zoptymalizowane pod kątem**, wybierz opcję **przyspieszanie witryn dynamicznych**, a następnie wybierz **Zapisz**.

> [!Note]
> DSA spowoduje naliczenie dodatkowych opłat. Aby uzyskać więcej informacji, zobacz [cennik usługi Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Optymalizacja DSA, za pomocą usługi Azure CDN

Przyspieszanie witryn dynamicznych w usłudze Azure CDN przyspiesza dostarczanie dynamicznych zasobów przy użyciu następujących technik:

-   [Optymalizację trasy](#route-optimization)
-   [Optymalizacje TCP](#tcp-optimizations)
-   [Object prefetch (Azure CDN from Akamai tylko)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Kompresja adaptacyjne obrazu (Azure CDN from Akamai tylko)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Optymalizację trasy

Optymalizacja trasy jest ważne, ponieważ połączenie z Internetem jest dynamiczne miejsce, w którym ruch i tymczasowe awarii są często zmieniane topologii sieci. Protokół Border Gateway Protocol (BGP) jest protokołem routingu Internetu, ale może być szybsze tras za pośrednictwem pośrednie serwerów punktu obecności (PoP). 

Optymalizacja trasy wybiera najbardziej optymalną ścieżka do źródła, aby lokacja jest stale dostępna i dynamicznej zawartości są dostarczane do użytkowników końcowych za pośrednictwem trasy najszybszy i najbardziej niezawodny, które muszą być możliwe. 

Sieć Akamai używa technik do zbierania danych w czasie rzeczywistym i porównanie różnych ścieżek przy użyciu różnych węzłach w serwerze Akamai, jak również domyślne trasy protokołu BGP w różnych Otwórz Internet w celu określenia najszybszy wyznaczać trasy między pochodzenie i krawędzi sieci CDN. Te techniki uniknąć Internet punktów przeciążenia i długi trasy. 

Podobnie używa sieci Verizon kombinację emisji dowolnej DNS, wysoka pojemność obsługują POP i kontrole kondycji określają najlepsze bram na najlepsze przesyłanie danych z klienta do źródła.
 
W rezultacie w pełni dynamicznego i transakcyjnych dostarczania zawartości szybciej i bardziej niezawodnie użytkownikom końcowym, nawet wtedy, gdy jest uncacheable. 

### <a name="tcp-optimizations"></a>Optymalizacje TCP

Transmission Control Protocol (TCP) to standard zestawem protokołów internetowych, które są używane do dostarczania informacji między aplikacjami sieci IP.  Domyślnie kilka żądań Wstecz i w przód są wymagane do skonfigurowania połączenia protokołu TCP, jak również limity, aby uniknąć congestions sieci, które skutkują nieefektywności na dużą skalę. **Usługa Azure CDN from Akamai** obsługuje ten problem, optymalizując w trzech obszarach: 

 - [Wyeliminowanie start powolne TCP](#eliminating-tcp-slow-start)
 - [Korzystanie z połączeń trwałych](#leveraging-persistent-connections)
 - [Dostrajanie parametrów pakiet TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Wyeliminowanie start powolne TCP

TCP *wolne start* jest algorytm protokołu TCP, który uniemożliwia przeciążenia sieci, ograniczając ilość danych przesyłanych przez sieć. Rozpoczynają się one od przeciążenia małe rozmiary okna między nadawcą i odbiorcą aż do osiągnięcia maksymalnej lub wykryciu utraty pakietów.

 Zarówno **Azure CDN from Akamai** i **Azure CDN from Verizon** profile wyeliminować TCP powolne rozpoczyna się od następujących trzech kroków:

1. Przepustowość monitorowania kondycji i służy do pomiaru przepustowości połączenia między serwerami punktu obecności krawędzi.
    
2. Metryki są udostępniane między serwerami punktu obecności krawędzi tak, aby każdy serwer ma informacje o warunkach sieciowych i serwera zdrowia lokalizacji POP wokół nich.  
    
3. Serwerów granicznych usługi CDN zakładają niektóre parametry transmisji, takie jak jakie optymalnego rozmiaru okna powinny być podczas komunikowania się z innych serwerów granicznych usługi CDN w jego odległości między elementami. W tym kroku oznacza, że przeciążenia początkowy rozmiar okna można zwiększyć, jeśli kondycję połączenia między serwerów granicznych usługi CDN jest w stanie wyższe transfery danych pakietu.  

#### <a name="leveraging-persistent-connections"></a>Korzystanie z połączeń trwałych

Korzystanie z sieci CDN, mniejszej liczby unikatowych maszyn, połączyć do serwera pochodzenia bezpośrednio w porównaniu z użytkowników łączących się bezpośrednio na źródło. Usługa Azure CDN pul również żądań użytkowników ze sobą, aby nawiązywać połączenia z mniejszą liczbą ze źródła.

Jak wspomniano wcześniej wiele żądań uzgadnianie są wymagane do nawiązania połączenia TCP. Trwałego połączenia, które są implementowane przez `Keep-Alive` nagłówka HTTP, ponowne używanie istniejących połączeń TCP dla wielu żądań HTTP zapisać czasy błądzenia i przyspieszyć dostawy. 

**Usługa Azure CDN from Verizon** wysyła również pakiety utrzymywania aktywności okresowe za pośrednictwem połączenia protokołu TCP, aby zapobiec otwartego połączenia z zamykane.

#### <a name="tuning-tcp-packet-parameters"></a>Dostrajanie parametrów pakiet TCP

**Usługa Azure CDN from Akamai** Dostraja parametry połączenia serwera z serwerem i zmniejsza liczbę rund długodystansowe wymagany na pobranie zawartości osadzone w lokacji przy użyciu następujących technik:

- Zwiększanie okna przeciążenia początkowej tak, aby więcej pakietów można wysłać bez oczekiwania na potwierdzenie.
- Tak, aby utratę zostanie wykryta, a retransmisji odbywa się szybciej, zmniejszając początkowej retransmisji przekroczenia limitu czasu.
- Zmniejszenie na retransmisji minimalną i maksymalną wartość limitu czasu zmniejszyć czas oczekiwania przed przy założeniu, że pakiety zostały utracone podczas transmisji.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Object prefetch (Azure CDN from Akamai tylko)

Większość witryn sieci Web składają się z strony HTML, który odwołuje się do różnych zasobów takich jak obrazy i skryptów. Zazwyczaj gdy klient zażąda strony sieci Web, przeglądarki najpierw pobiera analizuje obiektu HTML i następnie zgłasza żądania dodatkowych połączonych zasobów, które są wymagane do pełnego załadowania strony. 

*Pobieranie z wyprzedzeniem* jest technikę do pobierania obrazów i skryptów osadzony na stronie HTML a HTML są dostarczane do przeglądarki i przed przeglądarki umożliwia nawet te żądania obiektu. 

Z opcją pobierania z wyprzedzeniem, włączona w czasie, gdy usługa CDN służy strony podstawowej HTML do przeglądarki klienta sieci CDN analizuje plik HTML i dodatkowych żądań dla wszystkich połączonych zasobów i zapisać ją w pamięci podręcznej. Gdy klient wysyła żądania do połączonych zasobów, serwer krawędzi sieci CDN już ma żądane obiekty i może służyć ich bezpośrednio bez komunikacji dwustronnej do źródła. Tego rodzaju optymalizacji, przynosi korzyści zarówno podlega buforowaniu, jak i nie podlega buforowaniu zawartości.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Kompresja adaptacyjne obrazu (Azure CDN from Akamai tylko)

Niektóre urządzenia, szczególnie tymi przenośnych środowisko wolniejszej sieci od czasu do czasu. W tych scenariuszach jest bardziej korzystne dla szybciej odbieranie małe obrazy w ich strony sieci Web, zamiast czekać zbyt długo obrazy w pełnej rozdzielczości.

Ta funkcja automatycznie monitoruje jakości w sieci i stosuje standardowe metody kompresji JPEG, gdy szybkości sieci są wolniejsze poprawić czas dostawy.

Kompresja adaptacyjne obrazu | Rozszerzenia plików  
--- | ---  
Dekompresji JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Buforowanie

Za pomocą DSA, buforowanie jest wyłączone domyślnie w usłudze CDN nawet wtedy, gdy źródło zawiera `Cache-Control` lub `Expires` nagłówków w odpowiedzi. DSA zwykle jest używana do dynamicznego zasoby, które nie powinny być buforowane, ponieważ są unikatowe dla każdego klienta. Buforowanie może przerwać to zachowanie.

Jeśli masz witrynę z różnymi zasobami statycznych i dynamicznych, najlepiej wykonać podejście hybrydowe, aby uzyskać najlepszą wydajność. 

Dla **Azure CDN Standard from Verizon** i **Azure CDN Standard from Akamai** profile, można włączyć buforowanie dla określonych DSA punktów końcowych przy użyciu [reguły buforowania](cdn-caching-rules.md).

Dostęp do reguły buforowania:

1. Z **profil CDN** stronie w obszarze Ustawienia wybierz **reguły buforowania**.  
    
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    **Reguły buforowania** zostanie otwarta strona.

2. Utwórz globalne lub niestandardowe reguły buforowania włączenie buforowania dla punktu końcowego DSA. 

Aby uzyskać **Azure CDN Premium from Verizon** tylko profile, włączenie buforowania dla określonego DSA punktów końcowych przy użyciu [aparat reguł](cdn-rules-engine.md). Wszystkie reguły, które są tworzone wpływa na tylko te punkty końcowe profilu, które są zoptymalizowane pod kątem DSA. 

Dostęp do aparatu reguł:
    
1. Z **profil CDN** wybierz opcję **Zarządzaj**.  
    
    ![Profil CDN Zarządzanie przycisku](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Zostanie otwarty w portalu zarządzania usługi CDN.

2. W portalu zarządzania usługi CDN wybierz **sieci ADN**, a następnie wybierz **aparat reguł**. 

    ![Aparat reguł technologię DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Alternatywnie, można użyć dwóch punktów końcowych usługi CDN: jeden punkt końcowy zoptymalizowanej przy użyciu DSA dynamicznych zasobów i innego punktu końcowego, zoptymalizowanej przy użyciu typu statycznego optymalizacji, na przykład ogólne dostarczanie w Internecie, aby zasoby podlega buforowaniu, dostawy. Zmodyfikuj swoje adresy URL strony sieci Web, połączyć się bezpośrednio do elementu zawartości w punkcie końcowym usługi CDN, której planujesz używać. 

Na przykład: `mydynamic.azureedge.net/index.html` jest stroną dynamiczne, a także jest ładowany z punktu końcowego DSA.  Strony html odwołuje się do wielu zasobów statycznych, takich jak biblioteki JavaScript lub obrazów, które są ładowane z statycznego punktu końcowego usługi CDN, takie jak `mystatic.azureedge.net/banner.jpg` i `mystatic.azureedge.net/scripts.js`. 



