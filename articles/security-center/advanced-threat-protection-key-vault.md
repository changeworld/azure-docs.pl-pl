---
title: Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault w programie Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521880"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby dostępu do kont Key Vault lub ich wykorzystania. Korzystając z natywnej zaawansowanej ochrony przed zagrożeniami w Security Center, można rozwiązać zagrożenia bez eksperta zabezpieczeń i bez uczenia się dodatkowych systemów monitorowania zabezpieczeń.

Gdy Security Center wykrywa nietypowe działanie, wyświetla alerty. Również wysyła do administratora subskrypcji wiadomość e-mail z informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zidentyfikowanych zagrożeń. 

> [!NOTE]
> Zaawansowana ochrona przed zagrożeniami dla Azure Key Vault jest obecnie dostępna tylko w regionach Ameryka Północna.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Aby skonfigurować zaawansowaną ochronę przed zagrożeniami na podstawie Azure Security Center

Domyślnie Zaawansowana ochrona przed zagrożeniami jest włączona dla wszystkich kont usługi Key Vault, gdy subskrybujesz warstwę standardową Security Center (zobacz [Cennik](security-center-pricing.md)). 

Aby włączyć lub wyłączyć ochronę dla określonej subskrypcji:

1. Na pasku bocznym Security Center kliknij pozycję **cennik & Ustawienia**.
1. Wybierz subskrypcję z kontami magazynu, dla których chcesz włączyć lub wyłączyć ochronę przed zagrożeniami.
1. Kliknij pozycję **warstwa cenowa**.
1. W grupie **Wybieranie warstwy cenowej według typu zasobu** Znajdź wiersz magazyny kluczy i kliknij opcję **włączone** lub **wyłączone**.
    [![Włączanie lub wyłączanie zaawansowanej ochrony przed zagrożeniami dla Key Vault w Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Kliknij pozycję **Zapisz**.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i wyłączania zaawansowanej ochrony przed zagrożeniami dla Azure Key Vault. 

Inne powiązane materiały można znaleźć w następującym artykule:

- [Wykrywanie zagrożeń dla warstw usług platformy Azure w Security Center — w](security-center-alerts-service-layer.md) tym artykule opisano alerty związane z zaawansowaną ochroną przed zagrożeniami dla Azure Key Vault