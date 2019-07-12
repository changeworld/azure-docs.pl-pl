---
title: Łączenie danych zdarzeń zabezpieczeń Windows przez wartownika platformy Azure w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak nawiązać połączenie z danych zdarzeń zabezpieczeń Windows Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 188febf090ddb3f685f9d3c3b94d822f15bbcfcb
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673762"
---
# <a name="connect-windows-security-events"></a>Łączenie ze zdarzeniami zabezpieczeń systemu Windows 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wszystkie zdarzenia zabezpieczeń z serwerów Windows połączonego z obszarem roboczym usługi Azure przez wartownika można przesyłać strumieniowo. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń.  Możesz wybrać zdarzeń do usługi stream:

- **Wszystkie zdarzenia** — wszystkie Windows zabezpieczeń oraz zdarzeń funkcji AppLocker.
- **Typowe** -standardowy zestaw zdarzeń na potrzeby inspekcji. Dziennik inspekcji pełną użytkownika znajduje się w tym zestawie. Na przykład ten zestaw zawiera zarówno logowanie użytkownika, jak i użytkownika wylogowania zdarzeń (zdarzenie ID 4634). Dołączamy inspekcji akcji, takich jak zmiany w grupie zabezpieczeń, operacji protokołu Kerberos kontrolera domeny kluczy i innych zdarzeń, które są zalecane przez organizacje z branży.

Zdarzenia, które mają bardzo małą liczbą zostały uwzględnione w typowych Ustaw jako główny motywacja wybrać za pośrednictwem wszystkich zdarzeń jest redukować ilość danych, a nie odfiltrować określonych zdarzeń.
- **Minimalny** -niewielki zestaw zdarzeń, które mogą wskazywać potencjalne zagrożenia. Po włączeniu tej opcji, nie będziesz mieć możliwość pełnego dziennika inspekcji.  Ten zestaw zawiera tylko te zdarzenia, które mogą wskazywać na naruszenie pomyślne i ważne wydarzenia, które mają bardzo niskim poziomie. Na przykład ten zestaw zawiera udane i nieudane logowania użytkownika (zdarzenie 4624 identyfikatory 4625), ale nie zawiera on Wyloguj informacji jest ważne w przypadku inspekcji, ale nie ma istotnego znaczenia dla wykrywania, która jest stosunkowo dużych ilościach. Większość ilość danych, ten zestaw jest znak w zdarzeniach i procesu tworzenia zdarzeń (zdarzenie 4688 identyfikator).
- **Brak** — żadne zdarzenia funkcji AppLocker ani zabezpieczeń.

> [!NOTE]
> 
> - Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

Poniższa lista zawiera kompletnego podziału zabezpieczeń i AppLocker identyfikatory zdarzeń dla każdego zestawu:

| Warstwa danych | Wskaźniki zebranych zdarzeń |
| --- | --- |
| Minimalny | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Wspólne | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Skonfiguruj łącznik zdarzenia zabezpieczeń Windows

Pełną integrację zdarzeń związanych z zabezpieczeniami Windows z platformy Azure przez wartownika:

1. W portalu Azure przez wartownika wybierz **łączników danych** a następnie kliknij polecenie **zdarzenia zabezpieczeń Windows** kafelka. 
1. Wybierz typy danych, które ma być przesyłana strumieniowo.
1. Kliknij przycisk **Aktualizuj**.
6. Aby użyć odpowiednich schematu w usłudze Log Analytics dla zdarzeń zabezpieczeń Windows, wyszukaj **SecurityEvent**.

## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć około 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenie zdarzeń zabezpieczeń Windows Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

