---
title: Niestandardowe metryki w usłudze Azure Monitor
description: Dowiedz się więcej o metrykach niestandardowych w usłudze Azure Monitor i sposobie ich modelowania.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e104877ef641a87eac4ba19bb3342c6e029bf80c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294593"
---
# <a name="custom-metrics-in-azure-monitor"></a>Niestandardowe metryki w usłudze Azure Monitor

Podczas wdrażania zasobów i aplikacji na platformie Azure należy rozpocząć zbieranie danych telemetrycznych, aby uzyskać wgląd w ich wydajność i kondycję. Platforma Azure udostępnia niektóre metryki po wyjęciu z pudełka. Te metryki są nazywane standardem lub platformą. Mają jednak ograniczony charakter. Możesz zebrać niektóre niestandardowe wskaźniki wydajności lub metryki specyficzne dla firmy, aby zapewnić głębsze informacje.
Te **niestandardowe** metryki mogą być zbierane za pośrednictwem danych telemetrycznych aplikacji, agenta, który działa na zasoby platformy Azure, a nawet poza systemem monitorowania i przesyłane bezpośrednio do usługi Azure Monitor. Po ich opublikowaniu w usłudze Azure Monitor możesz przeglądać, wysyłać zapytania i alerty dotyczące niestandardowych metryk zasobów i aplikacji platformy Azure obok standardowych metryk emitowanych przez platformę Azure.

## <a name="send-custom-metrics"></a>Wysyłanie niestandardowych danych
Metryki niestandardowe mogą być wysyłane do usługi Azure Monitor za pomocą kilku metod:
- Instrumentacja aplikacji przy użyciu narzędzia Azure Application Insights SDK i wysyłania niestandardowych danych telemetrycznych do usługi Azure Monitor. 
- Zainstaluj rozszerzenie diagnostyki systemu Windows Azure (WAD) na maszynie [Wirtualnej Platformy Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [zestawie skalowania maszyny wirtualnej,](collect-custom-metrics-guestos-resource-manager-vmss.md) [klasycznej maszynie wirtualnej](collect-custom-metrics-guestos-vm-classic.md)lub [klasycznych usługach w chmurze](collect-custom-metrics-guestos-vm-cloud-service-classic.md) i wysyłaj liczniki wydajności do usługi Azure Monitor. 
- Zainstaluj [agenta Telegraf InfluxData](collect-custom-metrics-linux-telegraf.md) na maszynie wirtualnej systemu Azure Linux i wyślij metryki przy użyciu wtyczki wyjściowej usługi Azure Monitor.
- Wysyłaj niestandardowe metryki [bezpośrednio do interfejsu API REST usługi Azure Monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Podczas wysyłania niestandardowych metryk do usługi Azure Monitor każdy punkt danych lub wartość, zgłoszone muszą zawierać następujące informacje.

### <a name="authentication"></a>Uwierzytelnianie
Aby przesłać metryki niestandardowe do usługi Azure Monitor, jednostka, która przesyła metrykę, potrzebuje prawidłowego tokenu usługi Azure Active Directory (Azure AD) w nagłówku **elementu nośnego** żądania. Istnieje kilka obsługiwanych sposobów uzyskania prawidłowego tokenu okaziciela:
1. [Tożsamości zarządzane dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Nadaje tożsamość do samego zasobu platformy Azure, takich jak maszyna wirtualna. Tożsamość usługi zarządzanej (MSI) ma na celu przyznanie uprawnień zasobów do wykonywania niektórych operacji. Przykładem jest umożliwienie zasobu do emitowania metryk o sobie. Zasób lub jego MSI, można przyznać **monitorowania metryki wydawcy** uprawnień do innego zasobu. Z tego uprawnienia MSI można emitować metryki dla innych zasobów, jak również.
2. [Podmiot zabezpieczeń usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). W tym scenariuszu aplikacji lub usługi Azure AD można przypisać uprawnienia do emitowania metryk dotyczących zasobu platformy Azure.
Aby uwierzytelnić żądanie, usługa Azure Monitor sprawdza poprawność tokenu aplikacji przy użyciu kluczy publicznych usługi Azure AD. Istniejąca rola **wydawcy monitorowania metryk** ma już to uprawnienie. Jest on dostępny w witrynie Azure portal. Podmiot usługi, w zależności od zasobów, dla jakich emituje metryki niestandardowe, może mieć rolę **wydawcy monitorowania** w wymaganym zakresie. Przykładami są subskrypcja, grupa zasobów lub określony zasób.

> [!NOTE]  
> Gdy żądasz tokenu usługi Azure AD do emitowania niestandardowych metryk, `https://monitoring.azure.com/`upewnij się, że odbiorcy lub zasób, dla którego jest wymagany token, to . Pamiętaj, aby dołączyć końcowe "/".

### <a name="subject"></a>Podmiot
Ta właściwość przechwytuje identyfikator zasobu platformy Azure, dla którego jest raportowana metryka niestandardowa. Te informacje zostaną zakodowane w adresie URL wywoływania interfejsu API. Każdy interfejs API może przesyłać tylko wartości metryki dla pojedynczego zasobu platformy Azure.

> [!NOTE]  
> Nie można emitować niestandardowych metryk względem identyfikatora zasobu grupy zasobów lub subskrypcji.
>
>

### <a name="region"></a>Region
Ta właściwość przechwytuje region platformy Azure, w jakim jest wdrażany zasób emitujący metryki. Metryki muszą być emitowane do tego samego regionalnego punktu końcowego usługi Azure Monitor, w jakim jest wdrażany zasób. Na przykład metryki niestandardowe dla maszyny Wirtualnej wdrożonej w zachodnie stany USA muszą być wysyłane do regionalnego punktu końcowego usługi WestUS usługi Azure Monitor. Informacje o regionie są również kodowane w adresie URL wywołania interfejsu API.

> [!NOTE]  
> Podczas publicznej wersji zapoznawczej metryki niestandardowe są dostępne tylko w podzbiorze regionów platformy Azure. Lista obsługiwanych regionów jest udokumentowana w dalszej części tego artykułu.
>
>

### <a name="timestamp"></a>Znacznik czasu
Każdy punkt danych wysyłany do usługi Azure Monitor musi być oznaczony sygnaturą czasową. Ten sygnatura czasowa przechwytuje DateTime, w którym wartość metryki jest mierzona lub zbierana. Usługa Azure Monitor akceptuje dane metryki z sygnaturami czasowymi do 20 minut w przeszłości i 5 minut w przyszłości. Sygnatura czasowa musi być w formacie ISO 8601.

### <a name="namespace"></a>Przestrzeń nazw
Przestrzenie nazw są sposobem kategoryzowanie lub grupowanie podobnych metryk razem. Za pomocą obszarów nazw, można osiągnąć izolację między grupami metryk, które mogą zbierać różne statystyki lub wskaźniki wydajności. Na przykład może mieć obszar nazw o nazwie **contosomemorymetrics,** który śledzi metryki użycia pamięci, które profil aplikacji. Inny obszar nazw o nazwie **contosoapptransaction** może śledzić wszystkie metryki dotyczące transakcji użytkownika w aplikacji.

### <a name="name"></a>Nazwa
**Nazwa** to nazwa metryki, która jest zgłaszana. Zazwyczaj nazwa jest wystarczająco opisowa, aby pomóc zidentyfikować, co jest mierzone. Przykładem jest metryka, która mierzy liczbę bajtów pamięci używanych na danej maszynie wirtualnej. Może mieć nazwę metryki, taką jak **bajty pamięci w użyciu.**

### <a name="dimension-keys"></a>Klucze wymiarów
Wymiar jest parą klucza lub wartości, która pomaga opisać dodatkowe cechy dotyczące metryki, która jest zbierana. Za pomocą dodatkowych cech, można zbierać więcej informacji na temat metryki, co pozwala na głębsze spostrzeżenia. Na przykład **metryki Bajty pamięci w użyciu** może mieć klucz wymiaru o nazwie **Proces,** który przechwytuje, ile bajtów pamięci każdego procesu na maszynie wirtualnej zużywa. Za pomocą tego klucza, można filtrować metryki, aby zobaczyć, ile pamięci określonych procesów używać lub zidentyfikować pięć najważniejszych procesów według użycia pamięci.
Wymiary są opcjonalne, nie wszystkie metryki mogą mieć wymiary. Metryka niestandardowa może mieć maksymalnie 10 wymiarów.

### <a name="dimension-values"></a>Wartości wymiarów
Podczas raportowania punktu danych metryki dla każdego klucza wymiaru na zgłaszanej metryki istnieje odpowiednia wartość wymiaru. Na przykład można zgłosić pamięć używaną przez ContosoApp na maszynie wirtualnej:

* Nazwa metryki będzie **bajtów pamięci w użyciu**.
* Kluczem wymiaru będzie **Proces**.
* Wartością wymiaru będzie **ContosoApp.exe**.

Podczas publikowania wartości metryki można określić tylko pojedynczą wartość wymiaru dla klucza wymiaru. Jeśli zbierasz tego samego wykorzystania pamięci dla wielu procesów na maszynie Wirtualnej, można zgłosić wiele wartości metryki dla tego sygnatury czasowej. Każda wartość metryki określa inną wartość wymiaru dla klucza wymiaru **Proces.**
Wymiary są opcjonalne, nie wszystkie metryki mogą mieć wymiary. Jeśli wpis metryki definiuje klucze wymiarów, odpowiednie wartości wymiarów są obowiązkowe.

### <a name="metric-values"></a>Wartości metryk
Usługa Azure Monitor przechowuje wszystkie metryki w odstępach jednominutowych. Rozumiemy, że w danej minucie metryki może być konieczne próbki kilka razy. Przykładem jest wykorzystanie procesora CPU. Lub może być konieczne mierzone dla wielu zdarzeń dyskretnych. Przykładem jest opóźnienie transakcji logowania. Aby ograniczyć liczbę nieprzetworzonych wartości, które mają być emitowane i płacić w usłudze Azure Monitor, można lokalnie wstępnie zagregować i emitować wartości:

* **Min**: Minimalna zaobserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Max**: Maksymalna obserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Suma**: Suma wszystkich obserwowanych wartości ze wszystkich próbek i pomiarów w ciągu minuty.
* **Liczba**: Liczba próbek i pomiarów wykonanych w ciągu minuty.

Na przykład jeśli były 4 transakcji logowania do aplikacji w ciągu danej minuty, wynikowe zmierzone opóźnienia dla każdego z nich może być następująca:

|Transakcja 1|Transakcja 2|Transakcja 3|Transakcja 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Następnie wynikowa publikacja metryki w usłudze Azure Monitor będzie następująca:
* Min.
* Maks.
* Suma: 40
* Liczba: 4

Jeśli aplikacja nie może wstępnie zagregować lokalnie i musi emitować każdą oddzielną próbkę lub zdarzenie natychmiast po pobraniu, można emitować wartości miar nieprzetworzonych. Na przykład za każdym razem, gdy w aplikacji występuje transakcja logowania, publikujesz metrykę w usłudze Azure Monitor przy jednym pomiarze. Tak więc dla transakcji logowania, które miały 12 ms, publikacja metryki będzie w następujący sposób:
* Min.
* Maks.
* Suma: 12
* Liczba: 1

W tym procesie można emitować wiele wartości dla tej samej kombinacji wymiarów metryki plus w danej minucie. Usługa Azure Monitor następnie przyjmuje wszystkie wartości nieprzetworzone emitowane przez daną minutę i agreguje je razem.

### <a name="sample-custom-metric-publication"></a>Przykładowa niestandardowa publikacja metryczna
W poniższym przykładzie utworzysz metrykę niestandardową o nazwie **Bajty pamięci w użyciu w** obszarze nazw metryki Profil **pamięci** dla maszyny wirtualnej. Metryka ma jeden wymiar o nazwie **Proces**. Dla danego sygnatury czasowej emitujemy wartości metryk dla dwóch różnych procesów:

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
> Usługa Application Insights, rozszerzenie diagnostyki i agent InfluxData Telegraf są już skonfigurowane do emitowania wartości metryk względem poprawnego regionalnego punktu końcowego i przenoszenia wszystkich poprzednich właściwości w każdej emisji.
>
>

## <a name="custom-metric-definitions"></a>Niestandardowe definicje metryk
Nie ma potrzeby wstępnegodefiniunia metryki niestandardowej w usłudze Azure Monitor przed jej emisji. Każdy opublikowany punkt danych metryki zawiera obszar nazw, nazwę i informacje o wymiarach. Dlatego po raz pierwszy metryka niestandardowa jest emitowana do usługi Azure Monitor, definicji metryki jest tworzony automatycznie. Ta definicja metryki jest następnie wykrywalne dla dowolnego zasobu metryki jest emitowany za pośrednictwem definicji metryki.

> [!NOTE]  
> Usługa Azure Monitor nie obsługuje jeszcze definiowania **jednostek** dla metryki niestandardowej.

## <a name="using-custom-metrics"></a>Korzystanie z niestandardowych metryk
Po przesłaniu niestandardowych metryk do usługi Azure Monitor można przeglądać je za pośrednictwem witryny Azure portal i wysyłać do nich zapytania za pośrednictwem interfejsów API rest usługi Azure Monitor. Można również utworzyć alerty na nich, aby powiadomić cię, gdy pewne warunki są spełnione.

> [!NOTE]
> Aby wyświetlić metryki niestandardowe, musisz być rolą czytelnika lub współautora.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Przeglądanie niestandardowych metryk za pośrednictwem witryny Azure portal
1.    Przejdź do [witryny Azure portal](https://portal.azure.com).
2.    Wybierz okienko **Monitor.**
3.    Wybierz pozycję **Metryki**.
4.    Wybierz zasób, za który zostały wyemitowane niestandardowe metryki.
5.    Wybierz obszar nazw metryk dla metryki niestandardowej.
6.    Wybierz metrykę niestandardową.

## <a name="supported-regions"></a>Obsługiwane regiony
Podczas publicznej wersji zapoznawczej możliwość publikowania metryk niestandardowych jest dostępna tylko w podzbiorze regionów platformy Azure. To ograniczenie oznacza, że metryki mogą być publikowane tylko dla zasobów w jednym z obsługiwanych regionów. W poniższej tabeli wymieniono zestaw obsługiwanych regionów platformy Azure dla metryk niestandardowych. Zawiera również listę odpowiednich punktów końcowych, dla których metryki dla zasobów w tych regionach powinny zostać opublikowane:

|Region platformy Azure |Regionalny prefiks punktu końcowego|
|---|---|
| **Stany Zjednoczone i Kanada** | |
|Zachodnio-środkowe stany USA | https:\//westcentralus.monitoring.azure.com/ |
|Zachodnie stany USA 2       | https:\//westus2.monitoring.azure.com/ |
|Północno-środkowe stany USA | https:\//northcentralus.monitoring.azure.com
|Południowo-środkowe stany USA| https:\//southcentralus.monitoring.azure.com/ |
|Środkowe stany USA      | https:\//centralus.monitoring.azure.com |
|Kanada Środkowa | https:\//canadacentral.monitoring.azure.comc
|Wschodnie stany USA| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa Północna    | https:\//northeurope.monitoring.azure.com/ |
|Europa Zachodnia     | https:\//westeurope.monitoring.azure.com/ |
|Południowe Zjednoczone Królestwo | https:\//uksouth.monitoring.azure.com
|Francja Środkowa | https:\//francecentral.monitoring.azure.com |
| **Afryka** | |
|Republika Południowej Afryki Północ | https:\//southafricanorth.monitoring.azure.com
| **Azji** | |
|Indie Środkowe | https:\//centralindia.monitoring.azure.com
|Australia Wschodnia | https:\//australiaeast.monitoring.azure.com
|Japonia Wschodnia | https:\//japaneast.monitoring.azure.com
|Azja Południowo-Wschodnia  | https:\//southeastasia.monitoring.azure.com |
|Azja Wschodnia | https:\//eastasia.monitoring.azure.com
|Korea Środkowa   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia
Usługa Azure Monitor nakłada następujące limity użycia na metryki niestandardowe:

|Kategoria|Limit|
|---|---|
|Aktywne szeregi czasowe/subskrypcje/region|50 000|
|Klawisze wymiaru na metrykę|10|
|Długość ciągu dla metrycznych obszarów nazw, nazw metryk, kluczy wymiarów i wartości wymiarów|256 znaków|

Aktywne szeregi czasowe są definiowane jako dowolną unikatową kombinację metryki, klucza wymiaru lub wartości wymiaru, która miała opublikowane wartości metryki w ciągu ostatnich 12 godzin.

## <a name="next-steps"></a>Następne kroki
Użyj niestandardowych metryk z różnych usług: 
 - [Maszyny wirtualne](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Zestaw skalowania maszyn wirtualnych](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Maszyny wirtualne platformy Azure (klasyczne)](collect-custom-metrics-guestos-vm-classic.md)
 - [Maszyna wirtualna Systemu Linux przy użyciu agenta Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [INTERFEJS API ODPOCZYNKU](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Klasyczne usługi w chmurze](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
