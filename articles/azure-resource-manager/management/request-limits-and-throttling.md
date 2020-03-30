---
title: Limity żądań i ograniczanie
description: W tym artykule opisano sposób używania ograniczania przepustowości z żądaniami usługi Azure Resource Manager po osiągnięciu limitów subskrypcji.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239361"
---
# <a name="throttling-resource-manager-requests"></a>Ograniczanie przepływności żądań usługi Resource Manager

W tym artykule opisano, jak usługa Azure Resource Manager ogranicza żądania. Pokazuje, jak śledzić liczbę żądań, które pozostają przed osiągnięciem limitu i jak reagować po osiągnięciu limitu.

Dławienie odbywa się na dwóch poziomach. Usługa Azure Resource Manager ogranicza żądania subskrypcji i dzierżawy. Jeśli żądanie znajduje się w ramach limitów ograniczania subskrypcji i dzierżawy, Menedżer zasobów kieruje żądanie do dostawcy zasobów. Dostawca zasobów stosuje limity ograniczania przepustowości, które są dostosowane do jego operacji. Na poniższej ilustracji przedstawiono, jak ograniczanie jest stosowane jako żądanie przechodzi od użytkownika do usługi Azure Resource Manager i dostawcy zasobów.

![Ograniczanie żądań](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limity subskrypcji i dzierżawy

Każda operacja na poziomie subskrypcji i na poziomie dzierżawy podlega ograniczeniom ograniczania przepustowości. Żądania subskrypcji to żądania, które obejmują przekazywanie identyfikatora subskrypcji, na przykład pobieranie grup zasobów w ramach subskrypcji. Żądania dzierżawy nie zawierają identyfikatora subskrypcji, takiego jak pobieranie prawidłowych lokalizacji platformy Azure.

Domyślne limity ograniczania przepustowości na godzinę są wyświetlane w poniższej tabeli.

| Zakres | Operacje | Limit |
| ----- | ---------- | ------- |
| Subskrypcja | Odczytuje | 12000 |
| Subskrypcja | Usuwa | 15000 |
| Subskrypcja | Zapisuje | 1200 |
| Dzierżawa | Odczytuje | 12000 |
| Dzierżawa | Zapisuje | 1200 |

Limity te dotyczą podmiotu zabezpieczeń (użytkownika lub aplikacji) zgłaszającego te żądania i identyfikatora subskrypcji lub identyfikatora dzierżawy. Jeśli Twoje żądania pochodzą od więcej niż jednego podmiotu zabezpieczeń, limit w ramach subskrypcji lub dzierżawy jest większy niż 12 000 i 1200 na godzinę.

Te limity dotyczą każdego wystąpienia usługi Azure Resource Manager. Istnieje wiele wystąpień w każdym regionie platformy Azure, a usługa Azure Resource Manager jest wdrażana we wszystkich regionach platformy Azure.  Tak więc w praktyce limity są wyższe niż te limity. Żądania od użytkownika są zwykle obsługiwane przez różne wystąpienia usługi Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limity dostawców zasobów

Dostawcy zasobów stosują własne limity ograniczania przepustowości. Ponieważ Menedżer zasobów ogranicza się według identyfikatora głównego i przez wystąpienie Menedżera zasobów, dostawca zasobów może odbierać więcej żądań niż domyślne limity w poprzedniej sekcji.

W tej sekcji omówiono limity ograniczania niektórych dostawców zasobów powszechnie używanych.

### <a name="storage-throttling"></a>Ograniczanie pamięci masowej

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Ograniczanie przepustowości sieci

Dostawca zasobów firmy Microsoft.Network stosuje następujące limity przepustnicy:

| Operacja | Limit |
| --------- | ----- |
| zapis / usuwanie (PUT) | 1000 na 5 minut |
| odczyt (GET) | 10000 na 5 minut |

### <a name="compute-throttling"></a>Ograniczanie obliczeń

Aby uzyskać informacje na temat ograniczania limitów dla operacji obliczeniowych, zobacz [Rozwiązywanie problemów z błędami ograniczania przepustowości interfejsu API — Obliczanie](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Aby sprawdzić wystąpienia maszyny wirtualnej w zestawie skalowania maszyny wirtualnej, użyj [operacji Skalowanie maszyny wirtualnej](/rest/api/compute/virtualmachinescalesetvms). Na przykład użyj [maszyny wirtualnej Zestaw maszyn wirtualnych — lista z parametrami,](/rest/api/compute/virtualmachinescalesetvms/list) aby sprawdzić stan zasilania wystąpień maszyny wirtualnej. Ten interfejs API zmniejsza liczbę żądań.

### <a name="azure-resource-graph-throttling"></a>Ograniczanie wykresu zasobów platformy Azure

[Program Azure Resource Graph](../../governance/resource-graph/overview.md) ogranicza liczbę żądań do swoich operacji. Kroki opisane w tym artykule w celu określenia pozostałych żądań i jak odpowiedzieć po osiągnięciu limitu również dotyczą wykresu zasobów. Jednak wykres zasobów ustawia swój własny limit i szybkość resetowania. Aby uzyskać więcej informacji, zobacz [Ograniczanie nagłówków wykresu zasobów](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Kod błędu

Po osiągnięciu limitu otrzymasz kod stanu HTTP **429 Zbyt wiele żądań**. Odpowiedź zawiera **ponawianie próby po** wartości, która określa liczbę sekund aplikacji należy odczekać (lub uśpić) przed wysłaniem następnego żądania. Jeśli wyślesz żądanie przed upływem wartości ponawiania próby, żądanie nie zostanie przetworzone i zostanie zwrócona nowa wartość ponawiania.

Po odczekaniu określonego czasu można również zamknąć i ponownie otworzyć połączenie z platformą Azure. Zresetowanie połączenia może połączyć się z innym wystąpieniem usługi Azure Resource Manager.

Jeśli używasz zestawu SDK platformy Azure, zestaw SDK może mieć konfigurację automatycznego ponawiania. Aby uzyskać więcej informacji, zobacz [Ponów próbę obsługi usług platformy Azure](/azure/architecture/best-practices/retry-service-specific).

Niektórzy dostawcy zasobów zwracają 429, aby zgłosić tymczasowy problem. Problem może być warunkiem przeciążenia, który nie jest bezpośrednio spowodowane przez żądanie. Lub może reprezentować tymczasowy błąd o stanie zasobu docelowego lub zasobu zależnego. Na przykład dostawca zasobów sieciowych zwraca 429 z kodem błędu **RetryableErrorDueToAnotherOperation,** gdy zasób docelowy jest zablokowany przez inną operację. Aby ustalić, czy błąd pochodzi z ograniczania przepustowości lub warunek tymczasowy, zobacz szczegóły błędu w odpowiedzi.

## <a name="remaining-requests"></a>Pozostałe żądania

Liczbę pozostałych żądań można określić, sprawdzając nagłówki odpowiedzi. Żądania odczytu zwracają wartość w nagłówku dla liczby pozostałych żądań odczytu. Żądania zapisu zawierają wartość dla liczby pozostałych żądań zapisu. W poniższej tabeli opisano nagłówki odpowiedzi, które można sprawdzić pod kątem tych wartości:

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| x-ms-ratelimit-pozostałe-subskrypcja-odczyty |Subskrypcja o zakresie odczytuje pozostałe. Ta wartość jest zwracana w operacjach odczytu. |
| x-ms-ratelimit-remaining-subscription-writes |Subskrypcja o zakresie zapisuje pozostałe. Ta wartość jest zwracana w operacjach zapisu. |
| x-ms-ratelimit-remaining-tenant-odczyty |Dzierżawa o zakresie odczyty pozostałe |
| x-ms-ratelimit-remaining-tenant-writes |Dzierżawa o zakresie zapisuje pozostałe |
| x-ms-ratelimit-remaining-subscription-resource-requests x-ms-ratelimit-remaining-subscription-resource requests x-ms-ratelimit-remaining-subscription-resource requests x-ms- |Subskrypcja o określonym zakresie żądań typu zasobu pozostałych.<br /><br />Ta wartość nagłówka jest zwracana tylko wtedy, gdy usługa ma zastąpić domyślny limit. Menedżer zasobów dodaje tę wartość zamiast subskrypcji odczytuje lub zapisuje. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Pozostałe żądania zbierania typów zasobów o określonym zakresie subskrypcji.<br /><br />Ta wartość nagłówka jest zwracana tylko wtedy, gdy usługa ma zastąpić domyślny limit. Ta wartość zawiera liczbę pozostałych żądań zbierania (zasoby listy). |
| x-ms-ratelimit-remaining-tenant-resource-requests x-ms-ratelimit-remaining-tenant-resource requests x-ms-ratelimit-remaining-tenant-resource requests x-ms- |Dzierżawy zakres żądania typu zasobu pozostałych.<br /><br />Ten nagłówek jest dodawany tylko dla żądań na poziomie dzierżawy i tylko wtedy, gdy usługa ma zastąpić domyślny limit. Menedżer zasobów dodaje tę wartość zamiast dzierżawy odczytuje lub zapisuje. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Pozostała liczba żądań zbierania typów zasobów o określonym zakresie dzierżawcy.<br /><br />Ten nagłówek jest dodawany tylko dla żądań na poziomie dzierżawy i tylko wtedy, gdy usługa ma zastąpić domyślny limit. |

Dostawca zasobów może również zwracać nagłówki odpowiedzi z informacjami o pozostałych żądaniach. Aby uzyskać informacje o nagłówkach odpowiedzi zwracanych przez dostawcę zasobów obliczeniowych, zobacz [Nagłówki odpowiedzi informacyjnych szybkości połączeń](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Pobieranie wartości nagłówka

Pobieranie tych wartości nagłówka w kodzie lub skrypcie nie różni się od pobierania dowolnej wartości nagłówka. 

Na przykład w **języku C#** można pobrać wartość nagłówka z **httpWebResponse** obiektu o nazwie **odpowiedzi** z następującym kodem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

W **programie PowerShell**można pobrać wartość nagłówka z invoke-WebRequest operacji.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Aby zapoznać się z pełnym przykładem programu PowerShell, zobacz [Sprawdzanie limitów Menedżera zasobów dla subskrypcji](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Jeśli chcesz zobaczyć pozostałe żądania debugowania, możesz podać parametr **-Debug** na policzce cmdlet **programu PowerShell.**

```powershell
Get-AzResourceGroup -Debug
```

Który zwraca wiele wartości, w tym następującą wartość odpowiedzi:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Aby uzyskać limity zapisu, należy użyć operacji zapisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Który zwraca wiele wartości, w tym następujące wartości:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

W **usłudze Azure CLI**, można pobrać wartość nagłówka przy użyciu opcji bardziej pełne.

```azurecli
az group list --verbose --debug
```

Który zwraca wiele wartości, w tym następujące wartości:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Aby uzyskać limity zapisu, należy użyć operacji zapisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Który zwraca wiele wartości, w tym następujące wartości:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z pełnym przykładem programu PowerShell, zobacz [Sprawdzanie limitów Menedżera zasobów dla subskrypcji](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Aby uzyskać więcej informacji na temat limitów i przydziałów, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Aby dowiedzieć się więcej na temat obsługi asynchronicznych żądań REST, zobacz [Śledzenie asynchronicznych operacji platformy Azure.](async-operations.md)
