---
title: Łączenie danych zdarzeń zabezpieczeń systemu Windows z platformą Azure Microsoft Docs
description: Dowiedz się, jak połączyć dane zdarzeń zabezpieczeń systemu Windows z wskaźnikiem na platformę Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 70190eeb3adec239d1e52f51afcd173497d08e6a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588012"
---
# <a name="connect-windows-security-events"></a>Łączenie ze zdarzeniami zabezpieczeń systemu Windows 



Można przesyłać strumieniowo wszystkie zdarzenia zabezpieczeń z serwerów z systemem Windows połączonych z obszarem roboczym wskaźnikowego platformy Azure. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Zapewnia to dokładniejszy wgląd w sieć organizacji i zwiększa możliwości operacji zabezpieczeń.  Możesz wybrać zdarzenia do przesyłania strumieniowego:

- **Wszystkie zdarzenia** — wszystkie zdarzenia zabezpieczeń systemu Windows i funkcji AppLocker.
- **Wspólny** — standardowy zestaw zdarzeń do celów inspekcji. W tym zestawie znajduje się pełny dziennik inspekcji użytkownika. Na przykład ten zestaw zawiera zdarzenia logowania użytkownika i wylogowania użytkownika (Identyfikator zdarzenia 4634). Dołączamy inspekcji akcji, takich jak zmiany w grupie zabezpieczeń, operacji protokołu Kerberos kontrolera domeny kluczy i innych zdarzeń, które są zalecane przez organizacje z branży.

Zdarzenia, które mają bardzo małą liczbą zostały uwzględnione w typowych Ustaw jako główny motywacja wybrać za pośrednictwem wszystkich zdarzeń jest redukować ilość danych, a nie odfiltrować określonych zdarzeń.
- **Minimalny** — niewielki zestaw zdarzeń, które mogą wskazywać na potencjalne zagrożenia. Po włączeniu tej opcji nie będzie możliwe posiadanie pełnego dziennika inspekcji.  Ten zestaw obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie i ważne zdarzenia, które mają bardzo niski wolumin. Na przykład ten zestaw zawiera pomyślne i nieudane Logowanie użytkownika (identyfikatory zdarzeń 4624, 4625), ale nie zawiera informacji o wylogowaniu ważnych dla inspekcji, ale nie ma znaczenia dla wykrywania i ma stosunkowo duży wolumin. Większość ilości danych tego zestawu to zdarzenia logowania i zdarzenia tworzenia procesu (Identyfikator zdarzenia 4688).
- **Brak** — brak zdarzeń zabezpieczeń lub funkcji AppLocker.

> [!NOTE]
> 
> - Dane będą przechowywane w lokalizacji geograficznej obszaru roboczego, w którym jest uruchamiany wskaźnik platformy Azure.
> - Jeśli Azure Security Center i Azure — Wskaźnikowanie są uruchomione w tym samym obszarze roboczym, łącznik zdarzeń zabezpieczeń może być połączony tylko z Azure Security Center lub z platformy Azure. Aby zarządzać tymi zdarzeniami z platformy Azure, zalecamy odłączenie jej od Azure Security Center i połączenie jej z usługą Azure wskaźnikiem.


Poniższa lista zawiera kompletny podział identyfikatorów zdarzeń związanych z zabezpieczeniami i blokowaniem aplikacji dla każdego zestawu:

| Warstwa danych | Wskaźniki zebranych zdarzeń |
| --- | --- |
| Minimalny | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Wspólne | 1, 413, 300, 324, 340, 403, 404, 410, 411, 412,, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurowanie łącznika zdarzeń zabezpieczeń systemu Windows

Aby w pełni zintegrować zdarzenia zabezpieczeń systemu Windows z wskaźnikiem kontrolnym platformy Azure:

1. W portalu wskaźnikowym platformy Azure wybierz pozycję **Łączniki danych** , a następnie kliknij kafelek **zdarzenia zabezpieczeń** . 
1. Wybierz typy danych, które chcesz przesłać strumieniowo.
1. Kliknij przycisk **Update** (Aktualizuj).
6. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń zabezpieczeń systemu Windows, wyszukaj ciąg **SecurityEvent**.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać około 20 minut. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zdarzeń zabezpieczeń systemu Windows z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

