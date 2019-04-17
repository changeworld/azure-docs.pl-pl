---
title: Ręcznie zatrzymywać ani uruchamiać kontenery w usłudze Azure Container Instances
description: Dowiedz się, jak ręcznie zatrzymywać ani uruchamiać grupy kontenerów w usłudze Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 50f3ecf69561313a5bda67827cfb02d2f61d461f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610256"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ręcznie zatrzymywać ani uruchamiać kontenery w usłudze Azure Container Instances

[Zasady ponownego uruchamiania](container-instances-restart-policy.md) ustawienie grupy kontenerów Określa, jak uruchamianie lub zatrzymywanie domyślnie w wystąpienia kontenera. Można zastąpić domyślne ustawienie ręcznie zatrzymywania lub uruchamiania grupy kontenerów.

## <a name="stop"></a>Stop

Ręcznie zatrzymaj uruchomionej grupy kontenerów — na przykład za pomocą [az container stop] [ az-container-stop] polecenia lub witryny Azure portal. Dla niektórych obciążeń kontenerów można zatrzymać grupy kontenerów długotrwałych po określonym przedziale czasu, aby zaoszczędzić na kosztach. 

*Gdy grupa kontenerów przechodzi do stanu zatrzymania, kończy działanie i jest odtwarzana wszystkich kontenerów w grupie. Nie pozwala zachować stan kontenera.*

Chociaż recyklingowi, kontenerów w grupie kontenerów zatrzymane [zasobów](container-instances-container-groups.md#resource-allocation) pozostają przydzielone do użycia. W związku z tym rozliczenia nadal dla grupy kontenerów zatrzymany.

Akcja zatrzymania nie obowiązuje, jeśli grupa kontenerów już zakończone. (jest w stanie powodzenie lub niepowodzenie). Na przykład grupę kontenerów przy użyciu zadania uruchom raz kontenera, które został uruchomiony pomyślnie kończy się w stanie Powodzenie. Podejmuje próby zatrzymania grupy, w tym stanie nie zmieniaj stanu. 

## <a name="start"></a>Uruchamianie

Po zatrzymaniu grupy kontenerów —, albo ponieważ kontenery został przerwany w ich własnych lub grupa — zatrzymano ręcznie, kontenery można uruchomić. Na przykład użyć [az container start] [ az-container-start] polecenia lub witryny Azure portal, aby ręcznie uruchomić kontenerów w grupie. Jeśli zostanie zaktualizowany obraz kontenera dla dowolnego kontenera, zostanie ściągnięty nowego obrazu. 

Uruchamianie grupy kontenerów rozpoczyna nowe wdrożenie z taką samą konfiguracją kontenera. Ta akcja ułatwia szybkie ponowne zastosowanie konfiguracji grupy znanych kontenera, który działa zgodnie z oczekiwaniami. Nie trzeba utworzyć nową grupę kontenerów do uruchomienia tego samego obciążenia.

Wszystkie kontenery w grupie kontenerów są uruchamiane przez tę akcję. Nie można uruchomić określonego kontenera w grupie.

Po ręcznie uruchom lub uruchom ponownie grupę kontenerów, przebiegów grupy kontenera zgodnie ze skonfigurowanym ponownie uruchomić zasad.
  
## <a name="restart"></a>Ponowne uruchamianie

Możesz ponownie uruchomić grupy kontenerów jest uruchomiona — na przykład za pomocą [ponownego uruchomienia kontenera az] [ az-container-restart] polecenia. Ta akcja powoduje ponowne uruchomienie wszystkich kontenerów w grupie kontenerów. Jeśli zostanie zaktualizowany obraz kontenera dla dowolnego kontenera, zostanie ściągnięty nowego obrazu. 

Ponowne uruchamianie grupy kontenerów jest przydatne w przypadku, gdy chcesz rozwiązać problem z wdrożenia. Na przykład jeśli to ograniczenie zasobów zapobiega pomyślnemu kontenerów, ponowne uruchamianie grupy może rozwiązać problem.

Ta akcja ponownego uruchomienia wszystkich kontenerów w grupie kontenerów. Nie można ponownie uruchomić określonego kontenera w grupie.

Po ponownym uruchomieniu ręcznie grupy kontenerów, przebiegów grupy kontenera zgodnie ze skonfigurowanym ponownie uruchomić zasad.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Uruchom ponownie ustawienia zasad](container-instances-restart-policy.md) w usłudze Azure Container Instances.

Oprócz ręcznie zatrzymywania i uruchamiania grupy kontenerów przy użyciu istniejącej konfiguracji, możliwe jest również [zaktualizuj ustawienia](container-instances-update.md) uruchomionej grupy kontenerów.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
