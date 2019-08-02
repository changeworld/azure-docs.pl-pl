---
title: Uaktualnianie klasycznego rejestru kontenerów platformy Azure
description: Skorzystaj z rozszerzonego zestawu funkcji dla rejestrów kontenerów zarządzanych w warstwach Podstawowa, standardowa i Premium, uaktualniając niezarządzany klasyczny rejestr kontenerów.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310376"
---
# <a name="upgrade-a-classic-container-registry"></a>Uaktualnij klasyczny rejestr kontenerów

Azure Container Registry (ACR) jest dostępna w kilku warstwach usług, [znanych jako jednostki SKU](container-registry-skus.md). Początkowa wersja ACR oferuje pojedynczą jednostkę SKU, klasyczną, która nie ma kilku funkcji związanych z jednostkami SKU Basic, standard i Premium (zbiorowo znane jako rejestry *zarządzane* ).

Klasyczna jednostka SKU jest przestarzała i będzie niedostępna po 2019 kwietnia. W tym artykule szczegółowo opisano sposób migrowania niezarządzanego klasycznego rejestru do jednej z zarządzanych jednostek SKU, dzięki czemu można korzystać z rozszerzonego zestawu funkcji.

## <a name="why-upgrade"></a>Dlaczego warto przeprowadzić uaktualnienie?

Jednostka SKU rejestru klasycznego jest **przestarzała**i będzie niedostępna po **2019 kwietnia**. Przed 2019 kwietnia należy uaktualnić wszystkie istniejące rejestry klasyczne. Funkcje zarządzania portalem dla klasycznych rejestrów będą stopniowo wycofywane. Tworzenie nowych rejestrów klasycznych zostanie wyłączone po kwietnia 2019.

Ze względu na planowane wycofanie i ograniczone możliwości klasycznych niezarządzanych rejestrów należy uaktualnić wszystkie klasyczne rejestry do rejestrów zarządzanych (podstawowa, standardowa lub Premium). Te jednostki SKU wyższego poziomu bardziej głęboko integrują rejestr z możliwościami platformy Azure. Aby uzyskać więcej informacji na temat cen i możliwości różnych warstw usług, zobacz [Container Registry jednostek SKU](container-registry-skus.md).

Klasyczny rejestr zależy od konta magazynu, które platforma Azure automatycznie inicjuje w Twojej subskrypcji platformy Azure podczas tworzenia rejestru. Z kolei jednostki SKU w warstwach Podstawowa, standardowa i Premium wykorzystują [Zaawansowane funkcje magazynu](container-registry-storage.md) platformy Azure, aby w sposób niewidoczny obsługiwać magazyn obrazów. Osobne konto magazynu nie jest tworzone w ramach własnej subskrypcji.

Magazyn rejestru zarządzanego zapewnia następujące korzyści:

* Obrazy kontenerów są [szyfrowane w stanie spoczynku](container-registry-storage.md#encryption-at-rest).
* Obrazy są przechowywane przy użyciu [magazynu geograficznie](container-registry-storage.md#geo-redundant-storage)nadmiarowego, dzięki czemu można tworzyć kopie zapasowe obrazów przy użyciu replikacji wieloregionowej (tylko wersja Premium).
* Możliwość swobodnego [przenoszenia między](container-registry-skus.md#changing-skus)jednostkami SKU, co pozwala na większą przepływność po wybraniu jednostki SKU wyższego poziomu. W przypadku każdej jednostki SKU ACR może spełnić wymagania dotyczące przepływności w miarę wzrostu potrzeb.
* Ujednolicony model zabezpieczeń dla rejestru i jego magazyn zawiera uproszczone zarządzanie prawami. Zarządzasz uprawnieniami tylko dla rejestru kontenerów, bez konieczności zarządzania uprawnieniami dla oddzielnego konta magazynu.

Aby uzyskać dodatkowe informacje na temat przechowywania obrazów w programie ACR, zobacz temat [Container Image Storage w Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

W przypadku uaktualniania klasycznego rejestru do rejestru zarządzanego platforma Azure musi skopiować wszystkie istniejące obrazy kontenerów z konta magazynu utworzonego przez ACR w ramach subskrypcji do konta magazynu zarządzanego przez platformę Azure. W zależności od rozmiaru rejestru ten proces może potrwać kilka minut do kilku godzin. W celach szacunkowych oczekiwany jest czas migracji o około 0,5 GiB na minutę.

Podczas procesu `docker push` konwersji operacje są wyłączone w trakcie ostatnich 10% migracji. `docker pull`kontynuuje normalne działanie.

Nie usuwaj ani nie Modyfikuj zawartości konta magazynu tworzącego swój klasyczny rejestr w trakcie procesu konwersji. Wykonanie tej operacji może spowodować uszkodzenie obrazów kontenerów.

Po zakończeniu migracji konto magazynu w ramach subskrypcji, które początkowo utworzyła kopię zapasową klasycznego rejestru, nie jest już używane przez Azure Container Registry. Po zweryfikowaniu, że migracja zakończyła się pomyślnie, Rozważ usunięcie konta magazynu, aby zmniejszyć koszty.

>[!IMPORTANT]
> Uaktualnianie z wersji klasycznej do jednej z zarządzanych jednostek SKU jest **procesem**jednokierunkowym. Po przekonwertowaniu klasycznego rejestru do warstwy Podstawowa, standardowa lub Premium nie można przywrócić klasycznej wersji. Możliwe jest jednak swobodne przechodzenie między zarządzanymi jednostkami SKU z wystarczającą pojemnością dla rejestru.

## <a name="how-to-upgrade"></a>Jak uaktualnić

Niezarządzany rejestr klasyczny można uaktualnić do jednej z zarządzanych jednostek SKU na kilka sposobów. W poniższych sekcjach opisano proces używania [interfejsu wiersza polecenia platformy Azure][azure-cli] i [Azure Portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uaktualnianie w interfejsie wiersza polecenia platformy Azure

Aby uaktualnić rejestr klasyczny w interfejsie wiersza polecenia platformy Azure, wykonaj polecenie [AZ ACR Update][az-acr-update] i określ nową jednostkę SKU dla rejestru. W poniższym przykładzie rejestr klasyczny o nazwie *myclassicregistry* jest uaktualniany do jednostki SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po zakończeniu migracji powinny zostać wyświetlone dane wyjściowe podobne do poniższych. Zwróć uwagę, `sku` że jest to "Premium" `storageAccount` i `null`"is", co oznacza, że platforma Azure zarządza teraz magazynem obrazów dla tego rejestru.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

W przypadku określenia jednostki SKU zarządzanego rejestru, której Maksymalna pojemność jest mniejsza niż rozmiar Twojego rejestru klasycznego, zostanie wyświetlony komunikat o błędzie podobny do poniższego.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Jeśli wystąpi podobny błąd, uruchom ponownie polecenie [AZ ACR Update][az-acr-update] i określ SUGEROWANĄ jednostkę SKU, która jest kolejną jednostką SKU, która może obsłużyć Twoje obrazy.

## <a name="upgrade-in-azure-portal"></a>Uaktualnianie w Azure Portal

W przypadku uaktualniania klasycznego rejestru przy użyciu Azure Portal platforma Azure automatycznie wybiera jednostkę SKU w warstwie Standardowa lub Premium, w zależności od tego, która jednostka SKU może obsłużyć Twoje obrazy. Na przykład jeśli Rejestr zawiera mniej niż 100 GiB w obrazach, platforma Azure automatycznie wybiera i konwertuje klasyczny rejestr na Standard (100 GiB maksimum).

Aby uaktualnić rejestr klasyczny przy użyciu Azure Portal, przejdź do **omówienia** rejestru kontenerów i wybierz pozycję **Uaktualnij do rejestru zarządzanego**.

![Klasyczny przycisk Uaktualnij rejestr w interfejsie użytkownika Azure Portal][update-classic-01-upgrade]

Wybierz **przycisk OK** , aby potwierdzić, że chcesz przeprowadzić uaktualnienie do rejestru zarządzanego.

Podczas migracji Portal wskazuje, że **stan aprowizacji** rejestru jest *aktualizowany*. Jak wspomniano wcześniej `docker push` , operacje są wyłączone w trakcie ostatnich 10% migracji. Nie usuwaj ani nie Aktualizuj konta magazynu używanego przez rejestr klasyczny podczas migracji w toku — może to spowodować uszkodzenie obrazu.

![Postęp uaktualniania rejestru klasycznego w interfejsie użytkownika Azure Portal][update-classic-03-updating]

Po zakończeniu migracji **stan aprowizacji** wskazuje *powodzenie*i można wznowić normalne operacje z rejestrem.

![Stan ukończenia uaktualniania rejestru klasycznego w interfejsie użytkownika Azure Portal][update-classic-04-updated]

## <a name="next-steps"></a>Kolejne kroki

Po uaktualnieniu rejestru klasycznego do rejestru zarządzanego platforma Azure nie korzysta już z konta magazynu, które pierwotnie utworzyło kopię zapasową rejestru klasycznego. Aby obniżyć koszty, należy rozważyć usunięcie konta magazynu lub kontenera obiektów BLOB w ramach konta, które zawiera stare obrazy kontenerów.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com