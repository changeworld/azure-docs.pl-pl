---
title: Limity żądań i ograniczania przepustowości — usługi Azure Resource Manager
description: W tym artykule opisano, jak używać ograniczania żądań usługi Azure Resource Manager, gdy zostały osiągnięte limity subskrypcji.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: fc731b1abec9c101356a0fa57eef498b58612ab9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791363"
---
# <a name="throttling-resource-manager-requests"></a>Ograniczanie żądań usługi Resource Manager

Dla każdej subskrypcji platformy Azure i dzierżawy usługi Resource Manager pozwala maksymalnie 12 000 żądań na godzinę żądania odczytu i 1200 zapisu na godzinę. Limity te są ograniczone do Identyfikatora podmiotu zabezpieczeń, dzięki czemu żądania i identyfikator subskrypcji lub identyfikatora dzierżawy W przypadku żądań pochodzących z więcej niż jeden identyfikator podmiotu zabezpieczeń, limit jednej subskrypcji, czy dzierżawa jest większa niż 12 000 i 1200 na godzinę.

Żądania są stosowane do dzierżawy lub Twojej subskrypcji. Żądania subskrypcji są te dotyczą przekazanie subskrypcji IDENTYFIKATORA, takie jak pobieranie grup zasobów w ramach subskrypcji. Żądania dzierżawy nie zawierają Identyfikatora subskrypcji, takie jak pobieranie prawidłowych lokalizacji platformy Azure.

Te limity mają zastosowanie do każdego wystąpienia usługi Azure Resource Manager. Istnieje wiele wystąpień w każdym regionie platformy Azure i usługi Azure Resource Manager jest wdrażana we wszystkich regionach platformy Azure.  Zatem w praktyce skutecznie znacznie wyższa niż te limity są limity, jako użytkownik żądania są zazwyczaj obsługiwane przez wiele różnych wystąpień.

Jeśli aplikacji lub skryptu osiągnie te limity, należy ograniczania żądań. W tym artykule przedstawiono sposób określenia pozostałych żądań, które mają przed przekroczeniem limitu oraz reagować, gdy został osiągnięty limit.

W przypadku osiągnięcia limitu otrzymasz kod stanu HTTP **429 zbyt wiele żądań**.

Wykres zasobów platformy Azure ogranicza liczbę żądań do swoich operacji. Kroki opisane w tym artykule w celu określenia pozostałych żądań i jak reagować po osiągnięciu limitu dotyczą również wykres zasobów. Jednak wykres zasobów ustawia współczynnik swój własny limit i resetowania. Aby uzyskać więcej informacji, zobacz [ograniczania wykresie zasobów Azure](../governance/resource-graph/overview.md#throttling).

## <a name="remaining-requests"></a>Pozostałych żądań
Liczba pozostałych żądań, które można określić, sprawdzając nagłówki odpowiedzi. Żądania odczytu zwracają wartość w nagłówku liczbę pozostałych żądań odczytu. Zapisu żądania zawierać wartość dla liczby pozostałych żądań zapisu. W poniższej tabeli opisano nagłówki odpowiedzi, które można sprawdzić w przypadku tych wartości:

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Subskrypcja o określonym zakresie odczytuje pozostałych. Ta wartość jest zwracana na operacje odczytu. |
| x-ms-ratelimit-remaining-subscription-writes |Subskrypcja o określonym zakresie zapisuje pozostałe. Ta wartość jest zwracana w przypadku operacji zapisu. |
| x-ms-ratelimit-remaining-tenant-reads |Dzierżawa o określonym zakresie odczytuje pozostałe |
| x-ms-ratelimit-remaining-tenant-writes |Dzierżawa o określonym zakresie zapisuje pozostałe |
| x-ms-ratelimit-remaining-subscription-resource-requests |Subskrypcja obejmuje pozostałe żądania typu zasobu.<br /><br />Ta wartość nagłówka jest zwracany tylko wtedy, jeśli usługa została zastąpiona domyślny limit. Menedżer zasobów dodaje tę wartość, zamiast subskrypcji odczyty lub zapisy. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Subskrypcja obejmuje pozostałe żądania kolekcji dla typu zasobu.<br /><br />Ta wartość nagłówka jest zwracany tylko wtedy, jeśli usługa została zastąpiona domyślny limit. Ta wartość określa liczbę pozostałych żądań kolekcji (listy zasobów). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Pozostałe żądania typu zasobu dzierżawy o określonym zakresie.<br /><br />Tego pliku nagłówkowego jest dodawana tylko dla żądań na poziomie dzierżawy i tylko wtedy, gdy usługa ma zastąpić domyślny limit. Menedżer zasobów dodaje tę wartość, zamiast dzierżawy odczyty lub zapisy. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Dzierżawa o określonym zakresie pozostałe żądania kolekcji dla typu zasobu.<br /><br />Tego pliku nagłówkowego jest dodawana tylko dla żądań na poziomie dzierżawy i tylko wtedy, gdy usługa ma zastąpić domyślny limit. |

## <a name="retrieving-the-header-values"></a>Pobieranie wartości nagłówka
Podczas pobierania tych wartości nagłówka w kodzie albo skrypt nie różni się od pobierania dowolnej wartości nagłówka. 

Na przykład w **C#**, pobrać wartość nagłówka z **HttpWebResponse** obiektu o nazwie **odpowiedzi** następującym kodem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

W **PowerShell**, możesz pobrać wartość nagłówka z operacją Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Aby uzyskać kompletny przykład programu PowerShell, zobacz [Sprawdź limity usługi Resource Manager w przypadku subskrypcji](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Jeśli chcesz zobaczyć pozostałe żądania do debugowania, możesz podać **-debugowanie** parametrem w Twojej **programu PowerShell** polecenia cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Zwraca wiele wartości, łącznie z następującą wartością odpowiedzi:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Aby uzyskać limity zapisu, użyj operacji zapisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Zwraca wiele wartości, w tym następujące wartości:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

W **wiersza polecenia platformy Azure**, pobrać wartość nagłówka przy użyciu opcji bardziej szczegółowy.

```azurecli
az group list --verbose --debug
```

Zwraca wiele wartości, w tym następujące wartości:

```azurecli
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

Aby uzyskać limity zapisu, użyj operacji zapisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Zwraca wiele wartości, w tym następujące wartości:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Oczekiwania przed wysłaniem następnego żądania
W przypadku osiągnięcia limitu żądań usługi Resource Manager zwraca **429** kod stanu HTTP i **Retry-After** wartością w nagłówku. **Retry-After** wartość określa liczbę sekund, Twoja aplikacja powinna oczekiwać (lub uśpienia) przed wysłaniem następnego żądania. W przypadku wysłania żądania przed upływem wartość ponownych prób nie jest przetwarzanie Twojego żądania i nową wartość ponownych prób jest zwracana.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać kompletny przykład programu PowerShell, zobacz [Sprawdź limity usługi Resource Manager w przypadku subskrypcji](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Aby uzyskać więcej informacji na temat limity przydziału i ograniczenia, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
* Aby dowiedzieć się więcej informacji na temat obsługi żądań asynchronicznych REST, zobacz [śledzenie operacji asynchronicznych Azure](resource-manager-async-operations.md).
