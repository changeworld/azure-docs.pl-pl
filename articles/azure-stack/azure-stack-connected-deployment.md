---
title: Decyzji dotyczących platformy Azure połączonych wdrożenia dla usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: Określić wdrażania planowania decyzji dotyczących wdrożeniach z wieloma węzłami podłączonej do platformy Azure, Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e0f51306ab17e88db9d28da14faab9de0b445f74
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037910"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Wdrożenie za pomocą połączonej usługi Azure planowanie decyzji dla usługi Azure Stack zintegrowane systemy
Po podjęciu [zostanie sposób Zintegruj usługę Azure Stack w środowisku chmury hybrydowej](azure-stack-connection-models.md), następnie można zakończyć swoje decyzje dotyczące wdrożenia usługi Azure Stack.

Wdrażanie usługi Azure Stack połączony z platformą Azure oznacza, że dla magazynu tożsamości może mieć usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS). Możesz również korzystać z obu model rozliczeń: płatność za użycie lub oparty na pojemności. Połączone wdrożenia to opcja domyślna, ponieważ umożliwia klientom uzyskać optymalne wykorzystanie usługi Azure Stack, szczególnie w przypadku scenariuszy chmury hybrydowych obejmujących platformy Azure i usługi Azure Stack. 

## <a name="choose-an-identity-store"></a>Wybierz magazyn tożsamości
Za pomocą połączonych wdrożenia można wybrać usługi Azure AD lub AD FS dla magazynu tożsamości. Rozłączona wdrożenia bez łączności z Internetem, można używać tylko usług AD FS.

Wybór magazynu tożsamości nie ma żadnego wpływu na maszyny wirtualne dzierżawcy (VM). Maszyny wirtualne dzierżawy mogą wybrać magazyn tożsamości, które chcą łączenie z usługą, w zależności od tego, jak zostaną skonfigurowane: usługi Azure AD, przyłączone do domeny usługi Active Directory systemu Windows Server w grupie roboczej, itp. To nie jest powiązana decyzji dostawcy tożsamości usługi Azure Stack. 

Na przykład jeśli wdrożenia IaaS dzierżawy maszyn wirtualnych na podstawie usługi Azure Stack i chcesz, aby dołączyć firmowej domeny usługi Active Directory i używanie kont z tego miejsca możesz nadal to zrobić. Nie należy używać magazynu tożsamości usługi Azure AD, tutaj dla tych kont.

### <a name="azure-ad-identity-store"></a>Magazyn tożsamości w usłudze Azure AD
Jeśli używasz usługi Azure AD dla Twojego magazynu tożsamości wymaga dwóch kont usługi Azure AD: konto administratora globalnego i konta rozliczeniowego. Te konta można kont tych samych lub różnych kont. Przy użyciu tego samego konta użytkownika może być prostszy i przydatne w przypadku ograniczonej liczby kont platformy Azure, może sugerować Twoje potrzeby biznesowe, przy użyciu dwóch kont:

1. **Konto administratora globalnego** (wymagane tylko w przypadku wdrożeń połączone). To jest konto platformy Azure, który jest używany do tworzenia aplikacji i nazwy główne usług dla usług infrastruktury Azure Stack w usłudze Azure Active Directory. To konto musi mieć uprawnienia administratora katalog do katalogu, w którym system usługi Azure Stack, które zostaną wdrożone w obszarze. "Operator chmury" stanie się administratorem globalnym usługi Azure AD dzierżawy i będą używane: 
    - Aby aprowizować i delegować uprawnienia do aplikacji i nazwy główne usług dla wszystkich usług Azure Stack, które muszą wchodzić w interakcje z usługi Azure Active Directory i interfejsu API programu Graph. 
    - Za pomocą konta administratora usługi. To jest właścicielem domyślną subskrypcję dostawcy (które można później zmienić). Można zalogować się do portalu administracyjnego usługi Azure Stack przy użyciu tego konta, a następnie można użyć tworzyć oferty i plany, Ustaw limity przydziału i wykonywania innych zadań administracyjnych w usłudze Azure Stack.
2. **Konto rozliczeniowe** (wymagane dla obu podłączone i odłączone wdrożeń). To konto platformy Azure służy do ustanawiania rozliczeń relacji między system zintegrowany z usługi Azure Stack i Azure commerce wewnętrznej bazy danych. To konto które będą obciążani opłaty usługi Azure Stack. To konto będzie również zaoferować elementów w portalu marketplace oraz innych scenariuszy hybrydowych. 

### <a name="ad-fs-identity-store"></a>Magazyn tożsamości usługi AD FS
Wybierz tę opcję, jeśli chcesz użyć własnego magazynu tożsamości, takich jak usługi Active Directory firmowych dla kont administratora usługi.  

## <a name="choose-a-billing-model"></a>Wybierz model rozliczeń
Możesz wybrać dowolną **płatność za użycie** lub **pojemności** model rozliczeń. Płatność za użycie rozliczeń wdrożenia modelu musi umożliwiać raportowania użycia za pomocą połączenia Azure co najmniej raz na 30 dni. W związku z tym model rozliczania płatność jako — rzeczywiste użycie jest dostępna tylko w przypadku wdrożeń połączonych.  

### <a name="pay-as-you-use"></a>Płatność za użycie
Za pomocą modelu rozliczeniowego płatności za użycie użycie jest obciążany opłatą za subskrypcję platformy Azure. Płaci się tylko, gdy używasz usługi Azure Stack. Jeśli jest to model decyzję w sprawie, będziesz potrzebować subskrypcji platformy Azure i identyfikator konta skojarzonego z posiadaną subskrypcją (na przykład serviceadmin@contoso.onmicrosoft.com). Subskrypcje umowy EA, dostawca usług Kryptograficznych i CSL są obsługiwane. Użycie raportowanie jest skonfigurowane podczas [rejestracji w usłudze Azure Stack](azure-stack-registration.md).

> [!NOTE]
> W większości przypadków klienci korporacyjni użyje subskrypcji EA i dostawców usług będzie używać dostawcy usług Kryptograficznych lub CSL subskrypcji.

Jeśli zamierzasz używać subskrypcji dostawcy CSP, przejrzeć tabelę poniżej, aby zidentyfikować subskrypcji dostawcy CSP do użycia jako właściwe podejście zależy od danego scenariusza dostawcy usług Kryptograficznych:

|Scenariusz|Opcje domeny i subskrypcji|
|-----|-----|
|Jesteś **bezpośredniego programu CSP Partner** lub **pośredniego programu CSP dostawcy**, a będzie działał usługi Azure Stack|Użyj subskrypcji CSL (wspólnej warstwy usług).<br>     lub<br>Utwórz dzierżawę usługi Azure AD z opisową nazwę Centrum partnerskiego. Na przykład &lt;organizacji > CSPAdmin z subskrypcji Azure CSP skojarzonych z nim.|
|Jesteś **pośrednich odsprzedawca**, a będzie działał usługi Azure Stack|Poproś dostawcę pośredniego programu CSP utworzyć dzierżawę usługi Azure AD dla organizacji z subskrypcji Azure CSP skojarzonych z nim za pomocą Centrum partnerskiego.|

### <a name="capacity-based-billing"></a>Rozliczanie oparte na wydajności
Jeśli zdecydujesz się używać modelu rozliczeń wydajności, należy zakupić stosu pojemności planowanie jednostki SKU usługi Azure na podstawie wydajności systemu. Należy określić liczbę rdzeni fizycznych w usługi Azure Stack, poprawna ilość zakupu. 

Rozliczenia pojemności wymaga umowy Enterprise Agreement (EA) subskrypcji platformy Azure na potrzeby rejestracji. Przyczyną jest to, że rejestracji konfiguruje dostępność elementów portalu Marketplace, które wymaga subskrypcji platformy Azure. Subskrypcja nie jest używana do użycia usługi Azure Stack.

## <a name="learn-more"></a>Dowiedz się więcej
- Aby uzyskać informacji dotyczących przypadków użycia, zakupów, partnerami i dostawcami sprzętu OEM, zobacz [usługi Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stronę produktu.
- Uzyskać informacje na temat planu i udostępnienia georegionu dla usługi Azure Stack zintegrowanych systemów, zobacz oficjalny dokument: [usługi Azure Stack: stanowi rozszerzenie platformy Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Aby dowiedzieć się więcej o Microsoft Azure Stack, pakowania i ceny [Pobierz PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Kolejne kroki
[Integracja sieci centrum danych](azure-stack-network.md)