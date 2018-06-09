---
title: Omówienie usługi Azure stosu planu, oferty, przydziału i subskrypcji | Dokumentacja firmy Microsoft
description: Jako operator chmury ma być planów Azure stosu, oferty, przydziały i subskrypcje.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: d8aef778807d3a8a61cf9eedaae24abce84a19ab
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248762"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Omówienie planu, oferty, przydziału i subskrypcji

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

[Azure stosu](azure-stack-poc.md) umożliwia dostarczanie szerokiej gamy usług, takich jak maszyny wirtualne, bazy danych SQL Server, SharePoint, Exchange, a nawet [elementów portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Jako operator stosu Azure Skonfiguruj i dostarczanie tych usług w stosie Azure przy użyciu planów, ofertami i przydziały.

Oferty zawierają co najmniej jeden plan, a każdy plan zawiera co najmniej jedna usługa. Tworzenie planów i połączenie ich do innej oferty, można zarządzać:

- Które usługi i użytkownicy mogą uzyskiwać dostęp do zasobów.
- Ilość zasobów, które użytkownicy mogą korzystać.
- Regiony mają dostęp do zasobów.

W przypadku dostarczenia usługi, wykonaj następujące kroki wysokiego poziomu:

1. Dodaj usługi, które mają zostać dostarczone do użytkowników.
2. Utwórz plan, który ma co najmniej jedna usługa. Podczas tworzenia planu, wybierz lub utwórz przydziały definiujące ograniczenia zasobów poszczególnych usług w planie.
3. Utwórz ofertę, która zawiera co najmniej jeden plan. Oferty mogą obejmować plany podstawowe i planów dodatek opcjonalne.

Po utworzeniu oferty, użytkownicy mogą subskrybować on dostęp do usług i zasobów, który zapewnia oferty. Użytkownicy mogą subskrybować tyle oferty mogą. Na poniższym diagramie przedstawiono prosty przykład użytkownika, który subskrybuje dwa oferty. Każdy oferta ma planu lub dwóch i każdego planu umożliwi im dostęp do usług.

![Dzierżawy subskrypcja z ofertami i planów](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plany

Plany to grupy co najmniej jedna usługa. Jako operator stosu Azure możesz [tworzyć plany](azure-stack-create-plan.md) oferować użytkownikom. Z kolei użytkownikom subskrybować oferty korzystać z planów i usług, które obejmują one. Podczas tworzenia planów, upewnij się, że ustawienie przydziałami, zdefiniuj planów podstawowej i należy wziąć pod uwagę, łącznie z planami dodatek opcjonalne.

### <a name="quotas"></a>Przydziały

Aby ułatwić zarządzanie pojemność w chmurze, można za pomocą wstępnie skonfigurowanych przydziałów lub utworzyć nowy przydział dla każdej usługi w planie. Przydziały zdefiniować limity górny zasobów, które subskrypcji użytkownika można udostępnić lub korzystać z. Na przykład limit przydziału może zezwolić użytkownikowi na tworzenie do pięciu maszynach wirtualnych (VM). Dodatkowych przydziałach można ustawić na maszynach wirtualnych, takie jak ilość pamięci RAM i procesora CPU rdzeni.

Można skonfigurować przydziały według regionu. Na przykład plan, który udostępnia usługi obliczeniowe dla regionu A może mieć przydziału dwóch maszyn wirtualnych z 4 GB pamięci RAM i 8 rdzeni Procesora.

>[!NOTE]
>Azure stosu Development Kit, tylko jeden region (o nazwie *lokalnego*) jest dostępny.

Dowiedz się więcej o [typy przydziału w stosie Azure](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plan podstawowy

Podczas tworzenia oferty, administrator usługi mogą obejmować planu podstawowego. Te plany podstawowe są domyślnie dołączone, gdy użytkownik subskrybuje tej oferty. Gdy użytkownik subskrybuje, mają dostęp do wszystkich dostawców zasobów określone w te plany podstawowe (za pomocą odpowiednich przydziały.)

### <a name="add-on-plans"></a>Plany dodatkowe

Dodatkowe programy są opcjonalne plany, które można dodać do oferty. Plany dodatku nie są domyślnie włączone w subskrypcji. Dodatkowe programy są dodatkowe planów (limity) dostępna w propozycję subskrybenta można dodać do subskrypcji. Na przykład możesz zaoferować podstawowy plan o ograniczonych zasobów z wersji próbnej i planu dodatkowe zasoby bardziej znaczące klienci, którzy zdecydują się wdrożyć usługę.

## <a name="offers"></a>Oferta

Oferty to grupy co najmniej jeden plan utworzonych przez Ciebie, dzięki czemu użytkownicy mogą subskrybować je. Na przykład oferty alfa może zawierać A Plan, który zapewnia zbiór usług obliczeń i planu B, który zapewnia zbiór usług magazynu i sieci.

Gdy użytkownik [utworzyć ofertę](azure-stack-create-offer.md), musi zawierać co najmniej jeden plan bazowy, ale można również tworzyć plany dodatków, które użytkownicy mogą dodawać do nich.

## <a name="subscriptions"></a>Subskrypcje

Jak użytkownicy uzyskują dostęp do Twojej oferty jest subskrypcja. Jeśli operator Azure stosu dla dostawcy usług, użytkowników (dzierżawcami) Kup usług subskrypcja z ofertami. Jeśli operator Azure stosu w organizacji, użytkownicy (pracowników) mogą subskrybować oferowane bez płatności usługi.

Każda kombinacja użytkownika z oferty jest unikatowy subskrypcji. Użytkownik może mieć subskrypcji do wielu ofert, ale każda subskrypcja ma zastosowanie tylko do jednej oferty. Plany, ofertami i przydziały dotyczą tylko subskrypcji unikatowy — nie można udostępniać między subskrypcjami. Wszystkie zasoby, które użytkownik tworzy jest skojarzony z jedną subskrypcją.

### <a name="default-provider-subscription"></a>Domyślny dostawca subskrypcji

Subskrypcja dostawca domyślny jest tworzony automatycznie podczas wdrażania usługi Azure stosu Development Kit. Ta subskrypcja może służyć do zarządzania Azure stosu, wdrażania dostawców dodatkowych zasobów oraz tworzenie planów i oferty dla użytkowników. Zabezpieczeń i licencjonowania przyczyny należy ich używać do uruchamiania obciążeń klientów i aplikacji.

## <a name="next-steps"></a>Kolejne kroki

[Tworzenie planu](azure-stack-create-plan.md)
