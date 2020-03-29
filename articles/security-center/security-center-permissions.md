---
title: Uprawnienia w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak usługa Azure Security Center używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikuje dozwolone akcje dla każdej roli.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921321"
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w usłudze Azure Security Center

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów z zabezpieczeniami i luk w zabezpieczeniach. W U centrum zabezpieczeń informacje związane z zasobem są wyświetlane tylko wtedy, gdy przypisano rolę właściciela, współautora lub czytnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytnik zabezpieczeń:** Użytkownik należący do tej roli ma prawa do wyświetlania w Centrum zabezpieczeń. Użytkownik może wyświetlać zalecenia, alerty, zasady zabezpieczeń i stany zabezpieczeń, ale nie może wprowadzać zmian.
* **Administrator zabezpieczeń:** Użytkownik należący do tej roli ma takie same prawa jak czytnik zabezpieczeń i może również aktualizować zasady zabezpieczeń i odrzucać alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń, Czytnik zabezpieczeń i Administrator zabezpieczeń, mają dostęp tylko w Centrum zabezpieczeń. Role zabezpieczeń nie mają dostępu do innych obszarów usług platformy Azure, takich jak Magazyn, sieć Web & Mobile lub Internet rzeczy.
>
>

## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli przedstawiono role i dozwolone akcje w umykarze zabezpieczeń.

| Rola | Edytowanie zasad zabezpieczeń | Stosowanie zaleceń dotyczących zabezpieczeń dla zasobu</br> (w tym z "Quick Fix!") | Odrzucanie alertów i zaleceń | Wyświetlanie alertów i zaleceń |
|:--- |:---:|:---:|:---:|:---:|
| Właściciel subskrypcji | ✔ | ✔ | ✔ | ✔ |
| Współautor subskrypcji | -- | ✔ | ✔ | ✔ |
| Właściciel grupy zasobów | -- | ✔ | -- | ✔ |
| Współautor grupy zasobów | -- | ✔ | -- | ✔ |
| Czytelnik | -- | -- | -- | ✔ |
| Administrator zabezpieczeń | ✔ | -- | ✔ | ✔ |
| Czytelnik zabezpieczeń | -- | -- | -- | ✔ |

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład przypisz rolę Czytelnika do użytkowników, którzy muszą tylko wyświetlić informacje o kondycji zabezpieczeń zasobu, ale nie podejmują działań, takich jak stosowanie zaleceń lub zasad edycji.
>
>

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak usługa Security Center używa funkcji RBAC do przypisywania uprawnień użytkownikom i zidentyfikowano dozwolone akcje dla każdej roli. Teraz, gdy znasz przypisania ról potrzebne do monitorowania stanu zabezpieczeń subskrypcji, edytowania zasad zabezpieczeń i stosowania zaleceń, dowiedz się, jak:

- [Ustawianie zasad zabezpieczeń w usłudze Security Center](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w u centrum zabezpieczeń](security-center-recommendations.md)
- [Monitorowanie kondycji zabezpieczeń zasobów platformy Azure](security-center-monitoring.md)
- [Zarządzanie alertami zabezpieczeń i odpowiadanie na nie w Centrum zabezpieczeń](security-center-managing-and-responding-alerts.md)
- [Monitorowanie rozwiązań zabezpieczających dla partnerów](security-center-partner-solutions.md)
