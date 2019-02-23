---
title: Pobierz dzienniki kontenerów i zdarzeń za pomocą usługi Azure Container Instances
description: Dowiedz się, jak można debugować za pomocą dzienników kontenera i zdarzeń za pomocą usługi Azure Container Instances
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/30/2018
ms.author: juluk
ms.custom: mvc
ms.openlocfilehash: 21e75beffbf592f25257b0dca7ba48b0ffe560a8
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669775"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Pobierz dzienniki kontenerów i zdarzenia w usłudze Azure Container Instances

W przypadku kontenerów nieprawidłowo funkcjonującego start, wyświetlając jego dzienników za pomocą [dzienniki kontenerów az][az-container-logs]i przesyłania strumieniowego jego standardowe Wyjście i błąd standardowy [dołączyć az container] [az-container-attach].

## <a name="view-logs"></a>Wyświetlanie dzienników

Aby wyświetlić dzienniki, od kodu aplikacji w kontenerze, można użyć [dzienniki kontenerów az] [ az-container-logs] polecenia.

Oto dane wyjściowe dziennika kontenera opartego na zadaniach przykładu w [uruchamianie konteneryzowanych zadania w usłudze ACI](container-instances-restart-policy.md)po o przekazywani on nieprawidłowy adres URL do przetworzenia:

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

[Dołączyć az container] [ az-container-attach] polecenie dostarcza informacje diagnostyczne podczas uruchamiania kontenera. Po rozpoczęciu kontenera strumieni STDOUT i STDERR do konsoli lokalnej.

Na przykład poniżej przedstawiono dane wyjściowe z kontenera opartego na zadaniach w [uruchamianie konteneryzowanych zadania w usłudze ACI](container-instances-restart-policy.md)po o podany prawidłowy adres URL pliku duże pole tekstowe do przetworzenia:

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

## <a name="get-diagnostic-events"></a>Pobierz zdarzenia diagnostyczne

Jeśli kontener nie może pomyślnie wdrożone, należy przejrzeć informacje diagnostyczne, dostarczone przez dostawcę zasobów usługi Azure Container Instances. Aby wyświetlić zdarzenia kontenera, uruchom polecenie [az-container show] [az container show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe obejmują podstawowe właściwości kontenera, wraz z wydarzeniami wdrożenia (tutaj pokazane obcięte):

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
Dowiedz się, jak [rozwiązywania typowych problemów z kontenerów i wdrażanie](container-instances-troubleshooting.md) dla usługi Azure Container Instances.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
