---
title: Wprowadzenie do warstwy Premium pamięci podręcznej Redis Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i zarządzać trwałość Redis, Redis klastra i obsługa sieci Wirtualnej dla swoich wystąpień pamięci podręcznej Redis Azure warstwy Premium
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 38a43756678a3628040b1b995966eff6dd9fb363
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27911207"
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Wprowadzenie do warstwy Premium usługi Azure Redis Cache
Pamięć podręczna Redis Azure jest rozproszonych, zarządzane pamięci podręcznej, które ułatwia tworzenie aplikacji wysoce skalowalne i szybko reagowały zapewniając nadrzędne szybki dostęp do danych. 

Nowa warstwa Premium jest warstwy gotowy przedsiębiorstwa, który obejmuje wszystkie funkcje warstwy standardowa i inne, np. lepszą wydajność, większych obciążeń, odzyskiwania po awarii, importowania i eksportowania i zwiększonych zabezpieczeń. Kontynuuj lekturę, aby dowiedzieć się więcej o dodatkowych funkcji warstwy Premium pamięci podręcznej.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepszą wydajność w porównaniu do warstwy standardowa lub Basic
**Lepsza wydajność na standardowy lub podstawowa warstwy.** Pamięci podręczne w warstwie Premium są wdrażane na sprzęcie, szybkich procesorów i zapewnia lepszą wydajność w porównaniu do podstawowego lub warstwy standardowa. Pamięci podręczne warstwy Premium ma wyższą przepływność i dolnym opóźnienia. 

**Przepływność dla tej samej wielkości pamięci podręcznej jest większa w warstwie Premium w porównaniu do warstwy standardowa.** Na przykład przepływność 53 GB P4 (Premium) pamięci podręcznej jest 250 KB żądań na sekundę w porównaniu do 150 KB C6 (Standard).

Aby uzyskać więcej informacji o rozmiarze, przepływności i przepustowości z pamięci podręcznych premium, zobacz [często zadawane pytania pamięci podręcznej Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Stan trwały danych pamięci podręcznej Redis
Warstwy Premium pozwala zachować dane pamięci podręcznej na koncie magazynu Azure. W pamięci podręcznej Basic/Standard wszystkie dane są przechowywane tylko w pamięci. W przypadku podstawowej infrastruktury istnieje problem może być ryzyko utraty danych. Firma Microsoft zaleca, aby zwiększyć odporność przed utratą danych przy użyciu funkcji trwałości danych pamięci podręcznej Redis w warstwie Premium. Pamięć podręczna Redis Azure oferuje RDB i AOF (wkrótce) opcje w [trwałość Redis](http://redis.io/topics/persistence). 

Instrukcje na temat konfigurowania trwałości można znaleźć w temacie [Konfigurowanie trwałości dla usługi Azure Redis Cache w warstwie Premium](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Klaster redis
Jeśli chcesz utworzyć pamięci podręcznych większych niż 53 GB lub chcesz współdzielenie danych między wieloma węzłami Redis, można użyć Redis klastrowania, która jest dostępna w warstwie Premium. Każdy węzeł składa się z pary pamięć podręczna podstawowy/replik zarządzanych przez usługę Azure wysokiej dostępności. 

**Klaster redis daje maksymalną skalę i przepustowość.** Przepływność zwiększa liniowo zwiększyć liczbę fragmentów (węzłów) w klastrze. Eg. Jeśli tworzenie klastra P4 odłamków 10, a następnie dostępna przepustowość jest 250 KB * 10 = 2,5 mln żądań na sekundę. Zobacz [często zadawane pytania pamięci podręcznej Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) szczegółowe informacje o rozmiarze, przepływności i przepustowości z pamięci podręcznych premium.

Aby rozpocząć pracę z usługą klastrowania, zobacz [sposobu konfigurowania klastrowania podręczna Redis Azure Premium](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Większe bezpieczeństwo i izolacja
Pamięci podręczne utworzone w warstwie Basic lub Standard są dostępne w publicznej sieci internet. Dostęp do pamięci podręcznej jest ograniczony, na podstawie klucza dostępu. Z warstwy Premium dalsze zapewni, że tylko klientów w określonej sieci można uzyskać dostępu do pamięci podręcznej. Można wdrożyć pamięci podręcznej Redis w [sieci wirtualnej platformy Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Aby jeszcze bardziej ograniczyć dostęp do serwera Redis, można użyć wszystkich funkcji VNet, np. podsieci, zasad kontroli dostępu oraz innych funkcji.

Więcej informacji można znaleźć w temacie [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Redis Cache w warstwie Premium](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Import/Eksport jest operacji zarządzania pamięcią podręczną Redis Azure danych, co umożliwia importowanie danych do pamięci podręcznej Redis Azure lub eksportowania danych z pamięci podręcznej Redis Azure przez importowanie i Eksportowanie migawki Redis pamięci podręcznej bazy danych (RDB) z usługi pamięć podręczna premium do stronicowych obiektów blob na koncie magazynu Azure. Dzięki temu można przeprowadzić migrację między różnymi wystąpieniami usługi pamięć podręczna Redis Azure lub wypełnienie pamięci podręcznej danych przed użyciem.

Importu można przełączyć Redis zgodne RDB plików z dowolnego serwera Redis uruchomiona w chmurze lub środowiska, w tym Redis w systemie Linux, Windows albo dowolnego dostawcy chmury, takich jak usług Amazon Web Services i innych użytkowników. Importowanie danych jest w prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnione danych. Podczas procesu importowania pamięć podręczna Redis Azure ładuje RDB pliki z magazynu Azure do pamięci i wstawianie klucze do pamięci podręcznej.

Eksport pozwala na wyeksportowanie danych przechowywanych w pamięci podręcznej Redis Azure do Redis zgodne pliki RDB. Ta funkcja służy do przenoszenia danych z jednego wystąpienia pamięci podręcznej Redis Azure do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, która obsługuje wystąpienie serwera pamięci podręcznej Redis Azure, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania stan powodzenia lub błędu, plik tymczasowy zostanie usunięty.

Aby uzyskać więcej informacji, zobacz [sposobu importowania danych do i eksportowanie danych z pamięci podręcznej Redis Azure](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Ponowne uruchamianie
Warstwy premium pozwala na ponowny rozruch jeden lub większą liczbę węzłów z pamięci podręcznej na żądanie. Dzięki temu można testować aplikację odporności w przypadku awarii. Można ponownie uruchomić następujące węzły.

* Główny węzeł pamięci podręcznej
* Węzeł podrzędny z pamięci podręcznej
* Zarówno i podrzędna węzłów pamięci podręcznej
* Korzystając z usługi pamięć podręczna premium z usługą klastrowania, możesz ponownie głównym, podrzędny lub oba węzły dla poszczególnych fragmentów w pamięci podręcznej

Aby uzyskać więcej informacji, zobacz [ponowny rozruch](cache-administration.md#reboot) i [często zadawane pytania dotyczące ponownego uruchamiania](cache-administration.md#reboot-faq).

>[!NOTE]
>Ponowny rozruch funkcja jest teraz włączona dla wszystkich warstw pamięci podręcznej Redis Azure.
>
>

## <a name="schedule-updates"></a>Aktualizacje harmonogramu
Funkcja zaplanowanych aktualizacji umożliwia wyznaczenie okno obsługi pamięci podręcznej. Okno obsługi zostanie określona, wszystkie aktualizacje serwera Redis są wprowadzane podczas tego okna. Aby wyznaczyć okna obsługi, wybrać żądaną dni i określ konserwacji godzinę każdego dnia rozpoczęcia okna. Należy pamiętać, że czas okna obsługi w formacie UTC. 

Aby uzyskać więcej informacji, zobacz [Zaplanuj aktualizacje](cache-administration.md#schedule-updates) i [Zaplanuj aktualizacje — często zadawane pytania](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Tylko pamięci podręcznej Redis serwera wprowadzone aktualizacji podczas czasu zaplanowanego okna obsługi. Okna obsługi nie ma zastosowania do aktualizacji platformy Azure lub aktualizacje systemu operacyjnego maszyny Wirtualnej.
> 
> 

## <a name="geo-replication"></a>Replikacja geograficzna

**Replikacja geograficzna** zapewnia mechanizm łączenia dwa wystąpienia pamięci podręcznej Redis Azure warstwy Premium. Jedna pamięć podręczna jest wyznaczony jako głównej połączonego pamięci podręcznej, a drugi jako dodatkowej pamięci podręcznej połączony. Dodatkowej pamięci podręcznej połączonego staje się tylko do odczytu, a dane zapisywane w pamięci podręcznej podstawowej są replikowane do dodatkowej połączonego pamięci podręcznej. Ta funkcja umożliwia replikowanie pamięci podręcznej w regionach platformy Azure.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować replikację geograficzną dla pamięci podręcznej Redis Azure](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Aby skalować do warstwy premium
Aby skalować do warstwy premium, po prostu wybierz jedną z warstwy premium w **Zmiana warstwy cenowej** bloku. Można również skalować pamięć podręczną do warstwy premium za pomocą programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać instrukcje, zobacz [jak pamięć podręczna Redis Azure skali](cache-how-to-scale.md) i [dotyczące automatyzacji operacji skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Kolejne kroki
Tworzenie pamięci podręcznej i Poznaj nowe funkcje warstwy premium.

* [Konfigurowanie trwałości dla usługi Azure Redis Cache w wersji Premium](cache-how-to-premium-persistence.md)
* [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Redis Cache w wersji Premium](cache-how-to-premium-vnet.md)
* [Konfigurowanie klastrowania dla usługi Azure Redis Cache w wersji Premium](cache-how-to-premium-clustering.md)
* [Jak danych importowania i eksportowania danych z pamięci podręcznej Redis Azure](cache-how-to-import-export-data.md)
* [Administrowanie pamięć podręczna Redis Azure](cache-administration.md)

