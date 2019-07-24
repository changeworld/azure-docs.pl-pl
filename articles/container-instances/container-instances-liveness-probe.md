---
title: Skonfiguruj sondy na żywo w Azure Container Instances
description: Dowiedz się, jak skonfigurować sondy na żywo w celu ponownego uruchomienia kontenerów w złej kondycji w Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325863"
---
# <a name="configure-liveness-probes"></a>Konfigurowanie sond żywotności

Aplikacje kontenera mogą działać przez dłuższy czas, co może wymagać naprawy przez ponowne uruchomienie kontenera. Azure Container Instances obsługuje sondy na żywo w celu uwzględnienia konfiguracji, aby umożliwić ponowne uruchomienie kontenera w przypadku braku działania krytycznego.

W tym artykule wyjaśniono, jak wdrożyć grupę kontenerów zawierającą sondę na żywo, pokazującą automatyczne ponowne uruchomienie symulowanego kontenera w złej kondycji.

## <a name="yaml-deployment"></a>Wdrożenie YAML

`liveness-probe.yaml` Utwórz plik z następującym fragmentem kodu. Ten plik definiuje grupę kontenerów, która składa się z kontenera NGNIX, który ostatecznie stał się w złej kondycji.

```yaml
apiVersion: 2018-06-01
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

### <a name="start-command"></a>Uruchom polecenie

Wdrożenie definiuje uruchamianie polecenia, które ma być uruchamiane podczas pierwszego uruchomienia kontenera, zdefiniowane przez `command` właściwość, która akceptuje tablicę ciągów. W tym przykładzie zostanie uruchomiona sesja bash i zostanie utworzony plik o nazwie `healthy` `/tmp` w katalogu przez przekazanie tego polecenia:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Następnie zostanie ona przesunięta przez 30 sekund przed usunięciem pliku, a następnie zostanie przesunięta o 10 minut w stan uśpienia.

### <a name="liveness-command"></a>Dynamiczność — polecenie

To wdrożenie definiuje `livenessProbe` , które `exec` obsługuje polecenie na żywo, które działa jako sprawdzenie na żywo. Jeśli to polecenie kończy się wartością różną od zera, kontener zostanie zamknięty i ponownie uruchomiony, sygnalizując, że `healthy` nie można znaleźć pliku. Jeśli to polecenie zakończy się pomyślnie z kodem zakończenia 0, nie zostanie podjęta żadna akcja.

`periodSeconds` Właściwość określa, że polecenie ma być wykonywane co 5 sekund.

## <a name="verify-liveness-output"></a>Sprawdzanie danych wyjściowych na żywo

W ciągu pierwszych 30 sekund `healthy` plik utworzony przez polecenie uruchamiania istnieje. Gdy na żywo polecenie sprawdza `healthy` obecność pliku, kod stanu zwraca zero, sygnalizowanie sukcesu, dlatego nie następuje ponowne uruchomienie.

Po 30 sekundach `cat /tmp/healthy` rozpocznie się niepowodzenie, powodując wystąpienie złej kondycji i kasowanie zdarzeń.

Te zdarzenia można wyświetlić w Azure Portal lub interfejs wiersza polecenia platformy Azure.

![Zdarzenie w złej kondycji portalu][portal-unhealthy]

Wyświetlając zdarzenia w Azure Portal, zdarzenia typu `Unhealthy` będą wyzwalane w przypadku niepowodzenia polecenia na żywo. Kolejne zdarzenie będzie typu `Killing`, co oznacza usunięcie kontenera, aby można było rozpocząć ponowne uruchomienie. Liczba ponownych uruchomień dla kontenera zostanie zwiększona przy każdym wystąpieniu tego problemu.

Ponowne uruchomienia są wykonywane w miejscu, dzięki czemu zasoby, takie jak publiczne adresy IP i zawartość specyficzną dla węzła, zostaną zachowane.

![Licznik ponownego uruchamiania portalu][portal-restart]

Jeśli sonda na żywo zakończy się niepowodzeniem i wyzwala zbyt wiele ponownych uruchomień, kontener wprowadzi odwrotne opóźnienie.

## <a name="liveness-probes-and-restart-policies"></a>Sondy na żywo i zasady ponownego uruchamiania

Zasady ponownego uruchamiania zastępują zachowanie ponownego uruchamiania wyzwalane przez sondy na żywo. Na przykład jeśli ustawisz sondę `restartPolicy = Never` na żywo, Grupa kontenerów nie zostanie ponownie uruchomiona w przypadku niepowodzenia sprawdzenia na żywo. Grupa kontenerów będzie w zamian zgodna z zasadami `Never`ponownego uruchamiania grupy kontenerów.

## <a name="next-steps"></a>Kolejne kroki

Scenariusze oparte na zadaniach mogą wymagać sondy na żywo, aby włączyć automatyczne ponowne uruchamianie, jeśli funkcja wstępna nie działa prawidłowo. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie kontenerów zadań w Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
