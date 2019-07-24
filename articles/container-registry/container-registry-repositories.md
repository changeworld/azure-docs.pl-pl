---
title: Azure Container Registry repozytoria w Azure Portal
description: Jak wyświetlać repozytoria Azure Container Registry w Azure Portal.
services: container-registry
author: cristy
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: jeconnoc
ms.openlocfilehash: 22f84efee2b3996734e8e38c73d30ba891b745d9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310580"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Wyświetlanie repozytoriów rejestru kontenera w Azure Portal

Azure Container Registry umożliwia przechowywanie obrazów kontenerów platformy Docker w repozytoriach. Przechowywanie obrazów w repozytoriach umożliwia przechowywanie grup obrazów (lub wersji obrazów) w środowiskach izolowanych. Te repozytoria można określić podczas wypychania obrazów do rejestru i wyświetlania ich zawartości w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

* **Rejestr kontenerów**: Utwórz rejestr kontenerów w ramach subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* **Interfejs wiersza polecenia platformy Docker**: Zainstaluj [platformę Docker][docker-install] na komputerze lokalnym, która zapewnia interfejs wiersza polecenia platformy Docker.
* **Obraz kontenera**: Wypchnij obraz do rejestru kontenerów. Aby uzyskać wskazówki dotyczące wypychania i ściągania obrazów, zobacz [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Wyświetl repozytoria w Azure Portal

Można wyświetlić listę repozytoriów obsługujących obrazy, a także Tagi obrazu w Azure Portal.

Jeśli wykonano kroki opisane w sekcji [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md) (i dopiero po usunięciu obrazu), w rejestrze kontenerów powinien znajdować się obraz Nginx. Instrukcje zawarte w tym artykule podano po oznaczeniu obrazu za pomocą przestrzeni nazw, "Przykłady" w `/samples/nginx`. Jako odświeżacz, polecenie [Docker push][docker-push] określone w tym artykule było następujące:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Ponieważ Azure Container Registry obsługuje takie przestrzenie nazw wielopoziomowego repozytorium, można określać zakres kolekcji obrazów związanych z określoną aplikacją lub kolekcję aplikacji do różnych zespołów programistycznych lub operacyjnych. Aby dowiedzieć się więcej o repozytoriach w rejestrach kontenerów, zobacz [prywatne rejestry kontenerów platformy Docker na platformie Azure](container-registry-intro.md).

Aby wyświetlić repozytorium:

1. Zaloguj się do witryny [Azure Portal][portal].
1. Wybierz **Azure Container Registry** , do którego wypychasz obraz Nginx
1. Wybierz  pozycję repozytoria, aby wyświetlić listę repozytoriów zawierających obrazy w rejestrze
1. Wybierz repozytorium, aby wyświetlić Tagi obrazu w tym repozytorium

Na przykład, jeśli wypychasz obraz Nginx zgodnie z instrukcją [push i ściąganiem obrazu](container-registry-get-started-docker-cli.md), zobaczysz coś podobnego do:

![Repozytoria w portalu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawowe informacje na temat wyświetlania repozytoriów i pracy z nią w portalu, spróbuj użyć Azure Container Registry z klastrem [usługi Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) .

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
