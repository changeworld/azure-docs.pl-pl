---
title: Usługa Azure repozytoriów Container Registry w witrynie Azure portal
description: Jak wyświetlić repozytoriów usługi Azure Container Registry w witrynie Azure portal.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 685c978ff206e75d770918f2528a826ad522b706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60427105"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Wyświetlanie repozytoriów rejestru kontenerów w witrynie Azure portal

Usługa Azure Container Registry umożliwia przechowywanie Docker obrazów kontenerów w repozytoriach. Dzięki przechowywaniu obrazów w repozytoriach, można przechowywać grupy obrazów (lub wersji obrazów) w środowisku izolowanym. Można określić tych repozytoriów, gdy wypychanie obrazów do rejestru i przeglądać ich zawartość w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

* **Rejestr kontenerów**: Tworzenie rejestru kontenerów w ramach subskrypcji platformy Azure. Na przykład użyć [witryny Azure portal](container-registry-get-started-portal.md) lub [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Interfejs CLI platformy docker**: Zainstaluj [Docker] [ docker-install] na komputerze lokalnym, który zapewnia interfejs wiersza polecenia platformy Docker.
* **Obraz kontenera**: Wypychanie obrazu do rejestru kontenerów. Aby uzyskać wskazówki na temat sposobu wypychania i ściągania obrazów, zobacz [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Wyświetlanie repozytoriów w witrynie Azure portal

Można wyświetlić listę repozytoriów hostowania obrazów, a także znaczniki obrazów, w witrynie Azure portal.

Jeśli wykonano kroki opisane w [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md) (i nie został później usunąć obraz) powinien mieć obraz Nginx w rejestrze kontenera. Instrukcje w tym artykule określony tag obrazu z przestrzeni nazw "Przykłady" w `/samples/nginx`. Jako odświeżacz [docker push] [ docker-push] zostało określone w tym artykule polecenie:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Ponieważ usługa Azure Container Registry obsługuje takie wielopoziomowe przestrzenie nazw repozytoriów, można określić zakres kolekcje obrazów związanych z określoną aplikacją lub kolekcje aplikacji do tworzenia różnych lub zespołów operacyjnych. Aby dowiedzieć się więcej o repozytoriów w rejestry kontenerów, zobacz [rejestry kontenerów prywatnego platformy Docker na platformie Azure](container-registry-intro.md).

Aby wyświetlić repozytorium:

1. Zaloguj się do [witryny Azure portal][portal]
1. Wybierz **usługi Azure Container Registry** do którego wypychane obraz Nginx
1. Wybierz **repozytoriów** umożliwia wyświetlenie listy repozytoriów, które zawierają obrazów w rejestrze
1. Wybierz repozytorium, aby wyświetlić tagi obraz w ramach tego repozytorium

Na przykład, jeśli wypchnięto obraz Nginx jako instrukcjami w sekcji [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md), powinien zostać wyświetlony, polecenie podobne do następującego:

![Repozytoriów w portalu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, przeglądanie i Praca z repozytoriów w portalu, spróbuj przy użyciu usługi Azure Container Registry przy użyciu [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) klastra.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
