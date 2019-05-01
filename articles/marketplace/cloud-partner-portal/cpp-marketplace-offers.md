---
title: Platforma Azure i oferty w witrynie Marketplace usługi AppSource
description: Tworzenie i zarządzanie nimi ofert platformy Azure i usługi AppSource rynków
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: 2d440adc5111db11486d1fc606abbda0ffae9595
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942540"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Platforma Azure i oferty w witrynie Marketplace usługi AppSource

To pierwsza część w tej sekcji przedstawiono ogólnych operacji umożliwia tworzenie i zarządzanie ofertami dla platformy Azure i usługi AppSource rynków.  Ta część zawiera tła, o których trzeba wiedzieć, aby zarządzać określonych typów ofert, a także informacji technicznych, które są wspólne dla wszystkich oferuje typy.  Większość ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu tworzenia i zarządzania nimi określonych typów ofert.  

Poniższy klip wideo zawiera wprowadzenie różnych funkcji i typów różnych ofert dostępnych w portalu Azure Marketplace lub w usłudze AppSource.  Obejmuje ona również ważne technicznych i biznesowych aspektów publikowania aplikacji lub usługi w tych rynków.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Tworzenie aplikacji i usług do witryny Azure Marketplace i AppSource — tworzenie 2018 r.**

Aby uzyskać więcej informacji na temat tych rynków, zobacz [Podręcznik publikowania w portalu Azure Marketplace i AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Typowe operacje oferty

Proces tworzenia nowej oferty różni się znacząco różnych typów ofert, na przykład między [oferty usługi Azure application](./azure-applications/cpp-azure-app-offer.md) i [oferty usługi konsultingowe](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  Natomiast w wielu innych operacji można wykonać na ofertę w [portalu Cloud Partner](https://cloudpartner.azure.com) dość są standaryzowane różnych typów ofert.  Te typowych operacji — w tym publikowania, wyświetlanie stanu, update i delete — znajdują się w sekcji [Zarządzanie ofertami](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Wersja testowa

*Wersja testowa* jest funkcją portalu marketplace, która dostarcza klientom korzystającym z pokazu opcji "try przed zakupem" dla każdej oferty, dlatego jest włączone.  Możliwości wersji testowej jest ograniczona do podzbioru następujących typów ofert: [Aplikacje platformy Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [ Aplikacje SaaS](./saas-app/cpp-saas-offer.md), i [maszyn wirtualnych](./virtual-machine/cpp-virtual-machine-offer.md).  Ta funkcja wymaga z wydawcą, aby utworzyć szablon wersji testowej dostosowaną do swojej oferty.  Aby uzyskać więcej informacji, zobacz sekcję [wersji testowej](./test-drive/what-is-test-drive.md).

Możesz przeglądać istniejące oferty w witrynie marketplace, które mają wersję testową pokazów, stosując [filtru dysku testów](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive). 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace i AppSource oferuje typy

W poniższej tabeli wymieniono bieżącego typów ofert objęte [portalu Cloud Partner](https://cloudpartner.azure.com).  Dla każdego typu oferty Wyświetla ona marketplace(s), gdzie mogą być wyświetlane oferty, a także ogólny opis technologii rozwiązania oferty.

|                Typ oferty                |  Portal Marketplace  |   Opis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikacja platformy Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Rozwiązanie składa się z co najmniej jeden wdrożone maszyny wirtualne (VM), opcjonalny niestandardowego kodu platformy Azure za pomocą szablonu usługi Azure Resource Manager.  Wdrożenie może być albo przez klienta za pomocą szablonu rozwiązania lub zarządzana przez wydawcę. Ten typ jest używany do zapewniają większą elastyczność niż typ oferty podanej maszyny wirtualnej.  |
| [Usługa DORADCZA](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Oba | Kwalifikowana Microsoft konsultantów można wyświetlić listę swoich usług specyficznego dla domeny na portalu Azure Marketplace lub w usłudze AppSource.  Swoją wiedzę ułatwia klientom oceny swoje problemy oraz tworzenie i wdrażanie rozwiązań prawo, aby spełniać swoje cele biznesowe.  |
| [Kontener](./containers/cpp-containers-offer.md)  | Azure | Rozwiązanie jest inicjowana jak dla usługi Kubernetes lub Azure Container instances obraz kontenera Docker. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Pakiet, który rozszerza to planowanie zasobów przedsiębiorstwa (ERP) i systemu zarządzania biznesowych. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Pakiet, który rozszerza ten klient systemu zarządzania (CRM) zasobu, za pośrednictwem sprzedaży, usługi, usługi projektu i modułów usługi pola  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Pakiet, który rozszerza tej usługi (ERP), która obsługuje zaawansowane do planowania zasobów przedsiębiorstwa Finanse i operacje, produkcji i zarządzanie łańcuchem zaopatrzenia |
| [Moduł usługi IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontener zgodnego z platformą Docker, która jest uruchamiana na urządzeniu usługi IoT Edge.  Zawiera ona modułów małych obliczeniowych korzystających z kombinacji niestandardowego kodu, innych usług platformy Azure i usługi innych firm 3. |
| [Aplikacja Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Aplikacja Power BI, które pakiety można dostosować zawartość usługi Power BI, w tym zestawy danych, raporty i pulpity nawigacyjne |
| [Aplikacja SaaS](./saas-app/cpp-saas-offer.md) | Azure | Rozwiązanie jest subskrypcją oprogramowanie jako usługa zarządzana przez wydawcę, w których użytkownicy logują się za pośrednictwem dostosowanego interfejsu, który używa usługi Azure Active Directory. |
| [Maszyna wirtualna](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Rozwiązanie znajduje się w obrębie jednej maszyny wirtualnej wdrożone w subskrypcji klienta.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Aby uzyskać więcej informacji, zobacz [Podręcznik przez typ oferty publikowania](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Kolejne kroki

Zawiera informacje o ogólnych operacji można wykonywać na oferty w witrynie marketplace i ich atrybuty wspólne technicznych i zasobów w artykule [Zarządzanie ofertami](./manage-offers/cpp-manage-offers.md).
