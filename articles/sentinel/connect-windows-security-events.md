---
title: Łączenie danych zdarzeń zabezpieczeń systemu Windows z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane zdarzeń zabezpieczeń systemu Windows z usługą Azure Sentinel.
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
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124968"
---
# <a name="connect-windows-security-events"></a>Łączenie ze zdarzeniami zabezpieczeń systemu Windows 

Łącznik Zdarzenia zabezpieczeń umożliwia przesyłanie strumieniowe wszystkich zdarzeń zabezpieczeń z systemów Windows (serwerów i stacji roboczych, fizycznych i wirtualnych) do obszaru roboczego usługi Azure Sentinel. Dzięki temu można wyświetlać zdarzenia zabezpieczeń systemu Windows na pulpitach nawigacyjnych, używać ich do tworzenia niestandardowych alertów i polegać na nich w celu poprawy dochodzeń, zapewniając lepszy wgląd w sieć organizacji i rozszerzając operacje zabezpieczeń Możliwości. Można wybrać zdarzenia, które mają być przesyłane strumieniowo spośród następujących zestawów:<a name="event-sets"></a>

- **Wszystkie zdarzenia** — wszystkie zdarzenia zabezpieczeń systemu Windows i zasad ograniczeń oprogramowania.
- **Wspólne** — standardowy zestaw zdarzeń do celów inspekcji. Pełna ścieżka inspekcji użytkownika jest uwzględniona w tym zestawie. Na przykład zawiera zarówno zdarzenia logowania użytkownika, jak i wylogowania użytkownika (identyfikatory zdarzeń 4624, 4634). Istnieją również akcje inspekcji, takie jak zmiany w grupach zabezpieczeń, operacje protokołu Kerberos na kluczowym kontrolerze domeny i inne typy zdarzeń zgodnie z zaakceptowanych najlepszych rozwiązań.

    Zestaw zdarzeń **Common** może zawierać niektóre typy zdarzeń, które nie są tak powszechne.  Dzieje się tak, ponieważ głównym punktem **zestawu wspólnego** jest zmniejszenie ilości zdarzeń do łatwiejszego w zarządzaniu poziomu, przy jednoczesnym zachowaniu pełnej możliwości ścieżki inspekcji.

- **Minimalny** — mały zestaw zdarzeń, które mogą wskazywać na potencjalne zagrożenia. Ten zestaw nie zawiera pełnej ścieżki inspekcji. Obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie i inne ważne zdarzenia, które mają bardzo niskie wskaźniki występowania. Na przykład zawiera pomyślne i nieudane logowania użytkowników (identyfikatory zdarzeń 4624, 4625), ale nie zawiera informacji o wylogowaniu (4634), które, choć ważne dla inspekcji, nie ma znaczenia dla wykrywania naruszeń i ma stosunkowo dużą objętość. Większość woluminu danych tego zestawu składa się ze zdarzeń logowania i zdarzeń tworzenia procesu (identyfikator zdarzenia 4688).

- **Brak** — brak zabezpieczeń lub zdarzeń ograniczeń oprogramowania. (To ustawienie służy do wyłączania łącznika).

    Poniższa lista zawiera pełny podział identyfikatorów zdarzeń zabezpieczeń i aplikacji locker dla każdego zestawu:

    | Zestaw zdarzeń | Zebrane identyfikatory zdarzeń |
    | --- | --- |
    | **Minimalny** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Wspólne** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Zbieranie zdarzeń zabezpieczeń w kontekście pojedynczego obszaru roboczego można skonfigurować z usługi Azure Security Center lub Azure Sentinel, ale nie z obu. Jeśli dołączasz usługę Azure Sentinel w obszarze roboczym, w którego jest już uruchomione centrum zabezpieczeń Azure i jest ustawiona na zbieranie zdarzeń zabezpieczeń, masz dwie opcje:
> - Pozostaw kolekcję Zdarzenia zabezpieczeń w usłudze Azure Security Center w stanie zagrożenia. Będziesz mógł wysyłać zapytania i analizować te zdarzenia w usłudze Azure Sentinel, a także w usłudze Azure Security Center. Nie będzie jednak można monitorować stanu łączności łącznika ani zmieniać jego konfiguracji w usłudze Azure Sentinel. Jeśli jest to dla Ciebie ważne, rozważ drugą opcję.
>
> - [Wyłącz zbieranie zdarzeń zabezpieczeń](../security-center/security-center-enable-data-collection.md) w usłudze Azure Security Center, a dopiero potem dodaj łącznik zdarzeń zabezpieczeń w usłudze Azure Sentinel. Podobnie jak w przypadku pierwszej opcji, będzie można wyszukiwać i analizować zdarzenia zarówno w usłudze Azure Sentinel, jak i w usłudze Azure Security Center, ale teraz będzie można monitorować stan łączności łącznika lub zmienić jego konfigurację w — i tylko w — usłudze Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurowanie łącznika Zdarzenia zabezpieczeń systemu Windows

Aby zebrać zdarzenia zabezpieczeń systemu Windows w usłudze Azure Sentinel:

1. Z menu nawigacji usługi Azure Sentinel wybierz polecenie **Łączniki danych**. Z listy łączników kliknij pozycję **Zdarzenia zabezpieczeń**, a następnie przycisk Otwórz **stronę łącznika** w prawym dolnym dolnym przycisku. Następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie w zakładce **Instrukcje,** zgodnie z opisem w pozostałej części tej sekcji.

1. Sprawdź, czy masz odpowiednie uprawnienia zgodnie z **opisem**w obszarze Wymagania wstępne .

1. Pobierz i zainstaluj [agenta usługi Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (znanego również jako agent monitorowania firmy Microsoft lub MMA) na komputerach, dla których chcesz przesyłać strumieniowo zdarzenia zabezpieczeń do usługi Azure Sentinel.

    Dla maszyn wirtualnych platformy Azure:
    
    1. Kliknij **zainstaluj agenta na maszynie wirtualnej systemu Azure Windows,** a następnie na łącze, które pojawi się poniżej.
    1. Dla każdej maszyny wirtualnej, którą chcesz połączyć, kliknij jej nazwę na liście wyświetlanej po prawej stronie, a następnie kliknij pozycję **Połącz**.

    W przypadku maszyn z systemem Windows innych niż Azure (fizycznych, wirtualnych na prem lub wirtualnych w innej chmurze):

    1. Kliknij zainstaluj **agenta na komputerze z systemem Windows nienawiązanych z platformą Azure,** a następnie na łącze, które pojawi się poniżej.
    1. Kliknij odpowiednie łącza pobierania, które pojawiają się po prawej stronie, w obszarze **Komputery z systemem Windows**.
    1. Korzystając z pobranego pliku wykonywalnego, zainstaluj agenta w wybranych systemach Windows i skonfiguruj go przy użyciu **identyfikatora i kluczy obszaru roboczego,** które pojawiają się poniżej wyżej wymienionych łączy pobierania.

    > [!NOTE]
    >
    > Aby umożliwić systemom Windows bez konieczności przesyłania łączności z Internetem do przesyłania strumieniowego zdarzeń do usługi Azure Sentinel, pobierz i zainstaluj **bramę pakietu OMS** na oddzielnym komputerze, używając łącza w prawym dolnym przycisku, aby działać jako serwer proxy.  Nadal trzeba będzie zainstalować agenta usługi Log Analytics w każdym systemie Windows, którego zdarzenia chcesz zebrać.
    >
    > Aby uzyskać więcej informacji na temat tego scenariusza, zobacz [dokumentację **bramy usługi Log Analytics** ](../azure-monitor/platform/gateway.md).

    Aby uzyskać dodatkowe opcje instalacji i dalsze szczegóły, zobacz [dokumentację **agenta usługi Log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Wybierz zestaw zdarzeń[(Wszystkie, Wspólne lub Minimalne),](#event-sets)które chcesz przesyłać strumieniowo.

1. Kliknij przycisk **Update** (Aktualizuj).

1. Aby użyć odpowiedniego schematu w usłudze Log `SecurityEvent` Analytics dla zdarzeń zabezpieczeń systemu Windows, wpisz okno kwerendy.

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Może upłynąć około 20 minut, aż dzienniki zaczną pojawiać się w usłudze Log Analytics. 



## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak połączyć zdarzenia zabezpieczeń systemu Windows z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij wykrywanie zagrożeń za pomocą usługi Azure Sentinel przy użyciu [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.

