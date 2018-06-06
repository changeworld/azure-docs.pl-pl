---
title: Zarządzanie użycie i rozliczenia Azure stosu jako dostawcy usług w chmurze | Dokumentacja firmy Microsoft
description: Przejdź przez zarejestrowanie stosu Azure jako dostawcy usług w chmurze i dodawania klientów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 21a52af4943004789b0a9bdbe4695ab1a603c046
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796703"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Zarządzanie użycie i rozliczenia Azure stosu jako dostawcy usług w chmurze 

*Dotyczy: Azure stosu zintegrowane systemy*

W tym artykule przedstawiono sposób rejestrowania stosu Azure jako dostawca chmury (CSP) i dodawania klientów.

Jako dostawcy usług Kryptograficznych najprawdopodobniej będzie mieć wiele różnych klientów przy użyciu stosu Azure. Każdy klient ma subskrypcję dostawcy usług Kryptograficznych w systemie Azure i musisz przekierować użycia ze stosu Azure do subskrypcji każdego użytkownika.

Na poniższym diagramie przedstawiono kroki, które należy wybrać konto usług udostępnionych i zarejestrować konto platformy azure przy użyciu konta. Jeśli zostały wykonane, możesz dołączyć klientów końcowych.

**Kroki, aby dodać użycia śledzenie jako dostawcy usług Kryptograficznych**

![Proces włączania użycia i zarządzania nimi jako dostawcy usług w chmurze.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Utwórz subskrypcję dostawcy usług Kryptograficznych lub CSPSS

### <a name="cloud-service-provider-subscription-types"></a>Typy subskrypcji dostawcy usług w chmurze

Należy wybrać typ konta usług udostępnionych stosu Azure. Dostępne są następujące typy subskrypcji, które mogą służyć do rejestracji wielodostępnej Azure stosu:

 - Dostawcy usług w chmurze 
 - Partnerem subskrypcji usług udostępnionych 

#### <a name="csp-shared-services"></a>Dostawca usług Kryptograficznych usług udostępnionych

Subskrypcje w chmurze usługi dostawcy udostępnionych usług (CSPSS) są preferowane wyborem w przypadku rejestracji, gdy bezpośrednie dostawcy usług Kryptograficznych lub dostawcy usług Kryptograficznych dystrybutora działa stosu Azure.

Subskrypcje CSPSS są skojarzone z dzierżawcą usług udostępnionych. Podczas rejestrowania stosu Azure, należy podać poświadczenia dla konta, które jest właścicielem subskrypcji. Konto, które służy do rejestrowania stosu Azure może różnić się od konta administratora, służące do wdrażania; wykonaj dwa *nie* muszą należeć do tej samej domenie. Innymi słowy mogą wdrażać przy użyciu dzierżawy, który jest już używana. Na przykład może użyć ContosoCSP.onmicrosoft.com następnie zarejestrować przy użyciu innej dzierżawy, na przykład IURContosoCSP.onmicrosoft.com. Należy pamiętać logowania przy użyciu ContosoCSP.onmicrosoft.com po wykonaniu administracji stosu Azure dzień do wykonania. Podczas logowania się do platformy Azure przy użyciu IURContosoCSP.onmicrosoft.com, gdy należy wykonać czynności rejestracji.

Odwołuje się do następujących opis CSPSS subskrypcje i instrukcje dotyczące sposobu tworzenia subskrypcji [Dodawanie usług udostępnionych partnera Azure](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Subskrypcje dostawcy usług Kryptograficznych

Subskrypcje w chmurze dostawcy usług Kryptograficznych to wybór preferowanego rejestracji podczas sprzedawcą dostawcy usług Kryptograficznych lub odbiorcy końcowego działa stosu Azure.

## <a name="register-azure-stack"></a>Zarejestruj Azure stosu

Aby zarejestrować stosu Azure, zobacz [zarejestrować stosu Azure w ramach subskrypcji Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Dodaj odbiorcę zakończenia

Aby skonfigurować stosu Azure, dzięki czemu podczas nowej dzierżawy używa zasobów ich użycie jest informowany o swoją subskrypcję dostawcy usług chmury (CSP), zobacz [Dodaj dzierżawy użycie i rozliczenia Azure stos](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Dodatkowy prawo subskrypcji

Stos Azure wykorzystuje funkcję rejestracji. Rejestracja jest obiektem, przechowywane na platformie Azure, które dokumenty, które subskrypcji platformy Azure na potrzeby obciążenia dla danej stosu Azure. Ta sekcja dotyczy znaczenie rejestracji.

Za pomocą rejestracji stosu Azure wykonywać następujące czynności:
 - Przesyła dane dotyczące użycia stosu Azure Azure Commerce i naliczać opłaty subskrypcji platformy Azure.
 - Raport użycia każdego klienta na inną subskrypcję z wdrożenia wielodostępnego stosu Azure. Obsługa wielu podmiotów umożliwia stosu Azure do obsługi różnych organizacji dla tego samego wystąpienia usługi Azure stosu.

Dla każdego stosu Azure jest jedna subskrypcja domyślne i jak wiele subskrypcji dzierżawy zgodnie z potrzebami. Domyślna subskrypcja jest subskrypcji Azure, która jest rozliczana, jeśli nie istnieje żadne subskrypcji specyficznego dla dzierżawy. Musi być pierwszą osobą, która jest zarejestrowana. Wielodostępne użycia raporty działały subskrypcji musi być dostawca usług Kryptograficznych lub CSPSS subskrypcji.

Następnie rejestracji został zaktualizowany o subskrypcji platformy Azure dla każdego dzierżawcy, który będzie służyć stosu Azure. Subskrypcje dzierżawy musi być typu Dostawca usług Kryptograficznych i należy rzutować partnera, który jest właścicielem subskrypcji domyślne. Innymi słowy nie można zarejestrować klientów do kogoś innego.

Po stosu Azure przekazuje informacje o użyciu globalne Azure, usługa na platformie Azure sprawdza rejestracji i mapuje użycia każdego dzierżawcy do subskrypcji odpowiednie dzierżawy. Jeśli nie został zarejestrowany przez dzierżawcę, wykorzystanie prowadzi do subskrypcji domyślnego wystąpienia stosu Azure, z którego pochodzi.

Ponieważ subskrypcji dzierżawcy subskrypcje dostawcy usług Kryptograficznych, ich rachunku są wysyłane do dostawcy usług Kryptograficznych partnera, a informacje o użyciu nie jest widoczne dla klientów końcowych.



## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat dostawcy usług Kryptograficznych, zobacz [programu Cloud Solution Provider](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Aby dowiedzieć się więcej o tym, jak można pobrać informacji o użyciu zasobów ze stosu Azure, zobacz [użycie i rozliczenia Azure stosu](azure-stack-billing-and-chargeback.md).
