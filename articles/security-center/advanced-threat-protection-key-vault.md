---
title: Ochrona przed zagrożeniami dla usługi Azure Key Vault
description: W tym artykule wyjaśniono, jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla usługi Azure Key Vault w usłudze Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914810"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrona przed zagrożeniami dla usługi Azure Key Vault (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla usługi Azure Key Vault zapewnia dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby uzyskania dostępu do kont usługi Key Vault lub ich wykorzystania. Korzystając z natywnej zaawansowanej ochrony przed zagrożeniami w usłudze Azure Security Center, można rozwiązać zagrożenia bez bycia ekspertem w dziedzinie zabezpieczeń i bez uczenia się dodatkowych systemów monitorowania zabezpieczeń.

Gdy Usługa Security Center wykryje nietypową aktywność, wyświetla alerty. Wysyła również wiadomość e-mail do administratora subskrypcji ze szczegółami podejrzanej aktywności i zaleceniami dotyczącymi sposobu badania i korygować zidentyfikowane zagrożenia.

## <a name="configuring-threat-protection-from-security-center"></a>Konfigurowanie ochrony przed zagrożeniami z centrum zabezpieczeń

Domyślnie zaawansowana ochrona przed zagrożeniami jest włączona dla wszystkich kont usługi Key Vault podczas subskrybowania standardowej warstwy cenowej usługi Security Center. Aby uzyskać więcej informacji, zobacz [Cennik](security-center-pricing.md).

Aby włączyć lub wyłączyć ochronę dla określonej subskrypcji:

1. W lewym okienku w Centrum zabezpieczeń wybierz pozycję **Ustawienia & cennika**.

1. Wybierz subskrypcję z kontami magazynu, dla których chcesz włączyć lub wyłączyć ochronę przed zagrożeniami.

1. Wybierz **warstwę cenową**.

1. W grupie **Wybierz warstwę cenową według typu zasobnika** znajdź wiersz **Przechowalnia kluczy** i wybierz pozycję **Włączone** lub **Wyłączone**.

    [![Włączanie lub wyłączanie zaawansowanej ochrony przed zagrożeniami dla usługi Key Vault w usłudze Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Wybierz **pozycję Zapisz**.


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak włączyć i wyłączyć zaawansowaną ochronę przed zagrożeniami dla usługi Azure Key Vault. 

Materiały pokrewne można znaleźć w następujących artykułach:

- [Ochrona przed zagrożeniami w usłudze Azure Security Center](threat-protection.md)— w tym artykule opisano źródła alertów zabezpieczeń w usłudze Azure Security Center.
- [Alerty zabezpieczeń magazynu kluczy](alerts-reference.md#alerts-azurekv)— sekcja Magazyn kluczy w tabeli odwołań dla wszystkich alertów usługi Azure Security Center