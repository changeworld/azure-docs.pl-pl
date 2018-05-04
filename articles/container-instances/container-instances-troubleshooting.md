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
ms.openlocfilehash: a4067db9955b804f126e889fa73641f69fef56ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-container-and-deployment-issues-in-azure-container-instances"></a>Rozwiązywanie problemów i wdrożenia kontenera w wystąpień kontenera platformy Azure

W tym artykule przedstawiono sposób rozwiązywania problemów w przypadku wdrażania kontenerów do wystąpień kontenera platformy Azure. Omówiono także niektóre typowe problemy, które możesz napotkać.

## <a name="view-logs-and-stream-output"></a>Wyświetl dzienniki i strumieni wyjściowych

Jeśli masz błędna kontenera, Rozpocznij od przeglądanie jego dzienników z [az kontenera dzienniki][az-container-logs]i przesyłania strumieniowego jego wyjście standardowe i błąd standardowy z [dołączyć kontenera az] [az-container-attach].

### <a name="view-logs"></a>Wyświetlanie dzienników

Aby wyświetlić dzienniki w kodzie aplikacji w kontenerze, można użyć [dzienniki kontenera az] [ az-container-logs] polecenia.

Poniżej przedstawiono dane wyjściowe dziennika przykład opartego na zadaniach kontenera w [uruchamianie zadania konteneryzowanych w ACI](container-instances-restart-policy.md)po o przekazywani on nieprawidłowy adres URL do przetworzenia:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>Dołączanie strumieni wyjściowych

[Dołączyć kontenera az] [ az-container-attach] polecenie dostarcza informacje diagnostyczne podczas uruchamiania kontenera. Po rozpoczęciu kontenera, strumieni STDOUT i STDERR do konsoli lokalnej.

Na przykład poniżej przedstawiono dane wyjściowe z kontenera opartego na zadaniach w [uruchamianie zadania konteneryzowanych w ACI](container-instances-restart-policy.md)po o podać prawidłowy adres URL pliku tekstowego duży do przetworzenia:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Zdarzenia diagnostyczne

Kontener użytkownika nie może pomyślnie wdrożone, należy przejrzeć informacje diagnostyczne dostarczone przez dostawcę zasobów wystąpień kontenera platformy Azure. Aby wyświetlić zdarzenia z kontenera, uruchom [Pokaż kontenera az] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe zawiera właściwości core z kontenera, wraz ze zdarzeń wdrożenia (w tym miejscu pokazywane obcięty):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Typowe problemy z wdrażaniem

W poniższych sekcjach opisano typowe problemy tego konta dla większości błędy wdrożenia kontenera:

* [Nieobsługiwana wersja obrazu](#image-version-not-supported)
* [Nie można ściągania obrazu](#unable-to-pull-image)
* [Kontener stale kończy działanie i uruchamia ponownie](#container-continually-exits-and-restarts)
* [Kontener zajmuje dużo czasu do uruchomienia](#container-takes-a-long-time-to-start)
* [Błąd "Nie jest dostępna dla zasobu"](#resource-not-available-error)

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

Obrazy systemu Windows mają [uwagi dodatkowe](#use-recent-windows-images).

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

### <a name="use-recent-windows-images"></a>Użyj ostatnich obrazów systemu Windows

Azure wystąpień kontenera używa mechanizm buforowania, aby ułatwić czas uruchamiania kontenera szybkości dla obrazów oparte na niektórych obrazów systemu Windows.

Aby upewnić się o najszybszym czasie uruchomienia kontenera systemu Windows, użyj jednej z **trzech najnowszych** wersji następujących **dwa obrazy** jako obrazu podstawowego:

* [Windows Server 2016] [ docker-hub-windows-core] (tylko LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Zasób nie jest dostępny błąd

Z powodu różnych regionalnych zasobów obciążenia na platformie Azure, zostanie zgłoszony następujący błąd podczas próby wdrożenia wystąpień kontenera:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ten błąd wskazuje, czy ze względu na duże obciążenie w regionie, w którym chcesz wdrożyć, nie można przydzielić zasoby określone dla Twojego kontenera w tym czasie. W celu rozwiązania problemu, należy użyć co najmniej jeden z następujących kroków środki zaradcze.

* Sprawdź ustawienia wdrożenia kontenera objęte parametrów zdefiniowanych w [przydziały i dostępność wystąpień kontenera platformy Azure w danym regionie](container-instances-quotas.md#region-availability)
* Określ niższe ustawienia Procesora i pamięci dla kontenera
* Wdrażanie w innym regionie Azure
* Wdrażanie w późniejszym czasie

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show