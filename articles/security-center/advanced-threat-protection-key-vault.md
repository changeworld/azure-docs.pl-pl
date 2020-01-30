---
title: Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault | Microsoft Docs
description: W tym artykule wyjaśniono, jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault w programie Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f7f716734875d652de575991a4dc1b7bfe948ae3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773533"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby dostępu do kont Key Vault lub ich wykorzystania. Korzystając z natywnej zaawansowanej ochrony przed zagrożeniami w Azure Security Center, można rozwiązać zagrożenia bez eksperta zabezpieczeń i bez uczenia się dodatkowych systemów monitorowania zabezpieczeń.

Gdy Security Center wykrywa nietypowe działanie, wyświetla alerty. Również wysyła do administratora subskrypcji wiadomość e-mail z informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zidentyfikowanych zagrożeń.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami na podstawie Azure Security Center

Domyślnie Zaawansowana ochrona przed zagrożeniami jest włączona dla wszystkich kont Key Vault, gdy subskrybujesz warstwę Security Center Standard. Aby uzyskać więcej informacji, zobacz [Cennik](security-center-pricing.md).

Aby włączyć lub wyłączyć ochronę dla określonej subskrypcji, wykonaj następujące kroki.

1. W lewym okienku w obszarze Security Center wybierz pozycję **cennik & Ustawienia**.
1. Wybierz subskrypcję z kontami magazynu, dla których chcesz włączyć lub wyłączyć ochronę przed zagrożeniami.
1. Wybierz **warstwę cenową**.
1. W grupie **Wybieranie warstwy cenowej według typów zasobów** Znajdź wiersz **magazyny kluczy** i wybierz opcję **włączone** lub **wyłączone**.

    [![Włączanie lub wyłączanie zaawansowanej ochrony przed zagrożeniami dla Key Vault w Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Wybierz pozycję **Zapisz**.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i wyłączania zaawansowanej ochrony przed zagrożeniami dla Azure Key Vault. 

Inne powiązane materiały można znaleźć w następującym artykule:

- [Wykrywanie zagrożeń dla warstw usług platformy Azure w Security Center: w](security-center-alerts-service-layer.md)tym artykule opisano alerty związane z zaawansowaną ochroną przed zagrożeniami dla Azure Key Vault.
