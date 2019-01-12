---
title: Oferty usług w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Jako operator chmury możesz zaoferować użytkownikom usługi.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 754a0002aca52462910abe50825cff0254a7068f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244158"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Przegląd oferty usług w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

[Microsoft Azure Stack](azure-stack-poc.md) to hybrydowa platforma w chmurze, która umożliwia dostarczanie usług z centrum danych. Jako dostawca usług możesz zaoferować usług dla dzierżawców. W obrębie firmy lub w agencji rządowej może zaoferować usługami lokalnymi na potrzeby pracowników. 

Możesz zaoferować [infrastruktura jako usługa](https://azure.microsoft.com/overview/what-is-iaas/) usługi (IaaS), które pozwalają użytkownikom na tworzenie infrastruktury obliczeniowej na żądanie obsługiwana i zarządzana z portalu użytkownika usługi Azure Stack.

Można także wdrożyć [platforma jako usługa](https://azure.microsoft.com/overview/what-is-paas/) usługi (PaaS) dla usługi Azure Stack od firmy Microsoft i innych dostawców 3. Usługi, które mogą dostarczać obejmują, ale nie są ograniczone do:

- [Add an App Service resource provider to Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview) (Dodawanie dostawcy zasobów usługi App Service do usługi Azure Stack)

- [Dodaj dostawcę zasobów programu SQL Server do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Dodaj dostawcę zasobów programu MySQL Server do usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


Można także łączyć połączenia usług integracji i tworzenia złożonych rozwiązań dla różnych użytkowników.

Aby dostarczać użytkownikom tych usług, należy utworzyć [planów, ofert i przydziałów](azure-stack-plan-offer-quota-overview.md). Użytkownicy mogą następnie subskrybować oferty, aby korzystać z usług.

## <a name="plan-your-service-offers"></a>Planowanie swoje oferty usługi

Podczas planowane swoje oferty, należy uwzględnić następujące kwestie:

**Próbnych**: Ofert wersji próbnych można użyć w celu przyciągnięcia nowych użytkowników, którzy następnie można uaktualnić do dodatkowych usług. Oferta wersji próbnej, utworzyć małą [plan podstawowy](azure-stack-plan-offer-quota-overview.md#base-plan) za pomocą opcjonalnych większego planu dodatku.

**Planowanie pojemności**: Być może dane o użytkownikach z zainstalowanymi pobrania dużych ilości zasobów i zapychaniem systemu dla wszystkich użytkowników. Aby pomóc wydajności, można [skonfigurować swoje plany z limitami przydziału](azure-stack-plan-offer-quota-overview.md#plans) limitu użycia.

**Delegowane dostawców**: Inne, które oferuje możliwość utworzenia można udzielić w danym środowisku. Na przykład, jeśli jesteś dostawcą usługi, możesz to zrobić [delegować](azure-stack-delegated-provider.md) dzięki możliwości usługi odsprzedawców. Lub, w przypadku organizacji, możesz delegować innych działów/oddziałach.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie oferty w usłudze Azure Stack](azure-stack-create-offer.md)
