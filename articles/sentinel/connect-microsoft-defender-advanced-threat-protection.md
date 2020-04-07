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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756353"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Połącz alerty z zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender 


> [!IMPORTANT]
> Połknienie alertów zaawansowanej ochrony przed zagrożeniami firmy Microsoft Defender jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Łącznik [Zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) umożliwia przesyłanie alertów z zaawansowanej ochrony przed zagrożeniami usługi Microsoft Defender do usługi Azure Sentinel. Umożliwi to bardziej kompleksową analizę zdarzeń zabezpieczeń w całej organizacji i tworzenie podręczników w celu uzyskania skutecznej i natychmiastowej reakcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć ważną licencję na zaawansowaną ochronę przed zagrożeniami usługi Microsoft Defender, zgodnie z opisem w [programie Konfigurowanie wdrożenia narzędzia Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
