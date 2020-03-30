---
title: Ręczne zatrzymywania lub uruchamiania grupy kontenerów
description: Dowiedz się, jak ręcznie zatrzymać lub uruchomić grupę kontenerów w wystąpieniach kontenera platformy Azure.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533421"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ręczne zatrzymywać lub uruchamiać kontenery w wystąpieniach kontenerów platformy Azure

Ustawienie [zasad ponownego uruchamiania](container-instances-restart-policy.md) grupy kontenerów określa, jak domyślnie rozpoczynają się lub zatrzymują wystąpienia kontenerów. Ustawienie domyślne można zastąpić ręcznie, zatrzymując lub uruchamiając grupę kontenerów.

## <a name="stop"></a>Stop

Ręcznie zatrzymaj uruchomionej grupy kontenerów — na przykład za pomocą polecenia [zatrzymania kontenera az][az-container-stop] lub witryny Azure portal. W przypadku niektórych obciążeń kontenerów można zatrzymać długotrwałą grupę kontenerów po zdefiniowanym okresie, aby zaoszczędzić na kosztach. 

*Gdy grupa kontenerów wchodzi w stan Zatrzymane, kończy i odtwarza wszystkie kontenery w grupie. Nie zachowuje stanu kontenera.*

Gdy kontenery są poddane recyklingowi, [zasoby](container-instances-container-groups.md#resource-allocation) są cofnięte alokacji i rozliczeń zatrzymuje dla grupy kontenerów.

Akcja zatrzymania nie ma wpływu, jeśli grupa kontenerów została już zakończona (jest w stanie zakończone pomyślnie lub niepowodzeniem). Na przykład grupa kontenerów z zadaniami kontenera uruchamianego, która została pomyślnie zakończona w stanie pomyślnie. Próby zatrzymania grupy w tym stanie nie zmieniają stanu. 

## <a name="start"></a>Rozpoczęcie

Gdy grupa kontenerów zostanie zatrzymana — ponieważ kontenery zostały zakończone samodzielnie lub ręcznie zatrzymano grupę — można uruchomić kontenery. Na przykład użyj polecenia [uruchamiania kontenera az][az-container-start] lub witryny Azure portal, aby ręcznie uruchomić kontenery w grupie. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie pobrany nowy obraz. 

Uruchamianie grupy kontenerów rozpoczyna nowe wdrożenie z tą samą konfiguracją kontenera. Ta akcja może pomóc szybko ponownie użyć znanej konfiguracji grupy kontenerów, która działa zgodnie z oczekiwaniami. Nie trzeba tworzyć nową grupę kontenerów, aby uruchomić to samo obciążenie.

Wszystkie kontenery w grupie kontenerów są uruchamiane przez tę akcję. Nie można uruchomić określonego kontenera w grupie.

Po ręcznym uruchomieniu lub ponownym uruchomieniu grupy kontenerów grupa kontenerów jest uruchamiana zgodnie ze skonfigurowaną zasadą ponownego uruchamiania.
  
## <a name="restart"></a>Ponowne uruchamianie

Można ponownie uruchomić grupę kontenerów, gdy jest uruchomiona — na przykład za pomocą polecenia [az container restart.][az-container-restart] Ta akcja uruchamia ponownie wszystkie kontenery w grupie kontenerów. Jeśli obraz kontenera dla dowolnego kontenera zostanie zaktualizowany, zostanie pobrany nowy obraz. 

Ponowne uruchomienie grupy kontenerów jest przydatne, gdy chcesz rozwiązać problem z wdrażaniem. Na przykład jeśli tymczasowe ograniczenie zasobów uniemożliwia pomyślne uruchomienie kontenerów, ponowne uruchomienie grupy może rozwiązać problem.

Ta akcja jest uruchamiana ponownie przez wszystkie kontenery w grupie kontenerów. Nie można ponownie uruchomić określonego kontenera w grupie.

Po ręcznym ponownym uruchomieniu grupy kontenerów grupa kontenerów jest uruchamiana zgodnie ze skonfigurowaną zasadą ponownego uruchamiania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [ustawieniach zasad ponownego uruchamiania](container-instances-restart-policy.md) w wystąpieniach kontenera platformy Azure.

Oprócz ręcznego zatrzymywania i uruchamiania grupy kontenerów z istniejącą konfiguracją można [zaktualizować ustawienia](container-instances-update.md) uruchomionej grupy kontenerów.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
