---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157876"
---
### <a name="run-the-service"></a>Uruchamianie usługi

Aby uruchomić usługę, należy nacisnąć klawisz F5 (lub typu `azds up` w oknie terminalu) do uruchamiania usługi. Usługa jest uruchamiana automatycznie w nowo wybranej przestrzeni _dev/scott_. Upewnij się, że usługa jest uruchomiona w własną przestrzeń, uruchamiając `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Zwróć uwagę, wystąpienie *mywebapi* działa teraz w _dev/scott_ miejsca. Wersję uruchomionego _dev_ nadal działa, ale nie ma na liście.

Lista adresów URL dla bieżącego miejsca, uruchamiając `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Zwróć uwagę, adres URL punktu dostępu publicznego dla *webfrontend* jest poprzedzony znakiem *scott.s*. Ten adres URL jest unikatowy w przestrzeni _dev/scott_. Ten prefiks adresu URL informuje kontroler danych przychodzących w celu kierowania żądań trasy do _dev/scott_ wersji usługi. Gdy żądania z tego adresu URL jest obsługiwany przez deweloperów miejsca do magazynowania, kontroler danych przychodzących po raz pierwszy próbuje Kieruj żądania do *webfrontend* w _dev/scott_ miejsca. W przypadku niepowodzenia żądania będą kierowane do *webfrontend* w _dev_ miejsca jako rezerwowe. Należy również zauważyć, ma dostęp do usługi za pośrednictwem hosta lokalnego przy użyciu rozwiązania Kubernetes adres URL localhost *do przodu portu* funkcji. Aby uzyskać więcej informacji dotyczących adresów URL i routing w Azure Dev miejsca do magazynowania, zobacz [jak Azure Dev miejsca do magazynowania działa i jest skonfigurowany](../articles/dev-spaces/how-dev-spaces-works.md).



![Routing miejsca](../articles/dev-spaces/media/common/Space-Routing.png)

Ta wbudowana funkcja usługi Azure Dev Spaces umożliwia testowanie kodu w udostępnionej przestrzeni bez konieczności ponownego tworzenia pełnych stosów usług przez wszystkich deweloperów w ich przestrzeniach. Taki routing wymaga, aby kod aplikacji przekazywał nagłówki propagowania, jak pokazano w poprzednim kroku tego przewodnika.

### <a name="test-code-in-a-space"></a>Testowanie kodu w przestrzeni
Aby przetestować nową wersję aplikacji *mywebapi* z aplikacją *webfrontend*, otwórz w przeglądarce adres URL publicznego punktu dostępu dla aplikacji *webfrontend* i przejdź do strony Informacje. Powinien zostać wyświetlony Twój nowy komunikat.

Teraz usuń część „scott.s.” z adresu URL, a następnie odśwież przeglądarkę. Powinno zostać zastosowane stare zachowanie (dla wersji usługi *mywebapi* działającej w przestrzeni _dev_).

Mamy nadzieję, że po utworzeniu przestrzeni _dev_, która zawsze zawiera najnowsze zmiany, i zakładając, że aplikacja jest zaprojektowana tak, aby wykorzystywać oparty na przestrzeniach routing usługi DevSpace, łatwo będzie zobaczyć, że usługa Dev Spaces może znacznie ułatwić testowanie nowych funkcji w kontekście większej aplikacji. Zamiast wdrażać _wszystkie_ usługi w przestrzeni prywatnej, możesz utworzyć przestrzeń prywatną, która wywodzi się z przestrzeni _dev_, i uruchamiać tylko te usługi, nad którymi tak naprawdę pracujesz. Infrastruktura routingu usługi Dev Space zajmie się resztą, wykorzystując tyle usług spoza przestrzeni prywatnej, ile uda jej się znaleźć, jednocześnie przyjmując domyślnie najnowszą wersję uruchomioną w przestrzeni _dev_. A co najlepsze, _wielu_ deweloperów może aktywnie programować różne usługi w tym samym czasie we własnych przestrzeniach, nie przeszkadzając sobie nawzajem.

### <a name="well-done"></a>Gotowe!
Przewodnik Wprowadzenie został ukończony! W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie usługi Azure Dev Spaces za pomocą zarządzanego klastra Kubernetes na platformie Azure.
> * Iteracyjne tworzenie kodu w kontenerach.
> * Niezależne tworzenie dwóch oddzielnych usług i wywoływanie innej usługi przy użyciu funkcji odnajdywania usług DNS w środowisku Kubernetes.
> * Efektywne tworzenie i testowanie kodu w środowisku zespołu.
> * Ustalanie punktu odniesienia funkcjonalności za pomocą usługi Dev Spaces w celu łatwego testowania izolowanych zmian w kontekście większej aplikacji mikrousług.

Teraz, gdy zostały zbadane Azure Dev miejsca do magazynowania [udostępniać obszaru dev członek zespołu](../articles/dev-spaces/how-to/share-dev-spaces.md) i rozpocząć współpracę.

## <a name="clean-up"></a>Czyszczenie
Aby całkowicie usunąć z klastra wystąpienie usługi Azure Dev Spaces, w tym wszystkie przestrzenie deweloperów i usługi działające w ich obrębie, należy użyć polecenia `az aks remove-dev-spaces`. Pamiętaj, że ta akcja jest nieodwracalna. Możesz ponownie dodać obsługę usługi Azure Dev Spaces w klastrze, ale wszystko trzeba będzie zacząć od początku. Stare usługi i przestrzenie nie zostaną przywrócone.

Poniższy przykład wyświetla listę kontrolerów usługi Azure Dev Spaces w Twojej aktywnej subskrypcji, a następnie usuwa kontroler usługi Azure Dev Spaces, który jest skojarzony z klastrem usługi AKS „myaks” w grupie zasobów „myaks-rg”.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```