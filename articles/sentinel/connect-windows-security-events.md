---
title: Zbieraj dane zdarzenia zabezpieczeń Windows w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane dotyczące zdarzeń zabezpieczeń Windows w przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3c79747bf33e1769af5f8d3589904ba15105f216
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087605"
---
# <a name="connect-windows-security-events"></a>Łączenie ze zdarzeniami zabezpieczeń systemu Windows 

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wszystkie zdarzenia zabezpieczeń z serwerów Windows połączonego z obszarem roboczym usługi Azure przez wartownika można przesyłać strumieniowo. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń.  Możesz wybrać zdarzeń do usługi stream:

- **Wszystkie zdarzenia** — wszystkie Windows zabezpieczeń oraz zdarzeń funkcji AppLocker.
- **Typowe** -standardowy zestaw zdarzeń na potrzeby inspekcji.
- **Minimalny** -niewielki zestaw zdarzeń, które mogą wskazywać potencjalne zagrożenia. Po włączeniu tej opcji, nie będziesz mieć możliwość pełnego dziennika inspekcji.
- **Brak** — żadne zdarzenia funkcji AppLocker ani zabezpieczeń.

> [!NOTE]
> 
> - Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Skonfiguruj łącznik zdarzenia zabezpieczeń Windows

Pełną integrację zdarzeń związanych z zabezpieczeniami Windows z platformy Azure przez wartownika:

1. W portalu Azure przez wartownika wybierz **zbierania danych** a następnie kliknij polecenie **zdarzenia zabezpieczeń Windows** kafelka. 
1. Wybierz typy danych, które ma być przesyłana strumieniowo.
1. Kliknij przycisk **Update** (Aktualizuj).


## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć około 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób łączenie zdarzeń zabezpieczeń Windows Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

