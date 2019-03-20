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
ms.openlocfilehash: 5d66dcaccc6ca2e40fbd516f535ec56c1baf6b17
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195622"
---
### <a name="run-the-service"></a>Uruchamianie usługi

1. Naciśnij klawisz F5 (lub wpisz polecenie `azds up` w oknie terminalu), aby uruchomić usługę. Usługa jest uruchamiana automatycznie w nowo wybranej przestrzeni _dev/scott_. 
1. Aby potwierdzić, że usługa działa we własnej przestrzeni, możesz ponownie uruchomić polecenie `azds list-up`. Zauważysz, że wystąpienie usługi *mywebapi* działa teraz w przestrzeni _dev/scott_ (wersja uruchomiona w przestrzeni _dev_ nadal działa, ale nie ma jej na liście).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Uruchom polecenie `azds list-uris` i zwróć uwagę na adres URL punktu dostępu dla usługi *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Użyj adresu URL z prefiksem *scott.s*, aby przejść do aplikacji. Zauważ, że zaktualizowany adres URL jest nadal rozpoznawany. Ten adres URL jest unikatowy w przestrzeni _dev/scott_. Specjalny adres URL oznacza, że żądania wysyłane na „adres URL Scotta” będą najpierw kierowane do usług w przestrzeni _dev/scott_, a jeśli to się nie powiedzie, zostaną użyte usługi w przestrzeni _dev_.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

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