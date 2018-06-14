---
title: Jednostki SKU rejestru kontenera platformy Azure
description: Porównanie różnych warstwach usług dostępnych w rejestrze kontenera platformy Azure.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: a8dcc6fc60b80a19c4edebd57fdad5bb10cfdd0b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165218"
---
# <a name="azure-container-registry-skus"></a>Jednostki SKU rejestru kontenera platformy Azure

Rejestru kontenera platformy Azure (ACR) są dostępne w wielu warstw usług, znany jako jednostki SKU. Te jednostki SKU zapewniają przewidywalne ceny i kilka opcji wyrównywania do wzorców pojemność i użycie prywatnego rejestru Docker na platformie Azure.

| SKU | Zarządzane | Opis |
| --- | :-------: | ----------- |
| **Podstawowa** | Yes | Punkt wejścia optymalizacji kosztów dla deweloperów poznawania rejestru kontenera platformy Azure. Rejestry organizacji IANA podstawowa mają te same możliwości programowe jako standardowa i Premium (integracji uwierzytelniania usługi Azure Active Directory, usuwanie obrazów i punkty zaczepienia sieci web), istnieją jednak ograniczenia rozmiaru i użycia. |
| **Standardowa** | Yes | Rejestry organizacji IANA standardowe oferuje te same możliwości jako podstawowa z limitami zasobów pamięci masowej i przepływność obrazu. Rejestry organizacji IANA standardowe powinien zaspokoić potrzeby większości scenariuszy produkcji. |
| **Premium** | Yes | Rejestry organizacji IANA Premium zawierają wyższe limity ograniczenia, takie jak magazyn i jednoczesnych operacji włączania dużych scenariuszy. Oprócz wyższej przepustowości obrazu Premium dostępne są dodatkowe funkcje, takie jak [— replikacja geograficzna] [ container-registry-geo-replication] zarządzania pojedynczego rejestru w wielu regionach, obsługa rejestru sieci Zamknij wszystkie wdrożenia. |
| Wdrożenie klasyczne | Nie | Rejestr Classic jednostki SKU włączone początkowa wersja usługi rejestru kontenera platformy Azure w usłudze Azure. Rejestry organizacji IANA klasycznego bazują na konto magazynu Azure tworzy w ramach subskrypcji, co ogranicza możliwość dla ACR zapewnienie wyższego poziomu funkcji, takich jak zwiększona przepływność i replikacja geograficzna. Ze względu na ich ograniczone możliwości planujemy zastąpić klasycznego jednostki SKU w przyszłości. |

Wybór wyższego poziomu jednostki SKU zawiera więcej wydajność i skalę, jednak wszystkie jednostki SKU zarządzanych zapewniają te same możliwości programowe. Z wielu warstw usług możesz rozpocząć pracę z Basic, a następnie przekonwertować standardowa i Premium wraz ze wzrostem użycia z rejestru.

> [!NOTE]
> Z powodu planowanych amortyzacja rejestru Classic jednostka SKU firma Microsoft zaleca się, że używasz dla wszystkich nowych rejestrów podstawowa, standardowa lub Premium. Uzyskać informacje o konwersji istniejącej rejestru klasycznym, zobacz [uaktualnienia rejestru Classic][container-registry-upgrade].
>

## <a name="managed-vs-unmanaged"></a>Zarządzane i niezarządzane

Podstawowa, standardowa i Premium jednostki SKU są nazywane zbiorczo *zarządzane* rejestrów i rejestrów klasycznym jako *niezarządzane*. Podstawowa różnica między nimi jest, jak są przechowywane obrazy kontenera.

### <a name="managed-basic-standard-premium"></a>Zarządzane (Basic, Standard i Premium)

Zarządzane rejestrów korzyści z magazynu obrazu całkowicie zarządza Azure. Oznacza to, że konto magazynu, w którym są przechowywane obrazy nie jest wyświetlana w ramach Twojej subskrypcji platformy Azure. Ma wiele zalet uzyskane przy użyciu jednej z rejestru zarządzanej jednostki SKU, omówione szczegółowe w [kontenera magazynu obrazu w rejestrze kontenera Azure][container-registry-storage]. Ten artykuł dotyczy jednostki SKU zarządzanych rejestru i ich funkcji.

### <a name="unmanaged-classic"></a>Niezarządzane (klasyczne)

Rejestry organizacji IANA klasycznego są "niezarządzanych" w tym sensie, że konto magazynu, aby utworzyć kopię zapasową rejestru Classic znajduje się w *Twojego* subskrypcji platformy Azure. Tak jest odpowiedzialny za zarządzanie konta magazynu, w którym są przechowywane obrazy kontenera. Z rejestrów niezarządzane, nie można przełączać jednostki SKU stosownie do potrzeb (inne niż [uaktualniania] [ container-registry-upgrade] zarządzanych rejestru), i kilka funkcji rejestrów zarządzanych (na przykład są niedostępne Usuwanie obrazu kontenera, [— replikacja geograficzna][container-registry-geo-replication], i [elementów webhook][container-registry-webhook]).

Aby uzyskać więcej informacji na temat uaktualniania rejestru klasycznego do jednego z zarządzanego jednostki SKU zobacz [uaktualnienia rejestru Classic][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Jednostka SKU funkcji macierzy

W poniższej tabeli przedstawiono funkcje i limity warstwy usług podstawowa, standardowa i Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Zmiana jednostki SKU

Możesz zmienić SKU rejestru z wiersza polecenia platformy Azure lub w portalu Azure. Możesz można swobodnie przemieszczać się między zarządzanego jednostki SKU tak długo, jak SKU rozpoczynasz korzystanie z ma wymagany maksymalnej pojemności magazynu. Po przełączeniu do jednego z zarządzanego jednostki SKU z klasycznym, nie można przenieść ponownie Classic — jest jednokierunkowa konwersji.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby przenieść jednostki SKU w wiersza polecenia platformy Azure, użyj [aktualizacji acr az] [ az-acr-update] polecenia. Na przykład, aby przełączyć się do Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

W rejestrze kontenera **omówienie** w portalu Azure wybierz **aktualizacji**, następnie wybierz nowy **SKU** z listy rozwijanej jednostki SKU.

![Aktualizacja kontenera rejestr jednostki SKU w portalu Azure][update-registry-sku]

Jeśli masz rejestru klasycznym, nie można wybrać zarządzanego jednostki SKU w portalu Azure. Zamiast tego należy się najpierw [uaktualnienia] [ container-registry-upgrade] zarządzanych rejestru (zobacz [zmiana z klasycznym](#changing-from-classic)).

## <a name="changing-from-classic"></a>Zmiana z klasycznym

Istnieją dodatkowe zagadnienia dotyczące wziąć pod uwagę podczas migracji niezarządzane rejestru klasycznego do jednego z zarządzanego podstawowa, standardowa lub Premium jednostki SKU. Jeśli rejestr Classic zawiera dużą liczbę obrazów i rozmiar wielu gigabajtów, proces migracji może trochę potrwać. Ponadto `docker push` operacje są wyłączone, aż do ukończenia migracji.

Aby uzyskać więcej informacji na temat uaktualniania rejestru klasycznego do jednego z zarządzanego jednostki SKU, zobacz [uaktualnienia rejestru kontenera Classic][container-registry-upgrade].

## <a name="pricing"></a>Cennik

Aby cenach informacji o każdej jednostki SKU rejestru dla kontenera platformy Azure, zobacz [cennik rejestru kontenera][container-registry-pricing].

## <a name="next-steps"></a>Kolejne kroki

**Plan rejestru kontenera platformy Azure**

Odwiedź stronę [plan ACR] [ acr-roadmap] w witrynie GitHub, aby uzyskać informacje o nadchodzących funkcji w usłudze.

**UserVoice rejestru kontenera platformy Azure**

Przesłać opinię i głosuj na nowe sugestii funkcji w [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
