---
title: Uaktualnij rejestru kontenera Classic Azure
description: Korzystać z rozwiniętym zestaw funkcji podstawowa, standardowa i Premium zarządza uaktualniania niezarządzane rejestru kontenera Classic rejestrów kontenera.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 084dfc8f87aaea4b5bbad7cb5fdb9d445d566206
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-a-classic-container-registry"></a>Uaktualnij rejestru kontenera Classic

Rejestru kontenera platformy Azure (ACR) jest dostępna w kilku warstwach usług, [znany jako jednostki SKU](container-registry-skus.md). Początkowa wersja ACR oferowane jednej jednostki SKU, klasyczna, który nie ma kilka funkcje wbudowane w podstawowa, standardowa i Premium jednostki SKU (zbiorczo znane jako *zarządzane* rejestry organizacji IANA). W tym artykule szczegółowo sposób migracji niezarządzane rejestru klasycznego do jednego z zarządzanego jednostki SKU, dzięki czemu można skorzystać z ich zestawu funkcji rozszerzonej.

## <a name="why-upgrade"></a>Dlaczego uaktualnienie?

Ze względu na ograniczone możliwości rejestrów Classic niezarządzanych zaleca się wszystkie rejestrów Classic być uaktualniony do podstawowa, standardowa lub Premium rejestrów zarządzanych. Te wyższego poziomu jednostki SKU głębsze zintegrować rejestru możliwości platformy Azure.

Rejestry organizacji IANA zarządzanych zawierają:

* Integracja usługi Azure Active Directory dla [poszczególnych logowania](container-registry-authentication.md#individual-login-with-azure-ad)
* Obsługa usuwania obrazu i tagów
* [Geo-replication](container-registry-geo-replication.md) (Replikacja geograficzna)
* [elementów webhook](container-registry-webhook.md)

Najbardziej, rejestru Classic zależy od konta magazynu, że Azure będzie automatycznie przepisów w Twojej subskrypcji platformy Azure, podczas tworzenia rejestru. Z kolei podstawowa, standardowa i Premium jednostki SKU korzystać z platformy Azure [zaawansowane funkcje magazynu](container-registry-storage.md) w sposób niewidoczny dla obsługi magazynu obrazów. W ramach własnej subskrypcji oddzielnego konta magazynu nie zostanie utworzony.

Magazyn zarządzany rejestru zapewnia następujące korzyści:

* Kontener obrazów są [szyfrowane, gdy](container-registry-storage.md#encryption-at-rest).
* Obrazy są przechowywane przy użyciu [magazynu geograficznie nadmiarowego](container-registry-storage.md#geo-redundant-storage), jego kopii zapasowej z replikacji w przypadku obrazów.
* Możliwość za darmo [przechodzenie między jednostki SKU](container-registry-skus.md#changing-skus), włączanie wyższej przepustowości, po wybraniu SKU wyższego poziomu. Każdej jednostki SKU ACR można spełnia wymagania dotyczące przepływności zwiększania potrzeb.
* Model zabezpieczeń ujednoliconego rejestru i jej magazynu zapewnia uproszczony rights management. Możesz zarządzać uprawnienia tylko do rejestru kontenera, bez konieczności również zarządzać uprawnieniami do oddzielnego konta magazynu.

Aby uzyskać więcej informacji na temat przechowywania obrazów w ACR, zobacz [kontenera magazynu obrazu w rejestrze kontenera Azure](container-registry-storage.md).

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Jeśli zmienisz rejestru klasycznego do zarządzanego rejestru Azure należy skopiować do konta magazynu zarządzanego przez usługę Azure wszystkich istniejących obrazów kontenera z konta magazynu utworzone ACR w ramach subskrypcji. W zależności od rozmiaru rejestru może to potrwać kilka minut do kilku godzin.

W procesie konwersji wszystkich `docker push` operacji są zablokowane, gdy `docker pull` nadal działa.

Nie należy usuwać ani modyfikować zawartość konta magazynu kopii rejestru Classic w procesie konwersji. W ten sposób może spowodować uszkodzenie obrazów kontenera.

Po zakończeniu migracji konta magazynu w ramach subskrypcji, która pierwotnie skopiowana rejestru klasycznego jest już używany przez ACR. Po upewnieniu się, że migracja się powiodła, rozważ usunięcie konta magazynu, aby zminimalizować koszty.

>[!IMPORTANT]
> Uaktualnienie z klasycznego do jednego z zarządzanego jednostki SKU jest **jednokierunkowe procesu**. Po konwersji Classic rejestru do warstwy podstawowa, standardowa lub Premium, nie można przywrócić klasycznego. Zarządzane jednostki SKU wystarczającej do rejestru jednak można swobodnie przenoszeniu.

## <a name="how-to-upgrade"></a>Jak uaktualnić

Niezarządzane rejestru klasycznym można uaktualnić do jednego z zarządzanego jednostki SKU na kilka sposobów. W poniższych sekcjach opisano proces użycia [interfejsu wiersza polecenia Azure] [ azure-cli] i [portalu Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uaktualnienia w interfejsu wiersza polecenia platformy Azure

Aby uaktualnić rejestru klasycznego wiersza polecenia platformy Azure, należy wykonać [aktualizacji acr az] [ az-acr-update] polecenia i określ nowe jednostki SKU dla rejestru. W poniższym przykładzie o nazwie rejestru Classic *myclassicregistry* zostanie uaktualniony do wersji Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po zakończeniu migracji powinny zostać wyświetlone dane wyjściowe podobne do następującego. Zwróć uwagę, że `sku` jest "Premium" i `storageAccount` jest "pusty" wskazujący, że Azure zarządza teraz magazynu obrazu dla tego rejestru.

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

Jeśli określisz rejestru zarządzanego jednostki SKU którego maksymalną pojemność jest mniejsza niż rozmiar rejestru klasycznym, otrzymasz komunikat o błędzie podobny do następującego.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Jeśli komunikat o błędzie podobny, uruchom [aktualizacji acr az] [ az-acr-update] ponownie polecenia i określić sugerowane jednostka SKU, która jest SKU następnego najwyższego poziomu, która może obsłużyć obrazów.

## <a name="upgrade-in-azure-portal"></a>Po uaktualnieniu w portalu Azure

Po uaktualnieniu rejestru Classic przy użyciu portalu Azure, Azure automatycznie wybiera SKU najniższym poziomie, który może obsłużyć obrazów. Na przykład, jeśli rejestr zawiera 12 GiB w obrazach, Azure automatycznie wybiera i konwertuje rejestru klasycznego normą (maksymalnie 100 GiB).

Aby uaktualnić rejestru Classic przy użyciu portalu Azure, przejdź do kontenera rejestru **omówienie** i wybierz **uaktualniania zarządzane rejestrze**.

![Klasycznym rejestru uaktualnienia przycisk w portalu Azure, interfejsu użytkownika][update-classic-01-upgrade]

Wybierz **OK** potwierdzenie uaktualniania zarządzane rejestru.

![Klasycznym rejestru uaktualnienia potwierdzenia w portalu Azure, interfejsu użytkownika][update-classic-02-confirm]

Podczas migracji, portalu wskazuje, że w rejestrze **stan inicjowania obsługi** jest *aktualizacji*. Jak wspomniano wcześniej, `docker push` operacje są wyłączone podczas migracji i nie można usuwać lub aktualizacji konta magazynu używanych przez rejestru klasycznym, podczas migracji jest w toku — w ten sposób może spowodować uszkodzenie obrazu.

![Klasycznym rejestru postęp uaktualniania w portalu Azure, interfejsu użytkownika][update-classic-03-updating]

Po zakończeniu migracji **stan inicjowania obsługi** wskazuje *zakończyło się pomyślnie*, a możesz ponownie `docker push` do rejestru.

![Klasycznym rejestru uaktualnić stan wykonania w portalu Azure, interfejsu użytkownika][update-classic-04-updated]

## <a name="next-steps"></a>Kolejne kroki

Gdy został uaktualniony Classic rejestru do warstwy podstawowa, standardowa lub Premium, Azure nie są już używane konto magazynu, które pierwotnie kopii rejestru klasycznego. Będzie zmniejszenie kosztów, rozważ usunięcie konta magazynu lub kontenera obiektów Blob w ramach konta, które zawiera obrazy starego kontenera.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com