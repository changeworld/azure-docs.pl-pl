---
title: Najlepsze praktyki w zakresie rejestru
description: Dowiedz się, jak efektywnie korzystać z usługi Azure Container Registry dzięki zastosowaniu tych najlepszych rozwiązań.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 233d84b8bfa6f3d8c800e76032ef74a643db11ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247074"
---
# <a name="best-practices-for-azure-container-registry"></a>Najlepsze rozwiązania dla usługi Azure Container Registry

Stosując te najlepsze rozwiązania, można osiągnąć maksymalną wydajność i oszczędność kosztów podczas korzystania z rejestru prywatnego platformy Docker na platformie Azure.

Zobacz też [Zalecenia dotyczące oznaczania i przechowywania obrazów kontenerów](container-registry-image-tag-version.md) w celu uzyskania strategii oznaczania i tworzenia obrazów wersji w rejestrze. 

## <a name="network-close-deployment"></a>Wdrażanie w pobliskiej sieci

Utwórz rejestr kontenerów w tym samym regionie świadczenia usług Azure, w którym zostały wdrożone kontenery. Dzięki umieszczeniu rejestru w regionie blisko sieci, w której hostowane są kontenery, można zmniejszyć opóźnienia i obniżyć koszty.

Wdrożenie w pobliskiej sieci jest jednym z głównych powodów używania prywatnego rejestru kontenerów. Obrazy platformy Docker mają wydajną [konstrukcję warstw](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), która umożliwia przeprowadzanie wdrożeń przyrostowych. Jednak nowe węzły muszą ściągać wszystkie warstwy wymagane dla danego obrazu. Ten początkowy `docker pull` może szybko powiększyć się do wielu gigabajtów. Posiadanie prywatnego rejestru blisko wdrożenia minimalizuje opóźnienia sieci.
Ponadto wszystkich chmury publiczne, w tym Azure, stosują opłaty za transfer danych wychodzących z sieci. Ściąganie obrazów z jednego centrum danych do innego skutkuje dodatkowymi opłatami za dane wychodzące z sieci, a poza tym zwiększa opóźnienia.

## <a name="geo-replicate-multi-region-deployments"></a>Replikacja geograficzna wdrożeń w wielu regionach

Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) usługi Azure Container Registry, jeśli wdrażasz kontenery w wielu regionach. Bez względu na to, czy obsługujesz globalnych klientów z lokalnych centrów danych, czy też Twój zespół deweloperów znajduje się w różnych lokalizacjach, możesz uprościć zarządzanie rejestrem i zminimalizować opóźnienia dzięki replikacji geograficznej rejestru. Replikacja geograficzna jest dostępna tylko w przypadku rejestrów w wersji [Premium](container-registry-skus.md).

Aby dowiedzieć się, jak korzystać z replikacji geograficznej, zobacz trzyczęściowy samouczek [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Przestrzenie nazw repozytoriów

Dzięki wykorzystaniu przestrzeni nazw repozytoriów można zezwolić na udostępnianie jednego rejestru w wielu grupach w organizacji. Rejestry mogą być współużytkowane przez wdrożenia i zespoły. Usługa Azure Container Registry obsługuje zagnieżdżone przestrzenie nazw, umożliwiając izolację grup.

Rozważmy na przykład poniższe znaczniki obrazów kontenera. Obrazy, które są używane `aspnetcore`w całej firmie, takie jak , są umieszczane w głównym obszarze nazw, podczas gdy obrazy kontenerów należące do grup Produktów i Marketingu używają własnych obszarów nazw.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedykowana grupa zasobów

Ponieważ rejestry kontenerów są zasobami, które są używane w wielu hostach kontenerów, rejestr powinien znajdować się we własnej grupie zasobów.

Choć możesz eksperymentować z określonym typem hosta, na przykład z usługą Azure Container Instances, prawdopodobnie zechcesz usunąć wystąpienie kontenera po zakończeniu pracy z nim. Można jednak również zachować kolekcję obrazów, które zostały wypchnięte do usługi Azure Container Registry. Dzięki umieszczeniu rejestru w jego własnej grupie zasobów można zminimalizować ryzyko przypadkowego usunięcia kolekcji obrazów w rejestrze podczas usuwania grupy zasobów wystąpienia kontenera.

## <a name="authentication"></a>Uwierzytelnianie

Istnieją dwa podstawowe scenariusze uwierzytelniania w usłudze Azure Container Registry: uwierzytelnianie indywidualne i uwierzytelnianie usługi („bezobsługowe”). Poniższa tabela zawiera krótkie omówienie tych scenariuszy i wskazuje zalecaną metodę uwierzytelniania w przypadku każdego z nich.

| Typ | Przykładowy scenariusz | Zalecana metoda |
|---|---|---|
| Indywidualne tożsamości | Deweloper ściągający obrazy na swoją maszynę deweloperską lub wypychający z niej obrazy. | Polecenie [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Bezobsługowe/tożsamość usługi | Potoki kompilacji i wdrażania, w których użytkownik nie bierze bezpośrednio udziału. | [Dyrektor serwisu](container-registry-authentication.md#service-principal) |

Aby uzyskać szczegółowe informacje o uwierzytelnianiu w usłudze Azure Container Registry, zobacz [Authenticate with an Azure container registry](container-registry-authentication.md) (Uwierzytelnianie w usłudze Azure Container Registry).

## <a name="manage-registry-size"></a>Zarządzanie rozmiarem rejestru

Ograniczenia magazynu każdej [jednostki SKU rejestru kontenerów][container-registry-skus] służą zachowaniu zgodności z typowym scenariuszem: **Podstawowa** służąca do rozpoczynania pracy, **Standardowa** przeznaczona do większości aplikacji produkcyjnych oraz **Premium** zapewniająca wydajność w hiperskali i [replikację geograficzną][container-registry-geo-replication]. W ciągu cyklu życia rejestru należy zarządzać jego rozmiarem, okresowo usuwając nieużywaną zawartość.

Użyj polecenia interfejsu wiersza polecenia azure [az acr show-usage,][az-acr-show-usage] aby wyświetlić bieżący rozmiar rejestru:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Bieżący magazyn używany w **rejestrze** można również znaleźć w witrynie Azure portal:

![Informacje o użyciu rejestru w witrynie Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Usuwanie danych obrazu

Usługa Azure Container Registry obsługuje kilka metod usuwania danych obrazu z rejestru kontenerów. Obrazy można usuwać według znaczników lub podsumowania manifestu lub usuwać całe repozytorium.

Aby uzyskać szczegółowe informacje na temat usuwania danych obrazu z rejestru, w tym obrazów nieoznakowanych (czasami nazywanych "zwisającymi" lub "osieroconymi"), zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

## <a name="next-steps"></a>Następne kroki

Usługa Azure Container Registry jest dostępna w kilku warstwach, nazywanych jednostkami SKU, z których każda oferuje różne możliwości. Aby uzyskać szczegółowe informacje na temat dostępnych jednostek SKU, zobacz [Jednostki SKU usługi Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
