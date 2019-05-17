---
title: Wprowadzenie do usługi Azure Cache w warstwie Redis Premium | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i zarządzać trwałość Redis, Redis, klastrowania i obsługa sieci Wirtualnej w warstwie Premium usługi Azure Cache dla wystąpień usługi Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 6960c21091e0bc01c198e713c0c276984566ac41
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786076"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Wprowadzenie do platformy Azure pamięci podręcznej Redis w warstwie Premium
Pamięć podręczna systemu Azure dla usługi Redis jest rozproszona, zarządzana pamięć podręczna, która umożliwia tworzenie szybko reagujących aplikacji przy zapewnieniu superszybkiego dostępu do danych. 

Nowa warstwa Premium jest warstwa przeznaczona dla przedsiębiorstw, która obejmuje wszystkie funkcje warstwy standardowa, np. lepszą wydajność, obsługę większych obciążeń, odzyskiwanie po awarii, import/export i lepsze zabezpieczenia. Kontynuuj czytanie, aby dowiedzieć się więcej o dodatkowe funkcje warstwy Premium w pamięci podręcznej.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Lepszą wydajność w porównaniu do warstwy Standard lub Basic
**Lepsza wydajność przez Standard lub Basic warstwy.** Pamięci podręczne w warstwie Premium są wdrażane na sprzęcie, który mają szybsze procesory i zapewnia lepszą wydajność w porównaniu do warstwy podstawowa lub standardowa. Podręczna w warstwie Premium mają większą przepływnością i mniejszymi opóźnieniami. 

**Przepływność dla tej samej wielkości pamięci podręcznej jest większa w warstwie Premium w porównaniu do warstwy standardowa.** Na przykład, przepływność 53 GB P4 (Premium) pamięć podręczna jest 250 tys. żądań na sekundę w porównaniu do 150 KB C6 (standardowa).

Aby uzyskać więcej informacji na temat rozmiaru, przepływności i przepustowości w pamięciach podręcznych premium, zobacz [pamięci podręcznej Azure redis Cache — często zadawane pytania](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Stan trwały danych pamięci podręcznej Redis
Warstwa Premium umożliwia utrwalanie danych pamięci podręcznej na koncie usługi Azure Storage. W pamięci podręcznej Podstawowa/standardowa wszystkie dane są przechowywane tylko w pamięci. W przypadku podstawowej infrastruktury problemów może być możliwej utracie danych. Firma Microsoft zaleca używanie funkcji trwałości danych Redis w warstwie Premium, aby zwiększyć odporność na utratę danych. Pamięć podręczna systemu Azure dla usługi Redis zapewnia RDB i AOF (wkrótce), opcje w [trwałość Redis](https://redis.io/topics/persistence). 

Aby uzyskać instrukcje na temat konfigurowania trwałości, zobacz [Konfigurowanie trwałości dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Klaster Redis
Jeśli chcesz Tworzenie pamięci podręcznych większych niż 53 GB lub mają współdzielenie danych między wieloma węzłami Redis, możesz magazynu Redis można używać klastra, który jest dostępny w warstwie Premium. Każdy węzeł składa się z pary pamięci podręcznej podstawowy i węzeł repliki, zarządzane przez platformę Azure w celu zapewnienia wysokiej dostępności. 

**Klastrowanie redis zapewnia maksymalną skalowalność i przepływność.** Przepływność zwiększa liniowo, jak zwiększyć liczbę fragmentów (węzłów) w klastrze. Np. Jeśli tworzysz klaster P4 10 fragmentów, a następnie dostępna przepustowość jest 250 KB * 10 = 2,5 mln żądań na sekundę. Zobacz [pamięci podręcznej Azure redis Cache — często zadawane pytania](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) więcej szczegółowych informacji dotyczących rozmiaru, przepływności i przepustowości w pamięciach podręcznych premium.

Aby rozpocząć pracę z usługą klastrowania, zobacz [Konfigurowanie klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Większe bezpieczeństwo i izolacja
Pamięci podręczne utworzone w warstwie podstawowa lub standardowa są dostępne w publicznej sieci internet. Dostęp do pamięci podręcznej jest ograniczony, na podstawie klucza dostępu. W warstwie Premium można dodatkowo zagwarantować, że tylko klientom w określonej sieci mają dostęp do pamięci podręcznej. Pamięć podręczna systemu Azure można wdrożyć dla pamięci podręcznej Redis w [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Aby jeszcze bardziej ograniczyć dostęp do serwera Redis, można użyć wszystkich funkcji VNet, np. podsieci, zasad kontroli dostępu oraz innych funkcji.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importuj/eksportuj
Import/Export jest usługa Azure Cache dla operacji zarządzania danych Redis, która umożliwia importowanie danych do pamięci podręcznej Azure redis Cache lub eksportowanie danych z pamięci podręcznej Azure redis cache, importowanie i eksportowanie usługi Azure Cache dla migawki bazy danych Redis (RDB) z pamięci podręcznej — wersja premium, aby stronicowych obiektów blob na koncie usługi Azure Storage. Dzięki temu można dokonać migracji między różne usługi Azure Cache dla wystąpień pamięci podręcznej Redis ani wypełnienie pamięci podręcznej z danymi, przed użyciem.

Import może służyć do dostosowania zgodne pliki RDB pamięci podręcznej Redis z dowolnego serwera Redis, działające w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub wśród dostawców chmury, takich jak usługi Amazon Web Services i inne. Importowanie danych jest łatwe tworzenie pamięci podręcznej z wstępnie wypełniony danymi. Podczas procesu importowania pamięci podręcznej Redis Azure ładuje pliki RDB z usługi Azure storage do pamięci i wstawianie kluczy do pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w pamięci podręcznej Azure dla zgodne pliki RDB pamięci podręcznej Redis do pamięci podręcznej Redis. Ta funkcja służy do przenoszenia danych z jednej usługi Azure Cache dla wystąpienia usługi Redis do innego lub do innego serwera Redis. Podczas procesu eksportowania plik tymczasowy zostanie utworzony na maszynie Wirtualnej, który jest hostem usługi Azure Cache dla wystąpienia serwera Redis, a plik jest przekazywany do konta magazynu wyznaczonego. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie, plik tymczasowy zostanie usunięty.

Aby uzyskać więcej informacji, zobacz [sposób importowania danych do i eksportować dane z usługi Azure Cache dla usługi Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Uruchom ponownie
Warstwa premium umożliwia ponowne uruchomienie jednego lub więcej węzłów pamięci podręcznej na żądanie. Dzięki temu można testować swoją aplikację pod kątem odporności w przypadku awarii. Użytkownik może uruchomić następujących węzłów.

* Węzeł główny pamięci podręcznej
* Węzła pomocniczego w pamięci podręcznej
* Węzły podstawowych i pomocniczych w pamięci podręcznej
* Korzystając z klastrowaniem cache w warstwie premium, użytkownik może uruchomić podstawowego, pomocniczego lub oba węzły dla poszczególnych fragmentów w pamięci podręcznej

Aby uzyskać więcej informacji, zobacz [ponowny rozruch](cache-administration.md#reboot) i [ponowny rozruch — często zadawane pytania](cache-administration.md#reboot-faq).

>[!NOTE]
>Ponowne uruchomienie funkcji jest teraz włączone dla wszystkich pamięci podręcznej Azure redis Cache w warstwach.
>
>

## <a name="schedule-updates"></a>Zaplanuj aktualizacje
Funkcja zaplanowanych aktualizacji służy do wyznaczenia okno obsługi pamięci podręcznej. Jeśli okna obsługi jest określony, wszystkie aktualizacje serwera Redis są wprowadzane podczas tego okna. Aby wyznaczyć oknem konserwacji, wybierz żądane dni i określ konserwacji okna Uruchom godzinę każdego dnia. Należy pamiętać, że czas okna obsługi jest w formacie UTC. 

Aby uzyskać więcej informacji, zobacz [Lizacje](cache-administration.md#schedule-updates) i [Zaplanuj aktualizacje — często zadawane pytania](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Tylko usługa Redis serwera, które aktualizacje są wprowadzane podczas okna zaplanowanej konserwacji. Okna obsługi nie ma zastosowania do aktualizacji platformy Azure lub aktualizacje systemu operacyjnego maszyny Wirtualnej.
> 
> 

## <a name="geo-replication"></a>Replikacja geograficzna

**Replikacja geograficzna** udostępnia mechanizm do łączenia dwóch warstwa Premium usługi Azure Cache wystąpienia usługi Redis. Jeden pamięci podręcznej nazw jest wyznaczony jako podstawowy połączonej pamięci podręcznej, a drugi jako pomocniczy połączonej pamięci podręcznej. Pomocniczy połączonej pamięci podręcznej staje się tylko do odczytu, a dane zapisywane w głównej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej w różnych regionach platformy Azure.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować replikację geograficzną dla usługi Azure Cache dla pamięci podręcznej Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Aby możliwe było skalowanie warstwy premium
Aby możliwe było skalowanie warstwy premium, wystarczy wybrać jedną z warstwy premium w **zmianę warstwy cenowej** bloku. Możesz również skalować w pamięci podręcznej do warstwy premium przy użyciu programu PowerShell i interfejsu wiersza polecenia. Aby uzyskać instrukcje krok po kroku, zobacz [jak skalowanie pamięci podręcznej Azure dla usługi Redis](cache-how-to-scale.md) i [sposób automatyzowania operacji skalowania](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Kolejne kroki
Tworzenie pamięci podręcznej i Poznaj nowe funkcje warstwy premium.

* [Konfigurowanie trwałości dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-persistence.md)
* [Konfigurowanie obsługi sieci wirtualnej dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-vnet.md)
* [Konfigurowanie klastrowania dla usługi Azure Cache w warstwie Premium dla usługi Redis](cache-how-to-premium-clustering.md)
* [Jak do importowania danych do i eksportowanie danych z pamięci podręcznej Azure redis Cache](cache-how-to-import-export-data.md)
* [Administrowanie pamięć podręczna systemu Azure dla usługi Redis](cache-administration.md)

