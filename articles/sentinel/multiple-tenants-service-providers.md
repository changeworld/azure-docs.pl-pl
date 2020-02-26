---
title: Pracuj z wieloma dzierżawcami w przypadku dostawców usług Azure wskaźnikowych dla MSSP | Microsoft Docs
description: Jak korzystać z wielu dzierżawców na platformie Azure — wskaźnik usługi dla dostawców usług MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: caa79b572d0024b93abd2d32ca99d92cc2a8b4bb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77582079"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Współpraca z wieloma dzierżawcami na platformie Azure — wskaźnik 

Jeśli jesteś zarządzanym dostawcą usług zabezpieczeń (MSSP) i korzystasz z [usługi Azure Lighthouse](../lighthouse/overview.md) do zarządzania firmowymi centrami operacji zabezpieczeń (SOC), będziesz mieć możliwość zarządzania zasobami centrów danych platformy Azure dla klientów bez bezpośredniego połączenia z dzierżawcą klienta z własnej dzierżawy platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
- [Dołączanie usługi Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Aby to działanie działało prawidłowo, dzierżawa musi być zarejestrowana w dostawcy zasobów usługi Azure wskaźnikowej na co najmniej jednej subskrypcji. Jeśli masz zarejestrowany wskaźnik na platformie Azure w dzierżawie, możesz rozpocząć pracę. Jeśli tak nie jest, w Azure Portal wybierz **subskrypcje** , po których następuje **dostawcy zasobów** , a następnie wyszukaj `Microsoft.Security.Insights` i wybierz pozycję **zarejestruj**.
   ![sprawdzić dostawców zasobów](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Jak uzyskać dostęp do platformy Azure z innych dzierżawców
1. W obszarze **katalog i subskrypcja**wybierz pozycję katalogi delegowane i subskrypcje, w których znajdują się obszary robocze usługi Azure wskaźnikowej dla klienta.

   ![Generuj zdarzenia zabezpieczeń](media/multiple-tenants-service-providers/directory-subscription.png)

1. Otwórz wskaźnik platformy Azure. Zobaczysz wszystkie obszary robocze w wybranych subskrypcjach i będzie można bezproblemowo współpracować z nimi, podobnie jak w przypadku dowolnego obszaru roboczego w Twojej dzierżawie.

> [!NOTE]
> Nie będzie można wdrażać łączników na platformie Azure z poziomu zarządzanego obszaru roboczego. Aby wdrożyć łącznik, musisz bezpośrednio zalogować się do dzierżawy, w której ma zostać wdrożony łącznik, i uwierzytelnić się z wymaganymi uprawnieniami.





## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób bezproblemowego zarządzania wieloma dzierżawcami usługi Azure Wskaźnikowania. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

