---
title: Metryki niestandardowe w usłudze Azure Monitor
description: Więcej informacji na temat metryki niestandardowe w usłudze Azure Monitor i jak są modelowane.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8602027431fdf2c1378834419977606bab5c6921
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254059"
---
# <a name="custom-metrics-in-azure-monitor"></a>Metryki niestandardowe w usłudze Azure Monitor

Podczas wdrażania zasobów i aplikacji na platformie Azure, należy rozpocząć zbieranie danych telemetrycznych w celu uzyskania szczegółowych informacji o wydajności i kondycji. Dzięki systemowi Azure niektóre metryki dostępne dla Ciebie gotowe. Te metryki są nazywane standard lub platformy. Jednak w przypadku ograniczony charakter. Można zbierać niektóre niestandardowe wskaźników lub metryki specyficzne dla firmy, aby zapewnić lepszy wgląd w dane.
Te **niestandardowe** metryki mogą być zbierane za pomocą telemetrii aplikacji, agenta, który działa na zasoby platformy Azure lub nawet zewnętrznego monitorowania systemu i przesłane bezpośrednio do usługi Azure Monitor. Po są publikowane do usługi Azure Monitor możesz przeglądać, zapytania i alert po wystąpieniu metryki niestandardowe dla zasobów platformy Azure i aplikacji równolegle standardowych metryk emitowane przez platformę Azure.

## <a name="send-custom-metrics"></a>Wysyłanie metryk niestandardowych
Metryki niestandardowe mogą być wysyłane do usługi Azure Monitor za pomocą kilku metod:
- Instrumentuj swoją aplikację przy użyciu zestawu SDK usługi Azure Application Insights, a następnie wyślij niestandardowych danych telemetrycznych do usługi Azure Monitor. 
- Zainstaluj rozszerzenie Diagnostyka Azure Windows (WAD) na usługi [maszyny Wirtualnej platformy Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [zestawu skalowania maszyn wirtualnych](collect-custom-metrics-guestos-resource-manager-vmss.md), [klasyczna maszyna wirtualna](collect-custom-metrics-guestos-vm-classic.md), lub [klasycznej usługi w chmurze,](collect-custom-metrics-guestos-vm-cloud-service-classic.md) i wysłać liczników wydajności do usługi Azure Monitor. 
- Zainstaluj [agenta InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) na maszynie Wirtualnej systemu Linux platformy Azure i wysyłanie metryk za pomocą usługi Azure Monitor danych wyjściowych wtyczki.
- Wysyłanie metryk niestandardowych [bezpośrednio do interfejsu API REST usługi Azure Monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Podczas wysyłania metryki niestandardowe do usługi Azure Monitor, każdy punkt danych lub raportowana, musi zawierać następujące informacje.

### <a name="authentication"></a>Authentication
Aby przesłać metryki niestandardowe do usługi Azure Monitor, jednostka, która przesyła Metryka wymaga prawidłowego tokenu usługi Azure Active Directory (Azure AD) w **elementu nośnego** nagłówku żądania. Istnieją na kilka sposobów obsługiwane można uzyskać tokenu elementu nośnego prawidłowy:
1. [Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Zapewnia tożsamości do zasobów platformy Azure, np. maszyna wirtualna. Tożsamość usługi zarządzanej (MSI) jest przeznaczona do zasobom uprawnienia do wykonywania określonych operacji. Przykład zezwala na zasób, aby emitować metryki o sobie samym. Można udzielić zasobu lub jego MSI **wydawcy metryki monitorowania** uprawnienia do innego zasobu. Posiadając to uprawnienie MSI może emitować metryki dla innych zasobów, jak również.
2. [Nazwa główna usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). W tym scenariuszu, aplikacji usługi Azure AD lub usługi można przypisać uprawnienia do emitowania metryk dotyczących zasobów platformy Azure.
W celu uwierzytelnienia żądania usługi Azure Monitor sprawdza poprawność tokenu aplikacji przy użyciu kluczy publicznych do usługi Azure AD. Istniejące **wydawcy metryki monitorowania** Rola już ma to uprawnienie. Jest ona dostępna w witrynie Azure portal. Jednostki usługi, w zależności od tego, jakie zasoby emituje metryki niestandardowe, można podać **wydawcy metryki monitorowania** rolę w zakresie wymagane. Przykładami są subskrypcji, grupy zasobów lub określonego zasobu.

> [!NOTE]  
> W przypadku żądania tokenu usługi Azure AD, aby emitować metryki niestandardowe, upewnij się, że odbiorców lub token jest wymagany dla zasobów https://monitoring.azure.com/. Pamiętaj o uwzględnieniu "/".

### <a name="subject"></a>Subject
Ta właściwość umożliwia przechwytywanie identyfikator zasobu platformy Azure, w którym jest zgłaszany Metryka niestandardowa. Te informacje będą zakodowane w adresie URL wywołanie interfejsu API. Każdy interfejs API może przesłać tylko wartości metryk do jednego zasobu platformy Azure.

> [!NOTE]  
> Nie można wyemitować metryki niestandardowe względem Identyfikatora zasobu grupy zasobów lub subskrypcji.
>
>

### <a name="region"></a>Region
Ta właściwość umożliwia przechwytywanie jakie regionie platformy Azure, zasób, który w przypadku emitowania metryki dla został wdrożony. Metryki musi wysyłanego do tej samej usługi Azure Monitor, regionalne punktu końcowego jako region zasobu jest wdrożony w. Na przykład metryki niestandardowe dla maszyny Wirtualnej wdrożonej w regionie zachodnie stany USA muszą wysyłane do punktu końcowego usługi Azure Monitor regionalnych WestUS. Informacji o regionie również jest zakodowane w adresie URL wywołania interfejsu API.

> [!NOTE]  
> W publicznej wersji zapoznawczej metryki niestandardowe są dostępne tylko w regionach platformy Azure przez. Listę obsługiwanych regionów jest opisane w dalszej części tego artykułu.
>
>

### <a name="timestamp"></a>Znacznik czasu
Każdy punkt danych wysyłanych do usługi Azure Monitor musi być oznaczony przez sygnaturę czasową. Sygnatura czasowa przechwytuje daty/godziny, w którym wartość metryki jest mierzony lub zebrane. Usługa Azure Monitor akceptuje dane metryk z sygnaturami czasowymi, o ile to 20 minut w przeszłości i 5 minut w przyszłości. Sygnatura czasowa musi być w formacie ISO 8601.

### <a name="namespace"></a>Przestrzeń nazw
Przestrzenie nazw służą do kategoryzowania lub grupowanie podobnych miernikach. Za pomocą przestrzeni nazw, można osiągnąć izolacji między grupami miar, które może zbierać informacje na temat różnych technologii lub wskaźniki wydajności. Na przykład, Niewykluczone, że przestrzeń nazwy wywołaną **ContosoMemoryMetrics** który śledzi metryki wykorzystania pamięci, która profilu aplikacji. Innej przestrzeni nazw o nazwie **ContosoAppTransaction** może śledzić wszystkie metryki dotyczące transakcji użytkownika w aplikacji.

### <a name="name"></a>Name (Nazwa)
**Nazwa** nazywa się metrykę, która jest zgłaszany. Zazwyczaj nazwa jest opisowy ułatwiają określenie, co jest mierzone. Przykładem jest metrykę, która mierzy liczbę bajtów pamięci, używanych na danej maszynie Wirtualnej. Może mieć nazwy metryki, takie jak **pamięci bajty w użyciu**.

### <a name="dimension-keys"></a>Klucze wymiaru
Wymiar jest parą klucza lub wartości, która pomaga, opisz dodatkową charakterystykę metryki są zbierane. Za pomocą dodatkowych właściwości, można zebrać więcej informacji na temat metryki, co pozwala uzyskać lepszy wgląd w dane. Na przykład **pamięci bajty w użyciu** Metryka może mieć klucza wymiaru o nazwie **procesu** który przechwytuje zużywa każdy proces na maszynie Wirtualnej Określanie liczby bajtów pamięci. Za pomocą tego klucza, możesz filtrować metryki, aby zobaczyć, ile pamięci, użyj określonych procesów lub do identyfikowania najważniejszych procesów pięć przez użycie pamięci.
Wymiary są opcjonalne, nie wszystkie metryki może mieć wymiary. Metryka niestandardowa może zawierać maksymalnie 10 wymiarów.

### <a name="dimension-values"></a>Wartości wymiaru
Podczas zgłaszania punkt danych metryk dla każdego klucza wymiaru na metryce zgłaszane, istnieje odpowiadająca wartość w wymiarze. Na przykład możesz zgłosić pamięci używanej przez ContosoApp na maszynie Wirtualnej:

* Nazwa metryki będą **bajtów pamięci w użyciu**.
* Byłoby klucza wymiaru **procesu**.
* Wartość wymiaru będzie **ContosoApp.exe**.

Podczas publikowania wartość metryki, można określić tylko jeden wymiar wartość dla klucza wymiaru. Jeśli zbierasz wiele procesów w tej samej użycia pamięci na maszynie Wirtualnej, możesz zgłaszać wiele metryk wartości tej sygnatury czasowej. Każda wartość metryki określić wartości innego wymiaru **procesu** klucza wymiaru.
Wymiary są opcjonalne, nie wszystkie metryki może mieć wymiary. Jeśli wpis metryki definiuje klucze wymiarów, odpowiadające im wartości wymiaru są obowiązkowe.

### <a name="metric-values"></a>Wartości metryk
Usługa Azure Monitor są przechowywane wszystkie metryki w odstępach czasu z dokładnością do jednej minuty. Rozumiemy, że minucie danego Metryka może być konieczne będą próbkowane kilka razy. Przykładem jest użycie procesora CPU. Lub może być konieczne mierzone w przypadku wielu zdarzeń dyskretnych. Przykładem jest opóźnienia rejestrowania transakcji. Aby ograniczyć liczbę nieprzetworzonych wartości, które mają do emisji i Płać w usłudze Azure Monitor, możesz lokalnie wstępnej agregacji i emitować wartości:

* **Min**: Minimalna odczytaną wartość ze wszystkich przykładów i pomiary w ciągu minuty.
* **Maksymalna liczba**: Maksimum obserwowane wartość ze wszystkich przykładów i pomiary w ciągu minuty.
* **Suma**: Podsumowanie wszystkie wartości obserwowanych z przykładów i pomiary w ciągu minuty.
* **Liczba**: Liczba próbek i pomiarów dokonanych w ciągu minuty.

Na przykład, gdyby transakcji 4 logowania do aplikacji podczas biorąc pod uwagę na minutę, wynikowy mierzony opóźnienia dla każdego może wyglądać następująco:

|Transakcja 1|Transakcji 2|Transakcja 3|Transakcja 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Wynikowy publikacji metryk do usługi Azure Monitor będzie następujący:
* Minimalna: 4
* Maks.: 16
* Suma: 40
* Liczba: 4

Jeśli aplikacja nie będzie mógł wstępnej agregacji lokalnie, musi wysyłać właściwość każdego przykładowe discrete lub zdarzenia natychmiast po kolekcji może emitować wartości pierwotnych miary. Występuje na przykład każdorazowo transakcji logowania w aplikacji, możesz opublikować metryki usługi Azure Monitor za pomocą pojedynczego pomiaru. Więc dla logowania w transakcji, która miała 12 ms, metryki publikacji będzie w następujący sposób:
* Minimalna: 12
* Maks.: 12
* Suma: 12
* Liczba: 1

Za pomocą tego procesu może emitować wiele wartości dla tego samego metryki oraz kombinacja wymiarów minucie danego. Usługa Azure Monitor następnie pobiera wszystkie nieprzetworzone wartości emitowanych przez dany minutę i agreguje je ze sobą.

### <a name="sample-custom-metric-publication"></a>Przykładowe niestandardowe metryki publikacji
W poniższym przykładzie utworzysz Metryka niestandardowa o nazwie **bajtów pamięci w użyciu** w przestrzeni nazw metryki **pamięci profilu** dla maszyny wirtualnej. Metryka ma tylko jeden wymiar o nazwie **procesu**. Dla podanej sygnaturze czasowej możemy emitują metryki wartości dla dwóch różnych procesów:

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
> Application Insights, rozszerzenie diagnostyki i agent InfluxData Telegraf są już skonfigurowane do emisji wartości metryki względem właściwego punktu końcowego regionalnych i przenoszenia wszystkich poprzednich właściwości każdego emisji.
>
>

## <a name="custom-metric-definitions"></a>Niestandardowe definicje metryk
Nie ma potrzeby celu wstępnego zdefiniowania Metryka niestandardowa w usłudze Azure Monitor, zanim go jest emitowane. Każdy punkt danych metryk opublikowane zawiera przestrzeń nazw, nazwa i informacje o wymiarach. Dlatego po raz pierwszy Metryka niestandardowa jest emitowany do usługi Azure Monitor, definicja metryki jest tworzona automatycznie. Ta definicja metryki się wykrywalny na żaden zasób, który metryka jest emitowane względem za pośrednictwem definicji metryk.

> [!NOTE]  
> Usługa Azure Monitor nie obsługuje jeszcze definiowanie **jednostek** metryki niestandardowe.

## <a name="using-custom-metrics"></a>Za pomocą metryk niestandardowych
Po metryki niestandardowe są przesyłane do usługi Azure Monitor, można przeglądać je za pomocą witryny Azure portal i wyszukiwać w nich za pośrednictwem interfejsów API REST usługi Azure Monitor. Można również tworzyć alerty je, aby otrzymywać powiadomienia, gdy zostaną spełnione określone warunki.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Przeglądaj swoje metryki niestandardowe przy użyciu witryny Azure portal
1.  Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.  Wybierz **Monitor** okienka.
3.  Wybierz pozycję **Metryki**.
4.  Wybierz zasób, który został wyemitowany metryk niestandardowych.
5.  Wybierz przestrzeń nazw metryki dla usługi metryki niestandardowe.
6.  Wybierz metrykę, niestandardowe.

## <a name="supported-regions"></a>Obsługiwane regiony
W publicznej wersji zapoznawczej umożliwia publikowanie metryk niestandardowych jest dostępne tylko w regionach platformy Azure przez. To ograniczenie oznacza, że metryki mogą być publikowane tylko za zasoby w obsługiwanych regionach. Poniższa lista zawiera zbiór obsługiwane regiony platformy Azure dla metryki niestandardowe. Zawiera również listę odpowiednich punktów końcowych, które powinny być publikowane metryki dla zasobów w tych regionach:

|Region platformy Azure|Prefiks regionalnych punktu końcowego|
|---|---|
|Wschodnie stany USA| protokół https:\//eastus.monitoring.azure.com/ |
|Środkowo-południowe stany USA| https:\//southcentralus.monitoring.azure.com/ |
|Środkowo-zachodnie stany USA| https:\//westcentralus.monitoring.azure.com/ |
|Zachodnie stany USA 2| protokół https:\//westus2.monitoring.azure.com/ |
|Azja Południowo-Wschodnia| protokół https:\//southeastasia.monitoring.azure.com/ |
|Europa Północna| protokół https:\//northeurope.monitoring.azure.com/ |
|Europa Zachodnia| protokół https:\//westeurope.monitoring.azure.com/ |

## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia
Usługa Azure Monitor nakłada następujące limity użycia dla metryk niestandardowych:

|Category|Limit|
|---|---|
|Czas aktywności serii/subskrypcje/region|50,000|
|Klucze wymiarów na metrykę|10|
|Długość ciągu dla metryki przestrzeni nazw, nazwami metryk, wymiaru kluczy i wartości wymiaru|256 znaków|

Szeregi czasowe active jest zdefiniowany jako wszelkie unikatową kombinację metryki, wymiaru klucza lub wartości wymiaru, który miał wartości metryk, opublikowane w ciągu ostatnich 12 godzin.

## <a name="next-steps"></a>Kolejne kroki
Niestandardowe metryki z różnymi usługami: 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Zestaw skalowania maszyn wirtualnych](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Maszyny wirtualne platformy Azure (wersja klasyczna)](collect-custom-metrics-guestos-vm-classic.md)
 - [Maszyny wirtualnej systemu Linux przy użyciu agenta Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [Interfejs API REST](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Usługi w chmurze klasycznego](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
