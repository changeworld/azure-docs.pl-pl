---
title: Replikacja geograficzna rejestru
description: Get started creating and managing a geo-replicated Azure container registry, which enables the registry to serve multiple regions with multi-master regional replicas.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456445"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Firmy, które chcą zaznaczyć swoją obecność lokalną lub wykonywać dynamiczną kopię zapasową, decydują się na uruchamianie usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem jest umieszczenie rejestru kontenerów w każdym regionie, w którym uruchamiane są obrazy. Umożliwia to wykonywanie operacji w pobliskiej sieci, dzięki czemu transfery w warstwie obrazu są szybkie i niezawodne. Replikacja geograficzna umożliwia działanie rejestru kontenerów platformy Azure jako pojedynczego rejestru obsługującego wiele regionów przy użyciu wielowzorcowych rejestrów regionalnych. 

Rejestr z replikacją geograficzną zapewnia następujące korzyści:

* Możliwość używania pojedynczych nazw rejestru/obrazu/tagu w wielu regionach
* Dostęp do rejestru z pobliskiej sieci z poziomu wdrożeń regionalnych
* Brak dodatkowych opłat za ruch wychodzący — obrazy są pobierane z lokalnego, zreplikowanego rejestru znajdującego się w tym samym regionie co host kontenera
* Pojedyncza usługa zarządzania rejestrem w wielu regionach

> [!NOTE]
> Jeśli zachodzi potrzeba obsługi kopii obrazów kontenerów w więcej niż jednym rejestrze kontenerów platformy Azure, usługa Azure Container Registry obsługuje również [importowanie obrazów](container-registry-import-images.md). Na przykład w ramach przepływu pracy DevOps można zaimportować obraz z rejestru deweloperskiego do rejestru produkcyjnego bez konieczności używania poleceń platformy Docker.
>

## <a name="example-use-case"></a>Przykładowy przypadek użycia
Firma Contoso posiada dostępną publicznie witryny internetową zlokalizowaną w Stanach Zjednoczonych, Kanadzie i Europie. Aby obsługiwać te rynki przy użyciu zawartości lokalnej dostępnej w pobliskiej sieci, firma Contoso uruchamia klastry usługi [Azure Kubernetes Service](/azure/aks/) (AKS) w regionach Zachodnie stany USA, Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Aplikacja internetowa wdrożona jako obraz platformy Docker korzysta z tego samego kodu i obrazu we wszystkich regionach. Zawartość, lokalna w danym regionie, jest pobierana z bazy danych aprowizowanej w sposób unikatowy w każdym regionie. Dla każdego wdrożenia regionalnego istnieje unikatowa konfiguracja zasobów, takich jak lokalna baza danych.

Zespół programistyczny znajduje się w Seattle w stanie Waszyngton i korzysta z centrum danych w regionie Zachodnie stany USA.

![Wypychanie do wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Wypychanie do wielu rejestrów*

Zanim były używane funkcje replikacji geograficznej, firma Contoso posiadała rejestr znajdujący się w regionie Zachodnie stany USA oraz dodatkowy rejestr w regionie Europa Zachodnia. W celu obsługi tych regionów zespół programistyczny wypychał obrazy do dwóch różnych rejestrów.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Ściąganie z wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ściąganie z wielu rejestrów*

Typowe wyzwania związane z korzystaniem z wielu rejestrów:

* Klastry w regionach Wschodnie stany USA, Zachodnie stany USA i Kanada Środkowa są ściągane z rejestru w regionie Zachodnie stany USA, co powoduje naliczanie opłat za ruch wychodzący, ponieważ każdy z tych zdalnych hostów kontenerów ściąga obrazy z centrów danych w regionie Zachodnie stany USA.
* Zespół programistyczny musi wypychać obrazy do rejestrów w regionach Zachodnie stany USA i Europa Zachodnia.
* Zespół programistyczny musi konfigurować i konserwować każde wdrożenie regionalne przy użyciu nazw obrazów odwołujących się do rejestru lokalnego.
* Dostęp do rejestru musi być skonfigurowany dla każdego regionu.

## <a name="benefits-of-geo-replication"></a>Korzyści z replikacji geograficznej

![Ściąganie z rejestru z replikacją geograficzną](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Funkcja replikacji geograficznej usługi Azure Container Registry zapewnia następujące korzyści:

* Zarządzanie pojedynczym rejestrem w wielu regionach: `contoso.azurecr.io`
* Zarządzanie jedną konfiguracją wdrożeń obrazów, ponieważ we wszystkich regionach jest używany ten sam adres URL obrazu: `contoso.azurecr.io/public/products/web:1.2`
* Push to a single registry, while ACR manages the geo-replication. You can configure regional [webhooks](container-registry-webhook.md) to notify you of events in specific replicas.

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej

Konfigurowanie replikacji geograficznej jest równie proste, co klikanie regionów na mapie. You can also manage geo-replication using tools including the [az acr replication](/cli/azure/acr/replication) commands in the Azure CLI, or deploy a registry enabled for geo-replication with an [Azure Resource Manager template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Replikacja geograficzna jest funkcją dotyczącą tylko [rejestrów w warstwie Premium](container-registry-skus.md). Jeśli Twój rejestr nie znajduje się jeszcze w warstwie Premium, możesz zmienić warstwę z warstwy Podstawowa i Standardowa na warstwę Premium w [witrynie Azure Portal](https://portal.azure.com):

![Przełączanie jednostek SKU w witrynie Azure Portal](media/container-registry-skus/update-registry-sku.png)

Aby skonfigurować replikację geograficzną dla rejestru w warstwie Premium, zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Przejdź do używanego rejestru Azure Container Registry, a następnie wybierz pozycję **Replikacje**:

![Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zostanie wyświetlona mapa przedstawiająca wszystkie bieżące regiony platformy Azure:

 ![Mapa regionów w witrynie Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Niebieskie sześciokąty reprezentują bieżące repliki
* Zielone sześciokąty reprezentują możliwe regiony replik
* Szare sześciokąty reprezentują regiony platformy Azure, które nie są jeszcze dostępne dla replikacji

Aby skonfigurować replikę, wybierz zielony sześciokąt, a następnie wybierz pozycję **Utwórz**:

 ![Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal](media/container-registry-geo-replication/create-replication.png)

Aby skonfigurować dodatkowe repliki, wybierz zielone sześciokąty dla innych regionów, a następnie kliknij pozycję **Utwórz**.

Usługa ACR rozpocznie synchronizowanie obrazów między skonfigurowanymi replikami. Po ukończeniu tego zadania w portalu zostanie wyświetlony stan *Gotowe*. Stan repliki w portalu nie jest automatycznie aktualizowany. Użyj przycisku odświeżania, aby wyświetlić zaktualizowany stan.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Considerations for using a geo-replicated registry

* Each region in a geo-replicated registry is independent once set up. Azure Container Registry SLAs apply to each geo-replicated region.
* When you push or pull images from a geo-replicated registry, Azure Traffic Manager in the background sends the request to the registry located in the region closest to you.
* After you push an image or tag update to the closest region, it takes some time for Azure Container Registry to replicate the manifests and layers to the remaining regions you opted into. Larger images take longer to replicate than smaller ones. Images and tags are synchronized across the replication regions with an eventual consistency model.
* To manage workflows that depend on push updates to a geo-replicated , we recommend that you configure [webhooks](container-registry-webhook.md) to respond to the push events. You can set up regional webhooks within a geo-replicated registry to track push events as they complete across the geo-replicated regions.

## <a name="delete-a-replica"></a>Delete a replica

After you've configured a replica for your registry, you can delete it at any time if it's no longer needed. Delete a replica using the Azure portal or other tools such as the [az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) command in the Azure CLI.

To delete a replica in the Azure portal:

1. Navigate to your Azure Container Registry, and select **Replications**.
1. Select the name of a replica, and select **Delete**. Confirm that you want to delete the replica.

> [!NOTE]
> You can't delete the registry replica in the *home region* of the registry, that is, the location where you created the registry. You can only delete the home replica by deleting the registry itself.

## <a name="geo-replication-pricing"></a>Cennik replikacji geograficznej

Replikacja geograficzna jest funkcją [jednostki SKU w warstwie Premium](container-registry-skus.md) usługi Azure Container Registry. Replikacja rejestru w żądanych regionach wiąże się z naliczaniem opłat za rejestr w warstwie Premium w danym regionie.

W poprzednim przykładzie firma Contoso skonsolidowała dwa rejestry w ramach jednego, dodając repliki do regionu Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Firma Contoso zapłaciłaby miesięcznie czterokrotną opłatę za warstwę Premium bez dodatkowej konfiguracji ani zarządzania. W każdym regionie obrazy są teraz ściągane lokalnie, co zwiększa wydajność i niezawodność bez ponoszenia opłat za ruch wychodzący w sieci z regionu Zachodnie stany USA do regionu Kanada Środkowa i Wschodnie stany USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Troubleshoot push operations with geo-replicated registries
 
A Docker client that pushes an image to a geo-replicated registry may not push all image layers and its manifest to a single replicated region. This may occur because Azure Traffic Manager routes registry requests to the network-closest replicated registry. If the registry has two *nearby* replication regions, image layers and the manifest could be distributed to the two sites, and the push operation fails when the manifest is validated. This problem occurs because of the way the DNS name of the registry is resolved on some Linux hosts. This issue doesn't occur on Windows, which provides a client-side DNS cache.
 
If this problem occurs, one solution is to apply a client-side DNS cache such as `dnsmasq` on the Linux host. This helps ensure that the registry's name is resolved consistently. If you're using a Linux VM in Azure to push to a registry, see options in [DNS Name Resolution options for Linux virtual machines in Azure](../virtual-machines/linux/azure-dns.md).

To optimize DNS resolution to the closest replica when pushing images, configure a geo-replicated registry in the same Azure regions as the source of the push operations, or the closest region when working outside of Azure.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z trzyczęściową serią samouczków dostępną w sekcji [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md). Opisano w niej tworzenie rejestru z replikacją geograficzną, tworzenie kontenera, a następnie wdrażanie go w ramach wielu regionalnych aplikacji usługi Web Apps na potrzeby wystąpień kontenerów za pomocą pojedynczego polecenia `docker push`.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md)
