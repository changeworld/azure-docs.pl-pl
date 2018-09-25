---
title: Metryki niestandardowe w usłudze Azure Monitor
description: Więcej informacji na temat metryki niestandardowe w usłudze Azure Monitor i jak są modelowane.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: c136772e27dab014c22234f1ef1d2baddd2ffe58
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978084"
---
# <a name="custom-metrics-in-azure-monitor"></a>Metryki niestandardowe w usłudze Azure Monitor

Podczas wdrażania zasobów i aplikacji na platformie Azure, należy rozpocząć zbieranie danych telemetrycznych w celu uzyskania szczegółowych informacji o wydajności i kondycji. Dzięki systemowi Azure niektóre metryki dostępne dla Ciebie out-of--box podczas wdrażania zasobów. Są one nazywane metryki standard lub platformy. Jednak te metryki są ograniczony charakter. Możesz zbierać niektóre niestandardowe wskaźników lub metryki specyficzne dla firmy, aby zapewnić lepszy wgląd w dane.
Te metryki "niestandardowy" mogą być zbierane za pomocą telemetrii aplikacji, agenta uruchomionego na zasoby platformy Azure lub nawet poza — w system monitorowania i przesłane bezpośrednio do usługi Azure Monitor. Po opublikowaniu do usługi Azure Monitor możesz przeglądać, zapytań oraz alert po wystąpieniu metryki niestandardowe dla zasobów platformy Azure i aplikacji obok siebie standardowych metryk emitowane przez platformę Azure.

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych
Metryki niestandardowe mogą być wysyłane do usługi Azure Monitor za pomocą różnych metod.
- Instrumentuj swoją aplikację przy użyciu zestawu SDK usługi Application Insights i wysyłania niestandardowych danych telemetrycznych do usługi Azure Monitor 
- Instalacja rozszerzenia diagnostyki Windows na Twoje [maszyny Wirtualnej platformy Azure](metrics-store-custom-guestos-resource-manager-vm.md), [zestawu skalowania maszyn wirtualnych](metrics-store-custom-guestos-resource-manager-vmss.md), [klasyczna maszyna wirtualna](metrics-store-custom-guestos-classic-vm.md), lub [klasycznej usługi w chmurze,](metrics-store-custom-guestos-classic-cloud-service.md)i wysłać liczników wydajności do usługi Azure Monitor 
- Zainstaluj [agenta InfluxDB Telegraf](metrics-store-custom-linux-telegraf.md) na maszynie Wirtualnej systemu Linux platformy Azure i metryk wysyłania przy użyciu wtyczki dane wyjściowe usługi Azure Monitor
- Wysyłanie metryk niestandardowych [bezpośrednio do interfejsu API REST usługi Azure Monitor](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Metryki niestandardowe jest wysyłany do usługi Azure Monitor, każdy danych punktu (lub wartość) zgłosił musi zawierać następujące informacje:

### <a name="authentication"></a>Authentication
Do przesyłania metryki niestandardowe do usługi Azure Monitor jednostki przesyłania Metryka musi mieć prawidłowy token usługi Azure Active Directory w nagłówku "Bearer" żądania. Istnieją na kilka sposobów obsługiwane można uzyskać tokenu elementu nośnego prawidłowy:
1. [MSI (tożsamość usługi zarządzanej)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) — zapewnia tożsamość do samego zasobu platformy Azure (np. maszyna wirtualna). MSI został zaprojektowany do zasobom uprawnienia do wykonywania określonych operacji — na przykład, co zasób emitują metryki o sobie samym. Zasób (lub jego MSI) można udzielić uprawnienia "Monitorowanie metryk Publisher" inny zasób, umożliwiając w ten sposób MSI emitować metryki dla innych zasobów, jak również.
2. [Nazwa główna usługi AAD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) — scenariusz, w tym miejscu jest aplikacja usługi AAD (usługa) można przypisać uprawnienia do emitowania metryk dotyczących zasobów platformy Azure.
W celu uwierzytelnienia żądania usługi Azure Monitor sprawdza poprawność tokenu aplikacji przy użyciu usługi AAD kluczy publicznych. Istniejącą rolę "Monitorowanie metryk Publisher" ma już to uprawnienie, który jest dostępny w witrynie Azure portal. Jednostki usługi, w zależności od tego, jakie zasoby będą do niej emitowania metryki niestandardowe, mogą być podawane w roli "Monitorowanie metryk Publisher" w zakresie wymaganych (subskrypcji, grupy zasobów lub określonego zasobu).

> [!NOTE]
> Podczas żądania tokenu usługi AAD, aby emitować metryki niestandardowe upewnij się, token jest wymagany dla odbiorców lub zasobu https://monitoring.azure.com/ (Pamiętaj o uwzględnieniu "/")

### <a name="subject"></a>Temat
Ta właściwość umożliwia przechwytywanie identyfikator zasobu platformy Azure, w którym jest zgłaszany Metryka niestandardowa. Te informacje będą zakodowane w adresie URL wywołanie interfejsu API. Każdy interfejs API może przesłać tylko wartości metryk do jednego zasobu platformy Azure.

> [!NOTE]
> Nie można wyemitować metryki niestandardowe względem Identyfikatora zasobu grupy zasobów lub subskrypcji.
>
>

### <a name="region"></a>Region
Ta właściwość umożliwia przechwytywanie jakie regionie platformy Azure, zasób, który emitujesz metryki został wdrożony. Metryki musi wysyłanego do tej samej usługi Azure Monitor, regionalne punktu końcowego jako region zasobu jest wdrożony w. Na przykład metryki niestandardowe dla maszyny Wirtualnej są wdrażane w regionie zachodnie stany USA musi przesyłane do punktu końcowego WestUS regionalnej usługi Azure Monitor. Informacji o regionie również jest zakodowane w adresie URL wywołania interfejsu API.

> [!NOTE]
> W publicznej wersji zapoznawczej metryk niestandardowych jest dostępne tylko w regionach platformy Azure przez. Listę obsługiwanych regionów jest opisane w dalszej części tego artykułu.
>
>

### <a name="timestamp"></a>Znacznik czasu
Każdy punkt danych wysyłanych do usługi Azure Monitor musi być oznaczony przez sygnaturę czasową. Sygnatura czasowa przechwytuje daty/godziny, w którym wartość metryki został mierzony zbierane. Usługa Azure Monitor będzie akceptować dane metryk z sygnaturami czasowymi, o ile to 20 minut w przeszłości i zależnie od 5 minut w przyszłości.

### <a name="namespace"></a>Przestrzeń nazw
Przestrzenie nazw służą do kategoryzowania lub grupowanie podobnych miernikach. Przestrzenie nazw umożliwiają uzyskania izolacji między grupami miar, które może zbierać informacje na temat różnych technologii lub wskaźniki wydajności. Na przykład można mieć przestrzeń nazwy wywołaną *ContosoMemoryMetrics* oznacza to ścieżki używanej pamięci Użyj metryk, które profilować aplikację i innej przestrzeni nazw o nazwie *ContosoAppTransaction* który śledzi działanie wszystkich metryki dotyczące transakcji użytkownika w aplikacji.

### <a name="name"></a>Name (Nazwa)
Nazwa metryki, który jest zgłaszany. Nazwa jest zazwyczaj opisową, aby ułatwić zidentyfikowanie mierzoną. Na przykład metrykę, która jest pomiar liczby bajtów pamięci wykorzystywane na danej maszynie Wirtualnej może mieć nazwy metryki, takie jak "Pamięci bajty w użyciu".

### <a name="dimension-keys"></a>Klucze wymiaru
Wymiar jest parą klucz/wartość, która pomaga, opisz dodatkową charakterystykę metryki są zbierane. Dodatkowe charakterystyki umożliwiają zbieranie dowiedzieć się więcej o metrykę, która umożliwia bardziej szczegółowych informacji. Na przykład metryka "Pamięci bajty w użyciu" może mieć klucza wymiaru o nazwie "Procesu", która przechwytuje bajtów ile pamięci zużywa każdy proces na maszynie Wirtualnej. Dzięki temu można filtrować metryki, aby zobaczyć, ile dotyczące pamięci procesy używają lub do identyfikacji procesów 5 pierwszych przez użycie pamięci.
Wszystkie niestandardowe metryki może mieć maksymalnie 10 wymiarów.

### <a name="dimension-values"></a>Wartości wymiaru
Podczas raportowania metryk punktu danych dla każdego klucza wymiaru na metryce zgłaszane, istnieje odpowiadająca wartość w wymiarze. Jeśli na przykład chcesz zgłosić pamięci używanej przez ContosoApp na maszynie Wirtualnej:

* Nazwa metryki będą *bajtów pamięci w użyciu*
* Byłoby klucza wymiaru *procesu*
* Wartość wymiaru będzie *ContosoApp.exe*

Podczas publikowania wartość metryki, można określić tylko jeden wymiar wartość dla klucza wymiaru. Jeśli zbierasz wiele procesów w tej samej użycia pamięci na maszynie Wirtualnej można zgłaszać wiele metryk wartości tej sygnatury czasowej. Każda wartość metryki określić wartość innego wymiaru klucza wymiaru procesu.

### <a name="metric-values"></a>Wartości metryk
Usługa Azure Monitor są przechowywane wszystkie metryki w odstępach czasu z dokładnością do jednej minuty. Rozumiemy, że metryki może być konieczne będą próbkowane wiele razy (np.) Użycie procesora CPU) lub mierzony w przypadku wielu zdarzeń dyskretnych (np.) Zaloguj się z opóźnieniami transakcji) danego minuty. Aby ograniczyć liczbę nieprzetworzonych wartości, które mają do emisji i Płać w usłudze Azure Monitor, możesz lokalnie wstępnej agregacji i emitować wartości:

* Minimalna: Minimalna zaobserwowane Wykorzystaj wszystkie przykłady/pomiary ciągu minuty
* Maks.: Maksimum zaobserwowane WE Wykorzystaj wszystkie przykłady/pomiary ciągu minuty
* Suma: Suma wszystkich wartości obserwowanych z wszystkie przykładowe/pomiary ciągu minuty
* Liczba: Liczba próbek/pomiarów ciągu minuty

Na przykład, gdyby transakcji 4 logowania do aplikacji podczas podanej minutę i wynikowe są mierzone opóźnienia dla każdego:

|Transakcja 1|Transakcji 2|Transakcja 3|Transakcja 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Wynikowy publikacji metryk do usługi Azure Monitor będzie następująca:
* Minimalna: 4
* Maksymalna liczba: 16
* Sum: 40
* Liczba: 4

Jeśli aplikacja nie będzie mógł wstępnej agregacji lokalnie, musi wysyłać właściwość każdego przykładowe discrete lub zdarzenia natychmiast po kolekcji może emitować wartości pierwotnych miary.
Na przykład każdorazowo transakcji logowania wystąpił na swojej aplikacji za pomocą pojedynczego pomiaru publikował metrykę do usługi Azure Monitor. Dlatego dla logowania w transakcji, która miała 12 ms, a następnie metryki będą publikacji:
* Minimalna: 12
* Maksymalna liczba: 12
* Sum: 12
* Liczby: 1

Ten proces umożliwia emitowanie wiele wartości dla tego samego metryki + kombinacja wymiarów minucie danego. Usługa Azure Monitor zostanie następnie pobierają wszystkie nieprzetworzone wartości emitowanych przez dany minutę i agregowanie ich ze sobą.

### <a name="sample-custom-metric-publication"></a>Przykładowe niestandardowe metryki publikacji
W poniższym przykładzie utworzysz Metryka niestandardowa o nazwie "Pamięci bajty w użyciu", w obszarze metryki przestrzeni nazw "Profil pamięci" dla maszyny wirtualnej. Metryka ma tylko jeden wymiar o nazwie "Procesu". Dla podanej sygnaturze czasowej firma Microsoft jest emitowania wartości metryk dla dwóch różnych procesów:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> Application Insights, przez rozszerzenie Diagnostyka Azure Windows i agent InfluxData Telegraf są już skonfigurowane do emisji wartości metryki względem właściwego punktu końcowego regionalnych i przenoszenia wszystkich powyższych właściwości każdego emisji.
>
>

## <a name="custom-metric-definitions"></a>Niestandardowe definicje metryk
Nie ma potrzeby wstępnie zdefiniować Metryka niestandardowa w usłudze Azure Monitor, zanim go jest emitowane. Ponieważ każdy punkt danych metryk opublikowane zawiera przestrzeń nazw, nazwa i informacje o wymiarach, gdy Metryka niestandardowa jest emitowany do usługi Azure Monitor po raz pierwszy definicja metryki jest tworzona automatycznie. Ta definicja metryki się wykrywalny na żaden zasób, który Metryka został wyemitowany względem za pośrednictwem definicji metryk.

> [!NOTE]
> Usługa Azure Monitor nie obsługuje jeszcze Definiowanie "Jednostki" dla metryki niestandardowe.

## <a name="using-custom-metrics"></a>Za pomocą metryk niestandardowych
Po przesłaniu metryki niestandardowe do usługi Azure Monitor możesz przeglądać je za pomocą witryny Azure portal, wyszukiwać w nich za pośrednictwem interfejsów API REST usługi Azure Monitor lub tworzenie alertów dotyczących ich, dzięki czemu użytkownik może zostać powiadomiony, gdy są spełnione określone warunki.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Przeglądaj swoje metryki niestandardowe przy użyciu witryny Azure portal
1.  Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.  Wybierz bloku Monitor
3.  Kliknij pozycję metryki
4.  Wybierz zasób, który ma emitowane metryk niestandardowych
5.  Wybierz przestrzeń nazw metryki dla usługi metryki niestandardowe
6.  Wybieranie metryk niestandardowych

## <a name="supported-regions"></a>Obsługiwane regiony
W publicznej wersji zapoznawczej możliwość publikowania metryki niestandardowe jest dostępna tylko w regionach platformy Azure przez. Oznacza to, że metryki mogą być publikowane tylko dla zasobów w jednym z obsługiwanych regionów. W poniższej tabeli wymieniono zestaw obsługiwanych regionów platformy Azure dla metryki niestandardowe i odpowiedniego punktu końcowego, które powinny być publikowane metryki dla zasobów w tych regionach.

|Region Azure|Prefiks regionalnych punktu końcowego|
|---|---|
|Wschodnie stany USA|https://eastus.monitoring.azure.com/|
|Środkowo-południowe stany USA|https://southcentralus.monitoring.azure.com/|
|Środkowo-zachodnie stany USA|https://westcentralus.monitoring.azure.com/|
|Zachodnie stany USA 2|https://westus2.monitoring.azure.com/|
|Azja Południowo-Wschodnia|https://southeastasia.monitoring.azure.com/|
|Europa Północna|https://northeurope.monitoring.azure.com/|
|Europa Zachodnia|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia
Usługa Azure Monitor nakłada następujące limity użycia dla metryk niestandardowych.

|Kategoria|Limit|
|---|---|
|Aktywny czas serii/subskrypcje/region|50,000|
|Klucze wymiarów na metrykę|10|
|Długość ciągu dla metryki przestrzeni nazw, nazwami metryk, wymiaru kluczy i wartości wymiaru|256 znaków|
Szeregi czasowe active jest zdefiniowany jako dowolną unikatową kombinację metryki, klucza wymiaru, wartość wymiaru, który miał wartości metryk, opublikowane w ciągu ostatnich 12 godzin.

## <a name="next-steps"></a>Kolejne kroki
Metryki niestandardowe z różnymi usługami 
 - [Maszyna wirtualna](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Zestaw skalowania maszyn wirtualnych](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Maszyna wirtualna (klasyczna)](metrics-store-custom-guestos-classic-vm.md)
 - [Przy użyciu Telegraf agenta maszyny wirtualnej systemu Linux](metrics-store-custom-linux-telegraf.md)
 - [Interfejs API REST](metrics-store-custom-rest-api.md)
 - [Usługa w chmurze (klasyczna)](metrics-store-custom-guestos-classic-cloud-service.md)
 