---
title: Warstwy usług i jednostki SKU
description: Dowiedz się więcej o funkcjach i ograniczeniach w warstwach usług Basic, Standard i Premium (SKU) rejestru kontenerów platformy Azure.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456274"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU rejestru kontenerów platformy Azure

Usługa Azure Container Registry (ACR) jest dostępna w wielu warstwach usług, znanych jako jednostki SKU. Te jednostki SKU zapewniają przewidywalne ceny i kilka opcji dostosowywania do wzorców pojemności i użycia prywatnego rejestru platformy Docker na platformie Azure.

| SKU | Opis |
| --- | ----------- |
| **Podstawowa (Basic)** | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Rejestry podstawowe mają takie same możliwości programowe jak standard i premium (takie jak [integracja uwierzytelniania](container-registry-authentication.md#individual-login-with-azure-ad)usługi Azure Active Directory, [usuwanie obrazów][container-registry-delete]i [pliki webhook).][container-registry-webhook] Jednak uwzględnione magazynu i przepływności obrazu są najbardziej odpowiednie dla scenariuszy niższego użycia. |
| **Standardowa** | Rejestry standardowe oferują takie same możliwości jak basic, ze zwiększoną przepustowością dołączonej pamięci masowej i obrazu. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Rejestry premium zapewniają największą ilość dołączonego magazynu i równoczesnych operacji, umożliwiając scenariusze dużej liczby. Oprócz wyższej przepływności obrazu usługa Premium dodaje funkcje, takie jak [replikacja geograficzna][container-registry-geo-replication] do zarządzania pojedynczym rejestrem w wielu regionach, [zaufanie do zawartości](container-registry-content-trust.md) do podpisywania znaczników obrazu, [zapory i sieci wirtualne (wersja zapoznawcza),](container-registry-vnet.md) aby ograniczyć dostęp do rejestru. |

Jednostki SKU basic, standard i premium zapewniają te same możliwości programowe. Wszystkie one również korzystać z [magazynu obrazów][container-registry-storage] zarządzanych w całości przez platformę Azure. Wybór jednostki SKU wyższego poziomu zapewnia większą wydajność i skalę. Z wielu warstw usług, można rozpocząć z basic, a następnie konwertować do standardu i premium w miarę zwiększania użycia rejestru.

## <a name="sku-features-and-limits"></a>Funkcje i limity jednostki SKU

W poniższej tabeli przedstawiono funkcje i limity warstw usług Basic, Standard i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmiana jednostek SKU

Jednostkę SKU rejestru można zmienić za pomocą interfejsu wiersza polecenia platformy Azure lub w witrynie Azure portal. Można swobodnie przełączać się między jednostkami SKU, o ile jednostka SKU, do której przełączasz się, ma wymaganą maksymalną pojemność magazynu. 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przejść między jednostkami SKU w wierszu polecenia interfejsu wiersza polecenia platformy Azure, należy użyć polecenia [az acr update.][az-acr-update] Na przykład, aby przełączyć się na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal Azure

W **rejestrze** kontenerów przegląd w witrynie Azure portal wybierz pozycję **Aktualizuj**, a następnie wybierz nową **jednostkę SKU** z listy rozwijanej SKU.

![Aktualizowanie jednostki SKU rejestru kontenerów w witrynie Azure portal][update-registry-sku]

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach dla każdej jednostki SKU rejestru kontenerów platformy Azure, zobacz [Cennik rejestru kontenerów][container-registry-pricing].

Aby uzyskać szczegółowe informacje na temat cen przesyłania danych, zobacz [Szczegóły cen przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Następne kroki

**Mapa drogowa rejestru kontenerów platformy Azure**

Odwiedź [mapę drogową ACR][acr-roadmap] w usłudze GitHub, aby znaleźć informacje o nadchodzących funkcjach w usłudze.

**Głos użytkownika rejestru kontenerów platformy Azure**

Prześlij i głosuj na nowe sugestie funkcji w [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
