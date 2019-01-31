---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e3762744d2d1f021531694b2d9a3f75ca1c3ed49
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55290994"
---
### <a name="run-the-service"></a>Uruchamianie usługi

1. Naciśnij klawisz F5 (lub wpisz polecenie `azds up` w oknie terminalu), aby uruchomić usługę. Usługa jest uruchamiana automatycznie w nowo wybranej przestrzeni `default/scott`. 
1. Aby potwierdzić, że usługa działa we własnej przestrzeni, możesz ponownie uruchomić polecenie `azds list-up`. Po pierwsze możesz zauważyć, że wystąpienie usługi `mywebapi` działa teraz w przestrzeni `default/scott` (wersja uruchomiona w przestrzeni `default` nadal działa, ale nie ma jej na liście). Po uruchomieniu polecenia `azds list-uris` zauważysz, że adresu URL punktu dostępu `webfrontend` ma prefiks „scott.s.”. Ten adres URL jest unikatowy dla przestrzeni `default/scott`. Specjalny adres URL oznacza, że żądania wysyłane na adres URL Scotta będą najpierw kierowane do usług w przestrzeni `default/scott`, a jeśli to się nie powiedzie, zostaną użyte usługi w przestrzeni `default`.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich przestrzeniach. Taki routing wymaga, aby kod aplikacji przekazywał nagłówki propagowania, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-in-a-space"></a>Testowanie kodu w przestrzeni
Aby przetestować nową wersję usługi `mywebapi` z usługą `webfrontend`, otwórz w przeglądarce adres URL publicznego punktu dostępu dla usługi `webfrontend` i przejdź do strony Informacje. Powinien zostać wyświetlony Twój nowy komunikat.

Teraz usuń część „scott.s.” z adresu URL, a następnie odśwież przeglądarkę. Powinno zostać zastosowane stare zachowanie (dla wersji usługi `mywebapi` działającej w przestrzeni `default`).
