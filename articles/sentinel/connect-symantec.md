---
title: Połącz dane firmy Symantec ICDX przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych firmy Symantec ICDX przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 1a6843fb1668307aa442011233999c648901d404
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793476"
---
# <a name="connect-your-symantec-icdx-appliance"></a>Połącz urządzenie ICDX firmy Symantec 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik ICDX firmy Symantec umożliwia łatwe łączenie wszystkich Twojej firmy Symantec dzienników rozwiązań dotyczących zabezpieczeń, za pomocą usługi Azure przez wartownika, aby wyświetlić pulpity nawigacyjne, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń. Integracja między ICDX firmy Symantec i platformy Azure przez wartownika korzysta z interfejsu API REST.


> [!NOTE]
> Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="configure-and-connect-symantec-icdx"></a>Skonfigurować i połączyć z ICDX firmy Symantec 

Integrację i wyeksportuj dzienniki bezpośrednio na platformie Azure przez wartownika ICDX firmy Symantec.

1. Otwórz konsolę zarządzania ICDX.
2. W menu nawigacji po lewej stronie wybierz **konfiguracji** i następnie **usług przesyłania dalej** kartę.
3. W wierszu usługi Microsoft Azure Log Analytics kliknij **więcej**, a następnie **Edytuj**. 
4. W **Microsoft Azure Log Analytics usługi przesyłania dalej** okna, ustaw następujące opcje:
    - Pozostaw ustawienie domyślne, SymantecICDX nazwa dziennika niestandardowego.
    - Skopiuj identyfikator obszaru roboczego, a następnie wklej je **identyfikatora klienta** pola. Kopiuj **klucz podstawowy** i wklej go w polu klucza wspólnego. Możesz skopiować te wartości z przez wartownika Azure portal, wybierając **łączników danych** i następnie **Symantec ICDX**.
6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń ICDX firmy Symantec, możesz wyszukać **SymantecICDX_CL**.


## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób nawiązać Azure przez wartownika ICDX firmy Symantec. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

