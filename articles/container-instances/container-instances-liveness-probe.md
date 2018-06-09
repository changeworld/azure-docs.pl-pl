---
title: Skonfiguruj sondy liveness w wystąpień kontenera platformy Azure
description: Dowiedz się, jak skonfigurować liveness sond, aby ponownie uruchomić zła kontenery w wystąpień kontenera Azure
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 76ca4db28d99702532ae656a19f0d54b479a13fe
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249324"
---
# <a name="configure-liveness-probes"></a>Skonfiguruj liveness sondy

Konteneryzowanych aplikacje mogą działać przez dłuższy czas, co spowoduje uszkodzenie stanów, które może trzeba będzie można rozwiązać przez ponowne uruchomienie kontenera. Wystąpień kontenera platformy Azure obsługuje sond liveness do obsługują konfiguracje, tak więc jeśli krytyczne funkcje nie działa, można ponownie uruchomić z kontenera. 

W tym artykule opisano sposób wdrażania obejmuje badanie liveness prezentacja automatycznym ponownym uruchomieniu symulowane kontenera złej kondycji grupy kontenerów.

## <a name="yaml-deployment"></a>Wdrożenie yaml programu

Utwórz `liveness-probe.yaml` pliku z następującego fragmentu kodu. Ten plik definiuje grupy kontenerów, która składa się z kontenerem NGNIX, który ostatecznie staje się nieprawidłowy. 

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
  restartPolicy: Never
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Uruchom następujące polecenie, aby wdrożyć tę grupę kontenera powyższej konfiguracji yaml programu:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Polecenie uruchomienia

Wdrożenie Określa początkowy polecenia do uruchomienia po kontenera po pierwszym uruchomieniu uruchomiony, zdefiniowane przez `command` właściwość, która przyjmuje tablicę ciągów. W tym przykładzie będzie rozpocząć sesję bash i Utwórz plik o nazwie `healthy` w `/tmp` katalogu, przekazując to polecenie:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Następnie stan uśpienia 30 sekund przed usunięciem pliku następnie wprowadza uśpienia 10 minut.

### <a name="liveness-command"></a>Polecenie liveness

Definiuje to wdrożenie `livenessProbe` obsługujący `exec` liveness polecenia, który działa jako wyboru liveness. Jeśli to polecenie zakończenia o wartości innej niż zero, kontener zostaną skasowane i ponownie uruchomiona, sygnalizowania `healthy` nie można odnaleźć pliku. Jeśli to polecenie kończy się pomyślnie z kodem zakończenia 0, będą wykonywane żadne działania.

`periodSeconds` Właściwość wyznacza liveness polecenia powinien zostać wykonany co 5 sekund.

## <a name="verify-liveness-output"></a>Sprawdź dane wyjściowe liveness

W ciągu pierwszych 30 sekundach `healthy` istnieje plik utworzony przez polecenia uruchomienia. Jeśli polecenie liveness sprawdza, czy `healthy` istnienia tego pliku, kod stanu zwraca zero, sygnalizowania sukces, więc ponowne uruchomienie nie występuje.

Po 30 sekund `cat /tmp/healthy` rozpocznie się nie powiedzie się, powodując złej kondycji i zabicie zdarzenia. 

Zdarzenia te mogą być wyświetlane z portalu Azure lub 2.0 interfejsu wiersza polecenia platformy Azure.

![Zdarzenie nieprawidłowe portalu][portal-unhealthy]

Wyświetlając zdarzenia w portalu Azure zdarzeń typu `Unhealthy` zostanie wywołane po niepowodzeniu polecenia liveness. Kolejnych zdarzeń będzie typu `Killing`, co oznacza usunięcie kontenera, można rozpocząć ponowne uruchomienie. Ponowne uruchomienie liczbę elementów w kontenerze powoduje zwiększenie zawsze, gdy przyczyną.

Uruchamia ponownie zostały zakończone w miejscu, zasobów, takich jak publiczne adresy IP i zostanie zachowany zawartość właściwym dla węzła.

![Ponowne uruchomienie portalu licznika][portal-restart]

Sonda liveness stale zakończy się niepowodzeniem, wyzwala zbyt wiele ponownych uruchomień z kontenera wprowadzić wykładniczego wycofywania opóźnienia.

## <a name="liveness-probes-and-restart-policies"></a>Sondy liveness oraz zasady ponownego uruchamiania

Zasady ponownego uruchamiania zastępują zachowania ponownego uruchamiania wyzwalane przez liveness sondy. Na przykład jeśli ustawisz `restartPolicy = Never` *i* badanie liveness grupy kontenerów nie zostanie ponownie uruchomiony w przypadku wyboru liveness nie powiodło się. Grupy kontenerów zamiast tego będą spełniać wymagania ponownego uruchomienia zasady grupy kontenerów `Never`.

## <a name="next-steps"></a>Kolejne kroki

Scenariusze opartego na zadaniach może wymagać Tomasz sondowania liveness włączenie automatycznego ponownego uruchamiania wstępnych funkcja nie działa prawidłowo. Aby uzyskać więcej informacji o uruchomionych kontenerów opartego na zadaniach, zobacz [uruchamiać zadania konteneryzowanych wystąpień kontenera Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png