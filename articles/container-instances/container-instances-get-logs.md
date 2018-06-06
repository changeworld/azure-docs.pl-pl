---
title: Pobierz dzienniki kontenera i zdarzeń za pomocą wystąpień kontenera platformy Azure
description: Dowiedz się, jak można debugować z dzienników kontenera i zdarzeń za pomocą wystąpień kontenera platformy Azure
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/30/18
ms.author: juluk
ms.custom: mvc
ms.openlocfilehash: e10abc0e1b8c163af5d0d42cccfe62b05557b0a4
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701391"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Pobieranie dzienników kontenera i zdarzeń w wystąpień kontenera platformy Azure

Jeśli masz błędna kontenera, Rozpocznij od przeglądanie jego dzienników z [az kontenera dzienniki][az-container-logs]i przesyłania strumieniowego jego wyjście standardowe i błąd standardowy z [dołączyć kontenera az] [az-container-attach].

## <a name="view-logs"></a>Wyświetlanie dzienników

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

## <a name="attach-output-streams"></a>Dołączanie strumieni wyjściowych

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

Kontener użytkownika nie może pomyślnie wdrożone, należy przejrzeć informacje diagnostyczne dostarczone przez dostawcę zasobów wystąpień kontenera platformy Azure. Aby wyświetlić zdarzenia z kontenera, uruchom polecenie [az kontenera — Pokaż] [Pokaż kontenera az]:

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
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [rozwiązywania typowych problemów z kontenera i wdrażania](container-instances-troubleshooting.md) dla wystąpień kontenera platformy Azure.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs