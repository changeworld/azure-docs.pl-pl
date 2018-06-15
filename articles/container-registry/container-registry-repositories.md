---
title: Azure repozytoria rejestru kontenera w portalu Azure
description: Jak wyświetlić repozytoria rejestru kontenera platformy Azure w portalu Azure.
services: container-registry
author: cristy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 171593483fc94c1c67013ab520b0085ca98f3a82
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33768332"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Wyświetl repozytoria rejestru kontenera w portalu Azure

Rejestru kontenera Azure umożliwia przechowywanie Docker kontener obrazów w repozytoriów. Dzięki przechowywaniu obrazów w repozytoriów, można przechowywać grup obrazów (lub wersji obrazów) w izolowanym środowisku. Podczas wypychania obrazów do rejestru i wyświetlić ich zawartość w portalu Azure, można określić te repozytoriów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Kontener rejestru**: Tworzenie rejestru kontenera w Twojej subskrypcji platformy Azure. Na przykład użyć [portalu Azure](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia Azure](container-registry-get-started-azure-cli.md).
* **Interfejs wiersza polecenia docker**: Zainstaluj [Docker] [ docker-install] na komputerze lokalnym, które zapewnia interfejs wiersza polecenia Docker.
* **Kontener obrazu**: Push obrazu do rejestru kontenera. Aby uzyskać wskazówki dotyczące wypychania i ściągania obrazów, zobacz [wypychania i ściągania obrazu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Repozytoria widoku w portalu Azure

Można wyświetlić listę repozytoria hosting obrazów, a także tag obrazu, w portalu Azure.

Po wykonaniu kroków w [wypychania i ściągania obrazu](container-registry-get-started-docker-cli.md) i nie został później usunąć obrazu, powinien mieć obraz Nginx w rejestrze kontenera. Instrukcje w tym artykule określony tag obrazu z przestrzenią nazw, "Przykłady" w `/samples/nginx`. Jako odświeżacz [wypychania docker] [ docker-push] zostało określone w tym artykule polecenie:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Ponieważ rejestru kontenera Azure obsługuje takich nazw wielopoziomowej repozytorium, możesz zakresu kolekcji obrazów związane z konkretną aplikację lub zbiór aplikacji do różnych rozwoju lub zespołów operacyjnych. Aby uzyskać więcej informacji na temat repozytoria w rejestrach kontenera, zobacz [rejestrów kontenera prywatnego Docker na platformie Azure](container-registry-intro.md).

Aby wyświetlić repozytorium:

1. Zaloguj się do [portalu Azure][portal]
1. Wybierz **rejestru kontenera Azure** do której przypisany jest obraz Nginx
1. Wybierz **repozytoria** umożliwia wyświetlenie listy repozytoriów zawierających obrazy w rejestrze
1. Wybierz repozytorium, aby wyświetlić tagi obrazu, w tym repozytorium

Na przykład, jeśli przypisany jako obraz Nginx instrukcją w [wypychania i ściągania obrazu](container-registry-get-started-docker-cli.md), powinny pojawić się polecenie podobne do następującego:

![Repozytoria w portalu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz, przeglądanie i pracy z repozytoriami w portalu, spróbuj użyć rejestru kontenera Azure [usługi Kubernetes Azure (AKS)](../aks/tutorial-kubernetes-prepare-app.md) klastra.

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
