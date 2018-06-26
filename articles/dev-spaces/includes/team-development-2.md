---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936401"
---
### <a name="run-the-service"></a>Uruchom usługę

1. Naciśnij klawisz F5 (lub typu `azds up` okno terminalu) do uruchamiania usługi. Usługa zostanie automatycznie wykonane w nowo wybranego obszaru `default/scott`. 
1. Można potwierdzić, że usługa działa w jego własnej przestrzeni, uruchamiając `azds list` ponownie. Po pierwsze można zauważyć wystąpienia `mywebapi` działa teraz w `default/scott` miejsca (w wersji `default` jest nadal uruchomiony, ale nie ma na liście). Po drugie, adres URL z punktem dostępu `webfrontend` jest prefiksem tekst "scott.s.". Ten adres URL jest unikatowy dla `default/scott` miejsca. Adres URL specjalne oznacza, że żądania wysyłane na "scott URL" spróbuje pierwszy trasy w usługach `default/scott` miejsca, ale jeśli ten sposób również zawiedzie, będzie wracają do usług w `default` miejsca.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tej wbudowanej funkcji miejsca do deweloperów Azure umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności wszystkich deweloperów ponownie utworzyć pełnego stosu usług w ich miejscu. Marszruty wymaga kodu aplikacji do nagłówków propagacji do przodu, jak pokazano w poprzednim kroku w tym przewodniku.

### <a name="test-code-in-a-space"></a>Kod testu w miejscu
Aby przetestować nową wersję pakietu `mywebapi` z `webfrontend`, Otwórz w przeglądarce adres URL punktu dostępu publicznego dla `webfrontend` i przejdź do strony informacje. Wyświetlane nowe wiadomości powinna zostać wyświetlona.

Teraz Usuń "scott.s." część adresu URL i Odśwież przeglądarkę. Powinny pojawić się stare zachowanie (z `mywebapi` wersji działającej `default`)
