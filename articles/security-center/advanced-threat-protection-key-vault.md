---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami dla usługi Azure Key Vault
description: W tym artykule wyjaśniono, jak skonfigurować zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault w programie Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 3ca3026a259e7f23bbfa01398a96cbdbfae1321c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623979"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Ochrona przed zagrożeniami dla Azure Key Vault (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla Azure Key Vault stanowi dodatkową warstwę analizy zabezpieczeń. To narzędzie wykrywa potencjalnie szkodliwe próby dostępu do kont Key Vault lub ich wykorzystania. Korzystając z natywnej zaawansowanej ochrony przed zagrożeniami w Azure Security Center, można rozwiązać zagrożenia bez eksperta zabezpieczeń i bez uczenia się dodatkowych systemów monitorowania zabezpieczeń.

Gdy Security Center wykrywa nietypowe działanie, wyświetla alerty. Również wysyła do administratora subskrypcji wiadomość e-mail z informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zidentyfikowanych zagrożeń.

## <a name="configuring-threat-protection-from-security-center"></a>Konfigurowanie ochrony przed zagrożeniami na podstawie Security Center

Domyślnie Zaawansowana ochrona przed zagrożeniami jest włączona dla wszystkich kont Key Vault podczas subskrybowania Security Center warstwy cenowej standardowa. Aby uzyskać więcej informacji, zobacz [Cennik](security-center-pricing.md).

Aby włączyć lub wyłączyć ochronę dla określonej subskrypcji:

1. W lewym okienku w obszarze Security Center wybierz pozycję **cennik & Ustawienia**.

1. Wybierz subskrypcję z kontami magazynu, dla których chcesz włączyć lub wyłączyć ochronę przed zagrożeniami.

1. Wybierz **warstwę cenową**.

1. W grupie **Wybieranie warstwy cenowej według typów zasobów** Znajdź wiersz **magazyny kluczy** i wybierz opcję **włączone** lub **wyłączone**.

    [![Włączanie lub wyłączanie zaawansowanej ochrony przed zagrożeniami dla Key Vault w Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Wybierz pozycję **Zapisz**.


## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i wyłączania zaawansowanej ochrony przed zagrożeniami dla Azure Key Vault. 

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami:

- [Ochrona przed zagrożeniami w Azure Security Center](threat-protection.md)— w tym artykule opisano źródła alertów zabezpieczeń w programie Azure Security Center.
- [Key Vault alerty zabezpieczeń](alerts-reference.md#alerts-azurekv)— sekcja Key Vault w tabeli referencyjnej dla wszystkich Azure Security Centerych alertów