---
title: Konfigurowanie sondy żywotności w usłudze Azure Container Instances
description: Dowiedz się, jak skonfigurować sondy żywotności, aby ponownie uruchomić złej kondycji kontenerów w usłudze Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 89b76fc68c113b7931894c0cf003ffd846c646ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583842"
---
# <a name="configure-liveness-probes"></a>Konfigurowanie sond żywotności

Konteneryzowanych aplikacji może działać przez dłuższy czas, co w uszkodzona stanów, które może być konieczne, można naprawić przez ponowne uruchomienie kontenera. Usługa Azure Container Instances obsługuje sondy żywotności do konfiguracje można uwzględnić, tak aby kontenera można uruchomić ponownie, jeśli krytyczne funkcje nie działa.

W tym artykule opisano sposób wdrażania grupy kontenerów, która obejmuje sondy żywotności ukazujące automatycznym ponownym uruchomieniu symulowane niezdrowy kontenera.

## <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz `liveness-probe.yaml` pliku następującym fragmentem kodu. Ten plik definiuje grupy kontenerów, który składa się z kontenerem NGNIX, który ostatecznie staje się nieprawidłowy.

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

Uruchom następujące polecenie, aby wdrożyć tę grupę kontenerów w powyższej konfiguracji YAML:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Uruchomienie — polecenie

Wdrożenie definiuje wyjścia polecenia do uruchomienia, gdy kontener zaczyna się najpierw, uruchamianie, zdefiniowane przez `command` właściwość, która przyjmuje tablicę ciągów. W tym przykładzie będzie rozpocząć sesję powłoki bash i Utwórz plik o nazwie `healthy` w ramach `/tmp` katalogu, przekazując to polecenie:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Go następnie stan uśpienia na 30 sekund przed usunięciem pliku, a następnie wprowadza uśpienia 10 minut.

### <a name="liveness-command"></a>Polecenie żywotności

To wdrożenie definiuje `livenessProbe` obsługujący `exec` żywotności polecenia, który działa jako wyboru żywotności. Jeśli to polecenie kończy pracę z wartość niezerową, kontenera zostanie zabita i ponownym uruchomieniu sygnalizowanie `healthy` nie można odnaleźć pliku. Jeśli to polecenie kończy się pomyślnie z kodem zakończenia 0, zostaną wykonane żadne działanie.

`periodSeconds` Właściwość wyznacza żywotności polecenia powinien zostać wykonany co 5 sekund.

## <a name="verify-liveness-output"></a>Weryfikacja danych wyjściowych żywotności

W ciągu pierwszych 30 sekundach `healthy` istnieje plik utworzony za pomocą polecenia start. Gdy polecenie żywotności sprawdza, czy `healthy` istnienia pliku, kod stanu: zwraca wartość 0, sygnalizowanie sukces, więc nie ponowne uruchomienie wystąpienia.

Po 30 sekundach `cat /tmp/healthy` rozpocznie się nie powiedzie się, co powoduje złej kondycji i zabicia zdarzenia.

Te zdarzenia mogą być wyświetlane z witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

![Portal zdarzenie w złej kondycji][portal-unhealthy]

Wyświetlając zdarzenia w witrynie Azure portal, zdarzeń typu `Unhealthy` wyzwalany żywotności niepowodzenie polecenia. Kolejne zdarzenia będzie mieć typ `Killing`, co oznacza usuwania kontenera, więc można rozpocząć ponowne uruchomienie komputera. Ponowne uruchomienie liczbę elementów w kontenerze powoduje zwiększenie każdorazowo, gdy ten problem wystąpi.

Odbywa się ponowne uruchomienie w miejscu dzięki czemu zasoby, takie jak publiczne adresy IP i zawartość dla węzła zostaną zachowane.

![Ponowne uruchomienie portalu licznika][portal-restart]

Jeśli sonda żywotności stale kończy się niepowodzeniem, wyzwala zbyt wiele ponownych uruchomień kontenera staną się wykładniczego wycofywania opóźnienia.

## <a name="liveness-probes-and-restart-policies"></a>Sondy żywotności oraz zasady ponownego uruchamiania

Zasady ponownego uruchamiania zastępują wyzwolone przez sondy żywotności zachowanie dotyczące ponownego uruchamiania. Na przykład jeśli ustawisz `restartPolicy = Never` *i* sondy żywotności grupy kontenerów nie uruchamia ponownie w przypadku wyboru żywotności nie powiodło się. Grupy kontenerów zamiast tego będzie stosować zasady ponownego uruchamiania grupy kontenerów z `Never`.

## <a name="next-steps"></a>Kolejne kroki

Oparta na zadaniach scenariusze mogą wymagać sondy żywotności, aby włączyć automatyczne ponowne uruchamianie, jeśli wstępnie wymagane funkcja nie działa prawidłowo. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [uruchamianie zadań konteneryzowanych w usłudze Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
