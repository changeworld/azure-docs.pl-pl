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
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 9d495087a0b3db5bba3b00fa9d0d40c91e5e9244
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497861"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Omówienie planu, oferty, przydziału i subskrypcji

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

[Usługa Azure Stack](azure-stack-poc.md) umożliwia dostarczanie szerokiej gamy usług, takich jak maszyny wirtualne, bazy danych SQL Server, SharePoint, Exchange, a nawet [elementów portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Jako operatorów usługi Azure Stack, skonfiguruj i dostarczanie tych usług w usłudze Azure Stack przy użyciu planów, ofert i przydziałów.

Oferty zawierają co najmniej jeden plan, a każdy plan zawiera co najmniej jednej usługi. Tworzenie planów i łącząc je w różnych ofert, można zarządzać:

- Które usługi i zasoby, które użytkownicy mogą uzyskiwać dostęp.
- Ilość zasobów, które użytkownicy mogą korzystać.
- Które regiony mają dostęp do zasobów.

Gdy można dostarczać usługi, postępuj zgodnie z następującą ogólną procedurą:

1. Dodaj usługę, którą chcesz dostarczać użytkownikom.
2. Utwórz plan, który ma co najmniej jednej usługi. Podczas tworzenia planu, wybierz lub utwórz przydziały, które definiują limity zasobów dla każdej usługi w planie.
3. Utwórz ofertę, która zawiera co najmniej jeden plan. Oferta może zawierać planów podstawowych i planów dodatków opcjonalne.

Po utworzeniu oferty użytkowników może być subskrybowana przez dostęp do usług i zasobów, których oferta obejmuje stały przydział. Użytkownicy mogą subskrybować oferty tyle, jak chcą. Na poniższej ilustracji przedstawiono prosty przykład użytkownika, który subskrybuje dwóch ofert. Każda oferta ma plan lub dwa, a każdy plan umożliwi im dostęp do usług.

![Subskrypcji dzierżawcy przy użyciu oferty i plany](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plany

Plany są grupami obejmującymi co najmniej jedną usługę. Jako operatorów usługi Azure Stack możesz [tworzyć plany](azure-stack-create-plan.md) do zaoferowania użytkownikom. Z kolei użytkownicy mogą subskrybować oferty, aby korzystać z planów i objętych nimi usług. Podczas tworzenia planów, pamiętaj ustawić limity przydziału, zdefiniuj swoje plany podstawowe i należy rozważyć umieszczenie planów dodatków opcjonalne.

### <a name="quotas"></a>Przydziały

Aby ułatwić zarządzanie pojemnością chmury, możesz użyć wstępnie skonfigurowane *przydziały*, lub Utwórz nowy limit przydziału dla każdej usługi w ramach planu. Przydziały definiują limity górny zasobów, które subskrypcji użytkownika można aprowizować lub zużywają. Na przykład limit przydziału mogą zezwalać na użytkownika, aby utworzyć do pięciu maszyn wirtualnych (VM).

> [!IMPORTANT]
> Może upłynąć do dwóch godzin nowe przydziały, które mają być dostępne w portalu użytkowników, lub przed zmienionych limitu przydziału jest wymuszany.

Można skonfigurować przydziały uporządkowane według regionów. Na przykład plan, które świadczy usługi compute dla regionu, A może mieć limit przydziału dwie maszyny wirtualne.

>[!NOTE]
>W usłudze Azure Stack Development Kit, tylko jeden region (o nazwie *lokalnego*) jest dostępna.

Dowiedz się więcej o [typy limitów przydziału w usłudze Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plan podstawowy

Podczas tworzenia oferty, administrator usługi może zawierać plan podstawowy. Te plany podstawowe znajdują się domyślnie, gdy użytkownik subskrybuje tę ofertę. Gdy użytkownik subskrybuje, mają dostęp do wszystkich dostawców zasobów, określone w tych planów podstawowych (z odpowiednie limity przydziału).

### <a name="add-on-plans"></a>Plany dodatkowe

Plany dodatkowe są opcjonalne plany, które możesz dodać do oferty. Plany dodatkowe nie są uwzględniane domyślnie w ramach subskrypcji. Plany dodatkowe są plany dodatkowe (z limitami przydziału) dostępnych w ofercie, subskrybent można dodać do swojej subskrypcji. Na przykład możesz zaoferować plan podstawowy z ograniczonymi zasobami z wersji próbnej i planu dodatku z zasobami bardziej znaczące klienci, którzy zdecydują się podczas wdrażania usługi.

## <a name="offers"></a>Oferty

Oferty są grupami co najmniej jeden plany, które można utworzyć, dzięki czemu użytkownicy mogą je subskrybują. Na przykład alfa oferuje może zawierać, A Plan, który zawiera zbiór usług obliczeniowych i planowanie B, który zawiera zestaw usług magazynu i sieci.

Gdy użytkownik [Utwórz ofertę](azure-stack-create-offer.md), musi zawierać co najmniej jeden plan podstawowy, ale można również tworzyć plany dodatkowe, które użytkownicy mogą dodawać do swojej subskrypcji.

## <a name="subscriptions"></a>Subskrypcje

Subskrypcja stanowi, jak użytkownicy uzyskują dostęp swoje oferty. Jeśli operatorów usługi Azure Stack dla dostawcy usług, użytkowników (dzierżawcy) kupić usługi subskrybując Twoich ofert. Jeśli jesteś operatorów usługi Azure Stack w organizacji, użytkownicy (pracownicy) mogą subskrybować usługi, które oferują bez konieczności płacenia.

Każda kombinacja użytkownika z ofertą jest unikatowy subskrypcji. Użytkownik może mieć wiele ofert subskrypcji, ale każda subskrypcja ma zastosowanie tylko do jednej oferty. Plany, oferty i przydziały dotyczą tylko subskrypcji unikatowy — nie można udostępniać między subskrypcjami. Wszystkie zasoby, które tworzy użytkownik jest skojarzony z jedną subskrypcją.

### <a name="default-provider-subscription"></a>Domyślną subskrypcję dostawcy

Automatycznie zostanie utworzona domyślna subskrypcja dostawcy, podczas wdrażania usługi Azure Stack Development Kit. Ta subskrypcja może służyć do zarządzania usługi Azure Stack, wdrażanie dostawców dodatkowych zasobów i utworzyć plany i oferty dla użytkowników. Dla zabezpieczeń i powodów licencjonowania nie ją stosować do uruchamiania obciążeń klientów i aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat planów i ofert, zobacz [Utwórz plan](azure-stack-create-plan.md).
