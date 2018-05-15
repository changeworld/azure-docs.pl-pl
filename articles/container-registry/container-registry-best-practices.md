---
title: Najlepsze rozwiązania w usłudze Azure Container Registry
description: Dowiedz się, jak efektywnie korzystać z usługi Azure Container Registry dzięki zastosowaniu tych najlepszych rozwiązań.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: marsma
ms.openlocfilehash: 1272a4b547a4ba650678ce083a44bde3a5a3e583
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="best-practices-for-azure-container-registry"></a>Najlepsze rozwiązania dla usługi Azure Container Registry

Stosując te najlepsze rozwiązania, można osiągnąć maksymalną wydajność i oszczędność kosztów podczas korzystania z rejestru prywatnego platformy Docker na platformie Azure.

## <a name="network-close-deployment"></a>Wdrażanie w pobliskiej sieci

Utwórz rejestr kontenerów w tym samym regionie świadczenia usług Azure, w którym zostały wdrożone kontenery. Dzięki umieszczeniu rejestru w regionie blisko sieci, w której hostowane są kontenery, można zmniejszyć opóźnienia i obniżyć koszty.

Wdrożenie w pobliskiej sieci jest jednym z głównych powodów używania prywatnego rejestru kontenerów. Obrazy platformy Docker mają wydajną [konstrukcję warstw](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), która umożliwia przeprowadzanie wdrożeń przyrostowych. Jednak nowe węzły muszą ściągać wszystkie warstwy wymagane dla danego obrazu. Ten początkowy `docker pull` może szybko powiększyć się do wielu gigabajtów. Posiadanie prywatnego rejestru blisko wdrożenia minimalizuje opóźnienia sieci.
Ponadto wszystkich chmury publiczne, w tym Azure, stosują opłaty za transfer danych wychodzących z sieci. Ściąganie obrazów z jednego centrum danych do innego skutkuje dodatkowymi opłatami za dane wychodzące z sieci, a poza tym zwiększa opóźnienia.

## <a name="geo-replicate-multi-region-deployments"></a>Replikacja geograficzna wdrożeń w wielu regionach

Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) usługi Azure Container Registry, jeśli wdrażasz kontenery w wielu regionach. Bez względu na to, czy obsługujesz globalnych klientów z lokalnych centrów danych, czy też Twój zespół deweloperów znajduje się w różnych lokalizacjach, możesz uprościć zarządzanie rejestrem i zminimalizować opóźnienia dzięki replikacji geograficznej rejestru. Replikacja geograficzna jest dostępna tylko w przypadku rejestrów w wersji [Premium](container-registry-skus.md).

Aby dowiedzieć się, jak korzystać z replikacji geograficznej, zobacz trzyczęściowy samouczek [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Przestrzenie nazw repozytoriów

Dzięki wykorzystaniu przestrzeni nazw repozytoriów można zezwolić na udostępnianie jednego rejestru w wielu grupach w organizacji. Rejestry mogą być współużytkowane przez wdrożenia i zespoły. Usługa Azure Container Registry obsługuje zagnieżdżone przestrzenie nazw, umożliwiając izolację grup.

Rozważmy na przykład poniższe znaczniki obrazów kontenera. Obrazy, które są używane w całej firmie, na przykład `aspnetcore`, są umieszczane w głównej przestrzeni nazw, natomiast obrazy kontenerów należące do grup Produkcja i Marketing używają własnych przestrzeni nazw.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedykowana grupa zasobów

Ponieważ rejestry kontenerów to zasoby, które są używane na wielu hostach kontenerów, rejestr powinien znajdować się we własnej grupie zasobów.

Choć możesz eksperymentować z określonym typem hosta, na przykład z usługą Azure Container Instances, prawdopodobnie zechcesz usunąć wystąpienie kontenera po zakończeniu pracy z nim. Można jednak również zachować kolekcję obrazów, które zostały wypchnięte do usługi Azure Container Registry. Dzięki umieszczeniu rejestru w jego własnej grupie zasobów można zminimalizować ryzyko przypadkowego usunięcia kolekcji obrazów w rejestrze podczas usuwania grupy zasobów wystąpienia kontenera.

## <a name="authentication"></a>Authentication

Istnieją dwa podstawowe scenariusze uwierzytelniania w usłudze Azure Container Registry: uwierzytelnianie indywidualne i uwierzytelnianie usługi („bezobsługowe”). Poniższa tabela zawiera krótkie omówienie tych scenariuszy i wskazuje zalecaną metodę uwierzytelniania w przypadku każdego z nich.

| Typ | Przykładowy scenariusz | Zalecana metoda |
|---|---|---|
| Indywidualne tożsamości | Deweloper ściągający obrazy na swoją maszynę deweloperską lub wypychający z niej obrazy. | Polecenie [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Bezobsługowe/tożsamość usługi | Potoki kompilacji i wdrażania, w których użytkownik nie bierze bezpośrednio udziału. | [Jednostka usługi](container-registry-authentication.md#service-principal) |

Aby uzyskać szczegółowe informacje o uwierzytelnianiu w usłudze Azure Container Registry, zobacz [Authenticate with an Azure container registry](container-registry-authentication.md) (Uwierzytelnianie w usłudze Azure Container Registry).

## <a name="manage-registry-size"></a>Zarządzanie rozmiarem rejestru

Ograniczenia magazynu każdej [jednostki SKU rejestru kontenerów][container-registry-skus] służą zachowaniu zgodności z typowym scenariuszem: **Podstawowa** służąca do rozpoczynania pracy, **Standardowa** przeznaczona do większości aplikacji produkcyjnych oraz **Premium** zapewniająca wydajność w hiperskali i [replikację geograficzną][container-registry-geo-replication]. W ciągu cyklu życia rejestru należy zarządzać jego rozmiarem, okresowo usuwając nieużywaną zawartość.

Bieżące użycie rejestru można znaleźć w obszarze **Przegląd** rejestru kontenerów w witrynie Azure Portal:

![Informacje o użyciu rejestru w witrynie Azure Portal][registry-overview-quotas]

Możesz zarządzać rozmiarem rejestru za pomocą [interfejsu wiersza polecenia platformy Azure][azure-cli] lub witryny [Azure Portal][azure-portal]. Tyko zarządzane jednostki SKU (warstw Podstawowa, Standardowa i Premium) obsługują usuwanie repozytoriów i obrazów. W rejestrze klasycznym nie można usuwać repozytoriów, obrazów ani tagów.

### <a name="delete-in-azure-cli"></a>Usuwanie w interfejsie wiersza polecenia platformy Azure

Repozytorium lub znajdującą się w nim zawartość można usunąć za pomocą polecenia [az acr repository delete][az-acr-repository-delete].

Aby usunąć repozytorium, wraz ze wszystkimi znajdującymi się w nim tagami i danymi warstw obrazów, podaj tylko nazwę repozytorium podczas wykonywania polecenia [az acr repository delete][az-acr-repository-delete]. W następującym przykładzie usuniemy repozytorium *myapplication* oraz wszystkie znajdujące się w nim tagi i dane warstw obrazów:

```azurecli
az acr repository delete --name myregistry --repository myapplication
```

Dane obrazów można usunąć z repozytorium także za pomocą argumentów `--tag` i `--manifest`. Aby uzyskać szczegółowe informacje o tych argumentach, zobacz [dokumentację polecenia az acr repository delete][az-acr-repository-delete].

### <a name="delete-in-azure-portal"></a>Usuwanie w witrynie Azure Portal

Aby usunąć repozytorium z rejestru w witrynie Azure Portal, przejdź najpierw do rejestru kontenerów. Następnie w obszarze **USŁUGI** wybierz pozycję **Repozytoria** i kliknij prawym przyciskiem myszy repozytorium, które chcesz usunąć. Wybierz pozycję **Usuń**, aby usunąć repozytorium oraz znajdujące się w nim obrazy platformy Docker.

![Usuwanie repozytorium w witrynie Azure Portal][delete-repository-portal]

W podobny sposób można również usunąć z repozytorium tagi. Przejdź do repozytorium, w obszarze **TAGI** kliknij prawym przyciskiem myszy tag, który chcesz usunąć, i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Usługa Azure Container Registry jest dostępna w kilku warstwach, nazywanych jednostkami SKU, z których każda oferuje różne możliwości. Aby uzyskać szczegółowe informacje na temat dostępnych jednostek SKU, zobacz [Jednostki SKU usługi Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
