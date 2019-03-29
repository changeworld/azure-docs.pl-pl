---
title: Zbieranie danych zapory Windows w wersji zapoznawczej Azure przez wartownika | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane zapory Windows Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: rkarlin
ms.openlocfilehash: 3839d81f70b8bc6dcb1da3c4dd77f52443294707
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574844"
---
# <a name="connect-windows-firewall"></a>Łączenie z zaporą systemu Windows

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Łącznik zapory Windows pozwala na łatwe łączenie dzienników zapory Windows, jeśli są one połączone z obszarem roboczym usługi Azure przez wartownika. To połączenie umożliwia wyświetlanie pulpitów nawigacyjnych, tworzyć niestandardowe alerty i lepsze badanie. To zapewnia lepszy wgląd w sieci swojej organizacji i zwiększa możliwości operacji zabezpieczeń.  


> [!NOTE]
> 
> - Dane będą przechowywane w lokalizacji geograficznej w obszarze roboczym, na którym są uruchomione przez wartownika platformy Azure.

## <a name="enable-the-connector"></a>Włącz łącznik 

1. W portalu Azure przez wartownika wybierz **zbierania danych** a następnie kliknij polecenie **zapory Windows** kafelka. 
1. Wybierz typy danych, które ma być przesyłana strumieniowo.
1. Kliknij pozycję **Zainstaluj**.
6. Aby użyć odpowiednich schematu w usłudze Log Analytics zapory Windows, wyszukaj **SecurityEvent**.

## <a name="validate-connectivity"></a>Zweryfikuj łączność

Może upłynąć zgłaszane 20 minut do momentu dzienników rozpocząć pojawiają się w usłudze Log Analytics. 



## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób połączyć zapory Windows na platformie Azure przez wartownika. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).

