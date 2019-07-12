---
title: Wykrywanie dla warstwy usług platformy Azure w Centrum zabezpieczeń Azure zagrożeń | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono alertów warstwy usługi platformy Azure dostępnych w usłudze Azure Security Center.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571727"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Wykrywanie zagrożeń dla warstwy usług platformy Azure w Centrum zabezpieczeń Azure

W tym temacie przedstawiono alertów usługi Security Center, dostępne podczas monitorowania następujące warstwy usługi platformy Azure.

* [Azure Network layer](#network-layer)
* [Warstwa zarządzania platformy Azure (Azure Resource Manager) (wersja zapoznawcza)](#management-layer)

>[!NOTE]
>Przy użyciu telemetrii, że usługa Security Center korzysta z sięgnięcie do platformy Azure wewnętrznych źródeł danych, analizy, przedstawione poniżej mają zastosowanie do wszystkich typów zasobów.

## Azure Network layer<a name="network-layer"></a>

Analizy warstwy sieci usługi Security Center opierają się na przykład [danych protokołu IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówki pakietów zbierane przez routery platformy Azure. Oparte na tego strumieniowego źródła danych, modeli uczenia maszynowego Centrum zabezpieczeń identyfikować i oznaczać działania złośliwego ruchu. Wzbogacić adresów IP, usługa Security Center korzysta z bazy danych analizy zagrożeń firmy Microsoft.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Podejrzane wychodzące działanie sieci protokołu RDP**|Analiza ruchu próbkowanych sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop) pochodzące z zasobów w danym wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zasób bezpieczeństwo zostało naruszone i obecnie jest używany do atak siłowy jest zewnętrzny punkt końcowy protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci protokołu RDP do wielu miejsc docelowych**|Analiza ruchu próbkowanych sieci wykryła nietypową wychodzącą komunikację protokołu RDP (Remote Desktop) pochodzące z zasobów w danym wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zasób został złamany i jest teraz używany na atak siłowy zewnętrznych punktów końcowych protokołu RDP. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci protokołu SSH**|Analiza ruchu próbkowanych sieci wykryła nietypową wychodzącą komunikację protokołu Secure Shell (SSH) pochodzące z zasobów w danym wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zasób bezpieczeństwo zostało naruszone i obecnie jest używany do atak siłowy jest zewnętrzny punkt końcowy SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci protokołu SSH do wielu miejsc docelowych**|Analiza ruchu próbkowanych sieci wykryła nietypową wychodzącą komunikację protokołu Secure Shell (SSH) pochodzące z zasobów w danym wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska i może wskazywać, że zasób został złamany i jest teraz używany na atak siłowy zewnętrznych punktów końcowych protokołu SSH. Należy pamiętać, że ten typ działań może powodować oflagowanie adresu IP jako złośliwego przez jednostki zewnętrzne.|
|**Podejrzane przychodzące działanie sieci protokołu SSH z wielu źródeł**|Analiza ruchu w próbkowanych sieci wykryła nietypową SSH przychodzące z wielu źródeł do zasobu w danym wdrożeniu. Różne unikatowe adresy IP nawiązywania połączenia z zasobu, jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku atak siłowy interfejsu protokołu SSH z wielu hostów (Botnet).|
|**Podejrzane przychodzące działanie sieci protokołu SSH**|Analiza ruchu próbkowanych sieci wykryła nietypową przychodzącą komunikację protokołu SSH z zasobem w danym wdrożeniu. Stosunkowo dużą liczbę połączeń przychodzących do zasobu, jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku atak siłowy interfejsu SSH.
|**Podejrzane przychodzące działanie sieci protokołu RDP z wielu źródeł**|Analiza ruchu w próbkowanych sieci wykryła nietypową przychodzącej komunikacji RDP z wielu źródeł do zasobu w danym wdrożeniu. Różne unikatowe adresy IP nawiązywania połączenia z zasobu, jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku siłowego interfejsu protokołu RDP z wielu hostów (Botnet).|
|**Podejrzane przychodzące działanie sieci protokołu RDP**|Analiza ruchu próbkowanych sieci wykryła nietypową przychodzącą komunikację protokołu RDP do zasobu w danym wdrożeniu. Stosunkowo dużą liczbę połączeń przychodzących do zasobu, jest uznawane za nietypowe w przypadku tego środowiska. To działanie może oznaczać próbę ataku atak siłowy interfejsu SSH.|

Aby dowiedzieć się, jak używać sieci w Centrum zabezpieczeń związane z sygnałów, aby zastosować ochronę przed zagrożeniami, zobacz [rozwiązaniami do wykrywania heurystyczne DNS w usłudze Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Warstwa zarządzania platformy Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

>[!NOTE]
>Usługa Security Center warstwę ochrony, oparte na usłudze Resource Manager jest obecnie w wersji zapoznawczej.

Usługa Security Center oferuje dodatkową warstwę ochrony, wykorzystując zdarzeń usługi Azure Resource Manager, który jest uważany za płaszczyznę kontroli dla usługi Azure. Analizując rekordy usługi Azure Resource Manager, usługa Security Center wykrywa nietypowe i potencjalnie szkodliwe działania w środowisku subskrypcji platformy Azure.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Zestaw narzędzi microBurst uruchamiania**|Uruchom pakiet narzędzi Rekonesans znanym środowisku chmury została wykryta w danym środowisku. Narzędzie "MicroBurst" (zobacz https://github.com/NetSPI/MicroBurst) mogą być używane przez osoby atakującej (lub tester penetracji) do mapowania zasobów subskrypcje, zidentyfikować niezabezpieczone konfiguracje i przecieku informacji poufnych.|
|**Zestaw narzędzi azurite uruchamiania**|Uruchom pakiet narzędzi Rekonesans znanym środowisku chmury została wykryta w danym środowisku. Narzędzie "Azurite" (zobacz https://github.com/mwrlabs/Azurite) mogą być używane przez osoby atakującej (lub tester penetracji) do mapowania zasobów subskrypcji i znalezienia niezabezpieczone konfiguracji.|
|**Sesję podejrzane zarządzania przy użyciu nieaktywne konto**|Analiza dzienników aktywności subskrypcji wykrył podejrzanego zachowania. Podmiot zabezpieczeń, który nie był używany w długim okresie czasu, teraz wykonuje akcje, które można zabezpieczyć trwałości przez osobę atakującą.|
|**Podejrzane zarządzania sesji przy użyciu programu PowerShell**|Analiza dzienników aktywności subskrypcji wykrył podejrzanego zachowania. Podmiot zabezpieczeń, które regularnie nie używa programu PowerShell do zarządzania środowiskiem subskrypcji jest teraz przy użyciu programu PowerShell i wykonywanie akcji, które umożliwiają zabezpieczenie trwałości osobie atakującej.|
|**Użyj technik zaawansowane trwałości platformy Azure**|Analiza dzienników aktywności subskrypcji wykrył podejrzanego zachowania. Niestandardowe role mieć jednostek legitimized tożsamości. Może to prowadzić atakującej przejąć stały dostęp do sieci w środowisku klienta na platformie Azure.|
|**Aktywność z rzadko występującego kraju**|Podczas działania z lokalizacji, która nie był nigdy lub ostatnio odwiedzany przez dowolnego użytkownika w organizacji.<br/>Wykrywanie uwzględnia ostatnie lokalizacje działań do określenia lokalizacji nowych i rzadkie. Aparatu wykrywania anomalii są przechowywane informacje o powyższych lokalizacjach, które posługują się użytkownicy w organizacji. 
|**Aktywność z anonimowych adresów IP**|Użytkownicy działanie z adresu IP, który został zidentyfikowany jako adres IP anonimowego serwera proxy została wykryta. <br/>Te serwery proxy są wykorzystywane przez osoby, które chcą ukryć adres IP swojego urządzenia i mogą być używane do złośliwego działania. Wykrywanie korzysta z usługi machine learning algorytmu, który ogranicza "wyniki fałszywie dodatnie", takie jak źle oznakowane adresy IP są powszechnie używane przez użytkowników w organizacji.|
|**Niemożliwa podróż wykryto**|Dwa działania użytkownika (jest jedną lub wiele sesji) wystąpiły pochodzące z komputera odległym geograficznie lokalizacji w przedziale czasu krótszy niż czas jego będą miały użytkownikowi przesyłane z pierwszej lokalizacji do drugiej. Oznacza to, że inny użytkownik korzysta z tych samych poświadczeń. <br/>Wykrywanie korzysta z usługi machine learning algorytmu, który ignoruje oczywiste "wyniki fałszywie dodatnie" Współtworzenie warunki niemożliwych podróży, takich jak sieci VPN i lokalizacje, które regularnie używane przez innych użytkowników w organizacji. Wykrywanie ma wstępny okres uczenia wynoszący 7 dni, podczas których uczy się wzorca działania nowego użytkownika.|

>[!NOTE]
> Kilka z powyższych analizy są obsługiwane przez Microsoft Cloud App zabezpieczeń (MCAS). Aby korzystać z te dane analityczne, wymagana jest aktywowane licencja MCAS. Jeśli masz licencję MCAS, następnie te alerty są domyślnie włączone. Aby je wyłączyć:
>
> 1. W bloku usługi Security Center wybierz **zasady zabezpieczeń**. Kliknij subskrypcję, którą chcesz zmienić, **edytować ustawienia**.
> 2. Kliknij przycisk **wykrywanie zagrożeń**.
> 3. W obszarze **korzystanie z integracji**, usuń zaznaczenie pola wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do moich danych**i kliknij przycisk **Zapisz**.

>[!NOTE]
>Usługa Azure Security Center przechowuje dane klienta związanych z zabezpieczeniami, w tym samym obszarze geograficznym, jako jego zasobów. Jeśli firmy Microsoft nie ma jeszcze wdrożone usługi Azure Security Center w obszarze geograficznym, zasobu, następnie przechowuje dane w Stanach Zjednoczonych. Po włączeniu Microsoft Cloud App Security (MCAS), te informacje są przechowywane zgodnie z regułami lokalizacji geograficznej MCAS. Zobacz [przechowywanie danych na potrzeby usługi inne niż regionalne, aby uzyskać więcej informacji](http://azuredatacentermap.azurewebsites.net/).
