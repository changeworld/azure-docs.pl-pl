---
title: Wyświetlanie repozytoriów w portalu
description: Użyj witryny Azure Portal, aby wyświetlić repozytoria rejestru kontenerów platformy Azure, które obsługują obrazy kontenerów platformy Docker i inne obsługiwane artefakty.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456297"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Wyświetlanie repozytoriów rejestru kontenerów w witrynie Azure portal

Usługa Azure Container Registry umożliwia przechowywanie obrazów kontenerów platformy Docker w repozytoriach. Przechowując obrazy w repozytoriach, można przechowywać grupy obrazów (lub wersje obrazów) w odizolowanych środowiskach. Można określić te repozytoria podczas wypychania obrazów do rejestru i wyświetlić ich zawartość w witrynie Azure portal.

## <a name="prerequisites"></a>Wymagania wstępne

* **Rejestr kontenerów:** Utwórz rejestr kontenerów w ramach subskrypcji platformy Azure. Na przykład użyj [witryny Azure portal](container-registry-get-started-portal.md) lub interfejsu [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Docker CLI:** Zainstaluj platformę [Docker][docker-install] na komputerze lokalnym, która zapewnia interfejs wiersza polecenia platformy Docker.
* **Obraz kontenera**: Wypychanie obrazu do rejestru kontenerów. Aby uzyskać wskazówki dotyczące wciskania i ściągania obrazów, zobacz [Wypychanie i ciągnięcie obrazu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Wyświetlanie repozytoriów w witrynie Azure portal

Listę repozytoriów obsługujących obrazy, a także znaczniki obrazów, można wyświetlić w witrynie Azure portal.

Jeśli po czynnościach w [funkcji Wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md) (a następnie nie usunięno obrazu), w rejestrze kontenerów powinien znajdować się obraz Nginx. Instrukcje w tym artykule określone, że tag obrazu z obszaru `/samples/nginx`nazw, "przykłady" w . Jako odświeżacz polecenie [wypychania docker][docker-push] określone w tym artykule było:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Ponieważ usługa Azure Container Registry obsługuje takie wielopoziomowe obszary nazw repozytorium, można zakres kolekcji obrazów związanych z określonej aplikacji lub kolekcji aplikacji do różnych zespołów deweloperskich lub operacyjnych. Aby dowiedzieć się więcej o repozytoriach w rejestrach kontenerów, zobacz [Rejestry kontenerów platformy Docker private na platformie Azure.](container-registry-intro.md)

Aby wyświetlić repozytorium:

1. Logowanie się do [witryny Azure portal][portal]
1. Wybierz **rejestr kontenerów platformy Azure,** do którego wypchnięta została obraz Nginx
1. Wybierz **repozytoria,** aby wyświetlić listę repozytoriów zawierających obrazy w rejestrze
1. Wybierz repozytorium, aby wyświetlić znaczniki obrazu w tym repozytorium

Na przykład, jeśli obraz Nginx został wypchnięty zgodnie z instrukcją w [aplikacji Wypychanie i ściąganie obrazu,](container-registry-get-started-docker-cli.md)powinien zostać wyświetlony obraz podobny do:

![Repozytoria w portalu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawy wyświetlania i pracy z repozytoriami w portalu, spróbuj użyć usługi Azure Container Registry z klastrem [usługi Azure Kubernetes Service (AKS).](../aks/tutorial-kubernetes-prepare-app.md)

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
