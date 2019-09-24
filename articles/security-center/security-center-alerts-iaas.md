---
title: Wykrywanie zagrożeń dla maszyn wirtualnych i serwerów w Azure Security Center | Microsoft Docs
description: W tym temacie przedstawiono alerty dotyczące maszyn wirtualnych i serwerów dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 0070ab94196dfe752e048e97dd40a8f69cf98373
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202592"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Wykrywanie zagrożeń dla maszyn wirtualnych i serwerów w Azure Security Center

W tym temacie przedstawiono różne typy metod wykrywania i alerty dostępne dla maszyn wirtualnych i serwerów z następującymi systemami operacyjnymi. Aby uzyskać listę obsługiwanych wersji, zobacz [platform i funkcje obsługiwane przez Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Systemy<a name="windows-machines"></a>

Azure Security Center integruje się z usługami platformy Azure w celu monitorowania i ochrony maszyn z systemem Windows. Security Center przedstawia alerty i sugestie dotyczące korygowania ze wszystkich tych usług w łatwym do użycia formacie.

### Windows Server Defender ATP<a nanme="windows-atp"></a>

Security Center rozszerza platformę ochrony obciążeń w chmurze przez integrację z usługą Windows Server Defender Advanced Threat Protection (ATP). Zapewnia to kompleksowe możliwości wykrywania i reagowania punktów końcowych (EDR).

> [!NOTE]
> Czujnik ATP systemu Windows Server Defender jest automatycznie włączany na serwerach z systemem Windows, które używają Security Center.

Gdy usługa Windows Server Defender ATP wykryje zagrożenie, wyzwala alert. Alert jest wyświetlany na pulpicie nawigacyjnym Security Center. Z poziomu pulpitu nawigacyjnego można przestawiać do konsoli usługi Windows Defender ATP i przeprowadzić szczegółowe badanie w celu odzyskania zakresu ataku. Aby uzyskać więcej informacji o usłudze Windows Server Defender ATP, zobacz Dołączanie [serwerów do usługi Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analiza zrzutu awaryjnego<a nanme="windows-dump"></a>

Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii.

Awaria może być spowodowana przez złośliwe oprogramowanie lub zawiera złośliwe oprogramowanie. Aby uniknąć wykrycia przez produkty zabezpieczające, różne rodzaje złośliwego oprogramowania korzystają z ataku bez plików, który pozwala uniknąć zapisywania na dysku lub zaszyfrować składniki oprogramowania napisane na dysku. Atak tego typu jest trudny do wykrycia przy użyciu tradycyjnych metod opartych na dyskach.

Jednak za pomocą analizy pamięci można wykryć ten rodzaj ataku. Analizując pamięć w zrzucie awaryjnym, Security Center mogą wykrywać techniki używane przez atak. Na przykład atakujący może próbować wykorzystać luki w zabezpieczeniach oprogramowania, uzyskać dostęp do poufnych danych i dyskretnego pozostania go w ramach zagrożonego komputera. Security Center działa z minimalnym wpływem na wydajność hostów.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto iniekcję kodu**|Iniekcja kodu to wstawianie modułów wykonywalnych do uruchomionych procesów lub wątków. Ta technika jest używana przez złośliwe oprogramowanie do uzyskiwania dostępu do danych, a jednocześnie ukrywana, aby zapobiec ich znalezieniu i usunięciu. <br/>Ten alert wskazuje, że wprowadzony moduł znajduje się w zrzucie awaryjnym. Aby rozróżnić nieszkodliwe i nieszkodliwe moduły, Security Center sprawdza, czy wprowadzony moduł jest zgodny z profilem podejrzanego zachowania.|
|**Odnaleziono Podejrzany segment kodu**|Wskazuje, że segment kodu został przydzielony przy użyciu metod niestandardowych, takich jak iniekcja odbicia i puste przetwarzanie. Alert zawiera dodatkowe cechy segmentu kodu, który został przetworzony w celu zapewnienia kontekstu dla możliwości i zachowań zgłoszonego segmentu kodu.|
|**Wykryto kod powłoki odnalezione**|Kod powłoki to ładunek uruchamiany po wykorzystaniu przez złośliwe oprogramowanie luki w zabezpieczeniach oprogramowania.<br/>Ten alert oznacza, że analiza zrzutu awaryjnego wykryła kod wykonywalny, który wykazuje zachowanie zwykle wykonywane przez złośliwe ładunki. Chociaż niezłośliwe oprogramowanie może również wykonywać takie zachowanie, nie jest to typowe w normalnych praktykach programistycznych.|

### Wykrywanie ataków bezplikowych<a nanme="windows-fileless"></a>

Ataki bezplikowe ukierunkowane na punkty końcowe są wspólne. Aby uniknąć wykrywania, ataki bez plików wsuwają złośliwe ładunki do pamięci. Ładunki osoby atakującej pozostają w dużej ilości zagrożonych procesów i wykonują szeroką gamę złośliwych działań.

W przypadku wykrywania ataków bez plików, zautomatyzowane metody śledczej pamięci identyfikują narzędzia, techniki i zachowania ataku bez plików. To rozwiązanie okresowo skanuje maszynę w czasie wykonywania i wyodrębnia informacje bezpośrednio z pamięci procesów krytycznych dla zabezpieczeń.

Pozwala znaleźć dowód wykorzystania, iniekcję kodu i wykonywanie złośliwych ładunków. Wykrywanie ataków bezplikowych generuje szczegółowe alerty zabezpieczeń, które przyspieszają Klasyfikacja alertów, korelację i czas odpowiedzi podrzędnej. To podejście uzupełnia oparte na zdarzeniach rozwiązania EDR, co zapewnia większe pokrycie wykrywania.

> [!NOTE]
> Alerty systemu Windows można symulować przez pobranie [Azure Security Center element PlayBook: Alerty](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)zabezpieczeń.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto bezplikową technikę ataków**|Określona pamięć procesu zawiera zestaw narzędzi ataku bezplikowego: Meterpreter. Zestaw narzędzi ataku bez plików zazwyczaj nie ma obecności w systemie plików, dzięki czemu wykrywanie przez tradycyjne oprogramowanie antywirusowe jest trudne.|

### <a name="further-reading"></a>Dalsze informacje

Aby uzyskać przykłady i więcej informacji na temat wykrywania Security Center, zobacz:

* [Jak Azure Security Center automatyzuje wykrywanie ataku cybernetycznymi](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak Azure Security Center wykrywa luki w zabezpieczeniach przy użyciu narzędzi administracyjnych](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## System<a name="linux-machines"></a>

Security Center zbiera rekordy inspekcji z maszyn z systemem Linux przy użyciu **inspekcji**, jednej z najpopularniejszych struktur inspekcji systemu Linux. Inspekcja w jądrze linii głównej. 

### Monitorowane alerty systemu Linux i integracja z programem Microsoft Monitoring Agent (MMA)<a name="linux-auditd"></a>

System podlegający inspekcji składa się z podsystemu poziomu jądra, który jest odpowiedzialny za wywołania systemu monitorowania. Filtruje je według określonego zestawu reguł i zapisuje komunikaty dla nich w gnieździe. Security Center integruje funkcje z pakietu objętego inspekcją w ramach Microsoft Monitoring Agent (MMA). Ta Integracja umożliwia zbieranie zdarzeń inspekcji we wszystkich obsługiwanych dystrybucjach systemu Linux bez wymagań wstępnych.  

rekordy poddane inspekcji są zbierane, wzbogacane i agregowane do zdarzeń przy użyciu agenta MMA systemu Linux. Security Center ciągle dodaje nową analizę, która używa sygnałów systemu Linux do wykrywania złośliwych zachowań na maszynach w chmurze i lokalnych systemach Linux. Podobnie jak w przypadku funkcji systemu Windows, te analizy obejmują między podejrzanymi procesami, podejrzanych prób logowania, ładowanie modułu jądra i inne działania. Te działania mogą wskazywać, że komputer jest atakowany lub został naruszony.  

Poniżej przedstawiono kilka przykładów analizy, które rozciągają się na różne etapy cyklu życia.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Proces uzyskiwania dostępu do pliku kluczy autoryzowanych SSH w nietypowy sposób**|Dostęp do pliku kluczy autoryzowanych przez protokół SSH został uzyskany w metodzie podobnej do znanych kampanii złośliwego oprogramowania. Ten dostęp może wskazywać, że osoba atakująca próbuje uzyskać trwały dostęp do komputera.|
|**Wykryto próbę trwałości**|Analiza danych hosta wykryła, że zainstalowano skrypt uruchamiania dla trybu pojedynczego użytkownika. <br/>Ze względu na to, że każdy legalny proces będzie wymagany do uruchomienia w tym trybie, może to wskazywać, że osoba atakująca dodała złośliwy proces do każdego poziomu uruchomienia w celu zagwarantowania trwałości.|
|**Wykryto manipulację zaplanowanych zadań**|Analiza danych hosta wykryła możliwość manipulowania zaplanowanymi zadaniami. Osoby atakujące często dodają zaplanowane zadania do maszyn, które zostały naruszone, aby uzyskać trwałość.|
|**Modyfikowanie sygnatury czasowej podejrzanych plików**|Analiza danych hosta wykryła podejrzaną modyfikację sygnatury czasowej. Osoby atakujące często kopiują sygnatury czasowe z istniejących, uprawnionych plików do nowych narzędzi, aby uniknąć wykrywania nowo usuniętych plików.|
|**Nowy użytkownik został dodany do grupy sudo**|Analiza danych hosta wykryła, że użytkownik został dodany do grupy sudo, co umożliwia jego członkom uruchamianie poleceń z wysokim poziomem uprawnień.|
|**Potencjalnie wykorzystujące luki w zabezpieczeniach DynoRoot w kliencie DHCP**|Analiza danych hosta wykryła wykonanie nietypowego polecenia z procesem nadrzędnym skryptu dhclient.|
|**Wykryto podejrzany moduł jądra**|Analiza danych hosta wykryła, że plik obiektu udostępnionego jest ładowany jako moduł jądra. Może to być słuszne działanie lub wskazanie naruszenia bezpieczeństwa jednej z maszyn.|
|**Wykryto proces skojarzony z wyszukiwaniem w walucie cyfrowej**|Analiza danych hosta wykryła wykonanie procesu, który jest zwykle skojarzony z wyszukiwaniem w walucie cyfrowej.|
|**Potencjalne przekazywanie portów do zewnętrznego adresu IP**|Analiza danych hosta wykryła, że inicjowanie przekazywania portów do zewnętrznego adresu IP.|

> [!NOTE]
> Alerty systemu Windows można symulować przez pobranie [Azure Security Center element PlayBook: Wykrywanie systemu Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


Aby uzyskać więcej informacji, zobacz:  

* [Skorzystaj z Azure Security Center, aby wykryć, kiedy atak na komputery z systemem Linux zostanie naruszony](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center może wykryć pojawiające się luki w zabezpieczeniach systemu Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 