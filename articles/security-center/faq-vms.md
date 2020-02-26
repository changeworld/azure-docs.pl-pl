---
title: Azure Security Center często zadawane pytania — pytania dotyczące maszyn wirtualnych
description: Często zadawane pytania dotyczące maszyn wirtualnych w Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
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
ms.openlocfilehash: d84085115816a8fe1cba65e191ea391dd91a4aed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599368"
---
# <a name="faq---questions-about-virtual-machines"></a>Często zadawane pytania — pytania dotyczące maszyn wirtualnych


## <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?

Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych utworzonych przy użyciu zarówno [klasycznych, jak i Menedżer zasobów modeli wdrażania](../azure-classic-rm.md).

Zobacz [obsługiwane platformy w Azure Security Center](security-center-os-coverage.md) , aby uzyskać listę obsługiwanych platform.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego usługa Azure Security Center nie rozpoznaje rozwiązanie chroniące przed złośliwym kodem uruchomiona na maszynie Wirtualnej platformy Azure?

Usługa Azure Security Center ma wgląd we ochrony przed złośliwym kodem zainstalowane za pomocą rozszerzeń platformy Azure. Na przykład usługa Security Center nie jest w stanie wykryć ochrony przed złośliwym kodem, który został wstępnie zainstalowane w obrazie, podane lub jeśli zainstalowano ochrony przed złośliwym oprogramowaniem na maszynach wirtualnych przy użyciu własnych procesów (na przykład systemy zarządzania konfiguracją).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "Brak danych skanowania" dla mojej maszyny Wirtualnej?

Ten komunikat pojawia się, gdy nie ma żadnych danych skanowania dla maszyny Wirtualnej. Jego może zająć trochę czasu (mniej niż godzinę) dla danych skanowania wypełnić po włączeniu zbierania danych w usłudze Azure Security Center. Po początkowego zapełniania danych skanowania może odebrane tego komunikatu, ponieważ nie ma żadnych danych skanowania w ogóle lub nie ma żadnych ostatnich danych skanowania. Skanowanie nie wypełnić dla maszyny Wirtualnej w stanie zatrzymania. Ten komunikat może również zostać wyświetlony, jeśli danych skanowania nie została wypełniona ostatnio (zgodnie z zasadami przechowywania dla agenta programu Windows, który ma wartość domyślną w ciągu 30 dni).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często usługa Security Center skanowanie luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu i problemy z ochroną punktu końcowego?

Poniżej przedstawiono czasy opóźnienia Security Center skanowania luk w zabezpieczeniach, aktualizacji i problemów:

- Konfiguracje zabezpieczeń systemu operacyjnego — danych jest aktualizowany w ciągu 48 godzin
- Aktualizacje systemu — danych jest aktualizowany w ciągu 24 godzin
- Problemy z programem Endpoint Protection — dane są aktualizowane w ramach 8 godzin

Usługa Security Center zwykle skanowania pod kątem nowych danych co godzinę i odpowiednio odświeża zaleceń. 

> [!NOTE]
> Security Center używa Microsoft Monitoring Agent do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center migracji platformy](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego otrzymuję komunikat "Brak jest Agent maszyny Wirtualnej?"

Musi być zainstalowany Agent maszyny Wirtualnej na maszynach wirtualnych, aby włączyć zbieranie danych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Aby uzyskać informacje na temat sposobu instalowania agenta maszyny wirtualnej na innych maszynach wirtualnych, zobacz wpis w blogu [Agent i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).