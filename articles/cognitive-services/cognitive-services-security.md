---
title: Zabezpieczenia
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o różnych zagadnieniach dotyczących zabezpieczeń użycia usług Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131531"
---
# <a name="azure-cognitive-services-security"></a>Zabezpieczenia usług Azure Cognitive Services

Bezpieczeństwo należy uznać za najwyższy priorytet podczas tworzenia wszystkich aplikacji. Wraz z nadejściem aplikacji obsługujących sztuczną inteligencję bezpieczeństwo jest jeszcze ważniejsze. W tym artykule opisano różne aspekty zabezpieczeń usług Azure Cognitive Services, takie jak użycie zabezpieczeń warstwy transportu, uwierzytelnianie i bezpieczne konfigurowanie poufnych danych.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Wszystkie punkty końcowe usług Cognitive Services udostępniane za pośrednictwem protokołu HTTP wymuszają protokół TLS 1.2. W przypadku wymuszonego protokołu zabezpieczeń konsumenci próbujący wywołać punkt końcowy usług Cognitive Services powinni przestrzegać następujących wytycznych:

* System operacyjny klienta (OS) musi obsługiwać TLS 1.2
* Język (i platforma) używane do wywołania HTTP muszą określać TLS 1.2 jako część żądania
  * W zależności od języka i platformy określanie protokołu TLS odbywa się w sposób dorozumiany lub jawny

W przypadku użytkowników platformy .NET należy wziąć pod uwagę <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">najważniejsze wskazówki dotyczące zabezpieczeń warstwy <span class="docon docon-navigate-external x-hidden-focus"> </span>transportu </a>.

## <a name="authentication"></a>Uwierzytelnianie

Podczas omawiania uwierzytelniania, istnieje kilka typowych nieporozumień. Uwierzytelnianie i autoryzacja są często mylone dla siebie nawzajem. Tożsamość jest również głównym składnikiem zabezpieczeń. Tożsamość jest zbiorem informacji o <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">zleceniodawcy <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Dostawcy tożsamości (IdP) udostępniają tożsamości usługom uwierzytelniania. Uwierzytelnianie jest czynnością weryfikacji tożsamości użytkownika. Autoryzacja to specyfikacja praw dostępu i uprawnień do zasobów dla danej tożsamości. Kilka ofert usług Cognitive Services, obejmują kontrolę dostępu opartą na rolach (RBAC). RBAC może być stosowany w celu uproszczenia niektórych ceremonii związanych z ręcznym zarządzaniem zleceniodawcami. Aby uzyskać więcej informacji, zobacz [kontrola dostępu oparta na rolach dla zasobów platformy Azure.](../role-based-access-control/overview.md)

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą kluczy subskrypcji, tokenów dostępu i usługi Azure Active Directory (AAD), zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">uwierzytelnianie żądań<span class="docon docon-navigate-external x-hidden-focus"></span>w usługach Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Zmienne środowiskowe i konfiguracja aplikacji

Zmienne środowiskowe to pary nazwa-wartość, przechowywane w określonym środowisku. Bezpieczniejszą alternatywą dla używania wartości zakodowanych na stałe dla poufnych danych jest użycie zmiennych środowiskowych. Wartości zakodowane na stałe są niebezpieczne i należy ich unikać.

> [!CAUTION]
> **Nie** należy używać wartości zakodowanych na stałe dla poufnych danych, co stanowi poważną lukę w zabezpieczeniach.

> [!NOTE]
> Podczas gdy zmienne środowiskowe są przechowywane w postaci zwykłego tekstu, są one izolowane do środowiska. Jeśli środowisko jest zagrożone, tak też są zmienne ze środowiskiem.

### <a name="set-environment-variable"></a>Ustawianie zmiennej środowiskowej

Aby ustawić zmienne środowiskowe, należy użyć `ENVIRONMENT_VARIABLE_KEY` jednego z następujących `value` poleceń — gdzie jest to klucz nazwany i jest wartością przechowywaną w zmiennej środowiskowej.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Tworzenie i przypisywanie utrwalonych zmiennych środowiskowych, biorąc pod uwagę wartość.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

W nowym wystąpieniu **wiersza polecenia**odczytaj zmienną środowiskową.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Tworzenie i przypisywanie utrwalonych zmiennych środowiskowych, biorąc pod uwagę wartość.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

W nowym wystąpieniu **programu Windows PowerShell**odczytaj zmienną środowiskową.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Tworzenie i przypisywanie utrwalonych zmiennych środowiskowych, biorąc pod uwagę wartość.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

W nowym wystąpieniu **Bash**, przeczytaj zmienną środowiskową.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Po ustawieniu zmiennej środowiskowej uruchom ponownie zintegrowane środowisko programistyczne (IDE), aby upewnić się, że nowo dodane zmienne środowiskowe są dostępne.

### <a name="get-environment-variable"></a>Pobierz zmienną środowiskową

Aby uzyskać zmienną środowiskową, należy ją odczytać w pamięci. W zależności od języka, którego używasz, należy wziąć pod uwagę następujące fragmenty kodu. Te fragmenty kodu pokazują, jak uzyskać `ENVIRONMENT_VARIABLE_KEY` zmienną środowiskową, `value`biorąc pod uwagę i przypisać do zmiennej o nazwie .

# <a name="c"></a>[C #](#tab/csharp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Aby uzyskać więcej <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Cel C](#tab/objective-c)

Aby uzyskać więcej <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informacji, zobacz .

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Następne kroki

* Poznaj różne [usługi Cognitive Services](welcome.md)
* Dowiedz się więcej o [sieciach wirtualnych usług Cognitive Services](cognitive-services-virtual-networks.md)
