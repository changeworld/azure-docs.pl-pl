---
title: Rozwiązywanie problemów w usłudze Azure Container Instances
description: Dowiedz się, jak rozwiązywać problemy związane z usługą Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 550b53cf40133c8a67306c61cbfa7dae21be4648
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39164079"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Rozwiązywanie typowych problemów w usłudze Azure Container Instances

W tym artykule pokazano, jak rozwiązywać typowe problemy dotyczące zarządzania lub wdrażanie kontenerów w usłudze Azure Container Instances.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Podczas definiowania specyfikacji usługi kontenera, niektóre parametry wymagają gotowość do ograniczenia nazewnictwa. Poniżej przedstawiono tabelę z określonych wymagań dotyczących kontenera właściwości grupy. Aby uzyskać więcej informacji na temat konwencji nazewnictwa platformy Azure, zobacz [konwencje nazewnictwa] [ azure-name-restrictions] w Centrum architektury platformy Azure.

| Zakres | Długość | Wielkość liter | Prawidłowe znaki | Sugerowany wzorzec | Przykład |
| --- | --- | --- | --- | --- | --- | --- |
| Nazwa grupy kontenerów | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nazwa kontenera | 1-64 |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Porty kontenera | Od 1 do 65535 |Liczba całkowita |Liczba całkowita od 1 do 65535 |`<port-number>` |`443` |
| Etykieta nazwy DNS | 5 – 63. |Bez uwzględniania wielkości liter |Alfanumeryczne i łącznik w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>` |`frontend-site1` |
| Zmienna środowiskowa | 1-63 |Bez uwzględniania wielkości liter |Alfanumeryczne i podkreślenia (_) w dowolnym miejscu poza pierwszym ani ostatnim znakiem. |`<name>` |`MY_VARIABLE` |
| Nazwa woluminu | 5 – 63. |Bez uwzględniania wielkości liter |Małe litery i cyfry i łączniki w dowolnym miejscu poza pierwszym ani ostatnim znakiem. Nie może zawierać dwóch łączników pod rząd. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Wersja systemu operacyjnego obrazu nie jest obsługiwane

Jeśli określisz obraz, który nie obsługuje usługi Azure Container Instances, `OsVersionNotSupported` zostanie zwrócony błąd. Błąd jest podobne do następujących, gdzie `{0}` nazywa się obraz próbujesz wdrożyć:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Ten błąd występuje najczęściej w przypadku wdrażania obrazów Windows, które są oparte na półroczny kanał (Konsola SAC) wersji. Na przykład Windows w wersji 1709 i 1803 są wersje SAC i wygenerować ten błąd, po wdrożeniu.

Usługa Azure Container Instances obsługuje Windows oparte tylko na wersji Long-Term Servicing Channel (LTSC). Aby rozwiązać ten problem, gdy wdrażanie kontenerów Windows, należy zawsze wdrażać obrazy na podstawie LTSC.

Aby uzyskać szczegółowe informacje dotyczące LTSC i konsola SAC wersji systemu Windows, zobacz [Omówienie systemu Windows Server z kanału semi-Annual Channel][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Nie można do ściągania obrazu

W przypadku początkowo nie można ściągnąć obrazu usługi Azure Container Instances ponawia próbę w okresie czasu. Jeśli operacja ściągnięcia obrazu zakończy się niepowodzeniem, ACI ostatecznie zakończy się niepomyślnie wdrożenia i może zostać wyświetlony `Failed to pull image` błędu.

Aby rozwiązać ten problem, usuń wystąpienia kontenera i ponów próbę wdrożenia. Upewnij się, że obraz, który istnieje w rejestrze i że nazwa obrazu została wpisana poprawnie.

Jeśli nie można ściągnąć obrazu, zdarzenia, podobnie do poniższego są wyświetlane w danych wyjściowych [az container show][az-container-show]:

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

## <a name="container-continually-exits-and-restarts"></a>Kontener stale kończy działanie i uruchamia ponownie

Jeśli kontener zostaje ukończone i zostanie automatycznie uruchomiony ponownie, konieczne może być ustawiony [zasady ponownego uruchamiania](container-instances-restart-policy.md) z **OnFailure** lub **nigdy**. Jeśli określisz **OnFailure** i nadal znaleźć ciągłe ponowne uruchomienie, może to być problem z aplikacją lub skrypt wykonywany w kontenerze.

Interfejs API wystąpienia kontenera obejmuje `restartCount` właściwości. Aby sprawdzić liczbę ponownych uruchomień dla kontenera, można użyć [az container show] [ az-container-show] polecenia w interfejsie wiersza polecenia platformy Azure. W poniższych przykładowych danych wyjściowych (który został obcięty dla zwięzłości), zobacz `restartCount` właściwości na końcu danych wyjściowych.

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
> Większość obrazów kontenerów dystrybucje systemu Linux ustawić powłoki, takie jak bash, jako domyślnego polecenia. Ponieważ powłoki samodzielnie nie jest usługą długotrwałych, te kontenery natychmiast zakończyć i wpadają w pętli ponownego uruchamiania w przypadku skonfigurowania przy użyciu domyślnego **zawsze** zasady ponownego uruchamiania.

## <a name="container-takes-a-long-time-to-start"></a>Kontener zajmuje dużo czasu do uruchomienia

Są dwa podstawowe czynniki, które przyczyniają się do czasu uruchamiania kontenera w usłudze Azure Container Instances:

* [Rozmiar obrazu](#image-size)
* [Lokalizacja obrazu](#image-location)

Windows obrazy mają [dodatkowych kwestii dotyczących](#cached-windows-images).

### <a name="image-size"></a>Rozmiar obrazu

Jeśli kontener zajmuje dużo czasu, aby rozpocząć, ale ostatecznie zakończy się powodzeniem, zacznij od przejrzenia rozmiar obrazu kontenera. Ponieważ usługi Azure Container Instances ściąga obraz kontenera na żądanie, czas uruchamiania, który zostanie wyświetlony jest bezpośrednio związane z jego rozmiaru.

Rozmiar obrazu kontenera można wyświetlić za pomocą `docker images` polecenia w interfejsie wiersza polecenia platformy Docker:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Klucz zachowanie małe rozmiary obrazów jest zapewnienie, że końcowy obraz nie zawiera wszystko, co nie jest wymagane w czasie wykonywania. Jednym ze sposobów, aby zrobić to za pomocą [kompilacje wieloetapowych][docker-multi-stage-builds]. Wieloetapowe opiera się upewnij, które ułatwiają upewnić się, że finalnego obrazu zawiera artefakty potrzebne dla aplikacji, a nie jakiekolwiek nadmiarowe zawartością, która była wymagana w czasie kompilacji.

### <a name="image-location"></a>Lokalizacja obrazu

Innym sposobem, aby zmniejszyć wpływ ściągania obrazów na czas uruchamiania przez kontener jest hostowanie obrazu kontenera w [usługi Azure Container Registry](/azure/container-registry/) w tym samym regionie, w którym mają zostać wdrożone wystąpienia kontenera. Skraca ścieżkę sieciową wymagającym podróż, obraz kontenera, znacznie skrócić czas pobierania.

### <a name="cached-windows-images"></a>Pamięci podręcznej obrazów Windows

Usługa Azure Container Instances używa mechanizm buforowania, aby pomóc szybkość czas uruchamiania kontenera obrazów opartych na obrazach niektórych Windows.

Aby zapewnić najlepszy czas uruchamiania kontenera Windows, użyj jednej z **trzy najbardziej aktualne** wersje następujących **dwa obrazy** jako obraz podstawowy:

* [System Windows Server 2016] [ docker-hub-windows-core] (tylko LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Gotowość wolną sieć kontenery Windows

Kontenery Windows może spowodować naliczenie bez połączenia przychodzącego i wychodzącego do 5 sekund po utworzeniu początkowego. Po przeprowadzeniu instalacji początkowej kontenerowa powinna zostać wznowiona odpowiednio.

## <a name="resource-not-available-error"></a>Zasób błąd nie jest dostępna

Z powodu różnych regionalnych zasobów obciążenia na platformie Azure, możesz otrzymać następujący błąd podczas próby wdrożenia wystąpienia kontenera:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, że z powodu dużego obciążenia w regionie, w którym próbujesz wdrożyć, nie można przydzielić zasoby określone dla kontenera usługi w tym czasie. Aby pomóc rozwiązać problem, należy użyć co najmniej jeden z następujących czynności zaradcze.

* Sprawdź ustawienia wdrożenia kontenera mieszczą się w parametrów zdefiniowanych w [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md#region-availability)
* Określ niższe ustawienia Procesora i pamięci dla kontenera
* Wdrażanie w innym regionie platformy Azure
* Wdrażanie w późniejszym czasie

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Nie można nawiązać połączenie z podstawowego interfejsu API Docker lub uruchamiaj kontenery uprzywilejowanych

Usługa Azure Container Instances nie ujawnia bezpośredni dostęp do podstawowej infrastruktury, który jest hostem grupy kontenerów. Obejmuje to dostęp do interfejsu API platformy Docker zainstalowany na hoście kontenera, a uprzywilejowanych kontenerów. Jeśli potrzebujesz interakcji platformy Docker, sprawdź [Dokumentacja referencyjna REST](https://aka.ms/aci/rest) obsługuje interfejs API ACI. Jeśli jest coś, co jest Brak, Prześlij żądanie na [fora z opiniami ACI](https://aka.ms/aci/feedback).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [pobrać dzienniki kontenera i zdarzenia](container-instances-get-logs.md) debugować swoje kontenery.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
