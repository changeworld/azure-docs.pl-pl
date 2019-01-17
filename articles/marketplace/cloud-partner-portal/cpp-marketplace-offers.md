---
title: Platforma Azure i oferty w witrynie Marketplace usługi AppSource | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi ofert platformy Azure i usługi AppSource rynków
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 0d6879c6b9be06af4bb289761cc8f26b7e56d18e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355852"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Platforma Azure i oferty w witrynie Marketplace usługi AppSource

To pierwsza część w tej sekcji przedstawiono ogólnych operacji umożliwia tworzenie i zarządzanie ofertami dla platformy Azure i usługi AppSource rynków.  Ta część zawiera tła, o których trzeba wiedzieć, aby zarządzać określonych typów ofert, a także informacji technicznych, które są wspólne dla wszystkich oferuje typy.  Większość ta sekcja zawiera szczegółowe instrukcje dotyczące sposobu tworzenia i zarządzania nimi określonych typów ofert.  

Poniższy klip wideo zawiera wprowadzenie różnych funkcji i typów różnych ofert dostępnych w portalu Azure Marketplace lub w usłudze AppSource.  Obejmuje ona również ważne technicznych i biznesowych aspektów publikowania aplikacji lub usługi w tych rynków.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Tworzenie aplikacji i usług do witryny Azure Marketplace i AppSource — tworzenie 2018 r.**

Aby uzyskać więcej informacji na temat tych rynków, zobacz [Podręcznik publikowania w portalu Azure Marketplace i AppSource](../marketplace-publishers-guide.md).


## <a name="azure-marketplace-and-appsource-offer-types"></a>Azure Marketplace i AppSource oferuje typy

W poniższej tabeli wymieniono bieżącego typów ofert objęte [portalu Cloud Partner](https://cloudpartner.azure.com).  Dla każdego typu oferty Wyświetla ona marketplace(s), gdzie mogą być wyświetlane oferty, a także ogólny opis technologii rozwiązania oferty.

|                Typ oferty                |  Portal Marketplace  |   Opis                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Aplikacja platformy Azure](./azure-applications/cpp-azure-app-offer.md) | Azure | Rozwiązanie składa się z co najmniej jeden wdrożone maszyny wirtualne (VM), opcjonalny niestandardowego kodu platformy Azure za pomocą szablonu usługi Azure Resource Manager.  Wdrożenie może być albo przez klienta za pomocą szablonu rozwiązania lub zarządzana przez wydawcę. Ten typ jest używany do zapewniają większą elastyczność niż typ oferty podanej maszyny wirtualnej.  |
| [Usługa DORADCZA](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Oba | Kwalifikowana Microsoft konsultantów można wyświetlić listę swoich usług specyficznego dla domeny na portalu Azure Marketplace lub w usłudze AppSource.  Swoją wiedzę ułatwia klientom oceny swoje problemy oraz tworzenie i wdrażanie rozwiązań prawo, aby spełniać swoje cele biznesowe.  |
| [Kontener](./containers/cpp-containers-offer.md)  | Azure | Rozwiązanie jest inicjowana jak dla usługi Kubernetes lub Azure Container instances obraz kontenera Docker. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Pakiet, który rozszerza to planowanie zasobów przedsiębiorstwa (ERP) i systemu zarządzania biznesowych. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Pakiet, który rozszerza ten klient systemu zarządzania (CRM) zasobu, za pośrednictwem sprzedaży, usługi, usługi projektu i modułów usługi pola.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Pakiet, który rozszerza ten zasób przedsiębiorstwa (ERP), usługa planowania tego Finanse obsługuje zaawansowane operacje, produkcji i zarządzania łańcuchem dostaw. |
| [Moduł usługi IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Azure | Kontener zgodnego z platformą Docker, która jest uruchamiana na urządzeniu usługi IoT Edge.  Zawiera ona modułów małych obliczeniowych korzystających z kombinacji niestandardowego kodu, innych usług platformy Azure i usługi innych firm 3. |
| [Aplikacja SaaS](./saas-app/cpp-saas-offer.md) | Azure | Rozwiązanie jest subskrypcją oprogramowanie jako usługa zarządzana przez wydawcę, w których użytkownicy logują się za pośrednictwem dostosowanego interfejsu, który korzysta z usługi Azure Active Directory. |
| [Maszyna wirtualna](./virtual-machine/cpp-virtual-machine-offer.md)  | Azure  | Rozwiązanie znajduje się w obrębie jednej maszyny wirtualnej wdrożone w subskrypcji klienta.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Aby uzyskać więcej informacji, zobacz [Podręcznik przez typ oferty publikowania](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Kolejne kroki

Zawiera informacje o ogólnych operacji można wykonywać na oferty w witrynie marketplace i ich atrybuty wspólne technicznych i zasobów w temacie [Zarządzanie ofertami](./manage-offers/cpp-manage-offers.md).
