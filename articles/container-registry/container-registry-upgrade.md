---
title: Uaktualnij rejestr kontenerów klasycznej platformie Azure
description: Skorzystaj z zalet rozszerzonej zestawu funkcji komponentu Basic, Standard i Premium zarządzane rejestry kontenerów, uaktualniając niezarządzanych Classic rejestru kontenerów.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/28/2018
ms.author: danlep
ms.openlocfilehash: 73e207dae61dfc8912fd79a151adf982753701f1
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969111"
---
# <a name="upgrade-a-classic-container-registry"></a>Uaktualnianie rejestru klasycznego kontenera

Usługa Azure Container Registry (ACR) jest dostępna w kilku warstwach usługi, [znane jako jednostki SKU](container-registry-skus.md). Wstępną wersję ACR oferowane pojedynczej jednostki SKU i Model Klasyczny, który nie posiada kilka funkcji zarezerwowanymi podstawowa, standardowa i Premium jednostki SKU (łącznie znane jako *zarządzane* rejestrów).

Klasyczne jednostki SKU jest wycofywany, a będzie niedostępna po marca 2019 r. Ten artykuł szczegółowo opisuje sposób migrowania niezarządzanych rejestru klasycznego do jednego z zarządzanymi jednostkami SKU, można korzystać z zalet ich zestaw rozszerzonych funkcji.

## <a name="why-upgrade"></a>Dlaczego warto wykonać uaktualnienie?

Rejestru klasycznego jest jednostka SKU **przestarzałe**i będzie niedostępny z **marca 2019**. Wszystkie istniejące rejestrów Classic powinny zostać uaktualnione przed marca 2019 r.

Z powodu planowanej obsługi i ograniczone możliwości rejestrów Classic niezarządzanych wszystkich rejestrów klasyczny być został uaktualniony do podstawowa, standardowa lub Premium rejestrów zarządzanych. Te wyższego poziomu jednostki SKU głębiej zintegrować rejestru możliwości platformy Azure.

Rejestry zarządzane oferują:

* Integracja usługi Azure Active Directory dla [poszczególnych logowania](container-registry-authentication.md#individual-login-with-azure-ad)
* Obsługa usunięcie obrazu i tagu
* [Geo-replication](container-registry-geo-replication.md) (Replikacja geograficzna)
* [Elementy Webhook](container-registry-webhook.md)

W rejestrze klasycznym zależy od konta magazynu, które platforma Azure udostępnia automatycznie w ramach subskrypcji platformy Azure podczas tworzenia rejestru. Z drugiej strony, podstawowa, standardowa i Premium jednostki SKU z zalet platformy Azure [zaawansowane funkcje magazynu](container-registry-storage.md) przez przejrzystą obsługę magazynu obrazów dla Ciebie. Oddzielne konto magazynu nie jest tworzony w ramach własnej subskrypcji.

Rejestru zarządzanego magazynu zapewnia następujące korzyści:

* Obrazy kontenerów są [szyfrowane, gdy](container-registry-storage.md#encryption-at-rest).
* Obrazy są przechowywane przy użyciu [magazyn geograficznie nadmiarowy](container-registry-storage.md#geo-redundant-storage), jego kopii zapasowej obrazów za pomocą replikacja w wielu regionach.
* Możliwość swobodnie [przechodzenia między jednostkami SKU](container-registry-skus.md#changing-skus), umożliwiając wyższej przepustowości w przypadku wybrania wyższego poziomu jednostki SKU. Każdej jednostki SKU rejestru Azure container Registry można spełniać wymagań dotyczących przepływności zwiększania potrzeb.
* Model zabezpieczeń ujednoliconego rejestru i jego magazyn zapewnia uproszczoną usługi rights management. Zarządzanie uprawnieniami odbywa się tylko dla rejestru kontenerów, bez konieczności również zarządzać uprawnieniami do oddzielnego konta magazynu.

Więcej informacji na temat przechowywania obrazu w rejestru Azure container Registry, zobacz [magazyn obrazów kontenerów w usłudze Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Jeśli zmienisz rejestru klasycznego do rejestru zarządzanego Azure należy skopiować wszystkich istniejących obrazów kontenera z rejestru Azure container Registry utworzone konto magazynu w ramach subskrypcji na koncie magazynu zarządzanych przez platformę Azure. W zależności od rozmiaru rejestru ten proces może potrwać kilka minut do kilku godzin.

W procesie konwersji wszystkich `docker push` operacje są blokowane, podczas gdy `docker pull` nadal działa.

Nie należy usuwać ani modyfikować zawartość konta magazynu, kopii rejestru klasycznego w procesie konwersji. To może doprowadzić do uszkodzenia obrazów kontenerów.

Po zakończeniu migracji konta magazynu w ramach subskrypcji, która pierwotnie wspierana rejestru klasycznego jest już używany przez usługi ACR. Po zweryfikowaniu, że migracja się powiodła, rozważ usunięcie konta magazynu, aby zminimalizować koszty.

>[!IMPORTANT]
> Uaktualnienie z wersji klasycznej do jednego z zarządzanymi jednostkami SKU jest **jednokierunkowe procesu**. Po konwersji klasyczny rejestr do warstwy podstawowa, standardowa lub Premium, nie można przywrócić do klasycznego modelu. Jednak dowolnie przenosić między zarządzanymi jednostkami SKU o wystarczającej pojemności dla rejestru.

## <a name="how-to-upgrade"></a>Jak uaktualnić

Niezarządzane rejestrze klasycznym można uaktualnić do jednego z zarządzanymi jednostkami SKU na kilka sposobów. W poniższych sekcjach opisano proces użycia [wiersza polecenia platformy Azure] [ azure-cli] i [witryny Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Uaktualnienia w wiersza polecenia platformy Azure

Aby uaktualnianie rejestru klasycznego w interfejsie wiersza polecenia platformy Azure, wykonaj [az acr update] [ az-acr-update] polecenia i określić nowej jednostki SKU dla rejestru. W poniższym przykładzie o nazwie rejestru klasycznego *myclassicregistry* została uaktualniona do wersji Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Po zakończeniu migracji powinny być widoczne dane wyjściowe podobne do następujących. Należy zauważyć, że `sku` jest "Premium" i `storageAccount` jest "ma wartość null," wskazujący, że platforma Azure zarządza się teraz magazyn obrazów dla tego rejestru.

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

Jeśli określisz rejestru zarządzanego jednostki SKU, których maksymalną pojemność jest mniejszy niż rozmiar rejestru klasycznego, otrzymasz komunikat o błędzie podobny do następującego.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Jeśli komunikat o błędzie podobny, uruchom [az acr update] [ az-acr-update] ponownie polecenie, a następnie określ sugerowane jednostki SKU, który jest następnego najwyższego poziomu jednostki SKU, która może pomieścić obrazów.

## <a name="upgrade-in-azure-portal"></a>Uaktualnienie w witrynie Azure portal

Po uaktualnieniu rejestrze klasycznym przy użyciu witryny Azure portal, Azure automatycznie wybiera najniższego poziomu jednostki SKU, która może pomieścić obrazów. Na przykład, jeśli rejestr zawiera 12 GiB w obrazach, Azure automatycznie wybiera i konwertuje rejestru klasycznego do warstwy standardowa (maks. 100 GiB).

Aby uaktualnić rejestru klasycznego przy użyciu witryny Azure portal, przejdź do rejestru kontenerów **Przegląd** i wybierz **uaktualnianie do rejestru zarządzanego**.

![Klasyczny rejestr uaktualnienia przycisku w witrynie Azure portal, interfejsu użytkownika][update-classic-01-upgrade]

Wybierz **OK** aby upewnić się, że chcesz uaktualnić do rejestru zarządzanego.

![Klasyczny rejestr uaktualnienia potwierdzenia w witrynie Azure portal, interfejsu użytkownika][update-classic-02-confirm]

Podczas migracji portalu oznacza, że w rejestrze **stan aprowizacji** jest *aktualizowanie*. Jak wspomniano wcześniej, `docker push` operacje zostały wyłączone w trakcie migracji i nie należy usunąć lub aktualizacji, konto magazynu używane przez klasyczny rejestr, podczas gdy migracja jest w toku — w ten sposób może spowodować uszkodzenie obrazu.

![Klasyczny rejestr postęp uaktualniania w witrynie Azure portal, interfejsu użytkownika][update-classic-03-updating]

Po zakończeniu migracji **stan aprowizacji** wskazuje *Powodzenie*, i możesz ponownie `docker push` do rejestru.

![Stan ukończenia w witrynie Azure portal, interfejsu użytkownika uaktualnienia rejestru klasycznego][update-classic-04-updated]

## <a name="next-steps"></a>Kolejne kroki

Po uaktualnieniu klasyczny rejestr do warstwy podstawowa, standardowa lub Premium, Azure nie używa już konta magazynu, która pierwotnie wspierana rejestru klasycznego. Aby zmniejszyć koszt, rozważ usunięcie konta magazynu lub kontenera obiektów Blob w ramach konta, który zawiera stary obrazów kontenerów.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com