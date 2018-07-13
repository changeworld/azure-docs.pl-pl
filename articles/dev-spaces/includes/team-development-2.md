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
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933208"
---
### <a name="run-the-service"></a>Uruchamianie usługi

1. Naciśnij klawisz F5 (lub wpisz polecenie `azds up` w oknie terminalu), aby uruchomić usługę. Usługa jest uruchamiana automatycznie w nowo wybranej przestrzeni `default/scott`. 
1. Aby potwierdzić, że usługa działa we własnej przestrzeni, możesz ponownie uruchomić polecenie `azds list`. Po pierwsze możesz zauważyć, że wystąpienie usługi `mywebapi` działa teraz w przestrzeni `default/scott` (wersja uruchomiona w przestrzeni `default` nadal działa, ale nie ma jej na liście). Po drugie adres URL punktu dostępu usługi `webfrontend` ma prefiks „scott.s.”. Ten adres URL jest unikatowy dla przestrzeni `default/scott`. Specjalny adres URL oznacza, że żądania wysyłane na adres URL Scotta będą najpierw kierowane do usług w przestrzeni `default/scott`, a jeśli to się nie powiedzie, zostaną użyte usługi w przestrzeni `default`.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich przestrzeniach. Taki routing wymaga, aby kod aplikacji przekazywał nagłówki propagowania, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-in-a-space"></a>Testowanie kodu w przestrzeni
Aby przetestować nową wersję usługi `mywebapi` z usługą `webfrontend`, otwórz w przeglądarce adres URL publicznego punktu dostępu dla usługi `webfrontend` i przejdź do strony Informacje. Powinien zostać wyświetlony Twój nowy komunikat.

Teraz usuń część „scott.s.” z adresu URL, a następnie odśwież przeglądarkę. Powinno zostać zastosowane stare zachowanie (z wersją usługi `mywebapi` działającą w przestrzeni `default`)
