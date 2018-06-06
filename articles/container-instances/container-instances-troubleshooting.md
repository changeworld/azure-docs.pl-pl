---
title: Rozwiązywanie problemów z wystąpień kontenera platformy Azure
description: Dowiedz się, jak rozwiązać problemy z wystąpień kontenera platformy Azure
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 39c43c079ea4d10686bd656ba2d451ff42aac9f6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700234"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w wystąpień kontenera platformy Azure

W tym artykule przedstawiono sposób rozwiązywania typowych problemów z zarządzania lub wdrażanie kontenerów do wystąpień kontenera platformy Azure.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania określoną specyfikację kontenera, niektóre parametry wymagają przestrzegania ograniczeń dotyczących nazw. Poniżej znajdują się tabelę z określonych wymagań dotyczących kontenera właściwości grupy.
Aby uzyskać więcej informacji dotyczących konwencji nazewnictwa platformy Azure, zobacz [konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) w Centrum architektura platformy Azure.

| Zakres | Długość | Wielkość liter | Prawidłowe znaki | Sugerowane wzorca | Przykład |
| --- | --- | --- | --- | --- | --- | --- |
| Nazwa kontenera grupy | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łączniki dowolnym z wyjątkiem pierwszy i ostatni znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nazwa kontenera | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łączniki dowolnym z wyjątkiem pierwszy i ostatni znak |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontenera | Od 1 do 65535 |Liczba całkowita |Liczba całkowita od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5 – 63. |Bez uwzględniania wielkości liter |Alfanumeryczne i łączniki dowolnym z wyjątkiem pierwszy i ostatni znak |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i dowolnego miejsca za pomocą chracter "_", z wyjątkiem pierwszy i ostatni znak |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5 – 63. |Bez uwzględniania wielkości liter |Małe litery, cyfry i łączniki w dowolnym miejscu poza pierwszym lub ostatnim znakiem. Nie może zawierać dwóch łączników pod rząd. |`<name>` |`batch-output-volume` |

## <a name="image-version-not-supported"></a>Nieobsługiwana wersja obrazu

Jeśli określisz obrazu, który nie obsługuje wystąpień kontenera platformy Azure, `ImageVersionNotSupported` jest zwracany błąd. Wartość błędu jest `The version of image '{0}' is not supported.`i obecnie dotyczy obrazów 1709 systemu Windows. Aby zminimalizować ten problem, należy użyć obrazu systemu Windows LTS. Obsługa obrazów systemu Windows 1709 jest przetwarzane.

## <a name="unable-to-pull-image"></a>Nie można ściągania obrazu

W przypadku nie można ściągnąć obrazu początkowo wystąpień kontenera Azure ponowi próbę niektórych okres przed niepowodzeniem po pewnym czasie. Jeśli obraz nie może być pobierane, wynik są wyświetlane zdarzenia podobnie do następującej [Pokaż kontenera az][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Aby rozwiązać, usunięcie kontenera i ponów próbę wdrożenia, płatności zamknięcie uwagi prawidłowo wpisana nazwa obrazu.

## <a name="container-continually-exits-and-restarts"></a>Kontener stale kończy działanie i uruchamia ponownie

Jeśli Twoje kontenera jest uruchamiane w celu ukończenia i automatyczne ponowne uruchomienie, może być konieczne ustawić [ponowne uruchomienie zasad](container-instances-restart-policy.md) z **OnFailure** lub **nigdy**. Jeśli określisz **OnFailure** i nadal Zobacz ciągłe ponowne uruchomienie, może wystąpić problem z aplikacją lub skrypt wykonywany w kontenerze sieci.

Interfejs API wystąpień kontenera zawiera `restartCount` właściwości. Aby sprawdzić liczbę ponownych uruchomień kontenera, można użyć [Pokaż kontenera az] [ az-container-show] w 2.0 interfejsu wiersza polecenia platformy Azure. W poniższych przykładowe dane wyjściowe (który został obcięty do skrócenia), zobacz `restartCount` właściwości na końcu danych wyjściowych.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Większość obrazów kontener dla dystrybucje systemu Linux ustawić powłoki, takie jak bash, jako domyślne polecenie. Ponieważ powłoki samodzielnie nie jest usługą długotrwałe, kontenery natychmiast zamknąć i dzielą się na pętli ponownego uruchomienia, gdy skonfigurowana przy użyciu domyślnego **zawsze** ponowne uruchomienie zasad.

## <a name="container-takes-a-long-time-to-start"></a>Kontener zajmuje dużo czasu do uruchomienia

Są dwa podstawowe czynników, które przyczyniają się do czasu uruchomienia kontenera w wystąpień kontenera platformy Azure:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)

Obrazy systemu Windows mają [uwagi dodatkowe](#cached-windows-images).

### <a name="image-size"></a>Rozmiar obrazu

Jeśli Twoje kontenera zajmuje dużo czasu można uruchomić, ale ostatecznie zakończy się powodzeniem, przyjrzeć rozmiar obrazu kontenera. Ponieważ wystąpień kontenera platformy Azure pobiera kontener obrazu na żądanie, czas uruchamiania, które występują jest bezpośrednio powiązana z jego rozmiaru.

Rozmiar obrazu kontenera można wyświetlić przy użyciu `docker images` w Docker interfejsu wiersza polecenia polecenie:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Klucz do przechowywania małych rozmiary obrazów jest zapewnienie, że ostatecznego obrazu nie zawiera żadnych czynności, które nie są wymagane w czasie wykonywania. Jednym ze sposobów czy jest on [kompilacje wieloetapowym][docker-multi-stage-builds]. Wieloetapowym kompilacje upewnij ułatwiają zapewnienie finalnego obrazu zawiera artefakty potrzebnych dla aplikacji i nie jakiekolwiek nadmiarowe zawartości, która nie jest wymagana podczas kompilacji.

### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem zmniejszenia wpływu ściągania obrazu w chwili uruchomienia z kontenera jest do obsługi obrazów kontenera w [rejestru kontenera Azure](/azure/container-registry/) w tym samym regionie, w którym mają zostać wdrożone wystąpień kontenera. Skraca lokalizacji sieciowej, która obraz kontenera musi podróży, znacznie skrócić czas pobierania.

### <a name="cached-windows-images"></a>Pamięci podręcznej obrazów systemu Windows

Azure wystąpień kontenera używa mechanizm buforowania, aby ułatwić czas uruchamiania kontenera szybkości dla obrazów oparte na niektórych obrazów systemu Windows.

Aby upewnić się o najszybszym czasie uruchomienia kontenera systemu Windows, użyj jednej z **trzech najnowszych** wersji następujących **dwa obrazy** jako obrazu podstawowego:

* [Windows Server 2016] [ docker-hub-windows-core] (tylko LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Gotowość powolnej sieci kontenery systemu Windows

Kontenery systemu Windows może pociągnąć za sobą łączności ruchu przychodzącego lub wychodzącego do 5 sekund po utworzeniu początkowego. Po początkowej konfiguracji należy wznowić odpowiednio kontenera sieci.

## <a name="resource-not-available-error"></a>Zasób nie jest dostępny błąd

Z powodu różnych regionalnych zasobów obciążenia na platformie Azure, zostanie zgłoszony następujący błąd podczas próby wdrożenia wystąpień kontenera:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, czy ze względu na duże obciążenie w regionie, w którym chcesz wdrożyć, nie można przydzielić zasoby określone dla Twojego kontenera w tym czasie. W celu rozwiązania problemu, należy użyć co najmniej jeden z następujących kroków środki zaradcze.

* Sprawdź ustawienia wdrożenia kontenera objęte parametrów zdefiniowanych w [przydziały i dostępność wystąpień kontenera platformy Azure w danym regionie](container-instances-quotas.md#region-availability)
* Określ niższe ustawienia Procesora i pamięci dla kontenera
* Wdrażanie w innym regionie Azure
* Wdrażanie w późniejszym czasie

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [pobrać dzienniki dla kontenera & zdarzenia](container-instances-get-logs.md) debugować kontenerów.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show