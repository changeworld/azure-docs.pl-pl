---
title: Konfigurowanie sondy żywotności w wystąpieniu kontenera
description: Dowiedz się, jak skonfigurować sondy żywotności w celu ponownego uruchomienia kontenerów w złej kondycji w wystąpieniach kontenerów platformy Azure
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934164"
---
# <a name="configure-liveness-probes"></a>Konfigurowanie sond żywotności

Konteneryzowane aplikacje mogą działać przez dłuższy czas, co powoduje przerwanie stanów, które mogą wymagać naprawy przez ponowne uruchomienie kontenera. Wystąpienia kontenera platformy Azure obsługuje sondy żywotności, dzięki czemu można skonfigurować kontenery w grupie kontenerów, aby ponownie uruchomić, jeśli funkcje krytyczne nie działa. Sonda żywotności zachowuje się jak [sonda żywości Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

W tym artykule wyjaśniono, jak wdrożyć grupę kontenerów, która zawiera sondę żywotności, demonstrując automatyczne ponowne uruchomienie symulowanego kontenera w złej kondycji.

Wystąpienia kontenera platformy Azure obsługuje również [sondy gotowości](container-instances-readiness-probe.md), które można skonfigurować, aby upewnić się, że ruch dociera do kontenera tylko wtedy, gdy jest gotowy do niego.

> [!NOTE]
> Obecnie nie można użyć sondy żywotności w grupie kontenerów wdrożonych w sieci wirtualnej.

## <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz `liveness-probe.yaml` plik z następującym fragmentem kodu. Ten plik definiuje grupę kontenerów, która składa się z kontenera NGNIX, który ostatecznie staje się w złej kondycji.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Uruchom następujące polecenie, aby wdrożyć tę grupę kontenerów z powyższą konfiguracją YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Uruchomienie — Polecenie

Wdrożenie zawiera `command` właściwość definiującą polecenie startowe, które jest uruchamiane po pierwszym uruchomieniu kontenera. Ta właściwość akceptuje tablicę ciągów. To polecenie symuluje kontener wprowadzający w złej kondycji stan.

Najpierw rozpoczyna sesję bash i tworzy plik `healthy` o `/tmp` nazwie w katalogu. Następnie przeszukuje przez 30 sekund przed usunięciem pliku, a następnie wchodzi w 10-minutowy sen:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Żywość, polecenie

To wdrożenie `livenessProbe` definiuje, który `exec` obsługuje polecenie żywotności, który działa jako sprawdzanie żywotności. Jeśli to polecenie kończy pracę z wartością niezerową, kontener zostanie `healthy` zabity i ponownie uruchomiony, sygnalizując, że nie można odnaleźć pliku. Jeśli to polecenie zakończy się pomyślnie z kodem zakończenia 0, nie zostanie podjęta żadna akcja.

Właściwość `periodSeconds` wyznacza liveness polecenia należy wykonać co 5 sekund.

## <a name="verify-liveness-output"></a>Sprawdź dane wyjściowe żywotności

W ciągu pierwszych 30 `healthy` sekund istnieje plik utworzony przez polecenie start. Gdy polecenie żywotność `healthy` sprawdza istnienie pliku, kod stanu zwraca wartość 0, sygnalizując sukces, więc nie występuje ponowne uruchamianie.

Po 30 sekundach polecenie zaczyna się niepowodzeniem, `cat /tmp/healthy` powodując niezdrowe i zabijając zdarzenia.

Zdarzenia te można wyświetlić w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure.

![Zdarzenie w złej kondycji portalu][portal-unhealthy]

Przeglądając zdarzenia w witrynie Azure `Unhealthy` portal, zdarzenia typu są wyzwalane po awarii polecenia żywotności. Kolejne zdarzenie jest `Killing`typu, oznaczające usunięcie kontenera, aby można było rozpocząć ponowne uruchomienie. Liczba ponownego uruchamiania dla przyrostów kontenera za każdym razem, gdy wystąpi to zdarzenie.

Ponowne uruchomienie jest wykonywane w miejscu, dzięki czemu zasoby, takie jak publiczne adresy IP i zawartość specyficzne dla węzła są zachowywane.

![Licznik ponownego uruchamiania portalu][portal-restart]

Jeśli sonda żywotności stale ulegnie awarii i wyzwala zbyt wiele ponownych uruchomień, kontener wchodzi wykładnicze opóźnienie wycofywania.

## <a name="liveness-probes-and-restart-policies"></a>Sondy żywotności i zasady ponownego uruchamiania

Zasady ponownego uruchamiania zastępują zachowanie ponownego uruchamiania wyzwalane przez sondy żywotności. Na przykład jeśli ustawisz `restartPolicy = Never` *sondę żywotności i,* grupa kontenerów nie zostanie ponownie uruchomiona z powodu nieudanego sprawdzania żywotności. Grupa kontenerów jest zgodna z zasadami ponownego uruchamiania grupy kontenerów . `Never`

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach mogą wymagać sondy żywotności, aby włączyć automatyczne ponowne uruchomienie, jeśli funkcja wymagana nie działa poprawnie. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań kontenerowych w wystąpieniach kontenera platformy Azure.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
