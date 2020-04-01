---
title: Usługa Azure Security Center — pytania dotyczące maszyn wirtualnych
description: Często zadawane pytania dotyczące maszyn wirtualnych w usłudze Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0b7817539a26b7ebfa24efd086edf8b0ef82eae3
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436008"
---
# <a name="faq---questions-about-virtual-machines"></a>Często zadawane pytania - Pytania dotyczące maszyn wirtualnych


## <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?

Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych utworzonych przy użyciu [modeli wdrażania klasycznego i menedżera zasobów.](../azure-classic-rm.md)

Zobacz [obsługiwane platformy w usłudze Azure Security Center,](security-center-os-coverage.md) aby uzyskać listę obsługiwanych platform.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego usługa Azure Security Center nie rozpoznaje rozwiązania ochrony przed złośliwym oprogramowaniem uruchomionego na mojej maszynie wirtualnej platformy Azure?

Usługa Azure Security Center ma wgląd w ochronę przed złośliwym oprogramowaniem zainstalowaną za pośrednictwem rozszerzeń platformy Azure. Na przykład usługa Security Center nie jest w stanie wykryć ochrony przed złośliwym oprogramowaniem, które zostało wstępnie zainstalowane na dostarczonym obrazie lub jeśli zabezpieczenia przed złośliwym oprogramowaniem zostały zainstalowane na maszynach wirtualnych przy użyciu własnych procesów (takich jak systemy zarządzania konfiguracją).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "Brakujące dane skanowania" dla mojej maszyny Wirtualnej?

Ten komunikat pojawia się, gdy dla maszyny wirtualnej nie ma żadnych danych skanowania. Po włączeniu funkcji zbierania danych w usłudze Azure Security Center wypełnienie danych skanowania może zająć trochę czasu (mniej niż godzinę). Po początkowym wypełnieniu danych skanowania możesz otrzymać taki komunikat, ponieważ w ogóle nie ma danych skanowania lub nie ma ostatnich danych skanowania. Skanowanie nie wypełnia się dla zatrzymanej maszyny wirtualnej. Ten komunikat może również pojawić się, jeśli dane skanowania nie zostały wypełnione niedawno (zgodnie z zasadami przechowywania dla agenta systemu Windows, który ma wartość domyślną 30 dni).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często usługa Security Center skanuje w poszukiwaniu luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu i problemów z ochroną punktów końcowych?

Poniżej znajduje się czas opóźnienia skanowania luk w zabezpieczeniach, aktualizacji i problemów w u źródłach zabezpieczeń:

- Konfiguracje zabezpieczeń systemu operacyjnego – dane są aktualizowane w ciągu 48 godzin
- Aktualizacje systemu – dane są aktualizowane w ciągu 24 godzin
- Problemy z ochroną punktu końcowego — dane są aktualizowane w ciągu 8 godzin

Usługa Security Center zazwyczaj skanuje w poszukiwaniu nowych danych co godzinę i odpowiednio odświeża zalecenia. 

> [!NOTE]
> Usługa Security Center używa agenta usługi Log Analytics do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Migracja platformy Azure Security Center .](security-center-platform-migration.md)


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego jest wyświetlany komunikat "Brak agenta maszyny Wirtualnej?"

Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych, aby włączyć zbieranie danych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje na temat instalowania agenta maszyn wirtualnych na innych maszynach wirtualnych, zobacz wpis w blogu [Agent maszyn wirtualnych i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).