---
title: Pule zaplecza aplikacji i wewnętrznej bazy danych w usłudze Azure Service wejściu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jakie pul zaplecza i wewnętrznej bazy danych z przodu są drzwi biblioteki konfiguracją.
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
ms.openlocfilehash: 543e237a4a8390a8ebf74d0eb2a1f4be41dcd911
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60000593"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Pule zaplecza aplikacji i wewnętrznej bazy danych w usłudze Azure Service drzwi
W tym artykule opisano pojęcia dotyczące sposobu mapowania wdrożenia aplikacji z usługą Azure Service drzwiami frontowymi. Objaśniono także różne warunki w konfiguracji drzwiami frontowymi wokół zaplecza aplikacji.

## <a name="backends"></a>Zaplecza
Zapleczem jest taki sam jak wystąpienie wdrażania aplikacji w regionie. Usługa drzwiami frontowymi obsługuje platformy Azure i spoza platformy Azure, zaplecza aplikacji, więc region tylko nie jest ograniczona do regionów platformy Azure. Ponadto można go w lokalnym centrum danych lub wystąpienia aplikacji, w innej chmurze.

Zaplecza usługi drzwiami frontowymi odnoszą się do nazwy hosta lub publiczny adres IP aplikacji, która może obsługiwać żądania klientów. Zaplecza nie należy mylić z warstwy bazy danych, warstwy magazynowania i tak dalej. Zaplecza aplikacji powinny być traktowane jako publicznego punktu końcowego zaplecza aplikacji. Po dodaniu zaplecza w puli zaplecza drzwiami frontowymi, musisz również dodać następujące czynności:

- **Typ hosta zaplecza**. Typ zasobu, który chcesz dodać. Usługa drzwiami frontowymi obsługuje wykrywanie automatyczne z zaplecza aplikacji usługi app service, usługi w chmurze lub magazynu. Jeśli chcesz, aby inny zasób na platformie Azure lub nawet zaplecza spoza platformy Azure, wybierz opcję **hosta niestandardowego**.

    >[!IMPORTANT]
    >Podczas konfigurowania interfejsów API nie Sprawdź, czy wewnętrznej bazy danych jest niedostępna w środowiskach wejściu. Upewnij się, że drzwiami frontowymi może osiągnąć wewnętrzną bazą danych.

- **Nazwa hosta subskrypcji i zaplecze oparte na**. Jeśli jeszcze nie wybrano **hosta niestandardowego** dla wewnętrznej bazy danych typu hosta, należy wybrać wewnętrzną bazą danych, wybierając odpowiednią subskrypcję i odpowiednią nazwę hosta wewnętrznej bazy danych w interfejsie użytkownika.

- **Nagłówek hosta zaplecza**. Wartość nagłówka hosta wysyłana do wewnętrznej bazy danych dla każdego żądania. Aby uzyskać więcej informacji, zobacz [nagłówek hosta zaplecza](#hostheader).

- **Priorytet**. Przypisać priorytety do różnych zaplecza, podczas którego chcesz użyć podstawowa usługa zaplecza dla całego ruchu. Ponadto wykonywanie kopii zapasowych, jeśli główna osoba kontaktowa lub kopii zapasowej zaplecza są niedostępne. Aby uzyskać więcej informacji, zobacz [priorytet](front-door-routing-methods.md#priority).

- **Waga**. Przypisać wagi do różnych zaplecza do rozdzielenia ruchu między zbiór zapleczy, równomiernie lub zgodnie z współczynniki wagi. Aby uzyskać więcej informacji, zobacz [wagi](front-door-routing-methods.md#weighted).

### <a name = "hostheader"></a>Nagłówek hosta wewnętrznej bazy danych

Żądania przekazywane za wejściu do wewnętrznej bazy danych obejmują pole nagłówka hosta, który używa wewnętrznej bazy danych w celu pobrania zasobu docelowego. Wartość dla tego pola zazwyczaj pochodzi z identyfikatora URI wewnętrznej bazy danych, a także ma hosta i portu.

Na przykład żądania dotyczącego www\.contoso.com, będzie miał www nagłówka hosta\.contoso.com. Konfigurowanie zaplecza za pomocą witryny Azure portal, wartością domyślną dla tego pola czy nazwy hosta w wewnętrznej bazie danych. Jeśli zaplecza jest contoso-westus.azurewebsites.net w witrynie Azure portal wypełnianie automatycznie wartość nagłówka hosta w wewnętrznej bazie danych będzie westus.azurewebsites.net firmy contoso. Jednak jeśli używasz usługi Azure Resource Manager lub innej metody bez jawnego ustawienia tego pola, usługi drzwiami frontowymi wyśle przychodzących nazwy hosta jako wartość dla nagłówka hosta. Jeśli żądanie zostało wystosowane www\.contoso.com i zapleczu westus.azurewebsites.net firmy contoso, zawierającej pole nagłówka pusty, usługa drzwiami frontowymi ustawi nagłówek hosta jako www\.contoso.com.

Większość zaplecza aplikacji (aplikacje internetowe platformy Azure, usługi Blob storage i usług w chmurze) wymagają nagłówek hosta był zgodny z domeną wewnętrznej bazy danych. Jednak hosta serwera sieci Web, który kieruje do swojej wewnętrznej bazy danych będzie używać innej nazwy hosta, takie jak www\.contoso.azurefd.net.

Jeśli zaplecza wymaga nagłówka hosta, która jest zgodna z nazwą hosta wewnętrznej bazy danych, upewnij się, że nagłówek hosta wewnętrznej bazy danych obejmuje wewnętrznej bazy danych dla nazwy hosta.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Konfigurowanie zaplecza nagłówek hosta dla wewnętrznej bazy danych

Aby skonfigurować **nagłówek hosta zaplecza** pole zaplecza w sekcji puli zaplecza:

1. Otwórz zasób usługi drzwi wejściowe, a następnie wybierz pulę zaplecza za pomocą wewnętrznej bazy danych, aby skonfigurować.

2. Dodaj zaplecze, jeśli nie zostało to zrobione, lub edytować istniejący.

3. Ustaw pole nagłówka hosta zaplecza niestandardowej wartości lub pozostaw to pole puste. Nazwa hosta dla żądania przychodzącego będzie służyć jako wartość nagłówka hosta.

## <a name="backend-pools"></a>Pule zaplecza
Puli zaplecza z przodu drzwi odnosi się do zestawu zapleczy, aby utworzyć, odbierać podobne dane dla swoich aplikacji. Innymi słowy to logiczna Grupa wystąpień aplikacji, na całym świecie, odbierać tych samych danych, która odpowiada przy użyciu oczekiwane zachowanie. Te zaplecza są wdrażane w różnych regionach lub w ramach tego samego regionu. Wszystkie zaplecza może być w trybie wdrażania aktywny/aktywny lub co to jest zdefiniowany jako aktywny/pasywny konfiguracji.

Pula zaplecza definiuje, jak za pomocą sondy kondycji, w którym powinna być oceniana różnych zaplecza. Definiuje również, jak równoważenia obciążenia zachodzi między nimi.

### <a name="health-probes"></a>Sondy kondycji
Usługa drzwiami frontowymi wysyła okresowe żądania sondowania HTTP/HTTPS do każdego skonfigurowanego zaplecza. Żądania sondowania stwierdzić, że odległości i kondycję każdego zaplecza, aby załadować saldo żądań użytkowników końcowych. Ustawienia sondy kondycji dla puli zaplecza definiują, jak firma Microsoft sondowania stanu kondycji zaplecza aplikacji. Następujące ustawienia są dostępne dla konfiguracji równoważenia obciążenia:

- **Ścieżka**. Adres URL do badania żądań dla wszystkich zaplecza w puli zaplecza. Na przykład, jeśli ścieżka jest równa /probe/test.aspx jest jeden z zaplecza westus.azurewebsites.net firmy contoso, następnie środowisk usługi drzwiami frontowymi, zakładając, że ustawiono protokołu HTTP, będzie wysyłać żądań sondy kondycji HTTP\:/ / contoso-westus.azurewebsites.net/probe/test.aspx.

- **Protokół**. Określa, czy wysyłać żądań sondy kondycji usługi wejściu do zaplecza przy użyciu protokołu HTTP lub HTTPS.

- **Interwał (w sekundach)**. Określa częstotliwość sondy kondycji do zaplecza lub odstępach czasu, w których każdego z tych środowisk drzwiami frontowymi wysyła sondę.

    >[!NOTE]
    >Dla szybszego przejścia w tryb failover należy ustawić interwał niższą wartość. Im niższa wartość, im większy wolumin sondy kondycji otrzymują zaplecza. Na przykład, jeśli interwał wynosi 30 sekund przy użyciu 90 drzwi wejściowe lub w środowiskach POP globalnie, każde zaplecze otrzyma o 3 – 5 sondowania żądań na sekundę.

Aby uzyskać więcej informacji, zobacz [sondy kondycji](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Ustawienia równoważenia obciążenia
Ustawienia równoważenia obciążenia dla puli zaplecza definiują, jak możemy obliczyć sond kondycji. Te ustawienia określają, czy wewnętrznej bazy danych jest w dobrej kondycji lub w złej kondycji. Także sprawdzić sposób Równoważenie obciążenia ruchu między różnych zaplecza w puli zaplecza. Następujące ustawienia są dostępne dla konfiguracji równoważenia obciążenia:

- **Przykładowy rozmiar**. Określa, jak wiele przykładów sond kondycji należy wziąć pod uwagę oceny kondycji wewnętrznej bazy danych.

- **Rozmiar próbki pomyślne**. Określa rozmiar próbki, jak wcześniej wspomniano, liczbę pomyślnych próbek potrzebne do wywoływania zaplecza w dobrej kondycji. Załóżmy na przykład, interwał sondy kondycji drzwiami frontowymi jest 30 sekund, rozmiar próbki jest 5 i rozmiar próbki pomyślne to 3. Zawsze możemy obliczyć kondycji sondy zaplecza, przyjrzymy się pięć ostatnich przykłady ponad 150 sekund (5 x 30). Co najmniej trzech pomyślne sondy są wymagane do deklarowania wewnętrznej bazy danych jako w dobrej kondycji.

- **Czułość opóźnienia (dodatkowe opóźnienie)**. Określa, czy chcesz drzwiami frontowymi wysyłać żądania do zaplecza w zakresie poufności pomiaru opóźnienia lub przesłania żądania do najbliższego wewnętrznej bazy danych.

Aby uzyskać więcej informacji, zobacz [najmniejszego opóźnienia na podstawie metody routingu opartego na](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Kolejne kroki

- [Utwórz profil drzwi](quickstart-create-front-door.md)
- [Jak działa drzwi](front-door-routing-architecture.md)