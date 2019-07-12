---
title: Zagrożeń wykrywania dla maszyn wirtualnych i serwerów w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono maszyny Wirtualnej i serwera alerty dostępne w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571584"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Wykrywanie zagrożeń dla maszyn wirtualnych i serwerów w usłudze Azure Security Center

W tym temacie przedstawiono różne rodzaje metod wykrywania i alerty dostępności dla maszyn wirtualnych i serwerów z następującymi systemami operacji. Aby uzyskać listę obsługiwanych wersji, zobacz [platform i funkcji obsługiwanych przez usługę Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Usługa Security Center integruje się z usługami platformy Azure w celu monitorowania i chronienia maszynach z systemem Windows.  Usługa Security Center przedstawia alerty i sugestie dotyczące korygowania z wszystkie te usługi w formacie łatwy w użyciu.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Usługa Azure Security Center rozszerza jego platformach ochrony obciążenia w chmurze dzięki integracji z Windows Defender Advanced Threat Protection (ATP). Zapewnia kompleksowe funkcje do punktu końcowego wykrywania i odpowiedzi (EDR).

> [!NOTE]
> Czujnik serwera usługi Windows Defender ATP jest automatycznie włączone na serwerach Windows, które są dołączone do usługi Azure Security Center.

Jeśli serwer usługi Windows Defender ATP wykrywa zagrożenie, wyzwala alert. Alert jest wyświetlany na pulpicie nawigacyjnym usługi Security Center. Na pulpicie nawigacyjnym można przestawiać konsolę programu Windows Defender ATP w taki sposób, aby wykonywać szczegółowe badania, aby odkryć zakres ataku. Aby uzyskać więcej informacji na temat serwera usługi Windows Defender ATP zobacz [dołączyć serwery do usługi Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analiza zrzutu awaryjnego <a nanme="windows-dump"></a>

Jeśli wystąpi awaria oprogramowania, zrzut awaryjny przechwytuje część pamięci w chwili pojawienia się awarii.

Awarii może być spowodowana przez złośliwe oprogramowanie lub złośliwego kodu. W celu uniknięcia wykrycia przez zabezpieczeń, różne rodzaje złośliwego oprogramowania, użyj fileless ataków, co pozwala uniknąć zapisywania na dysku lub szyfrowania składniki oprogramowania zapisywane na dysku. Ten rodzaj ataku jest trudny do wykrycia przy użyciu tradycyjnych metod opartej na dyskach.

Jednak tego rodzaju atak może zostać wykryte za pomocą analizy pamięci. Analizując pamięć w zrzucie awaryjnym, usługa Security Center może wykryć technik, które się wykorzystać luki w zabezpieczeniach w oprogramowaniu, uzyskać dostęp do poufnych danych i dyskretnego pozostania na zainfekowanym komputerze za pomocą ataku. Odbywa się przez zaplecze usługi Security Center, przy minimalnym wpływie wydajność na hostach.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Wykryto iniekcję kodu**|Iniekcja kodu to wstawianie modułów wykonywalnych do uruchomionych procesów lub wątków. Ta technika jest używany przez złośliwe oprogramowanie, dostęp do danych, podczas pomyślnie ukrywać się w celu zapobieżenia trwa Wykryto i usunięto. <br/>Ten alert wskazuje, że wprowadzony moduł znajduje się w zrzucie awaryjnym. Aby rozróżnienie złośliwych i niezłośliwych niezłośliwych modułów, usługa Security Center sprawdza, czy wprowadzony moduł odpowiada profilowi podejrzanego zachowania.|
|**Podejrzany segment kodu odnalezione**|Wskazuje, że segment kodu został przydzielony przy użyciu niestandardowych metod, takich jak iniekcję odbijającą i zamianę pamięci procesu. Ten alert zawiera dodatkową charakterystykę segment kodu, które zostały przetworzone do zapewniania kontekstu dla funkcji i zachowań zgłoszonego segmentu kodu.|
|**Wykryto kod powłoki**|Kod powłoki to ładunek uruchamiany po wykorzystaniu przez złośliwe oprogramowanie luki w zabezpieczeniach oprogramowania.<br/>Ten alert oznacza, że analiza zrzutu awaryjnego pliku wykonywalnego wykryła zachowanie kodu złośliwych ładunków. Mimo że — złośliwe oprogramowanie może również wykonywać takie zachowanie, nie jest typowy dla praktyk tworzenia oprogramowania normalny.|

### Wykrywanie ataków fileless <a nanme="windows-fileless"></a>

Na platformie Azure firma Microsoft regularnie pojawiają się fileless ataków przeznaczonych dla naszych klientów punktów końcowych.

Aby uniknąć wykrycia, fileless ataków wstrzyknąć złośliwe ładunki do pamięci. Osoba atakująca ładunków pozostania pamięci procesów, których bezpieczeństwo zostało naruszone i wykonać szeroką gamę złośliwych działań.

Za pomocą wykrywania ataków fileless śledczej techniki automatycznego pamięci zidentyfikować zestaw narzędzi fileless ataku, technik i zachowania. To rozwiązanie okresowo skanuje maszynę w czasie wykonywania i wyodrębnia informacje bezpośrednio w pamięci, zabezpieczenia krytyczny procesów.

Znajdzie dowodów na wykorzystanie iniekcji kodu i wykonywania złośliwych ładunków. Wykrywanie ataków fileless generuje alerty zabezpieczeń szczegółowe działanie funkcji Klasyfikacja alertów, określania korelacji i czas odpowiedzi podrzędnego. To podejście uzupełnia rozwiązania EDR oparte na zdarzeniach, zapewniając większą zasięgu wykrywania.

> [!NOTE]
> Alerty Windows można symulować przez pobieranie [element Playbook Centrum zabezpieczeń Azure](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Alerty zabezpieczeń i postępuj zgodnie z wytycznymi podana

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Technika ataku fileless wykryto**|Pamięć procesu wymienionymi poniżej zawiera pakiet narzędzi fileless ataku: Meterpreter. Zestaw narzędzi ataku fileless zwykle nie mają będzie ona widoczna w systemie plików, utrudniając wykrycie przez tradycyjny oprogramowanie antywirusowe.|

### <a name="further-reading"></a>Dalsze informacje

Przykłady i więcej informacji na temat wykrywania usługi Security Center:

* [Jak usługa Azure Security Center automatyzuje wykrywanie ataków cybernetycznych](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Jak usługa Azure Security Center wykrywa luki w zabezpieczeniach przy użyciu narzędzia administracyjne](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Usługa Security Center zbiera dane inspekcji rekordy z maszyny z systemem Linux przy użyciu **auditd**, co najbardziej typowe systemu Linux inspekcji struktur. auditd ma tę zaletę pozostawanie wokół dużo czasu i mieszkających w jądrze linii głównej. 

### Alerty auditd systemu Linux i integracji programu Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

Auditd system składa się z podsystemu poziomie jądra, który jest odpowiedzialny za monitorowanie wywołań systemowych, filtrując ją według danego zestawu reguł i zapisując komunikatów dla nich gniazda. Usługa Security Center integruje się funkcje związane z pakietu wykorzystują w ramach programu Microsoft Monitoring Agent (MMA). Ta integracja umożliwia zbieranie zdarzeń wykorzystują w obsługiwanych dystrybucjach systemu Linux bez wymagań wstępnych.  

wykorzystują rekordy inspekcji są zbierane, wzbogacone, a następnie agregowane do zdarzenia przy użyciu agenta MMA systemu Linux. Centrum zabezpieczeń stale pracuje się nad dodaniem nowych analizy, że Wykorzystaj sygnały w celu wykrywania złośliwych zachowań w chmurze i maszyn z systemem Linux dla lokalnego systemu Linux. Podobnie jak w funkcji Windows, te dane analityczne rozciągać się na podejrzanych procesów, prób zalogowania wątpliwych, ładowanie modułu jądra i innych działań, które wskazują na maszynie jest atakowana lub został naruszony.  

Poniżej przedstawiono kilka przykładów analizy, które pokazują, jak firma Microsoft rozciągać się na różnych etapach cyklu życia ataku.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Występuje, uzyskiwanie dostępu do pliku autoryzowanych kluczy SSH w nietypowy sposób procesu**|W metodzie, podobne do znanego złośliwego oprogramowania kampanii uzyskały dostęp do pliku autoryzowanych kluczy SSH. Dostęp może wskazywać, że osoba atakująca próbuje uzyskać trwały dostęp do maszyny|
|**Próba wykryte trwałości**|Analiza danych hosta wykrył zainstalowanie skryptu uruchamiania w trybie jednego użytkownika. <br/>Ponieważ jest to rzadkie, że żaden proces uzasadnione będzie potrzebować do wykonania w trybie, może to oznaczać osoba atakująca został dodany złośliwego procesu na poziomie każdego przebiegu do zagwarantowania trwałości.|
|**Manipulowanie zaplanowanych zadań wykryto**|Analiza danych hosta wykrył możliwe manipulowania zaplanowanych zadań. Osoby atakujące często Dodaj zaplanowanych zadań na maszynach, które zostały one złamane uzyskanie stanu trwałego.|
|**Podejrzany plik sygnatura czasowa modyfikacji**|Analiza danych hosta wykrył modyfikacji podejrzane sygnatury czasowej. Osoby atakujące często skopiuj sygnatury czasowe z istniejących plików uprawnionych do nowych narzędzi w celu uniknięcia wykrycia nowo porzuconych plików|
|**Nowy użytkownik został dodany do grupy użytkowników programu sudo**|Analiza danych hosta wykrył na tym, czy użytkownik został dodany do grupy użytkowników programu sudo, co umożliwia jego członkom uruchamianie poleceń z wysokim poziomem uprawnień.|
|**Program wykorzystujący luki prawdopodobnie DynoRoot luki w zabezpieczeniach w klienta dhcp**|Analiza danych hosta wykryto wykonanie nietypowe polecenia przy użyciu procesu nadrzędnego dhclient skryptu.|
|**Wykryto moduł jądra podejrzanych**|Analiza danych hosta wykryła pliku udostępnionego obiektu ładowany jako modułu jądra. Może to być uzasadnione działania lub wskazanie, że jednej z maszyn zostało naruszone.|
|**Proces skojarzony z wyszukiwania cyfrowego waluty wykryto**|Analiza danych hosta wykryto wykonanie procesu, zwykle występujących w walucie cyfrowego wyszukiwania|
|**Potencjalne przekazywanie portu zewnętrzny adres IP**|Analiza danych hosta wykryła inicjowania portu przekazywania zewnętrzny adres IP.|

> [!NOTE]
> Można symulować Windows alerty, pobierając [element Playbook Centrum zabezpieczeń Azure: Alerty zabezpieczeń](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) i postępuj zgodnie z wytycznymi podana.


Więcej informacji można znaleźć w tych artykułach:  

* [Wykorzystaj usługę Azure Security Center do wykrycia podczas Linux zagrożonych maszyn ataku](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Usługa Azure Security Center może wykryć pojawiających się luki w zabezpieczeniach w systemie Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 