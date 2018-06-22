---
title: Usługa przydziały i limity dla partii zadań Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przydziałów domyślnych partii zadań Azure, ograniczenia i ograniczenia i zwiększa jak utworzyć żądanie przydziału
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12880ba3aa918873343ee8eb98e92130106e8362
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304451"
---
# <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch

Zgodnie z innymi usługami Azure, istnieją ograniczenia na niektóre zasoby skojarzone z usługi partia zadań. Wiele tych limitów jest przydziałów domyślnych zastosowane przez platformę Azure na poziomie konta lub subskrypcji. W tym artykule omówiono te ustawienia domyślne, i jak mogą żądać przydziału zwiększa.

Pamiętać przydziałów podczas projektowania i skalowania w górę obciążeń partii. Na przykład jeśli pulę będzie niższa niż docelowy liczba węzłów obliczeniowych, określony, może osiągnięto limit przydziału rdzeni dla Twojego konta usługi partia zadań.

Można uruchomić wiele obciążeń usługi Batch na jednym koncie usługi Batch lub rozdzielić obciążenia pomiędzy konta tej usługi znajdujące się w jednej subskrypcji, ale różnych regionach świadczenia usługi Azure.

Jeśli planujesz uruchamianie obciążeń produkcyjnych w partii, może być konieczne zwiększyć co najmniej jeden przydziały powyżej domyślny. Jeśli użytkownik chce podnieść limit przydziału, możesz otworzyć online [żądania obsługi klienta](#increase-a-quota) bez dodatkowych opłat.

> [!NOTE]
> Limit przydziału jest limit kredytu nie gwarancji pojemności. Jeśli wymagana pojemność na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure.
> 
> 

## <a name="resource-quotas"></a>Limity przydziałów zasobów
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Przydziały rdzeni w trybie użytkownika subskrypcji

Jeśli utworzono konto usługi partia zadań z trybem przydziału puli ustawioną **subskrypcji użytkownika**, przydziały są stosowane w inny sposób. W tym trybie partii maszyny wirtualne i inne zasoby są tworzone bezpośrednio w Twojej subskrypcji po utworzeniu puli. Konto utworzone w tym trybie nie dotyczą przydziały rdzeni partii zadań Azure. Zamiast tego przydziały w subskrypcji dla regionalne obliczeniowe rdzeni i inne zasoby są stosowane. Dowiedz się więcej o te przydziały w [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Inne ograniczenia.
| **Zasób** | **Limit maksymalny** |
| --- | --- |
| [Równoczesnych zadań](batch-parallel-node-tasks.md) na węzeł obliczeń |4 x liczba rdzeni węzła |
| [Aplikacje](batch-application-packages.md) na konto usługi partia zadań |20 |
| Pakiety aplikacji na aplikację |40 |
| Zadanie maksymalny okres istnienia | 7 dni<sup>1</sup> |
| Węzły obliczeniowe komunikacji między węzłami włączone puli | 100 |

<sup>1</sup> maksymalny okres istnienia zadania, gdy jest ona dodawana do zadania, po jego ukończeniu, wynosi 7 dni. Ukończone zadania podrzędne są utrwalone przez czas nieokreślony. Dane dla zadań podrzędnych nieukończonych w ciągu maksymalnego okresu istnienia nie są dostępne.


## <a name="view-batch-quotas"></a>Przydziały partii widoku
Wyświetl przydziałami konta wsadowego w [portalu Azure][portal].

1. Wybierz **partii kont** w portalu, a następnie wybierz konto usługi partia zadań interesuje Cię.
2. Wybierz **przydziały** menu konta usługi partia zadań.
3. Wyświetl przydziały zastosowanym do konta usługi partia zadań
   
    ![Przydziały konta usługi partia zadań][account_quotas]



## <a name="increase-a-quota"></a>Zwiększ limit przydziału
Wykonaj następujące kroki, aby zażądać przydział zwiększyć dla swojego konta usługi partia zadań lub subskrypcją za pomocą [portalu Azure][portal]. Typ zwiększenia limitu przydziału zależy od trybu alokacji puli konta partii zadań.

### <a name="increase-a-batch-cores-quota"></a>Zwiększ limit przydziału rdzeni partii 

1. Wybierz **Pomoc i obsługa techniczna** kafelka na pulpicie nawigacyjnym portalu lub znak zapytania (**?**) w prawym górnym rogu portalu.
2. Wybierz **nowy obsługuje żądania** > **podstawy**.
3. W **podstawy**:
   
    a. **Wystawianie typu** > **przydziału**
   
    b. Wybierz subskrypcję.
   
    c. **Typ limitu przydziału** > **partii**
   
    d. **Plan pomocy technicznej** > **Obsługa przydziałów — włączone**
   
    Kliknij przycisk **Dalej**.
4. W **Problem**:
   
    a. Wybierz **ważność** zgodnie z [wpływ na prowadzoną działalność][support_sev].
   
    b. W **szczegóły**, określ każdego przydziału, aby zmienić nazwę konta wsadowego i nowego limitu.
   
    Kliknij przycisk **Dalej**.
5. W **informacje kontaktowe**:
   
    a. Wybierz **preferowana metoda kontaktu**.
   
    b. Sprawdź i wprowadź wymagane szczegóły dotyczące kontaktu.
   
    Kliknij przycisk **Utwórz**, aby przesłać żądanie pomocy technicznej.

Po przesłaniu żądania obsługi pomocy technicznej platformy Azure skontaktuje się z Tobą. Należy pamiętać, że wykonywania żądania może potrwać maksymalnie 2 dni roboczych.


## <a name="related-topics"></a>Powiązane tematy
* [Tworzenie konta usługi partia zadań Azure za pomocą portalu Azure](batch-account-create-portal.md)
* [Omówienie funkcji usługi partia zadań Azure](batch-api-basics.md)
* [Azure subscription and service limits, quotas, and constraints (Limity subskrypcji i usług, limity przydziału oraz ograniczenia platformy Azure)](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
