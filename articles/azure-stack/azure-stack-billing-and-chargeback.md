---
title: Odbiorcy rozliczeń i obciążenia zwrotnego w stosie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać informacji o użyciu zasobów Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877487"
---
# <a name="usage-and-billing-in-azure-stack"></a>Użycie i rozliczenia Azure stosu

W tym artykule opisano, jak użytkownicy stosu Azure są rozliczane dotyczące użycia zasobów. Aby dowiedzieć się sposobu dostępu do informacji dotyczących rozliczeń dla analizy i wstecznego.

Azure stosu zbiera grupuje dane użycia dla wszystkich zasobów używanych i przekazuje te dane do handlu Azure. Azure Commerce rachunków możesz dla wykorzystanie stosu Azure w taki sam sposób jak czy obciążać Cię do użycia usługi Azure.

Można również uzyskać dane dotyczące użycia i eksportowanie własnych rozliczeń lub opłata kopii systemu za pomocą karty rozliczeń lub wyeksportować go do narzędzia analizy biznesowej, takich jak Microsoft Power BI i użyć jej do analizy.


## <a name="usage-pipeline"></a>Użycie potoku

Każdy dostawca zasobów w stosie Azure emituje dane użycia na obciążenie zasobów. Usługa użycia okresowo (co godzinę i codziennie) agreguje dane dotyczące użycia i zapisuje je w bazie danych użycia. Azure operatory stosu i użytkownicy mają dostęp do danych przechowywane dane dotyczące użycia za pośrednictwem interfejsów API do użycia zasobów usługi Azure stosu. 

Jeśli masz [zarejestrowany wystąpienia stosu Azure Azure](azure-stack-register.md), stos Azure jest skonfigurowany do wysyłania danych użycia do obsługi handlu Azure. Po przekazaniu danych do platformy Azure, można do niego dostęp za pośrednictwem portalu rozliczeń lub za pomocą interfejsów API użycia zasobów Azure. Zapoznaj się [raportowania danych użycia](azure-stack-usage-reporting.md) tematu, aby dowiedzieć się więcej o korzystaniu z jakiego danych jest zgłaszany na platformie Azure.  

Na poniższej ilustracji przedstawiono najważniejsze składniki w potoku użycia: 

![Użycie potoku](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Jakie informacje dotyczące użycia można znaleźć i w jaki sposób?

Azure dostawcy zasobów stos, na przykład obliczeniowych, magazynu i sieci, generowania danych użycia co godzinę dla każdej subskrypcji. Dane użycia zawiera informacje o tym zasobie używane np. Nazwa zasobu, subskrypcji używane i ilość używane. Aby dowiedzieć się o zasobach identyfikator liczników, zapoznaj się [użycia interfejsu API często zadawane pytania dotyczące](azure-stack-usage-related-faq.md) artykułu.

Po gromadzenia danych użycia jest [raportowanych do programu Azure](azure-stack-usage-reporting.md) do generowania rachunek, które można wyświetlić w portalu Azure rozliczeń. 


> [!NOTE]
> Raportowanie danych użycia nie jest wymagana dla usługi Azure stosu Development Kit i stosu Azure zintegrowany system licencjonowanych zgodnie z modelem pojemności. Aby dowiedzieć się więcej na temat licencjonowania w stosie Azure, zobacz [pakowania i cenach](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) arkusz danych.

Portal Azure rozliczeń zawiera dane użycia zasobów mogą być obciążane. Oprócz zasobów mogą być obciążane stosu Azure przechwytuje dane użycia dla szerszy zestaw zasobów, które są dostępne w środowisku Azure stosu za pośrednictwem interfejsów API REST lub programu PowerShell. Operatory stosu Azure można pobrać danych użycia dla wszystkich subskrypcji użytkownika. Poszczególni użytkownicy mogą korzystać tylko ich szczegóły użycia. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Użycie raportowanie dla wielodostępnej dostawcom usług w chmurze

Wielodostępna dostawca usług chmury (CSP) mającego wielu klientów przy użyciu stosu Azure może być oddzielnie, raport każdego użycia przez klientów tak, aby dostawca naliczać użycia do innej subskrypcji platformy Azure. 

Każdy klient będzie ma swoją tożsamość reprezentowany przez różne dzierżawy usługi Azure Active Directory (Azure AD). Stos Azure obsługuje przypisywanie jedną subskrypcję dostawcy usług Kryptograficznych do każdej dzierżawy usługi Azure AD. Możesz dodać dzierżawców i ich subskrypcji do podstawowej rejestracji stosu Azure. Podstawowy rejestracja została wykonana dla wszystkich stosy Azure. Jeśli subskrypcja nie jest zarejestrowany dla dzierżawy, użytkownik nadal może użyć stosu Azure i ich użycia, które zostaną wysłane do subskrypcja używana na potrzeby rejestracji podstawowej. 


## <a name="next-steps"></a>Kolejne kroki

[Zarejestrować w usłudze Azure stosu](azure-stack-registration.md)

[Raport danych użycia usługi Azure stosu na platformie Azure](azure-stack-usage-reporting.md)

[Użycie zasobów dostawcy interfejsu API](azure-stack-provider-resource-api.md)

[Interfejs API użycia zasobów dzierżawcy](azure-stack-tenant-resource-usage-api.md)

[Często zadawane pytania dotyczące wykorzystania](azure-stack-usage-related-faq.md)