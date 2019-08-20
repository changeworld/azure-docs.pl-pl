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
ms.openlocfilehash: 18579f716f80cdf79b703dc78fe520c675e82c51
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575631"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center

W tym temacie przedstawiono Security Center alerty dostępne podczas monitorowania następujących warstw usług platformy Azure.

* [Warstwa sieci platformy Azure](#network-layer)
* [Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)](#management-layer)

>[!NOTE]
>Korzystając z danych telemetrycznych, które Security Center wykorzystują od przydzielenia do wewnętrznych kanałów informacyjnych platformy Azure, analiza udostępniona poniżej ma zastosowanie do wszystkich typów zasobów.

## Warstwa sieci platformy Azure<a name="network-layer"></a>

Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center modele uczenia maszynowego identyfikują i flagą złośliwych działań związanych z ruchem. Aby wzbogacić adresy IP, Security Center korzysta z bazy danych analizy zagrożeń firmy Microsoft.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Podejrzane działanie wychodzącej sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Remote Desktop Protocol (RDP) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zabezpieczenia zasobu zostały naruszone i są teraz używane do rozważenia zewnętrznego punktu końcowego RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane działanie sieci wychodzącej RDP z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Remote Desktop Protocol (RDP) pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zabezpieczenia zasobu zostały naruszone i są teraz używane do rozważenia zewnętrznych punktów końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH**|Przykładowa analiza ruchu sieciowego wykryła nietypową komunikację wychodzącą Secure Shell (SSH) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zabezpieczenia zostały naruszone i są teraz używane do rozważenia zewnętrznego punktu końcowego protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Secure Shell (SSH) pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zabezpieczenia zostały naruszone i są teraz używane do rozważenia zewnętrznych punktów końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane przychodzące działanie sieci SSH z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację SSH z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę rozdzielenia interfejsu SSH z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci SSH**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację SSH z zasobem w danym wdrożeniu. Stosunkowo wysoka liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania połączenia z interfejsem SSH.
|**Podejrzane przychodzące działanie sieci RDP z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę rozdzielenia interfejsu RDP z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z zasobem w danym wdrożeniu. Stosunkowo wysoka liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania połączenia z interfejsem SSH.|
|**Wykryto komunikację sieciową ze złośliwym adresem**|Przykładowa analiza ruchu w sieci wykryła komunikację pochodzącą z zasobu we wdrożeniu za pomocą możliwego serwera z poleceniem i kontrolą (C & C). Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|

Aby zrozumieć, jak Security Center mogą używać sygnałów związanych z siecią w celu zastosowania ochrony przed zagrożeniami, zobacz [heurystyczne wykrywanie nazw DNS w Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

>[!NOTE]
>Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony, wykorzystując zdarzenia Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Uruchomiono zestaw narzędzi mikroserii**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Narzędzie "mikroseria" (zobacz https://github.com/NetSPI/MicroBurst) , że może być używane przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji, identyfikowania niezabezpieczonych konfiguracji i przecieku poufnych informacji.|
|**Azurite zestaw narzędzi**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Narzędzie "azurite" (zobacz https://github.com/mwrlabs/Azurite) może być używane przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji i identyfikowania niezabezpieczonych konfiguracji.|
|**Podejrzana sesja zarządzania przy użyciu nieaktywnego konta**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który nie był używany przez długi czas, wykonuje teraz akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Podejrzana sesja zarządzania przy użyciu programu PowerShell**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który nie używa programu PowerShell do zarządzania środowiskiem subskrypcji, korzysta obecnie z programu PowerShell i wykonuje akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Korzystanie z zaawansowanych technik trwałości platformy Azure**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Role niestandardowe otrzymały jednostki tożsamości legitimized. Może to spowodować, że osoba atakująca uzyska utrwalenie w środowisku klienta platformy Azure.|
|**Aktywność z rzadko występującego kraju**|Wystąpiło działanie z lokalizacji, która nie była ostatnio lub nigdy odwiedzana przez żadnego użytkownika w organizacji.<br/>Wykrywanie uwzględnia ostatnie lokalizacje działań do określenia lokalizacji nowych i rzadkie. Aparatu wykrywania anomalii są przechowywane informacje o powyższych lokalizacjach, które posługują się użytkownicy w organizacji. 
|**Aktywność z anonimowych adresów IP**|Wykryto aktywność użytkowników z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. <br/>Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania. To wykrywanie wykorzystuje algorytm uczenia maszynowego, który zmniejsza "fałszywie dodatnie", takie jak źle otagowane adresy IP, które są powszechnie używane przez użytkowników w organizacji.|
|**Wykryto niemożliwa podróż**|Wystąpią dwie działania użytkownika (to jedna lub wiele sesji), pochodzące z lokalizacji geograficznie odległych w okresie krótszym niż czas, w którym użytkownik przejdzie z pierwszej lokalizacji do drugiej. Oznacza to, że inny użytkownik korzysta z tych samych poświadczeń. <br/>To wykrywanie wykorzystuje algorytm uczenia maszynowego, który ignoruje oczywiste "fałszywie dodatnie" wpływ na niemożliwe warunki podróży, takie jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. Wykrywanie ma wstępny okres uczenia wynoszący 7 dni, podczas których uczy się wzorca działania nowego użytkownika.|

>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security (MCAS). Aby skorzystać z tych analiz, wymagana jest aktywowana licencja MCAS. Jeśli masz licencję MCAS, te alerty są domyślnie włączone. Aby je wyłączyć:
>
> 1. W bloku Security Center wybierz pozycję **zasady zabezpieczeń**. W przypadku subskrypcji, którą chcesz zmienić, kliknij pozycję **Edytuj ustawienia**.
> 2. Kliknij pozycję **wykrywanie zagrożeń**.
> 3. W obszarze **Włączanie integracji**Usuń zaznaczenie pola wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do danych**i kliknij przycisk **Zapisz**.

>[!NOTE]
>Azure Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft nie wdrożona jeszcze Azure Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Microsoft Cloud App Security (MCAS) jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej MCAS. [Aby uzyskać więcej informacji, zobacz Magazyn danych dla usług innych niż regionalne](http://azuredatacentermap.azurewebsites.net/).
