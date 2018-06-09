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
ms.openlocfilehash: 78dca327a470394d19e6befc6578abf2d499850c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247589"
---
### <a name="run-the-service"></a>Uruchom usługę

1. Naciśnij klawisz F5 (lub typu `azds up` okno terminalu) do uruchamiania usługi. Usługa zostanie automatycznie wykonane w nowo wybranego obszaru `scott`. 
1. Można potwierdzić, że usługa działa w jego własnej przestrzeni, uruchamiając `azds space list` ponownie. Po pierwsze można zauważyć wystąpienia `mywebapi` działa teraz w `scott` miejsca (w wersji `default` jest nadal uruchomiony, ale nie ma na liście). Po drugie, adres URL z punktem dostępu `webfrontend` jest prefiksem tekst "scott.s.". Ten adres URL jest unikatowy dla `scott` miejsca. Adres URL specjalne oznacza, że żądania wysyłane na "scott URL" spróbuje pierwszy trasy w usługach `scott` miejsca, ale jeśli ten sposób również zawiedzie, będzie wracają do usług w `default` miejsca.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Tej wbudowanej funkcji miejsca do deweloperów Azure umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności wszystkich deweloperów ponownie utworzyć pełnego stosu usług w ich miejscu. Marszruty wymaga kodu aplikacji do nagłówków propagacji do przodu, jak pokazano w poprzednim kroku w tym przewodniku.

### <a name="test-code-in-a-space"></a>Kod testu w miejscu
Aby przetestować nową wersję pakietu `mywebapi` z `webfrontend`, Otwórz w przeglądarce adres URL punktu dostępu publicznego dla `webfrontend` i przejdź do strony informacje. Wyświetlane nowe wiadomości powinna zostać wyświetlona.

Teraz Usuń "scott.s." część adresu URL i Odśwież przeglądarkę. Powinny pojawić się stare zachowanie (z `mywebapi` wersji działającej `default`)
