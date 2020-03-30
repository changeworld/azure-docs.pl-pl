---
title: Kontrola dostępu skoroszytów monitora platformy Azure
description: Uprość składanie złożonych raportów dzięki wstępnie utworzonym i niestandardowych sparametryzowanym skoroszytom dzięki kontroli dostępu opartej na rolach
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658425"
---
# <a name="access-control"></a>Kontrola dostępu

Kontrola dostępu w skoroszytach odnosi się do dwóch rzeczy:

* Dostęp wymagany do odczytu danych w skoroszycie. Ten dostęp jest kontrolowany przez standardowe [role platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) na zasobach używanych w skoroszycie. Skoroszyty nie określają ani nie konfigurują dostępu do tych zasobów. Użytkownicy zwykle uzyskać ten dostęp do tych zasobów przy użyciu roli [czytnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) na tych zasobów.

* Dostęp wymagany do zapisywania skoroszytów

    - Zapisywanie `("My")` prywatnych skoroszytów nie wymaga żadnych dodatkowych uprawnień. Wszyscy użytkownicy mogą zapisywać prywatne skoroszyty i tylko oni mogą je zobaczyć.
    - Zapisywanie skoroszytów udostępnionych wymaga uprawnień do zapisu w grupie zasobów w celu zapisania skoroszytu. Te uprawnienia są zwykle określone przez rolę [Monitorowania współautora,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) ale można również ustawić za pośrednictwem roli *Współautor skoroszytów.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Role standardowe z uprawnieniami związanymi ze skoroszytem

[Czytnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) zawiera standardowe /read uprawnienia, które będą używane przez narzędzia monitorowania (w tym skoroszyty) do odczytu danych z zasobów.

[Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) `/write` zawiera ogólne uprawnienia używane przez różne `workbooks/write` narzędzia monitorowania do zapisywania elementów (w tym uprawnienia do zapisywania skoroszytów udostępnionych).
"Współtwórca skoroszytów" dodaje do obiektu uprawnienia "skoroszyty/zapis", aby zapisać skoroszyty udostępnione.
Nie są wymagane żadne specjalne uprawnienia dla użytkowników do zapisywania prywatnych skoroszytów, które tylko oni widzą.

W przypadku niestandardowej kontroli dostępu opartej na rolach:

Dodaj, `microsoft.insights/workbooks/write` aby zapisać skoroszyty udostępnione. Aby uzyskać więcej informacji, zobacz rolę [Współautor skoroszytu.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę o skoroszytach wiele rozbudowanych opcji wizualizacji.
