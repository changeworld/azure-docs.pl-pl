---
title: Usługa Azure Event Grid — przewodnik dotyczący rozwiązywania problemów
description: Ten artykuł zawiera listę kodów błędów, komunikatów o błędach, opisów i zalecanych akcji.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645076"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Rozwiązywanie problemów z błędami usługi Azure Event Grid
Ten przewodnik rozwiązywania problemów zawiera listę kodów błędów usługi Azure Event Grid, komunikatów o błędach, ich opisów i zalecanych akcji, które należy wykonać po otrzymaniu tych błędów. 

## <a name="error-code-400"></a>Kod błędu: 400
| Kod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest (HttpStatusCode.BadRequest)<br/>400 | Nazwa tematu musi mieć długość od 3 do 50 znaków. | Długość nazwy tematu niestandardowego powinna mieć długość od 3 do 50 znaków. W nazwie tematu dozwolone są tylko litery alfanumeryczne, cyfry i znak "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zastrzeżonych: <ul><li>Microsoft</li><li>EventGrid ( EventGrid )</li><li>System</li></ul> | Wybierz inną nazwę tematu, która jest zgodna z wymaganiami dotyczącymi nazwy tematu. |
| HttpStatusCode.BadRequest (HttpStatusCode.BadRequest)<br/>400 | Nazwa domeny musi mieć długość od 3 do 50 znaków. | Długość nazwy domeny powinna wynosić od 3 do 50 znaków. W nazwie tematu dozwolone są tylko litery alfanumeryczne, cyfry i znak "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zastrzeżonych:<ul><li>Microsoft</li><li>EventGrid ( EventGrid )</li><li>System</li> | Wybierz inną nazwę domeny, która jest zgodna z wymaganiami dotyczącymi nazwy domeny. |
| HttpStatusCode.BadRequest (HttpStatusCode.BadRequest)<br/>400 | Nieprawidłowy czas wygaśnięcia. | Czas wygaśnięcia subskrypcji zdarzenia określa, kiedy subskrypcja zdarzenia zostanie wycofana. Ta wartość powinna być prawidłową wartością DateTime w przyszłości.| Upewnij się, że czas wygaśnięcia subskrypcji zdarzeń w prawidłowym formacie DateTime i jest ustawiony na przyszłość. |

## <a name="error-code-409"></a>Kod błędu: 409
| Kod błędu | Komunikat o błędzie | Opis | Zalecana akcja |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.Conflict <br/>409 | Temat o określonej nazwie już istnieje. Wybierz inną nazwę tematu.   | Nazwa tematu niestandardowego powinna być unikatowa w jednym regionie platformy Azure, aby zapewnić prawidłową operację publikowania. Tej samej nazwie można używać w różnych regionach platformy Azure. | Wybierz inną nazwę tematu. |
| HttpStatusCode.Conflict <br/> 409 | Domena z określoną już istnieje. Wybierz inną nazwę domeny. | Nazwa domeny powinna być unikatowa w jednym regionie platformy Azure, aby zapewnić prawidłową operację publikowania. Tej samej nazwie można używać w różnych regionach platformy Azure. | Wybierz inną nazwę domeny. |
| HttpStatusCode.Conflict<br/>409 | Osiągnięto limit przydziału. Aby uzyskać więcej informacji na temat tych limitów, zobacz [Limity usługi Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Każda subskrypcja platformy Azure ma limit liczby zasobów usługi Azure Event Grid, których może używać. Część lub całość tego przydziału została przekroczona i nie można było utworzyć więcej zasobów. |    Sprawdź bieżące użycie zasobów i usuń wszystkie, które nie są potrzebne. Jeśli nadal musisz zwiększyć przydział, wyślij [aeg@microsoft.com](mailto:aeg@microsoft.com) wiadomość e-mail z dokładną liczbą potrzebnych zasobów. |


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
