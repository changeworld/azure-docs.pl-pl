---
title: Połącz dane firmy Symantec w usłudze AWS do platformy Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych usługi AWS firmy Symantec przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673975"
---
# <a name="connect-azure-sentinel-to-aws"></a>Azure wartownik nawiązać połączenie usług AWS

Umożliwia przesyłanie strumieniowe zdarzeń narzędzie CloudTrail usług AWS do platformy Azure przez wartownika łącznika usług AWS. Ten proces połączenia deleguje dostęp do platformy Azure przez wartownika do dzienników zasobów AWS, utworzenie relacji zaufania między narzędzie CloudTrail usług AWS i Azure przez wartownika. Jest to realizowane na platformie AWS, tworząc rolę, która zapewnia uprawnienia do platformy Azure przez wartownika na uzyskiwanie dostępu do usług AWS dzienników.

## <a name="prerequisites"></a>Wymagania wstępne

W obszarze roboczym usługi Azure przez wartownika, musi mieć uprawnienia do zapisu.

## <a name="connect-aws"></a>Łączenie usługi AWS 
 
1.  W swojej usługi Amazon Web Services konsoli, w obszarze **Security, Identity & Compliance**, kliknij pozycję **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Wybierz **role** i kliknij przycisk **tworzenia ról**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Wybierz **AWS innego konta.** W **Accountid** wprowadź **identyfikator konta Microsoft** (**123412341234**), można znaleźć na stronie łącznika usługi AWS w portalu Azure przez wartownika.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Upewnij się, że **wymagają Identyfikatora zewnętrznego** jest zaznaczone i następnie i wprowadź identyfikator zewnętrzny (identyfikator obszaru roboczego), którą można znaleźć na stronie łącznika usługi AWS w portalu Azure przez wartownika.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  W obszarze **Dołączanie zasad uprawnień** wybierz **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Wprowadź numer seryjny (opcjonalnie).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Następnie wprowadź **nazwy roli** i kliknij przycisk **tworzenia ról** przycisku.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  Na liście ról wybierz rolę, którą utworzono.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Kopiuj **ARN roli** i wklej go w **roli, aby dodać** pola w witrynie Azure Portal przez wartownika.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń usługi AWS, możesz wyszukać **AWSCloudTrail**.



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączeń narzędzie CloudTrail usług AWS do platformy Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

