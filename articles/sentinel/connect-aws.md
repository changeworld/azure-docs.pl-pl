---
title: Łączenie AWS CloudTrail z usługą Azure Sentinel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane AWS CloudTrail z usługą Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588658"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Łączenie usługi Azure Sentinel z urządzeniem AWS CloudTrail

Użyj łącznika AWS, aby przesyłać strumieniowo wszystkie zdarzenia AWS CloudTrail do usługi Azure Sentinel. Ten proces połączenia deleguje dostęp do usługi Azure Sentinel do dzienników zasobów AWS, tworząc relację zaufania między usługą AWS CloudTrail i usługą Azure Sentinel. Jest to realizowane w usłudze AWS, tworząc rolę, która daje uprawnienia do usługi Azure Sentinel, aby uzyskać dostęp do dzienników AWS.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć uprawnienia do zapisu w obszarze roboczym usługi Azure Sentinel.

> [!NOTE]
> Usługa Azure Sentinel zbiera zdarzenia CloudTrail ze wszystkich regionów. Zaleca się, aby nie przesyłać strumieniowo zdarzeń z jednego regionu do drugiego.

## <a name="connect-aws"></a>Łączenie usługi AWS 


1. W usłudze Azure Sentinel wybierz **pozycję Łączniki danych,** a następnie wybierz linię **Amazon Web Services** w tabeli i w okienku AWS po prawej stronie kliknij pozycję Otwórz stronę **łącznika**.

1. Postępuj zgodnie z instrukcjami w obszarze **Konfiguracja,** wykonując następujące kroki.
 
1.  W konsoli Amazon Web Services w obszarze **Zabezpieczenia, Zgodność tożsamości &** wybierz pozycję **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Wybierz pozycję **Role** i wybierz pozycję **Utwórz rolę**.

    ![AWS2 (WSO2)](./media/connect-aws/aws-2.png)

1.  Wybierz **inne konto AWS.** W polu **Identyfikator konta** wprowadź identyfikator **konta Microsoft** (**123412341234**), który można znaleźć na stronie łącznika AWS w portalu Azure Sentinel.

    ![AWS3 (WSO3)](./media/connect-aws/aws-3.png)

1.  Upewnij się, że jest zaznaczona opcja **Wymagaj identyfikatora zewnętrznego,** a następnie wprowadź identyfikator zewnętrzny (identyfikator obszaru roboczego), który można znaleźć na stronie łącznika AWS w portalu Azure Sentinel.

    ![AWS4 (WSE4)](./media/connect-aws/aws-4.png)

1.  W obszarze **Dołącz zasady uprawnień** wybierz **pozycję AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Wprowadź znacznik (opcjonalnie).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Następnie wprowadź **nazwę roli** i wybierz przycisk **Utwórz rolę.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Na liście Role wybierz utworzoną rolę.

    ![AWS8 (WSO)](./media/connect-aws/aws-8.png)

1.  Skopiuj **rolę ARN**. W portalu Azure Sentinel na ekranie łącznika amazon web services wklej go do **roli, aby dodać** pole, a następnie kliknij przycisk **Dodaj**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla zdarzeń AWS, wyszukaj **AWSCloudTrail**.



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć AWS CloudTrail z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats-built-in.md).
- Monitorowanie danych [za pomocą skoroszytów.](tutorial-monitor-your-data.md)

