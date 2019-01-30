---
title: Zarządzanie użycia i rozliczeń dla usługi Azure Stack jako dostawca usług w chmurze | Dokumentacja firmy Microsoft
description: Przewodnik rejestracji usługi Azure Stack jako dostawca chmury (CSP) i dodawania klientów na potrzeby rozliczeń.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 5fd4297cdd6edb048e213503124f5ecc08589552
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243804"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Zarządzanie użycia i rozliczeń dla usługi Azure Stack jako dostawca usług w chmurze 

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

W tym artykule opisano za pośrednictwem rejestrowania usługi Azure Stack jako dostawca chmury (CSP) i dodawania klientów.

Jako dostawca CSP możesz pracować z różnych klienci korzystający z usługi Azure Stack. Każdy klient ma subskrypcją dostawcy CSP na platformie Azure. Należy kierować do użycia z usługi Azure Stack, aby każda subskrypcja użytkownika.

Na poniższym diagramie przedstawiono kroki, które należy wybrać konta usług udostępnionych i zarejestrować konto platformy Azure przy użyciu konta usługi Azure Stack. Zarejestrowany, można dołączyć klientów końcowych.

**Kroki w celu dodania śledzenia jako dostawca CSP użycia**

[ ![Proces włączania użycia i zarządzania jako dostawca usług w chmurze](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "procesu włączania użycia i zarządzania jako dostawca usług w chmurze") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Tworzenie dostawcy usług Kryptograficznych lub APSS subskrypcji

### <a name="cloud-service-provider-subscription-types"></a>Typy subskrypcji dostawcy usług w chmurze

Należy wybrać typ konta usług udostępnionych, które jest używane dla usługi Azure Stack. Dostępne są następujące typy subskrypcji, które mogą służyć do rejestracji wielodostępnej usługi Azure Stack:

 - Dostawca usług w chmurze 
 - Subskrypcja usług udostępnionych partnera 

#### <a name="azure-partner-shared-services"></a>Usługi udostępnione przez partnerów platformy Azure

Subskrypcje platformy Azure Partner udostępnionych usług (APSS) są preferowanych przez rejestracji podczas bezpośredniego programu CSP lub dystrybutora dostawcy usług Kryptograficznych działa usługa Azure Stack.

Subskrypcje APSS są skojarzone z dzierżawą usług udostępnionych. Podczas rejestrowania usługi Azure Stack, musisz podać poświadczenia dla konta które jest właścicielem subskrypcji. Konto, które służy do rejestrowania usługi Azure Stack może być inne niż konto administratora, którego używasz do wdrożenia. Ponadto, wykonaj dwa konta *nie* muszą należeć do tej samej domenie. Innymi słowy mogą wdrażać przy użyciu dzierżawy, które są już używane. Może na przykład użyć ContosoCSP.onmicrosoft.com następnie zarejestrować się przy użyciu innej dzierżawy, na przykład IURContosoCSP.onmicrosoft.com. Należy pamiętać, zaloguj się przy użyciu ContosoCSP.onmicrosoft.com po wykonaniu administracji usługi Azure Stack dzień do wykonania. Po zalogowaniu do platformy Azure przy użyciu IURContosoCSP.onmicrosoft.com, gdy trzeba wykonywać operacje rejestracji.

Zapoznaj się z poniższymi opis subskrypcji APSS i instrukcje dotyczące sposobu tworzenia subskrypcji [Dodawanie usług udostępnionych partnerem Azure](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Subskrypcje dostawcy usług Kryptograficznych

Subskrypcje chmury programu Service Provider (CSP) są preferowanych przez rejestracji, gdy odsprzedawca lub klienta końcowego działa usługa Azure Stack.

## <a name="register-azure-stack"></a>Rejestrowanie usługi Azure Stack

Użyj subskrypcji APSS utworzone następujące informacje w poprzedniej sekcji, aby rejestrowania usługi Azure Stack na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarejestrować w usłudze Azure Stack z subskrypcją platformy Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Dodaj klienta końcowego

Aby skonfigurować usługę Azure Stack, gdy nową dzierżawę używa zasobów ich użycie jest informowany o swoją subskrypcję dostawcy usług chmury (CSP), zobacz [Dodaj dzierżawy na potrzeby użycia i rozliczeń w usłudze Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Opłaty za subskrypcje w prawo

Usługa Azure Stack wykorzystuje funkcję rejestracji. Rejestracja jest obiektem przechowywanych na platformie Azure. Obiekt rejestracji dokumenty, które subskrypcji platformy Azure służące do obciążenia dla danej usługi Azure Stack. W tej sekcji omówiono znaczenie rejestracji.

Korzystanie z rejestracji usługi Azure Stack wykonywać następujące czynności:
 - Przesyła dane dotyczące użycia usługi Azure Stack Commerce platformy Azure i płatności subskrypcji platformy Azure.
 - Raport użycia każdego klienta w innej subskrypcji przy użyciu wdrożenia wielodostępnego usługi Azure Stack. Wielodostępność umożliwia usłudze Azure Stack obsługiwać różne organizacje w jednym wystąpieniu usługi Azure Stack.

Dla każdej usługi Azure Stack jest jedną subskrypcję domyślną i wiele dzierżawy subskrypcji. Domyślna subskrypcja jest subskrypcji platformy Azure, która jest naliczana, jeśli nie ma subskrypcji specyficznym dla dzierżawy. Musi być pierwszym dla subskrypcji zarejestrowanej. Użycie wielu obcych dzierżaw raportowania do pracy subskrypcja musi być dostawca usług Kryptograficznych lub APSS subskrypcji.

Następnie w rejestracji jest aktualizowany z subskrypcją platformy Azure dla każdego dzierżawcy, która będzie używać usługi Azure Stack. Subskrypcje dzierżawy musi być typu Dostawca usług Kryptograficznych i należy rzutować z partnerem, który jest właścicielem subskrypcji domyślnej. Innymi słowy nie można zarejestrować klientów innego użytkownika.

Gdy usługi Azure Stack przekazuje informacje o użyciu do globalnej platformy Azure, usługa na platformie Azure konsultacje dotyczące rejestracji i mapuje użycia każdego dzierżawcy subskrypcji odpowiedniego dzierżawy. Jeśli nie został zarejestrowany w dzierżawie, to użycie jest przesyłany do domyślnej subskrypcji dla wystąpienia usługi Azure Stack, z którego pochodzi.

Ponieważ subskrypcje dzierżawy subskrypcji CSP, fakturze z rozliczeniem są wysyłane do partner programu CSP i informacje dotyczące użycia nie jest widoczne dla klientów końcowych.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat programu CSP, zobacz [programu Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Aby dowiedzieć się więcej o tym, jak pobrać informacje o użyciu zasobów z usługi Azure Stack, zobacz [użycie i rozliczenia w usłudze Azure Stack](azure-stack-billing-and-chargeback.md).
