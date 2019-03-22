---
title: Klient, rozliczeń i obciążeń zwrotnych w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać informacje o użyciu zasobów z usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: c1b0a1523e65014b153ff3edb0c22b53ce7fb8a3
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337854"
---
# <a name="usage-and-billing-in-azure-stack"></a>Użycie i rozliczenia w usłudze Azure Stack

W tym artykule opisano, jak użytkownicy usługi Azure Stack zasobu za użycie będą naliczane, i jak informacje rozliczeniowe jest dostępny do analizy i opłat z powrotem.

Usługa Azure Stack zbiera i grupuje dane użycia dla zasobów, które są używane. Usługi Azure Stack przekazuje te dane do handlu platformy Azure. Azure Commerce opłaty naliczane są za użycie usługi Azure Stack w taki sam sposób jej opłaty naliczane są za użycie systemu Azure.

Można również uzyskać dane dotyczące użycia i eksportowanie go do własnych rozliczeń lub utworzyć kopię systemu za pomocą karty rozliczeń lub wyeksportować je do narzędzia analizy biznesowej, takich jak Microsoft Power BI.

## <a name="usage-pipeline"></a>Użycie potoku

Każdy dostawca zasobów w usłudze Azure Stack publikuje dane użycia na obciążenie zasobów. Usługa użycia okresowo (co godzinę i codziennie) agreguje dane dotyczące użycia i zapisuje je w bazie danych użycia. Usługa Azure Stack operatorów i użytkowników ma dostęp do danych przechowywane dane dotyczące użycia, za pośrednictwem interfejsów API użycia zasobów usługi Azure Stack.

Jeśli masz [zarejestrowane wystąpienie usługi Azure Stack przy użyciu usługi Azure](azure-stack-register.md), usługi Azure Stack jest skonfigurowany do wysyłania danych użycia do handlu platformy Azure. Przekazane dane na platformie Azure można do niego dostęp za pośrednictwem portalu rozliczeń lub za pomocą interfejsów API. użycia zasobów platformy Azure. Aby uzyskać więcej informacji na temat danych użycia jest zgłaszany do usługi Azure zobacz [raportowanie danych użycia](azure-stack-usage-reporting.md).  

Na poniższej ilustracji przedstawiono najważniejsze składniki w potoku użycia:

![Użycie potoku](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Jakie informacje dotyczące użycia można znaleźć i w jaki sposób?

Dostawcy zasobów usługi Azure Stack (na przykład obliczeń, magazynu i sieci) generowania danych użycia co godzinę dla każdej subskrypcji. Dane użycia zawiera informacje o zasobie, używane, takie jak nazwa zasobu, subskrypcja używana i użytej ilości. Aby dowiedzieć się o zasobach identyfikator liczników, zobacz [— często zadawane pytania do użycia interfejsu API](azure-stack-usage-related-faq.md).

Zebrane dane użycia po [zgłaszane na platformie Azure](azure-stack-usage-reporting.md) do wygenerowania faktury, który można wyświetlić w portalu rozliczeń systemu Azure.

> [!NOTE]  
> Raportowanie danych użycia nie jest wymagane dla usługi Azure Stack Development Kit (ASDK) oraz dla usługi Azure Stack, zintegrowany system użytkowników, którzy licencji w ramach modelu pojemności. Aby dowiedzieć się więcej na temat licencjonowania w usłudze Azure Stack, zobacz [pakowania i ceny arkusz danych](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Portalu rozliczeń systemu Azure zawiera dane użycia dla płatnych zasobów. Oprócz obciążające zasoby usługi Azure Stack umożliwia przechwytywanie danych użycia dla szerszy zestaw zasobów, które są dostępne w danym środowisku usługi Azure Stack za pośrednictwem interfejsów API REST lub polecenia cmdlet programu PowerShell. Operatorzy usługi Azure Stack można uzyskać dane użycia dla wszystkich subskrypcji użytkownika. Poszczególni użytkownicy mogą uzyskać jedynie własne szczegóły użycia.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Użycie raportowanie dla wielodostępnej dostawców usług w chmurze

Wieloma dzierżawami dostawca usług chmury (CSP), który ma wielu klientów przy użyciu usługi Azure Stack może być oddzielnie, raport użycia przez poszczególnych klientów tak, aby dostawca może opłaty za użycie do różnych subskrypcji platformy Azure.

Każdy klient ma swoją tożsamość, reprezentowane przez innej dzierżawy usługi Azure Active Directory (Azure AD). Usługa Azure Stack obsługuje przypisywanie jedną subskrypcją dostawcy CSP do każdej dzierżawy usługi Azure AD. Dzierżawcy i ich subskrypcjami można dodać do podstawowej rejestracji usługi Azure Stack. Podstawowy rejestracja odbywa się dla wszystkich wystąpień usługi Azure Stack. Jeśli subskrypcja nie jest zarejestrowany dla dzierżawy, użytkownik może nadal używać usługi Azure Stack i ich użycia, które będą wysyłane do subskrypcja używana na potrzeby rejestracji podstawowej.

## <a name="next-steps"></a>Kolejne kroki

- [Rejestrowanie w usłudze Azure Stack](azure-stack-registration.md)
- [Raportowanie platformie Azure danych użycia usługi Azure Stack](azure-stack-usage-reporting.md)
- [Interfejs API użycia zasobów dostawcy](azure-stack-provider-resource-api.md)
- [Interfejs API użycia zasobów dzierżawy](azure-stack-tenant-resource-usage-api.md)
- [— Często zadawane pytania dotyczące wykorzystania](azure-stack-usage-related-faq.md)