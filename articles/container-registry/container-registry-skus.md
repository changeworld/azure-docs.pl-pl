---
title: Jednostki SKU rejestru kontenerów platformy Azure
description: Porównaj z różnymi warstwami usług dostępnych w usłudze Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521962"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU rejestru kontenerów platformy Azure

Usługa Azure Container Registry (ACR) jest dostępna w wielu warstwach usług, znane jako jednostki SKU. Te jednostki SKU zapewniają przewidywalne ceny i kilka opcji umożliwiających dopasowanie do wzorców pojemność i użycie prywatnego rejestru platformy Docker na platformie Azure.

| SKU | Zarządzane | Opis |
| --- | :-------: | ----------- |
| **Podstawowa** | Yes | Zoptymalizowany pod kątem kosztów punkt wejścia dla deweloperów poznających usługę Azure Container Registry. Rejestry podstawowe mają te same możliwości programowe jako Standard i Premium (takich jak Azure Active Directory [integracja uwierzytelniania](container-registry-authentication.md#individual-login-with-azure-ad), [obraz usuwania][container-registry-delete], i [elementów webhook][container-registry-webhook]). Jednak uwzględnionego magazynu i przepływności obrazu są najbardziej odpowiednie dla dolnej scenariusze użycia. |
| **Standardowa** | Yes | Standardowymi rejestrami oferuje te same możliwości jak podstawowe, za pomocą zwiększona przepływność dołączony magazyn i obrazów. Rejestry w warstwie Standardowa powinny spełniać wymagania większości scenariuszy produkcyjnych. |
| **Premium** | Yes | Rejestrach w warstwie Premium zapewniają największa ilość magazynu w pakiecie i obsługi jednoczesnych, scenariusze dużej liczby. Oprócz większą przepływność obrazu, Premium dodaje funkcje, w tym [geografickou replikaci] [ container-registry-geo-replication] Zarządzanie pojedynczym rejestrem w wielu regionach, [zawartości zaufania(wersjazapoznawcza)](container-registry-content-trust.md) podpisywania tag obrazu i [zapory i sieci wirtualne (wersja zapoznawcza)](container-registry-vnet.md) do ograniczania dostępu do rejestru. |
|  Klasyczny (*nie jest dostępna od kwietnia 2019*) | Nie | Ta jednostka SKU włączone wstępną wersję usługi Azure Container Registry na platformie Azure. Rejestry klasyczne są wspierane przez konto magazynu, które platforma Azure tworzy się w ramach subskrypcji, który ogranicza możliwość rejestru Azure container Registry w celu zapewnienia możliwości wyższego poziomu, takie jak zwiększona przepływność i replikacji geograficznej. |

> [!IMPORTANT]
> Rejestru klasycznego jest jednostka SKU **przestarzałe**, a będzie niedostępna po **2019 kwietnia**. Zalecamy używanie podstawowa, standardowa lub Premium dla wszystkich nowych rejestrów. Wszystkie istniejące rejestrów Classic powinny zostać uaktualnione przed kwietnia 2019 r. Aby uzyskać informacje o uaktualnianiu, zobacz [uaktualnianie rejestru klasycznego][container-registry-upgrade].

Podstawowa, standardowa i Premium jednostki SKU (nazywane zbiorczo o nazwie *zarządzane rejestry*) oferować takie same możliwości programowe. One również wszystkie korzyści z [obrazu magazynu] [ container-registry-storage] zarządzane w całości na platformie Azure. Wybieranie wyższego poziomu jednostki SKU zapewnia większą wydajność i skalę. Z wieloma warstwami usług możesz zacząć korzystać z Basic, a następnie przekonwertować Standard i Premium wzrostu użycia rejestru.

## <a name="sku-feature-matrix"></a>Tabela funkcji jednostki SKU

W poniższej tabeli przedstawiono funkcje i limity warstwy usług podstawowa, standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmiana jednostki SKU

Można zmienić jednostki SKU rejestru, przy użyciu wiersza polecenia platformy Azure lub w witrynie Azure portal. Możesz przenosić za darmo między zarządzanymi jednostkami SKU tak długo, jak jednostki SKU na ma wymagane maksymalnej pojemności. Po przełączeniu się do jednego z zarządzanymi jednostkami SKU z wersji klasycznej, nie można przenieść ponownie Classic — jest konwersją jednokierunkowe.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść między jednostkami SKU w interfejsie wiersza polecenia platformy Azure, użyj [az acr update] [ az-acr-update] polecenia. Na przykład, aby przełączyć się do warstwy Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W rejestrze kontenerów **Przegląd** w witrynie Azure portal wybierz **aktualizacji**, następnie wybierz nowy **jednostki SKU** z listy rozwijanej jednostki SKU.

![Aktualizacja jednostki SKU rejestru kontenerów w witrynie Azure portal][update-registry-sku]

W przypadku rejestrze klasycznym nie można wybrać zarządzaną jednostką SKU w witrynie Azure portal. Zamiast tego należy się najpierw [uaktualnienia] [ container-registry-upgrade] do rejestru zarządzanego.

## <a name="pricing"></a>Cennik

Aby uzyskać informacje na każdym z jednostki SKU rejestru kontenerów platformy Azure, zobacz [ceny Container Registry][container-registry-pricing].

Aby uzyskać szczegóły cennika transferów danych, zobacz [przepustowość — szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Kolejne kroki

**Harmonogram działania dla usługi Azure Container Registry**

Odwiedź stronę [plan ACR] [ acr-roadmap] w serwisie GitHub, aby znaleźć informacje o nadchodzących funkcjach w usłudze.

**Usługa Azure Container Registry w witrynie UserVoice**

Przesyłaj i głosuj na propozycje dotyczące nowych funkcji w [ACR UserVoice][container-registry-uservoice].

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
