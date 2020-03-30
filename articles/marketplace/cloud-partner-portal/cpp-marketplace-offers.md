---
title: Oferty platformy Azure i appsource marketplace
description: Tworzenie ofert platformy Azure i witryn AppSource Marketplaces i zarządzanie nimi
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: dsindona
ms.openlocfilehash: 7f6fd723355426a49cff032d51da0e09f13e295d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278488"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Oferty platformy Azure i appsource marketplace

W tej pierwszej części tej sekcji przedstawiono ogólne operacje używane do tworzenia ofert platformy Azure i appsource marketplaces i zarządzania nimi.  Ta część zawiera tło, które należy zrozumieć, aby zarządzać określonymi typami ofert, a także informacje techniczne, które są wspólne dla wszystkich typów ofert.  Większość tej sekcji zawiera szczegółowe instrukcje dotyczące tworzenia określonych typów ofert i zarządzania nimi.  

W poniższym klipie wideo przedstawiono różne możliwości i różne typy ofert dostępne w portalu Azure Marketplace lub AppSource.  Obejmuje również ważne techniczne i biznesowe aspekty publikowania aplikacji lub usługi na tych rynkach.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Tworzenie aplikacji i usług dla portalu Azure Marketplace i AppSource — kompilacja 2018**

Aby uzyskać więcej informacji na temat tych rynków, zobacz [Przewodnik po publikacjach w portalu Azure Marketplace i AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Wspólne operacje ofertowe

Proces tworzenia nowej oferty różni się znacznie między typami ofert, na przykład między [ofertą aplikacji platformy Azure](./azure-applications/cpp-azure-app-offer.md) a [ofertą usług konsultingowych.](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md)  Z drugiej strony wiele innych operacji, które wykonujesz w ramach oferty w [portalu Cloud Partner Portal,](https://cloudpartner.azure.com) jest dość standardowych w różnych typach ofert.  Te typowe operacje — w tym publikowanie, wyświetlanie, aktualizowanie i usuwanie — są opisane w sekcji [Zarządzanie ofertami](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Wersja testowa

*Test Drive* to funkcja marketplace, która zapewnia klientom opcję demonstracenia "spróbuj przed zakupem" dla każdej oferty tak włączonej.  Funkcja Dysku testowego jest ograniczona do następującego podzbioru typów ofert: [aplikacje platformy Azure,](./azure-applications/cpp-azure-app-offer.md) [Dynamics 365 Business Central,](../cloud-partner-portal-orig/cpp-business-central-offer.md) [Dynamics 365 for Customer Engagement,](./dyn365ce/cpp-customer-engagement-offer.md) [Dynamics 365 for Finance and Operations,](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) [Aplikacje SaaS](./saas-app/cpp-saas-offer.md)i [maszyny wirtualne.](./virtual-machine/cpp-virtual-machine-offer.md)  Ta funkcja wymaga od wydawcy utworzenia szablonu dysku testowego dostosowanego do jego oferty.  Aby uzyskać więcej informacji, zobacz sekcję [Jazda testowa](./test-drive/what-is-test-drive.md).

Możesz przeglądać istniejące oferty rynku, które mają pokazy z jazdy testowej, stosując [filtr dysku testowego.](https://azuremarketplace.microsoft.com/marketplace/apps?filters=test-drive) 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Typy ofert w portalu Azure Marketplace i AppSource

W poniższej tabeli wymieniono bieżące typy ofert obsługiwane przez [portal cloud partner.](https://cloudpartner.azure.com)  Dla każdego typu oferty zawiera listę rynków, na których można wymienić ofertę, a także ogólny opis technologii rozwiązania oferty.

|                Typ oferty                |  Portal Marketplace  |   Opis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikacja platformy Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Rozwiązanie składa się z co najmniej jednej maszyny wirtualnej (VM), opcjonalny niestandardowy kod platformy Azure, wdrożony za pomocą szablonu usługi Azure Resource Manger.  Wdrożenie może być przez klienta za pośrednictwem szablonu rozwiązania lub zarządzane przez wydawcę. Ten typ jest używany w celu zapewnienia większej elastyczności niż pod warunkiem, typ oferty maszyny wirtualnej.  |
| [Usługi konsultingowe](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | oba | Konsultanci z zastrzeżeniami firmy Microsoft mogą wystawiać swoje usługi specyficzne dla domeny w witrynie Azure Marketplace lub AppSource.  Ich wiedza specjalistyczna pomaga klientom w ocenie ich problemów oraz tworzeniu i wdrażaniu odpowiednich rozwiązań, aby osiągnąć swoje cele biznesowe.  |
| [Kontener](./containers/cpp-containers-offer.md)  | Azure | Rozwiązanie jest obraz kontenera platformy Docker aprowizacji jako usługi opartej na usłudze Kubernetes lub wystąpienia kontenera platformy Azure. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Pakiet rozszerzający ten system planowania zasobów przedsiębiorstwa (ERP) i system zarządzania przedsiębiorstwem. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Pakiet rozszerzający ten system zarządzania zasobami klienta (CRM) za pośrednictwem modułów sprzedaży, usługi, usługi projektu i serwisu terenowego  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Pakiet rozszerzający tę usługę planowania zasobów przedsiębiorstwa (ERP), która obsługuje zaawansowane finansowanie, operacje, produkcję i zarządzanie łańcuchem dostaw |
| [Moduł IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontener zgodny z docker, który działa na urządzeniu usługi IoT Edge.  Składa się z małych modułów obliczeniowych, które używają kombinacji kodu niestandardowego, innych usług platformy Azure i usług innych firm. |
| [Aplikacja usługi Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikacja Power BI, która pakuje konfigurowalną zawartość usługi Power BI, w tym zestawy danych, raporty i pulpity nawigacyjne |
| [Aplikacja SaaS](./saas-app/cpp-saas-offer.md) | Azure | Rozwiązanie to subskrypcja oprogramowania jako usługi zarządzana przez wydawcę, którą użytkownicy logują się za pośrednictwem dostosowanego interfejsu korzystającego z usługi Azure Active Directory. |
| [Maszyna wirtualna](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Rozwiązanie znajduje się w ramach jednej maszyny wirtualnej wdrożonej w ramach subskrypcji klienta.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Aby uzyskać więcej informacji, zobacz [Przewodnik publikowania według typu oferty](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Następne kroki

O ogólnych operacjach, które możesz wykonać w ofertach marketplace, oraz o ich wspólnych atrybutach technicznych i zasobach, dowiesz się w artykule [Zarządzanie ofertami.](./manage-offers/cpp-manage-offers.md)
