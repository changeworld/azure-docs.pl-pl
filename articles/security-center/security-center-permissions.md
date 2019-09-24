---
title: Uprawnienia w Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak Azure Security Center używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikowania dozwolonych akcji dla każdej roli.
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
ms.openlocfilehash: f52d518d2ed1dfb62eed72cf9c0b839a37b7f856
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201642"
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w Azure Security Center

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytelnik zabezpieczeń**: Użytkownik, który należy do tej roli, ma uprawnienia do wyświetlania Security Center. Użytkownik może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian.
* **Administrator zabezpieczeń**: Użytkownik należący do tej roli ma takie same prawa jak czytelnik zabezpieczeń, a także może zaktualizować zasady zabezpieczeń i odrzucić alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń, czytelnik zabezpieczeń i administrator zabezpieczeń mają dostęp tylko w Security Center. Role zabezpieczeń nie mają dostępu do innych obszarów usługi platformy Azure, takich jak Storage, Web & Mobile lub Internet rzeczy.
>
>

## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli przedstawiono role i dozwolone akcje w Security Center. Symbol X wskazuje, że akcja jest dozwolona dla tej roli.

| Role | Edytuj zasady zabezpieczeń | Stosowanie zaleceń dotyczących zabezpieczeń dla zasobu | Odrzuć alerty i zalecenia | Wyświetlanie alertów i zaleceń |
|:--- |:---:|:---:|:---:|:---:|
| Właściciel subskrypcji | X | X | X | X |
| Współautor subskrypcji | -- | X | X | X |
| Właściciel grupy zasobów | -- | X | -- | X |
| Współautor grupy zasobów | -- | X | -- | X |
| Czytelnik | -- | -- | -- | X |
| Administrator zabezpieczeń | X | -- | X | X |
| Czytelnik zabezpieczeń | -- | -- | -- | X |

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład Przypisz rolę czytelnik do użytkowników, którzy muszą jedynie wyświetlać informacje o kondycji zabezpieczeń zasobu, ale nie podejmować działań, takich jak stosowanie zaleceń lub edytowanie zasad.
>
>

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, w jaki sposób Security Center używać RBAC do przypisywania uprawnień użytkownikom i identyfikowania dozwolonych akcji dla każdej roli. Teraz, po zapoznaniu się z przypisaniami ról potrzebnymi do monitorowania stanu zabezpieczeń subskrypcji, edytowania zasad zabezpieczeń i stosowania zaleceń, Dowiedz się, jak:

- [Ustawianie zasad zabezpieczeń w Security Center](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Security Center](security-center-recommendations.md)
- [Monitoruj kondycję zabezpieczeń zasobów platformy Azure](security-center-monitoring.md)
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w usłudze Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorowanie rozwiązań zabezpieczeń partnerów](security-center-partner-solutions.md)
