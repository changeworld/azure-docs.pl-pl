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
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921321"
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w Azure Security Center

Centrum zabezpieczeń Azure używa [kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md), która zapewnia [wbudowane role](../role-based-access-control/built-in-roles.md), które można przypisać do użytkowników, grup i usług Azure.

Usługa Security Center ocenia konfigurację zasobów, aby zidentyfikować problemy dotyczące zabezpieczeń i luki w zabezpieczeniach. W usłudze Security Center widoczne są tylko informacje związane z zasobem, gdy masz przypisaną rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytelnik zabezpieczeń**: użytkownik, który należy do tej roli, ma uprawnienia do wyświetlania Security Center. Użytkownik może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian.
* **Administrator zabezpieczeń**: użytkownik należący do tej roli ma takie same prawa jak czytelnik zabezpieczeń, a także może zaktualizować zasady zabezpieczeń i odrzucić alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń, czytelnik zabezpieczeń i administrator zabezpieczeń mają dostęp tylko w Security Center. Role zabezpieczeń nie mają dostępu do innych obszarów usługi platformy Azure, takich jak Storage, Web & Mobile lub Internet rzeczy.
>
>

## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli przedstawiono role i dozwolone akcje w Security Center.

| Rola | Edytuj zasady zabezpieczeń | Stosowanie zaleceń dotyczących zabezpieczeń dla zasobu</br> (w tym z opcją "szybkie rozwiązanie!") | Odrzuć alerty i zalecenia | Wyświetlanie alertów i zaleceń |
|:--- |:---:|:---:|:---:|:---:|
| Właściciel subskrypcji | ✔ | ✔ | ✔ | ✔ |
| Współautor subskrypcji | -- | ✔ | ✔ | ✔ |
| Właściciel grupy zasobów | -- | ✔ | -- | ✔ |
| Współautor grupy zasobów | -- | ✔ | -- | ✔ |
| Czytelnik | -- | -- | -- | ✔ |
| Administrator zabezpieczeń | ✔ | -- | ✔ | ✔ |
| Czytelnik zabezpieczeń | -- | -- | -- | ✔ |

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
