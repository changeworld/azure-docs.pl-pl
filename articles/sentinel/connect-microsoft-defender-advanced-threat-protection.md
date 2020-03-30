---
title: Łączenie danych usługi Microsoft Defender ATP z usługą Azure Sentinel| Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć dane zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender z usługą Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588216"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Połącz alerty z zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender 


> [!IMPORTANT]
> Połknienie dzienników zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Alerty z [zaawansowanej ochrony przed zagrożeniami](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) usługi Microsoft Defender można przesyłać strumieniowo za pomocą jednego kliknięcia. To połączenie umożliwia przesyłanie strumieniowe alertów z zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender do usługi Azure Sentinel. 

## <a name="prerequisites"></a>Wymagania wstępne

- Prawidłowa licencja dla zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender, która jest włączona zgodnie z opisem w [poprawność obsługi administracyjnej licencjonowania i pełna konfiguracja zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Musisz być administratorem lub administratorem zabezpieczeń w dzierżawie usługi Azure Sentinel.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Połącz się z zaawansowaną ochroną przed zagrożeniami w usłudze Microsoft Defender

Jeśli usługa Microsoft Defender Advanced Threat Protection zostanie wdrożona i pochłonie twoje dane, alerty można łatwo przesyłać strumieniowo do usługi Azure Sentinel.


1. W usłudze Azure Sentinel wybierz **pozycję Łączniki danych**, kliknij kafelek **Zaawansowane zabezpieczenia przed zagrożeniami usługi Microsoft Defender** i wybierz pozycję Otwórz stronę **łącznika**.
1. Kliknij pozycję **Połącz**. 
1. Aby użyć odpowiedniego schematu w usłudze Log Analytics dla alertów usługi Defender ATP, wyszukaj **securityalert,** a **nazwą dostawcy** jest **MDATP**.




## <a name="next-steps"></a>Następne kroki
W tym dokumencie opisano, jak połączyć usługę Microsoft Defender ATP z usługą Azure Sentinel. Aby dowiedzieć się więcej o usłudze Azure Sentinel, zobacz następujące artykuły:
- Dowiedz się, jak [uzyskać wgląd w dane i potencjalne zagrożenia.](quickstart-get-visibility.md)
- Rozpocznij [wykrywanie zagrożeń za pomocą usługi Azure Sentinel](tutorial-detect-threats.md).
