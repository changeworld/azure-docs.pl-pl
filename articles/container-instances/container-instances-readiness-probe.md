---
title: Konfigurowanie sondy gotowości w wystąpieniu kontenera
description: Dowiedz się, jak skonfigurować sondę, aby upewnić się, że kontenery w wystąpieniach kontenerów platformy Azure odbierają żądania tylko wtedy, gdy są gotowe
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935676"
---
# <a name="configure-readiness-probes"></a>Konfigurowanie sond gotowości

W przypadku aplikacji konteneryzowanych, które obsługują ruch, można sprawdzić, czy kontener jest gotowy do obsługi żądań przychodzących. Wystąpienia kontenera platformy Azure obsługuje sondy gotowości do uwzględnienia konfiguracji, dzięki czemu kontener nie można uzyskać dostępu w określonych warunkach. Sonda gotowości zachowuje się jak [sonda gotowości Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Na przykład aplikacja kontenera może być konieczne załadowanie dużego zestawu danych podczas uruchamiania i nie chcesz, aby odbierał żądania w tym czasie.

W tym artykule wyjaśniono, jak wdrożyć grupę kontenerów, która zawiera sondę gotowości, dzięki czemu kontener odbiera ruch tylko wtedy, gdy sonda powiedzie się.

Wystąpienia kontenera platformy Azure obsługuje również [sondy żywotności](container-instances-liveness-probe.md), które można skonfigurować, aby spowodować automatyczne ponowne uruchomienie kontenera w złej kondycji.

> [!NOTE]
> Obecnie nie można użyć sondy gotowości w grupie kontenerów wdrożonej w sieci wirtualnej.

## <a name="yaml-configuration"></a>Konfiguracja YAML

Na przykład utwórz `readiness-probe.yaml` plik z następującym fragmentem kodu, który zawiera sondę gotowości. Ten plik definiuje grupę kontenerów, która składa się z kontenera z małą aplikacją sieci web. Aplikacja jest wdrażana z `mcr.microsoft.com/azuredocs/aci-helloworld` obrazu publicznego. Ta konteneryzowana aplikacja jest również zademonstrowana w [deploy a container instance in Azure using the Azure CLI](container-instances-quickstart.md) and other Quickstarts.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Uruchomienie — Polecenie

Wdrożenie zawiera `command` właściwość definiującą polecenie startowe, które jest uruchamiane po pierwszym uruchomieniu kontenera. Ta właściwość akceptuje tablicę ciągów. To polecenie symuluje czas, kiedy aplikacja sieci web działa, ale kontener nie jest gotowy. 

Najpierw uruchamia sesję powłoki i `node` uruchamia polecenie, aby uruchomić aplikację sieci web. Uruchamia również polecenie uśpienia przez 240 sekund, po czym `ready` tworzy `/tmp` plik o nazwie w katalogu:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Polecenie Gotowość

Ten plik YAML `readinessProbe` definiuje, `exec` który obsługuje polecenie gotowości, który działa jako sprawdzanie gotowości. W tym przykładzie testy polecenia `ready` gotowości dla `/tmp` istnienia pliku w katalogu.

Gdy `ready` plik nie istnieje, polecenie gotowości kończy działanie z wartością niezerową; kontener nadal działa, ale nie można uzyskać do niego dostępu. Gdy polecenie kończy się pomyślnie z kodem zakończenia 0, kontener jest gotowy do uzyskania dostępu. 

Właściwość `periodSeconds` wyznacza polecenie gotowości należy wykonać co 5 sekund. Sonda gotowości działa przez cały okres istnienia grupy kontenerów.

## <a name="example-deployment"></a>Przykładowe wdrożenie

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów z poprzednią konfiguracją YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Wyświetlanie kontroli gotowości

W tym przykładzie w ciągu pierwszych 240 sekund polecenie gotowości `ready` kończy się niepowodzeniem, gdy sprawdza istnienie pliku. Kod stanu zwrócił sygnały, że kontener nie jest gotowy.

Zdarzenia te można wyświetlić w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure. Na przykład portal pokazuje zdarzenia `Unhealthy` typu są wyzwalane po awarii polecenia gotowości. 

![Zdarzenie w złej kondycji portalu][portal-unhealthy]

## <a name="verify-container-readiness"></a>Weryfikowanie gotowości kontenera

Po uruchomieniu kontenera można sprawdzić, czy początkowo nie jest dostępny. Po zainicjowaniu obsługi administracyjnej uzyskaj adres IP grupy kontenerów:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Spróbuj uzyskać dostęp do witryny, gdy sonda gotowości nie powiedzie się:

```bash
wget <ipAddress>
```

Dane wyjściowe pokazują, że witryna nie jest początkowo dostępna:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Po 240 sekundach polecenie gotowości powiedzie się, sygnalizując, że kontener jest gotowy. Teraz po uruchomieniu `wget` polecenia, to powiedzie się:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Gdy kontener jest gotowy, można również uzyskać dostęp do aplikacji sieci web, przeglądając adres IP za pomocą przeglądarki sieci Web.

> [!NOTE]
> Sonda gotowości jest nadal uruchamiana przez cały okres istnienia grupy kontenerów. Jeśli polecenie gotowości nie powiedzie się w późniejszym czasie, kontener ponownie staje się niedostępny. 
> 

## <a name="next-steps"></a>Następne kroki

Sonda gotowości może być przydatne w scenariuszach obejmujących grupy wielu kontenerów, które składają się z kontenerów zależnych. Aby uzyskać więcej informacji na temat scenariuszy wielu kontenerów, zobacz [Grupy kontenerów w wystąpieniach kontenerów platformy Azure.](container-instances-container-groups.md)

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
