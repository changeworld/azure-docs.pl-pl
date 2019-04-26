---
title: (PRZESTARZAŁE) Zarządzanie klastrem Azure DC/OS za pomocą interfejsu API REST platformy Marathon
description: Wdrażanie kontenerów do klastra usługi Azure Container Service DC/OS za pomocą interfejsu API REST platformy Marathon.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: fd109a72b092e963bc4fda7894bf67f998b7d0c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60511584"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(PRZESTARZAŁE) Zarządzanie kontenerem DC/OS za pomocą interfejsu API REST platformy Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Platforma DC/OS dostarcza środowisko wdrażania i skalowania obciążeń klastrowanych, zapewniając jednocześnie abstrakcyjność sprzętu bazowego. Ponad systemem DC/OS istnieje platforma, która zarządza planowaniem i wykonywaniem obciążeń obliczeniowych. Chociaż struktur są dostępne dla wielu popularnych zadań, ten dokument ułatwia rozpoczęcie pracy tworzenia i skalowania wdrożenia kontenerów przy użyciu interfejsu API REST platformy Marathon. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do pracy nad tymi przykładami będziesz potrzebować klastra DC/OS skonfigurowanego w usłudze kontenera platformy Azure. Potrzebna będzie także zdalna łączność z tym klastrem. Aby uzyskać więcej informacji na temat tych elementów, zobacz następujące artykuły:

* [Wdrażanie klastra usługi Azure Container Service](container-service-deployment.md)
* [Łączenie z klastrem usługi Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Dostęp do interfejsów API platformy DC/OS
Po połączeniu z klastrem usługi kontenera platformy Azure jest dostępne DC/OS i powiązanych interfejsów API REST za pośrednictwem protokołu http:\//localhost:local-port. W przykładach przedstawionych w tym dokumencie założono, że tunelowanie korzysta z portu 80. Na przykład punkty końcowe platformy Marathon można skontaktować, identyfikatorów URI rozpoczynający się za pośrednictwem protokołu http: \/ /localhost/marathon/2 /. 

Aby uzyskać więcej informacji o różnych interfejsach API, zobacz dokumentację Mesosphere dotyczącą [interfejsu API platformy Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) i [interfejsu API programu Chronos](https://mesos.github.io/chronos/docs/api.html) oraz dokumentację Apache dotyczącą [interfejsu API aplikacji Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Gromadzenie informacji z platform DC/OS i Marathon
Przed wdrożeniem kontenerów do klastra DC/OS Zbierz określone informacje o klastrze DC/OS, takie jak nazwy i stan agentów DC/OS. W tym celu wykonaj zapytanie w punkcie końcowym `master/slaves` interfejsu API REST platformy DC/OS. Jeśli operacja zostanie wykonana pomyślnie, zapytanie zwróci listę agentów DC/OS i szereg właściwości każdego z nich.

```bash
curl http://localhost/mesos/master/slaves
```

Teraz użyj punktu końcowego `/apps` platformy Marathon, aby sprawdzić bieżące wdrożenia aplikacji w klastrze DC/OS. Jeśli jest to nowy klaster, pojawi się pusta tablica aplikacji.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Wdrażanie kontenera w formacie programu Docker
Możesz wdrożyć kontenerów w formacie platformy Docker za pomocą interfejsu API REST platformy Marathon przy użyciu pliku JSON, który opisuje zamierzone wdrożenie. W poniższym przykładzie wdrożono kontener Nginx do agenta prywatnego w klastrze. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Aby wdrożyć kontener w formacie Docker, należy przechowywać plik JSON w dostępnej lokalizacji. Następnie w celu wdrożenia kontenera uruchom następujące polecenie. Określ nazwę pliku JSON (`marathon.json` w tym przykładzie).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Dane wyjściowe będą podobne do następujących:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Teraz po wykonaniu zapytania dotyczącego aplikacji na platformie Marathon nowa aplikacja pojawi się w danych wyjściowych.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Dotrzeć do kontenera

Aby sprawdzić, czy Nginx jest uruchomiona w kontenerze na jednej z prywatnych agentów w klastrze. Aby znaleźć hosta i port, na którym działa kontener, należy wyszukać Marathon uruchomione zadania podrzędne: 

```bash
curl localhost/marathon/v2/tasks
```

Znajdź wartość `host` w danych wyjściowych (podobnie do adresu IP `10.32.0.x`), a wartością `ports`.


Teraz Utwórz połączenie końcowych SSH (nie połączeń tunelowych) do zarządzania klastrem w pełni kwalifikowaną nazwę domeny. Po nawiązaniu połączenia, należy wprowadzić następujące żądania, zastępując poprawne wartości `host` i `ports`:

```bash
curl http://host:ports
```

Dane wyjściowe z serwera Nginx jest podobny do następującego:

![Serwer Nginx z kontenera](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Skalowanie kontenerów
Można użyć interfejsu API platformy Marathon, aby skalować w poziomie lub w przypadku wdrożeń aplikacji. W poprzednim przykładzie wdrożono jedno wystąpienie aplikacji. Wykonamy teraz skalowanie w poziomie, aby uzyskać trzy wystąpienia aplikacji. W tym celu utwórz plik JSON zawierający następujący tekst JSON i zapisz go w dostępnej lokalizacji.

```json
{ "instances": 3 }
```

Z połączenia tunelowania uruchom następujące polecenie, aby skalować aplikację w poziomie.

> [!NOTE]
> Identyfikator URI jest protokół http: \/ /localhost/marathon/2/aplikacji/następuje identyfikator aplikacji do skalowania. Jeśli używasz próbka Nginx, która została podana w tym miejscu będą http identyfikatora URI:\//localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Na koniec wykonaj zapytanie dotyczące aplikacji w punkcie końcowym platformy Marathon. Widoczne będą trzy kontenery Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Równoważne polecenia programu PowerShell
Te same akcje można wykonać za pomocą poleceń programu PowerShell w systemie Windows.

Aby zebrać informacje dotyczące klastra DC/OS, takie jak nazwy i stan agenta, uruchom następujące polecenie:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Kontenery w formacie programu Docker można wdrażać za pośrednictwem platformy Marathon przy użyciu pliku JSON, który opisuje zamierzone wdrożenie. W poniższym przykładzie wdrożono kontener Nginx, który powiąże port 80 agenta DC/OS z portem 80 kontenera.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Aby wdrożyć kontener w formacie Docker, należy przechowywać plik JSON w dostępnej lokalizacji. Następnie w celu wdrożenia kontenera uruchom następujące polecenie. Określ ścieżkę do pliku JSON (`marathon.json` w tym przykładzie).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Interfejs API platformy Marathon umożliwia także skalowanie w poziomie oraz skalowanie na zewnątrz wdrożeń aplikacji. W poprzednim przykładzie wdrożono jedno wystąpienie aplikacji. Wykonamy teraz skalowanie w poziomie, aby uzyskać trzy wystąpienia aplikacji. W tym celu utwórz plik JSON zawierający następujący tekst JSON i zapisz go w dostępnej lokalizacji.

```json
{ "instances": 3 }
```

Uruchom następujące polecenie, aby skalować aplikację w poziomie:

> [!NOTE]
> Identyfikator URI jest protokół http: \/ /localhost/marathon/2/aplikacji/następuje identyfikator aplikacji do skalowania. W tym miejscu używasz przykładowym Nginx, identyfikator URI będzie mieć http:\//localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Kolejne kroki
* [Więcej informacji na temat punktów końcowych HTTP platformy Mesos](https://mesos.apache.org/documentation/latest/endpoints/)
* [Więcej informacji na temat interfejsu API REST platformy Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

