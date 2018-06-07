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
ms.openlocfilehash: f6245a97f5d94c90e022ac509b61da477f4d9494
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823836"
---
### <a name="run-the-service"></a>Uruchom usługę

1. Naciśnij klawisz F5 (lub typu `azds up` okno terminalu) do uruchamiania usługi. Usługa zostanie automatycznie wykonane w nowo wybranego obszaru `scott`. 
1. Można potwierdzić, że usługa działa w jego własnej przestrzeni, uruchamiając `azds resource list` ponownie. Po pierwsze można zauważyć wystąpienia `mywebapi` działa teraz w `scott` miejsca (w wersji `default` miejsca jest nadal uruchomiona, ale nie ma na liście). Po drugie, adres URL z punktem dostępu `webfrontend` ma teraz prefiks `scott.s.`. Ten adres URL jest unikatowy dla `scott` miejsca i oznacza, że żądania wysyłane na "scott URL" najpierw próbują trasy w usługach `scott` miejsca, i jeśli nie istnieje żadne określonych `scott` wystąpienia usługi, żądania rezerwowe w usługach `default` miejsca.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

To wbudowane możliwości spacje deweloperów Azure umożliwia przetestowanie kodu end-to-end w obszarze udostępnionego deweloperów bez konieczności wszystkich deweloperów ponownie utworzyć pełnego stosu usług w ich miejscu. Marszruty wymaga kodu aplikacji do nagłówków propagacji do przodu, jak pokazano w poprzednim kroku w tym przewodniku.

### <a name="test-code-in-a-space"></a>Kod testu w miejscu
Aby przetestować nową wersję pakietu `mywebapi` z `webfrontend`, Otwórz w przeglądarce adres URL punktu dostępu publicznego webfrontend i przejdź do strony informacje. Wyświetlane nowe wiadomości powinna zostać wyświetlona.

Teraz, Usuń `scott.s.` część adresu URL i Odśwież przeglądarkę. Powinny pojawić się stare zachowanie (eksponowane przez `mywebapi` wersji działającej `default`)