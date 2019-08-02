---
title: Jednostki SKU Azure Container Registry
description: Porównaj różne warstwy usług dostępne w Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311887"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU Azure Container Registry

Azure Container Registry (ACR) jest dostępny w wielu warstwach usług, znanych jako jednostki SKU. Te jednostki SKU zapewniają przewidywalną cenę i kilka opcji dopasowania do wzorców pojemności i użycia prywatnego rejestru platformy Docker na platformie Azure.

| SKU | Zarządzane | Opis |
| --- | :-------: | ----------- |
| **Podstawowa** | Yes | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Podstawowe rejestry mają takie same funkcje programistyczne jak standard i Premium (takie jak Azure Active Directory [integracja](container-registry-authentication.md#individual-login-with-azure-ad)z uwierzytelnianiem, [Usuwanie obrazu][container-registry-delete]i elementy webhook). [][container-registry-webhook] Jednak przepływność dołączonego magazynu i obrazu jest najbardziej odpowiednia w przypadku małych scenariuszy użycia. |
| **Standardowa** | Tak | Standardowe rejestry oferują te same możliwości co podstawowa, dzięki czemu można zwiększyć pojemność magazynu i przepływność obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Tak | Rejestry Premium zapewniają największą ilość dołączonego magazynu i współbieżnych operacji, co umożliwia wykonywanie scenariuszy o dużej pojemności. Oprócz wyższej przepływności obrazów Premium dodaje funkcje, w tym [replikację geograficzną][container-registry-geo-replication] służącą do zarządzania pojedynczym rejestrem w wielu regionach, [zaufania zawartości](container-registry-content-trust.md) dla podpisywania tagów obrazu oraz [zapory i sieci wirtualne (wersja zapoznawcza)](container-registry-vnet.md) do Ogranicz dostęp do rejestru. |
|  Klasyczny (niedostępne*po 2019 kwietnia*) | Nie | Ta jednostka SKU włączyła początkową wersję usługi Azure Container Registry na platformie Azure. Klasyczne rejestry są obsługiwane przez konto magazynu utworzone w ramach subskrypcji przez platformę Azure, co ogranicza możliwość udostępniania funkcji ACR na wyższym poziomie, takich jak zwiększona przepływność i replikacja geograficzna. |

> [!IMPORTANT]
> Jednostka SKU rejestru klasycznego jest **przestarzała**i będzie niedostępna po **2019 kwietnia**. Zalecamy używanie wersji Basic, Standard lub Premium dla wszystkich nowych rejestrów. Przed 2019 kwietnia należy uaktualnić wszystkie istniejące rejestry klasyczne. Informacje o uaktualnianiu znajdują się w temacie [uaktualnianie rejestru klasycznego][container-registry-upgrade].

Jednostki SKU w warstwach Podstawowa, standardowa i Premium (nazywane zbiorczo rejestrami zarządzanymi) zapewniają te same możliwości programistyczne. Wszystkie korzyści z [magazynu obrazów][container-registry-storage] są również zarządzane całkowicie przez platformę Azure. Wybranie jednostki SKU wyższego poziomu zapewnia większą wydajność i skalowalność. W przypadku wielu warstw usług możesz rozpocząć pracę z usługą Basic, a następnie przekonwertować ją na Standard i Premium w miarę wzrostu użycia rejestru.

## <a name="sku-feature-matrix"></a>Macierz funkcji SKU

W poniższej tabeli przedstawiono funkcje i limity warstw usług podstawowa, standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmienianie jednostek SKU

Jednostkę SKU rejestru można zmienić za pomocą interfejsu wiersza polecenia platformy Azure lub w Azure Portal. Można swobodnie poruszać się między zarządzanymi JEDNOSTKAmi SKU, o ile wymagana jest maksymalna pojemność magazynu. Po przełączeniu do jednej z zarządzanych jednostek SKU z klasycznego nie można przenieść z powrotem do klasycznej — jest to jednokierunkowa konwersja.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby poruszać się między jednostkami SKU w interfejsie wiersza polecenia platformy Azure, użyj polecenia [AZ ACR Update][az-acr-update] . Na przykład, aby przełączyć się do warstwy Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W obszarze **Przegląd** rejestru kontenerów w Azure Portal wybierz pozycję **Aktualizuj**, a następnie wybierz nową **jednostkę SKU** z listy rozwijanej jednostka SKU.

![Aktualizowanie jednostki SKU rejestru kontenerów w Azure Portal][update-registry-sku]

Jeśli masz klasyczny rejestr, nie możesz wybrać zarządzanej jednostki SKU w ramach Azure Portal. Zamiast tego należy najpierw [przeprowadzić uaktualnienie][container-registry-upgrade] do rejestru zarządzanego.

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach dla każdej jednostki SKU Azure Container Registry, zobacz [Cennik usługi Container Registry][container-registry-pricing].

Aby uzyskać szczegółowe informacje o cenach transferów danych, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości. 

## <a name="next-steps"></a>Następne kroki

**Azure Container Registry plan**

Odwiedź [Plan ACR][acr-roadmap] w witrynie GitHub, aby uzyskać informacje o nadchodzących funkcjach usługi.

**Azure Container Registry UserVoice**

Prześlij i zagłosuj na nowe sugestie dotyczące funkcji w witrynie [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
