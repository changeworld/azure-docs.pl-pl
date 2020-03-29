---
title: Zaplecze i pule zaplecza w drzwiach frontowych platformy Azure| Dokumenty firmy Microsoft
description: W tym artykule opisano, jakie zaplecze i pule wewnętrznej bazy danych są w konfiguracji drzwiami frontowymi.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293470"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Zaplecze i pule zaplecza w drzwiach frontowych platformy Azure
W tym artykule opisano pojęcia dotyczące mapowania wdrożenia aplikacji za pomocą usługi Azure Front Door. Wyjaśniono również różne terminy w konfiguracji drzwi ami frontowymi wokół zaplecza aplikacji.

## <a name="backends"></a>Zaplecza
Wewnętrznej bazy danych jest równa wystąpienia wdrażania aplikacji w regionie. Drzwi frontowe obsługuje zaplecze platformy Azure i nienawiązywać się z platformą Azure, więc region nie ogranicza się tylko do regionów platformy Azure. Ponadto może to być lokalne centrum danych lub wystąpienie aplikacji w innej chmurze.

Zaplecze drzwi frontowych odnoszą się do nazwy hosta lub publicznego adresu IP aplikacji, który może obsługiwać żądania klientów. Zaplecze nie należy mylić z warstwy bazy danych, warstwy magazynu i tak dalej. Zaplecze powinny być wyświetlane jako publiczny punkt końcowy wewnętrznej bazy danych aplikacji. Po dodaniu wewnętrznej bazy danych w puli zaplecza drzwi frontowych należy również dodać następujące elementy:

- **Typ hosta wewnętrznej bazy danych**. Typ zasobu, który chcesz dodać. Drzwi frontowe obsługuje automatyczne wykrywanie zaplecza aplikacji z usługi aplikacji, usługi w chmurze lub magazynu. Jeśli chcesz użyć innego zasobu na platformie Azure lub nawet zaplecza innej niż azure, wybierz opcję **Host niestandardowy**.

    >[!IMPORTANT]
    >Podczas konfiguracji interfejsy API nie sprawdzają poprawności, jeśli wewnętrznej bazy danych jest niedostępny ze środowisk drzwi ami frontowymi. Upewnij się, że drzwi frontowe mogą dotrzeć do wewnętrznej bazy danych.

- **Nazwa hosta subskrypcji i wewnętrznej bazy danych**. Jeśli nie wybrano **hosta niestandardowego** dla typu hosta wewnętrznej bazy danych, wybierz zaplecze, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta wewnętrznej bazy danych w interfejsie użytkownika.

- **Nagłówek hosta wewnętrznej bazy danych**. Wartość nagłówka hosta wysłana do wewnętrznej bazy danych dla każdego żądania. Aby uzyskać więcej informacji, zobacz [Nagłówek hosta wewnętrznej bazy danych](#hostheader).

- **Priorytet**. Przypisz priorytety do różnych zaplecza, jeśli chcesz użyć wewnętrznej bazy danych usługi podstawowej dla całego ruchu. Ponadto należy udostępnić kopie zapasowe, jeśli podstawowe lub kopii zapasowej zaplecza są niedostępne. Aby uzyskać więcej informacji, zobacz [Priorytet](front-door-routing-methods.md#priority).

- **Waga**. Przypisz wagi do różnych zaplecza, aby rozdzielić ruch na zestaw zaplecza, równomiernie lub zgodnie ze współczynnikami wagi. Aby uzyskać więcej informacji, zobacz [Wagi](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Nagłówek hosta wewnętrznej bazy danych

Żądania przekazywane przez drzwi frontowe do wewnętrznej bazy danych zawierają pole nagłówka hosta używane do pobierania zasobu docelowego. Wartość dla tego pola zazwyczaj pochodzi z identyfikatora URI wewnętrznej bazy danych i ma hosta i portu.

Na przykład żądanie wykonane `www.contoso.com` dla będzie mieć nagłówek hosta www.contoso.com. Jeśli używasz witryny Azure Portal do konfigurowania wewnętrznej bazy danych, wartością domyślną dla tego pola jest nazwa hosta wewnętrznej bazy danych. Jeśli wewnętrznej bazy danych jest contoso-westus.azurewebsites.net, w witrynie Azure portal, autopopulated wartość nagłówka hosta wewnętrznej bazy danych zostanie contoso-westus.azurewebsites.net. Jeśli jednak używasz szablonów usługi Azure Resource Manager lub innej metody bez jawnego ustawiania tego pola, drzwiami frontowymi będą wysyłać przyjście nazwę hosta jako wartość nagłówka hosta. Jeśli żądanie zostało złożone\.dla contoso.com www, a zaplecze jest contoso-westus.azurewebsites.net, które ma puste pole nagłówka, drzwi frontowe ustawią nagłówek hosta jako www\.contoso.com.

Większość zaplecza aplikacji (usługi Azure Web Apps, magazyn obiektów Blob i usługi w chmurze) wymaga nagłówka hosta, aby dopasować domenę wewnętrznej bazy danych. Jednak host frontend, który trasy do wewnętrznej bazy danych użyje innej nazwy hosta, takich jak www.contoso.net.

Jeśli wewnętrznej bazy danych wymaga nagłówka hosta, aby dopasować nazwę hosta wewnętrznej bazy danych, upewnij się, że nagłówek hosta wewnętrznej bazy danych zawiera wewnętrznej bazy danych nazwy hosta.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurowanie nagłówka hosta wewnętrznej bazy danych dla wewnętrznej bazy danych

Aby skonfigurować pole **nagłówka hosta wewnętrznej bazy danych** dla wewnętrznej bazy danych w sekcji puli wewnętrznej bazy danych:

1. Otwórz zasób drzwi frontowych i wybierz pulę wewnętrznej bazy danych z zapleczem do skonfigurowania.

2. Dodaj zaplecze, jeśli jeszcze tego nie zrobiono, lub edytuj istniejącą.

3. Ustaw pole nagłówka hosta wewnętrznej bazy danych na wartość niestandardową lub pozostaw go pustym. Nazwa hosta żądania przychodzącego będzie używana jako wartość nagłówka hosta.

## <a name="backend-pools"></a>Pule zaplecza
Pula wewnętrznej bazy danych w drzwiach frontowych odnosi się do zestawu zaplecza, które otrzymują podobny ruch dla swojej aplikacji. Innymi słowy jest to logiczne grupowanie wystąpień aplikacji na całym świecie, które otrzymują ten sam ruch i reagują z oczekiwanym zachowaniem. Te zaplecze są wdrażane w różnych regionach lub w tym samym regionie. Wszystkie zaplecze mogą być w trybie wdrażania Active/Active lub zdefiniowane jako konfiguracja aktywna/pasywna.

Pula wewnętrznej bazy danych definiuje, jak różne zaplecze powinny być oceniane za pomocą sond kondycji. Definiuje również, jak występuje równoważenie obciążenia między nimi.

### <a name="health-probes"></a>Sondy kondycji
Drzwi frontowe wysyłają okresowe żądania sondy HTTP/HTTPS do każdej skonfigurowanej wewnętrznej bazy danych. Żądania sondowania określają bliskość i kondycję każdej wewnętrznej bazy danych, aby zrównoważyć żądania użytkowników końcowych. Ustawienia sondy kondycji dla puli wewnętrznej bazy danych definiują sposób sondowania stanu kondycji zaplecza aplikacji. Następujące ustawienia są dostępne dla konfiguracji równoważenia obciążenia:

- **Ścieżka:** Adres URL używany dla żądań sondowania dla wszystkich zaplecza w puli wewnętrznej bazy danych. Na przykład jeśli jedna z twoich wewnętrznej bazy danych jest contoso-westus.azurewebsites.net, a ścieżka jest ustawiona na /probe/test.aspx, środowiska drzwi y\:frontowe, przy założeniu, że protokół jest ustawiony na HTTP, będą wysyłać żądania sondy kondycji do http //contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokół:** Określa, czy żądania sondy kondycji z drzwi frontowych do zaplecza z protokołem HTTP lub HTTPS.

- **Metoda**:Metoda HTTP, która ma być używana do wysyłania sond kondycji. Opcje obejmują GET lub HEAD (domyślnie).
    > [!NOTE]
    > W celu zmniejszenia obciążenia i kosztów zaplecza drzwiami frontowymi zaleca się użycie żądań HEAD dla sond kondycji.

- **Interwał (sekundy)**: Definiuje częstotliwość sond kondycji do zaplecza lub interwały, w których każde ze środowisk drzwi y frontowej wysyła sondę.

    >[!NOTE]
    >W przypadku szybszego pracy awaryjnej należy ustawić interwał na niższą wartość. Im niższa wartość, tym większa objętość sondy kondycji, jaką otrzymują twoje wewnętrznej bazy danych. Na przykład jeśli interwał jest ustawiony na 30 sekund z powiedzmy, 100 punktów pogłówkowych drzwi frontowych globalnie, każda wewnętrznej bazy danych otrzyma około 200 żądań sondy na minutę.

Aby uzyskać więcej informacji, zobacz [Sondy kondycji](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia
Ustawienia równoważenia obciążenia dla puli wewnętrznej bazy danych definiują sposób oceny sond kondycji. Te ustawienia określają, czy wewnętrznej bazy danych jest w dobrej kondycji lub w złej kondycji. Sprawdzają również, jak równoważyć obciążenie ruchu między różnymi zapleczem w puli wewnętrznej bazy danych. Następujące ustawienia są dostępne dla konfiguracji równoważenia obciążenia:

- **Rozmiar próbki**. Określa, ile próbek sond kondycji, które musimy wziąć pod uwagę do oceny kondycji wewnętrznej bazy danych.

- **Pomyślny rozmiar próbki**. Definiuje rozmiar próbki, jak wcześniej wspomniano, liczba pomyślnych próbek potrzebnych do wywołania wewnętrznej bazy danych w dobrej kondycji. Na przykład załóżmy, że interwał sondy kondycji drzwi y frontowej wynosi 30 sekund, rozmiar próbki wynosi 5, a pomyślny rozmiar próbki wynosi 3. Za każdym razem, gdy oceniamy sondy kondycji dla wewnętrznej bazy danych, patrzymy na pięć ostatnich próbek w ciągu 150 sekund (5 x 30). Co najmniej trzy pomyślne sondy są wymagane do deklarowania wewnętrznej bazy danych jako w dobrej kondycji.

- **Czułość opóźnienia (dodatkowe opóźnienie)**. Określa, czy drzwi frontowe mają wysyłać żądanie do zaplecza w zakresie czułości pomiaru opóźnienia, czy przesyłać dalej żądanie do najbliższego zaplecza.

Aby uzyskać więcej informacji, zobacz [Metoda routingu oparta na najmniejszym opóźnieniu](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie profilu drzwi przednich](quickstart-create-front-door.md)
- [Jak działają drzwi wejściowe](front-door-routing-architecture.md)