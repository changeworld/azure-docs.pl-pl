---
title: Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center | Microsoft Docs
description: W tym temacie przedstawiono alerty warstwy usług platformy Azure dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 5458d26172123f0f6cb9914a04ec05b3163a084f
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013271"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center

W tym temacie przedstawiono Azure Security Center alerty dostępne podczas monitorowania następujących warstw usługi platformy Azure:

* [Warstwa sieci platformy Azure](#network-layer)
* [Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)](#management-layer)

>[!NOTE]
>Poniższe analizy dotyczą wszystkich typów zasobów. Wykorzystują dane telemetryczne, które Security Center udostępniają, wybierając wewnętrzne kanały informacyjne platformy Azure.

## Warstwa sieci platformy Azure<a name="network-layer"></a>

Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center modele uczenia maszynowego identyfikują i flagą złośliwych działań związanych z ruchem. Aby wzbogacić adresy IP, Security Center korzysta z bazy danych Microsoft Threat Intelligence.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Podejrzane działanie wychodzącej sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Remote Desktop Protocol (RDP) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego ataku zewnętrznego punktu końcowego RDP. Należy zauważyć, że ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane działanie sieci wychodzącej RDP z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową wychodzącą komunikację RDP, pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego wymuszania ataku zewnętrznych punktów końcowych RDP. Należy zauważyć, że ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Secure Shell (SSH) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego ataku zewnętrznego punktu końcowego protokołu SSH. Należy zauważyć, że ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową wychodzącą komunikację SSH, pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego wymuszania ataku zewnętrznych punktów końcowych SSH. Należy zauważyć, że ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane przychodzące działanie sieci SSH z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację SSH z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę nawiązania pełnego ataku interfejsu SSH z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci SSH**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację SSH z zasobem w danym wdrożeniu. Relatywnie duża liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania ataku na ataki w interfejsie SSH.
|**Podejrzane przychodzące działanie sieci RDP z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę rozdzielenia ataku interfejsu RDP z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z zasobem w danym wdrożeniu. Relatywnie duża liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania ataku na ataki w interfejsie SSH.|
|**Wykryto komunikację sieciową ze złośliwym adresem**|Przykładowa analiza ruchu w sieci wykryła komunikację pochodzącą z zasobu we wdrożeniu za pomocą możliwego serwera z poleceniem i kontrolą (C & C). Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|

Aby zrozumieć, jak Security Center mogą używać sygnałów związanych z siecią w celu zastosowania ochrony przed zagrożeniami, zobacz [heurystyczne wykrywanie nazw DNS w Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Alerty wykrywania zagrożeń warstwy sieciowej platformy Azure w Azure Security Center nie są generowane w ciągu pierwszej godziny po utworzeniu maszyny wirtualnej.

## Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

>[!NOTE]
>Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Uruchomiono zestaw narzędzi mikroserii**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Mikroseria [](https://github.com/NetSPI/MicroBurst) narzędzi może być używana przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji, identyfikowania niezabezpieczonych konfiguracji i przecieku poufnych informacji.|
|**Azurite zestaw narzędzi**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Narzędzie [azurite](https://github.com/mwrlabs/Azurite) może być używane przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji i identyfikowania niezabezpieczonych konfiguracji.|
|**Podejrzana sesja zarządzania przy użyciu nieaktywnego konta**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który nie jest używany przez długi czas, wykonuje teraz akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Podejrzana sesja zarządzania przy użyciu programu PowerShell**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który regularnie nie używa programu PowerShell do zarządzania środowiskiem subskrypcji, korzysta obecnie z programu PowerShell i wykonuje akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Korzystanie z zaawansowanych technik trwałości platformy Azure**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Role niestandardowe otrzymały jednostki tożsamości legitimized. Może to spowodować, że osoba atakująca uzyska utrwalenie w środowisku klienta platformy Azure.|
|**Aktywność z rzadko występującego kraju**|Wystąpiło działanie z lokalizacji, która nie była ostatnio lub kiedykolwiek odwiedzana przez dowolnego użytkownika w organizacji.<br/>Wykrywanie uwzględnia ostatnie lokalizacje działań do określenia lokalizacji nowych i rzadkie. Aparatu wykrywania anomalii są przechowywane informacje o powyższych lokalizacjach, które posługują się użytkownicy w organizacji. 
|**Aktywność z anonimowych adresów IP**|Wykryto aktywność użytkowników z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. <br/>Te serwery proxy są używane przez osoby, które chcą ukryć adres IP urządzenia i mogą być używane do złośliwego celu. Ta funkcja wykrywania używa algorytmu uczenia maszynowego, który zmniejsza liczbę fałszywych dodatnich, takich jak źle otagowane adresy IP, które są szeroko używane przez użytkowników w organizacji.|
|**Wykryto niemożliwa podróż**|Wystąpiły dwa działania użytkownika (w jednej lub wielu sesjach) pochodzące z lokalizacji geograficznie odległej. Dzieje się tak w przedziale czasowym krótszym niż czas, przez który użytkownik mógł podróżować z pierwszej lokalizacji do drugiej. Oznacza to, że inny użytkownik korzysta z tych samych poświadczeń. <br/>Ta funkcja wykrywania używa algorytmu uczenia maszynowego, który ignoruje oczywistych fałszywych zwracanych wyników, takich jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. Wykrycie ma początkowy okres uczenia wynoszący siedem dni, podczas którego nauczy się wzorca aktywności nowego użytkownika.|

>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security. Aby skorzystać z tych analiz, należy aktywować licencję Cloud App Securityową. Jeśli masz licencję na Cloud App Security, te alerty są domyślnie włączone. Aby je wyłączyć:
>
> 1. W bloku **Security Center** wybierz pozycję **zasady zabezpieczeń**. W przypadku subskrypcji, którą chcesz zmienić, wybierz pozycję **Edytuj ustawienia**.
> 2. Wybierz pozycję **wykrywanie zagrożeń**.
> 3. W obszarze **Włącz integracje**wyczyść pole wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do moich danych**i wybierz pozycję **Zapisz**.

>[!NOTE]
>Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft jeszcze nie wdrożona Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Cloud App Security jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej Cloud App Security. Aby uzyskać więcej informacji, zobacz [Magazyn danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).
