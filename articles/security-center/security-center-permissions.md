---
title: Uprawnienia w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak usługa Azure Security Center używa kontroli dostępu opartej na rolach do udzielania uprawnień użytkownikom i identyfikuje akcje dozwolone dla każdej roli.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905623"
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w usłudze Azure Security Center

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytelnik zabezpieczeń**: Użytkownik, który należy do tej roli ma uprawnienia do usługi Security Center przeglądania. Użytkownika można wyświetlić, zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian.
* **Administrator zabezpieczeń**: Użytkownik, który należy do tej roli ma te same prawa jako czytelnik zabezpieczeń i również zaktualizować zasady zabezpieczeń i zamknąć alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń: rola Czytelnik zabezpieczeń i Administrator zabezpieczeń może korzystać tylko w usłudze Security Center. Role zabezpieczeń nie mają dostępu do innych obszarów usług platformy Azure, takich jak magazyn, sieci Web i Mobile lub Internetu rzeczy.
>
>

## <a name="roles-and-allowed-actions"></a>Ról i dozwolonych akcji

Poniższa tabela przedstawia role i dozwolonych akcji w usłudze Security Center. Znak X wskazuje, że akcja jest dozwolona dla tej roli.

| Rola | Edytowanie zasad zabezpieczeń | Stosowanie zalecenia dotyczące zabezpieczeń zasobu | Odrzuć alerty i zalecenia | Wyświetl alerty i zalecenia |
|:--- |:---:|:---:|:---:|:---:|
| Właściciel subskrypcji | X | X | X | X |
| Współautor subskrypcji | -- | X | X | X |
| Właściciel grupy zasobów | -- | X | -- | X |
| Współautor grupy zasobów | -- | X | -- | X |
| Czytelnik | -- | -- | -- | X |
| Administrator zabezpieczeń | X | -- | X | X |
| Czytelnik zabezpieczeń | -- | -- | -- | X |

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Dla użytkowników, którzy tylko przeglądać informacje o kondycji zabezpieczeń zasobu, ale nie podejmować działań, np. stosować zaleceń ani edytować zasad, na przykład przypisać rolę czytelnika.
>
>

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wyjaśniono, jak usługa Security Center korzysta z funkcji RBAC można przypisywać uprawnienia do użytkowników i określone akcje dozwolone dla każdej roli. Teraz, kiedy znasz już przypisań ról potrzebnych do monitorowania stanu zabezpieczeń wszystkich subskrypcji, edytować zasady zabezpieczeń i stosowania zaleceń, Dowiedz się, jak:

- [Ustawianie zasad zabezpieczeń w usłudze Security Center](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Security Center](security-center-recommendations.md)
- [Monitorowanie kondycji zabezpieczeń zasobów platformy Azure](security-center-monitoring.md)
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorowanie rozwiązań zabezpieczających partnerów](security-center-partner-solutions.md)
