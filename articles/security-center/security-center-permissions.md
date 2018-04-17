---
title: Uprawnienia w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak Centrum zabezpieczeń Azure używa kontroli dostępu opartej na rolach, aby przypisać uprawnienia dla użytkowników i identyfikuje akcje dozwolone dla każdej roli.
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: f85f49bd54eacbca67143b35eaf555cfb744a41d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w Centrum zabezpieczeń Azure

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md) który można przypisać do użytkowników, grup i usług Azure.

Centrum zabezpieczeń ocenia konfiguracji zasobów, aby zidentyfikować problemy z zabezpieczeniami i luk w zabezpieczeniach. W Centrum zabezpieczeń widoczne są tylko informacje związane z zasobem, jeśli przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytnik zabezpieczeń**: użytkownika, który należy do tej roli ma uprawnienia do Centrum zabezpieczeń przeglądania. Użytkownika można wyświetlić zalecenia, alertów, zasad zabezpieczeń i stanów zabezpieczeń, ale nie można wprowadzić zmian.
* **Administrator zabezpieczeń**: użytkownika, który należy do tej roli ma te same prawa odczytu zabezpieczeń może aktualizować zasady zabezpieczeń oraz odrzucać alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń, czytnika zabezpieczeń i Administrator zabezpieczeń mają dostęp tylko w Centrum zabezpieczeń. Role zabezpieczeń nie mają dostępu do innych obszarów usługi Azure, takiego jak magazyn, sieci Web i mobilnych lub Internetu rzeczy.
>
>

## <a name="roles-and-allowed-actions"></a>Role i dozwolonych akcji

Poniższa tabela przedstawia role i dozwolonych akcji w Centrum zabezpieczeń. X oznacza, że akcja jest dozwolony dla tej roli.

| Rola | Edytowanie zasad zabezpieczeń | Zastosuj zalecenia dotyczące zabezpieczeń zasobu | Odrzucać alerty i zalecenia | Wyświetlanie alertów i zalecenia |
|:--- |:---:|:---:|:---:|:---:|
| Właściciel subskrypcji | X | X | X | X |
| Współautor subskrypcji | X | X | X | X |
| Właściciel grupy zasobów | -- | X | -- | X |
| Współautor grupy zasobów | -- | X | -- | X |
| Czytelnik | -- | -- | -- | X |
| Administrator zabezpieczeń | X | -- | X | X |
| Odczytywanie zabezpieczeń | -- | -- | -- | X |

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład przypisać rolę czytelnika do użytkowników, którzy muszą tylko przeglądać informacje o kondycji zabezpieczeń zasobu, ale nie podejmować działań, np. stosować zaleceń ani edytować zasad.
>
>

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wyjaśniono, jak Centrum zabezpieczeń używa RBAC można przypisywać uprawnienia do użytkowników i określone akcje dozwolone dla każdej roli. Teraz, kiedy znasz przypisań ról niezbędne do monitorowania stanu zabezpieczeń subskrypcji, edytować zasady zabezpieczeń i stosować zalecenia, Dowiedz się, jak:

- [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń](security-center-policies.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń](security-center-recommendations.md)
- [Monitorowanie kondycji zabezpieczeń zasobów platformy Azure](security-center-monitoring.md)
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorowanie rozwiązań partnerskich zabezpieczeń](security-center-partner-solutions.md)
