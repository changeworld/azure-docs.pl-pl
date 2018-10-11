---
title: Omówienie usługi Azure Stack planu, oferty, przydziału i subskrypcji | Dokumentacja firmy Microsoft
description: Operator chmury chcę zrozumieć plany, oferty, przydziały i subskrypcje usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 70ed5d45701133434c708ad80aaafc58645297e8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077123"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Omówienie planu, oferty, przydziału i subskrypcji

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

[Usługa Azure Stack](azure-stack-poc.md) pozwala dostarczać cały szereg usług, takie jak maszyny wirtualne, bazy danych SQL Server, SharePoint, Exchange, a nawet [elementów portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Jako operatorów usługi Azure Stack, skonfiguruj i dostarczanie tych usług w usłudze Azure Stack przy użyciu planów, ofert i przydziałów.

Oferty zawierają co najmniej jeden plan, a każdy plan zawiera co najmniej jednej usługi. Tworzenie planów i łącząc je w różnych ofert, można zarządzać:

- Które usługi i zasoby, które użytkownicy mogą uzyskiwać dostęp.
- Ilość zasobów, które użytkownicy mogą korzystać.
- Które regiony mają dostęp do zasobów.

Gdy można dostarczać usługi, postępuj zgodnie z następującą ogólną procedurą:

1. Dodaj usługę, którą chcesz dostarczać użytkownikom.
2. Utwórz plan, który ma co najmniej jednej usługi. Podczas tworzenia planu, wybierz lub utwórz przydziały, które definiują limity zasobów dla każdej usługi w planie.
3. Utwórz ofertę, która zawiera co najmniej jeden plan. Oferta może zawierać planów podstawowych i planów dodatków opcjonalne.

Po utworzeniu oferty użytkowników może być subskrybowana przez dostęp do usług i zasobów, których oferta obejmuje stały przydział. Użytkownicy mogą subskrybować oferty tyle, jak chcą. Na poniższym diagramie przedstawiono prosty przykład użytkownika, który subskrybuje dwóch ofert. Każda oferta ma plan lub dwa, a każdy plan umożliwi im dostęp do usług.

![Subskrypcji dzierżawcy przy użyciu oferty i plany](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plany

Plany są grupowania co najmniej jednej usługi. Jako operatorów usługi Azure Stack możesz [tworzyć plany](azure-stack-create-plan.md) do zaoferowania użytkownikom. Z kolei użytkownicy subskrybują oferty, aby korzystać z planów i usług, które należą do. Podczas tworzenia planów, pamiętaj ustawić limity przydziału, zdefiniuj swoje plany podstawowe i należy rozważyć umieszczenie planów dodatków opcjonalne.

### <a name="quotas"></a>Przydziały

Aby ułatwić zarządzanie pojemnością chmury, możesz użyć wstępnie skonfigurowane limity przydziału lub tworzenie nowego limitu przydziału dla każdej usługi w ramach planu. Przydziały definiują limity górny zasobów, które subskrypcji użytkownika można aprowizować lub zużywają. Na przykład limit przydziału mogą zezwalać na użytkownika, aby utworzyć do pięciu maszyn wirtualnych (VM). Możesz ustawić dodatkowe przydziały na maszynach wirtualnych, takie jak ilość pamięci RAM i procesora CPU rdzeni.

Można skonfigurować przydziały uporządkowane według regionów. Na przykład plan, które świadczy usługi compute dla regionu, A może mieć limit przydziału dwie maszyny wirtualne z 4 GB pamięci RAM i 8 rdzeni procesora CPU.

>[!NOTE]
>W usłudze Azure Stack Development Kit, tylko jeden region (o nazwie *lokalnego*) jest dostępna.

Dowiedz się więcej o [typy limitów przydziału w usłudze Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plan podstawowy

Podczas tworzenia oferty, administrator usługi może zawierać plan podstawowy. Te plany podstawowe znajdują się domyślnie, gdy użytkownik subskrybuje tę ofertę. Gdy użytkownik subskrybuje, mają dostęp do wszystkich dostawców zasobów, określone w tych planów podstawowych (z odpowiedniej limitów przydziału.)

### <a name="add-on-plans"></a>Plany dodatkowe

Plany dodatkowe są opcjonalne plany, które możesz dodać do oferty. Plany dodatkowe nie są domyślnie włączone w subskrypcji. Plany dodatkowe są plany dodatkowe (z limitami przydziału) dostępnych w ofercie, subskrybent można dodać do swojej subskrypcji. Na przykład możesz zaoferować plan podstawowy z ograniczonymi zasobami z wersji próbnej i planu dodatku z zasobami bardziej znaczące klienci, którzy zdecydują się podczas wdrażania usługi.

## <a name="offers"></a>Oferta

Oferty są grupami co najmniej jeden plany, które można utworzyć, dzięki czemu użytkownicy mogą je subskrybują. Na przykład alfa oferuje może zawierać, A Plan, który zawiera zbiór usług obliczeniowych i planowanie B, który zawiera zestaw usług magazynu i sieci.

Gdy użytkownik [Utwórz ofertę](azure-stack-create-offer.md), musi zawierać co najmniej jeden plan podstawowy, ale można również tworzyć plany dodatkowe, które użytkownicy mogą dodawać do swojej subskrypcji.

## <a name="subscriptions"></a>Subskrypcje

Subskrypcja stanowi, jak użytkownicy uzyskują dostęp swoje oferty. W przypadku operatorów usługi Azure Stack dla dostawcy usług, użytkowników (dzierżawcy) kupować usługi subskrybując Twoich ofert. Jeśli jesteś operatorów usługi Azure Stack w organizacji, użytkownicy (pracownicy) mogą subskrybować usługi, które oferują bez konieczności płacenia.

Każda kombinacja użytkownika z ofertą jest unikatowy subskrypcji. Użytkownik może mieć wiele ofert subskrypcji, ale każda subskrypcja ma zastosowanie tylko do jednej oferty. Plany, oferty i przydziały dotyczą tylko subskrypcji unikatowy — nie można udostępniać między subskrypcjami. Wszystkie zasoby, które tworzy użytkownik jest skojarzony z jedną subskrypcją.

### <a name="default-provider-subscription"></a>Domyślną subskrypcję dostawcy

Subskrypcję dostawcy domyślne jest tworzone automatycznie podczas wdrażania usługi Azure Stack Development Kit. Ta subskrypcja może służyć do zarządzania usługi Azure Stack, wdrażanie dostawców dodatkowych zasobów i utworzyć plany i oferty dla użytkowników. Dla zabezpieczeń i licencjonowania przyczyny należy ich używać do uruchamiania obciążeń klientów i aplikacji.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie planu](azure-stack-create-plan.md)
