---
title: Wykrywanie zagrożeń warstwy usług platformy Azure — Azure Security Center
description: W tym temacie przedstawiono alerty warstwy usług platformy Azure dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: d4033989830323856ac14ed06eea7df74806f128
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665709"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center

W tym temacie przedstawiono Azure Security Center alerty dostępne podczas monitorowania następujących warstw usługi platformy Azure:

* [Warstwa sieci platformy Azure](#network-layer)
* [Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)](#management-layer)
* [Usługa Azure Key Vault](#azure-keyvault)

## Warstwa sieci platformy Azure<a name="network-layer"></a>

Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center korzysta z modeli uczenia maszynowego do identyfikowania i flagowania złośliwych działań w zakresie ruchu. Security Center używa również bazy danych Microsoft Threat Intelligence do wzbogacania adresów IP.

Niektóre konfiguracje sieci mogą ograniczać Security Center generowania alertów dotyczących podejrzanych działań w sieci. Aby Security Center wygenerować alerty sieciowe, upewnij się, że:

- Maszyna wirtualna ma publiczny adres IP (lub znajduje się w usłudze równoważenia obciążenia z publicznym adresem IP).

- Ruch wychodzący z sieci maszyny wirtualnej nie jest blokowany przez rozwiązanie identyfikatorów zewnętrznych.

- Twoja maszyna wirtualna ma przypisany ten sam adres IP przez całą godzinę, w której wystąpiła podejrzana komunikacja. Dotyczy to również maszyn wirtualnych utworzonych w ramach usługi zarządzanej (np. AKS, datacegłs).

Aby uzyskać listę alertów warstwy sieci platformy Azure, zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-azurenetlayer).

Aby uzyskać szczegółowe informacje o tym, jak Security Center mogą używać sygnałów związanych z siecią w celu zastosowania ochrony przed zagrożeniami, zobacz [heurystyczne wykrywanie nazw DNS w Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).


## Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

Aby uzyskać listę alertów Azure Resource Manager (wersja zapoznawcza), zobacz [tabelę referencyjną alertów](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security. Aby skorzystać z tych analiz, należy aktywować licencję Cloud App Securityową. Jeśli masz licencję na Cloud App Security, te alerty są domyślnie włączone. Aby je wyłączyć:
>
> 1. W bloku **Security Center** wybierz pozycję **zasady zabezpieczeń**. W przypadku subskrypcji, którą chcesz zmienić, wybierz pozycję **Edytuj ustawienia**.
> 2. Wybierz pozycję **wykrywanie zagrożeń**.
> 3. W obszarze **Włącz integracje**wyczyść pole wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do moich danych**i wybierz pozycję **Zapisz**.

>[!NOTE]
>Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft jeszcze nie wdrożona Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Cloud App Security jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej Cloud App Security. Aby uzyskać więcej informacji, zobacz [Magazyn danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault (wersja zapoznawcza)<a name="azure-keyvault"></a>

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. 

Azure Security Center obejmuje platformę Azure — natywną, zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Security Center wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Key Vault lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności postanowień eksperta w zakresie bezpieczeństwa, bez konieczności zarządzania systemami monitorowania zabezpieczeń innych firm.  

W przypadku wystąpienia nietypowych działań Security Center wyświetla alerty i opcjonalnie wysyła je za pośrednictwem poczty e-mail do administratorów subskrypcji. Te alerty obejmują szczegóły podejrzanego działania i zalecenia dotyczące sposobu badania i korygowania zagrożeń. 

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

Listę alertów Azure Key Vault można znaleźć w [tabeli referencyjnej alertów](alerts-reference.md#alerts-azurekv).
