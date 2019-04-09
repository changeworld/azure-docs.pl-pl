---
title: Zarządzanie infrastrukturą magazynu dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Zarządzaj infrastrukturą magazynu dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 416d75b254d0fbe14a0b39e5ae77d09a48e548f6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271292"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Zarządzanie infrastrukturą magazynu dla usługi Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W tym artykule opisano kondycję i stan operacyjny zasobów infrastruktury magazynu usługi Azure Stack. Zasoby te obejmują dyski i woluminy. Informacje przedstawione w tym temacie może być nieocenione, gdy rozwiązanie różnych problemów, takich jak dysk nie można dodać do puli.

## <a name="understand-drives-and-volumes"></a>Dyski i woluminy

### <a name="drives"></a>Dyski

Obsługiwane przez oprogramowanie systemu Windows Server, usługi Azure Stack definiuje możliwości magazynu przy użyciu kombinacji bezpośrednimi miejscami do magazynowania (S2D) i Windows Server Failover Clustering umożliwia wydajne, skalowalne i usługi storage odporne na błędy.

Partnerzy systemu usługi Azure Stack, zintegrowane oferują wiele zmian rozwiązania, łącznie z szerokiej gamy elastyczność pamięci masowej. Obecnie można wybrać kombinację trzech typów dysku: NVMe (pamięci trwałej Express), SATA/sygnatury dostępu Współdzielonego dysku SSD (SSD), dysk twardy (dysk twardy).

Bezpośrednimi miejscami do magazynowania funkcje pamięci podręcznej w celu zmaksymalizowania wydajności magazynu. W usłudze Azure Stack urządzenia za pomocą jednego lub wielu typów dysków, bezpośrednimi miejscami do magazynowania automatycznie używać wszystkie dyski "najszybszej" (NVMe &gt; SSD &gt; HDD) typ do buforowania. Pozostałe dyski są używane do pojemności. Dyski można pogrupować w "samych pamięci flash" lub "hybrydowe" wdrożenie:

![Infrastruktura magazynu usługi Azure Stack](media/azure-stack-storage-infrastructure-overview/image1.png)

Samych pamięci flash w celu wdrożenia, aby zmaksymalizować wydajność magazynowania, a nie dołączaj obrotowych dysków twardych (HDD).

![Infrastruktura magazynu usługi Azure Stack](media/azure-stack-storage-infrastructure-overview/image2.png)

Wdrożeń hybrydowych Staraj się, aby zrównoważyć wydajność i pojemność lub zwiększyć pojemność i dołączaj obrotowych dysków twardych (HDD).

Zachowanie pamięci podręcznej jest określana automatycznie w oparciu o typy dysków buforowanych dla. Gdy buforowanie na dyskach półprzewodnikowych (takich jak buforowanie dyski SSD NVMe), tylko operacje zapisu są buforowane. Zmniejsza zużycie dyski pojemności, zmniejszenie ruchu związanego z zbiorczą do pojemności dysków i rozszerzanie ich istnienia. W międzyczasie ponieważ odczyty nie znaczącego wpływu na okres flash i dysków SSD powszechnie oferuje małe opóźnienia odczytu, operacje odczytu nie są buforowane. Podczas buforowania z stacje dysków twardych (takich jak dyski SSD buforowanie HDD), operacje odczytu i zapisu są buforowane, zapewnienie flash podobne opóźnienie (często ~ 10 x lepsze) dla obu.

![Infrastruktura magazynu usługi Azure Stack](media/azure-stack-storage-infrastructure-overview/image3.png)

W przypadku dostępnych konfiguracji magazynu można sprawdzić partnera usługi Azure Stack OEM (https://azure.microsoft.com/overview/azure-stack/partners/) szczegółowe specyfikacji.

> [!Note]  
> Urządzenie z usługą Azure Stack mogą być dostarczane w ramach wdrożenia hybrydowego z stacje zarówno HDD i SSD (lub NVMe). Ale dyski typu szybsze będzie służyć jako dyski pamięci podręcznej, a wszystkie pozostałe dyski będzie służyć jako dyski o pojemności jako pula. Dane dzierżawy (obiekty BLOB, tabel, kolejek i dysków) zostają umieszczone na dyskach pojemności. Dlatego gwarantowane aprowizacji dysków w warstwie premium lub wybierając konto magazynu premium storage typu nie oznaczać, że obiekty do przydzielenia na dyski SSD lub dyski NVMe i uzyskać większą wydajność.

### <a name="volumes"></a>Woluminy

*Usługi storage* partycje dostępnego magazynu na oddzielnych woluminach, które są przydzielane do przechowywania danych systemu i dzierżawy. Woluminy łączyć dysków w puli magazynów, aby wprowadzić odporności na uszkodzenia, skalowalności i wydajności zalety bezpośrednimi miejscami do magazynowania.

![Infrastruktura magazynu usługi Azure Stack](media/azure-stack-storage-infrastructure-overview/image4.png)

Istnieją trzy typy woluminów utworzonych w puli magazynu usługi Azure Stack:

-   Infrastruktura: pliki hosta używane przez maszyny wirtualne infrastruktury Azure Stack i podstawowych usług.

-   Tymczasowej maszyny Wirtualnej: host tymczasowe dyski dołączone do maszyn wirtualnych dzierżawy, a dane są przechowywane w tych dysków.

-   Obiektu Store: dane dzierżawy hosta obsługi obiektów blob, tabel, kolejek i dysków maszyny Wirtualnej.

We wdrożeniu wieloma węzłami zobaczysz trzy woluminy infrastruktury, gdy liczba woluminy tymczasowej maszyny Wirtualnej i woluminy Store obiektu jest równa liczbie węzłów w ramach wdrożenia usługi Azure Stack:

-   We wdrożeniu z czterema węzłami istnieją cztery równe woluminy tymczasowej maszyny Wirtualnej i cztery równe woluminy Store obiektu.

-   Po dodaniu nowego węzła do klastra będzie istnieć nowego woluminu dla obu typów można utworzyć.

-   Liczba woluminów jest identyczny, nawet jeśli węzeł nieprawidłowego działania lub zostanie usunięta.

-   Jeśli używasz usługi Azure Stack Developer Kit, istnieje pojedynczy wolumin przy użyciu wielu udziałów.

Woluminy w bezpośrednimi miejscami do magazynowania zapewniają odporność, aby zapewnić ochronę przed problemów sprzętowych, takich jak awarie dysku lub serwera i Włącz funkcję ciągłej dostępności w całej konserwacja serwera, takich jak aktualizacje oprogramowania. Wdrożenie usługi Azure Stack używa dublowania trzystopniowego aby zagwarantować odporność danych. Trzy kopie danych dzierżawy są zapisywane na różnych serwerach, w którym trafili w pamięci podręcznej:

![Infrastruktura magazynu usługi Azure Stack](media/azure-stack-storage-infrastructure-overview/image5.png)

Dublowanie zapewnia odporność na uszkodzenia przez przechowywanie wielu kopii wszystkich danych. Jak rozkładane i umieścić tych danych jest trywialny (zobacz ten blog, aby dowiedzieć się więcej), ale jest to absolutnie wartość true, załóżmy, że wszystkie dane przechowywane przy użyciu funkcji dublowania jest zapisana w całości, wiele razy. Każda kopia są zapisywane w różnych fizycznych sprzętu (różne dyski na różnych serwerach), które są uznawane za niezależnie zakończyć się niepowodzeniem. Dublowania trzystopniowego bezpiecznie może tolerować co najmniej dwa problemy sprzętowe (dysku lub serwera) w danym momencie. Na przykład w przypadku ponownego uruchomienia jednego serwera, gdy nagle innego dysku lub serwera nie powiedzie się, wszystkie dane pozostają bezpieczne i jest stale dostępna.

## <a name="volume-states"></a>Stany woluminu

Aby dowiedzieć się, jakie są w stanie volumess, użyj następujących poleceń programu PowerShell:

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_nazwa - StorageSubSystem \$podsystemu\_nazwa | Select-Object VolumeLabel progowej, OperationalStatus, RepairStatus, opis, działania, TotalCapacityGB, RemainingCapacityGB

Oto przykład przedstawiający odłączony wolumin i negatywny wpływ na dostępność niekompletne woluminu danych wyjściowych:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Nieznana | Odłączone |
| ObjStore_2 | Ostrzeżenie | {Negatywny wpływ na dostępność, niepełne} |

W poniższych sekcjach wymieniono kondycji i stany operacyjne.

### <a name="volume-health-state-healthy"></a>Stan kondycji woluminu: W dobrej kondycji

| Stan operacyjny | Opis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Wolumin jest w dobrej kondycji. |
| Suboptymalny | Dane nie są równomiernie zapisywane na dyskach.<br> <br>**Akcja:** Skontaktuj się z pomocą techniczną, aby zoptymalizować użycia dysków w puli magazynów. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. Może być konieczne przywrócenie z kopii zapasowej, po przywróceniu połączenia nie powiodło się. |


### <a name="volume-health-state-warning"></a>Stan kondycji woluminu: Ostrzeżenie

Gdy wolumin jest ostrzegawczy stan kondycji, oznacza to, że co najmniej jeden kopie danych są niedostępne, ale usługa Azure Stack, nadal można odczytywać co najmniej jedną kopię danych.

| Stan operacyjny | Opis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| W usłudze | Usługa Azure Stack jest Naprawianie woluminu, takie jak po dodaniu lub usunięciu dysku. Po zakończeniu naprawy wolumin należy powrócić do stanu kondycji OK.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć naprawiania woluminu, a potem sprawdź stan. |
| Niekompletne | Odporność woluminu jest ograniczone, ponieważ jeden lub więcej dysków nie powiodło się lub Brak. Jednak brakujące dyski zawierają aktualne kopie danych.<br> <br>**Akcja:** Połącz ponownie wszystkie brakujące dyski, Zastąp wszystkie dyski nie powiodło się i Przełącz wszystkie serwery, które są w trybie offline do trybu online. |
| Obniżono poziom | Odporność woluminu jest ograniczone, ponieważ jeden lub więcej dysków nie powiodło się lub nie istnieje, a nieaktualne kopie danych na tych dyskach.<br> <br>**Akcja:** Połącz ponownie wszystkie brakujące dyski, Zastąp wszystkie dyski nie powiodło się i Przełącz wszystkie serwery, które są w trybie offline do trybu online. |

 

### <a name="volume-health-state-unhealthy"></a>Stan kondycji woluminu: W złej kondycji

Gdy wolumin jest w stanie złej kondycji kondycji, niektórych lub wszystkich danych na woluminie jest obecnie niedostępna.

| Stan operacyjny | Opis |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nie nadmiarowości | Wolumin ma utracone dane ponieważ zbyt wiele dysków nie powiodło się.<br> <br>**Akcja:** Skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Stan kondycji woluminu: Nieznana

Wolumin również może być w stanie kondycji nieznany, jeśli dysk wirtualny stają się odłączony.

| Stan operacyjny | Opis |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Odłączone | Awaria urządzenia magazynującego wystąpił, co może spowodować woluminu staną się niedostępne. Niektóre dane mogą zostać utracone.<br> <br>**Akcja:** <br>1. Sprawdź fizyczne i sieciowe łączności urządzeń pamięci masowej.<br>2. Jeśli wszystkie urządzenia łączą się poprawnie, skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. Może być konieczne przywrócenie z kopii zapasowej, po przywróceniu połączenia nie powiodło się. |

## <a name="drive-states"></a>Stany dysków

Aby monitorować stan dysków, należy użyć następujących poleceń programu PowerShell:

 

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

, Numer seryjny

 

Get-AzsDrive - ScaleUnit \$scaleunit\_nazwa - StorageSubSystem \$podsystemu\_nazwa | StorageNode Select-Object, PhysicalLocation, progowej, OperationalStatus, opis, działania, użycia, CanPool, CannotPoolReason, numer seryjny, modelu, typ nośnika, CapacityGB

Stany kondycji, który dysk może znajdować się w można znaleźć w poniższych sekcjach.

### <a name="drive-health-state-healthy"></a>Stan kondycji dysku: W dobrej kondycji

| Stan operacyjny | Opis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Wolumin jest w dobrej kondycji. |
| W usłudze | Dysk wykonuje niektóre operacje celów wewnętrznych. Po zakończeniu działania dysk powinien zwrócić do stanu kondycji OK. |

### <a name="drive-health-state-healthy"></a>Stan kondycji dysku: W dobrej kondycji

Dysku w mogą Państwo ostrzeżenie odczytywać i zapisywać dane pomyślnie, ale wystąpił problem.

| Stan operacyjny | Opis |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Utrata łączności | Połączenie zostało utracone na dysku.<br> <br>**Akcja:** Przełącz wszystkie serwery z powrotem do trybu online. Jeśli to nie rozwiąże go, ponownie podłącz dysk. Jeśli to utrzymuje pojawia się, Zastąp dysk, aby zapewnić pełną odporność. |
| Przewidywanie awarii | Awaria dysku przewiduje się wkrótce.<br> <br>**Akcja:** Wymiana stacji dysków tak szybko, jak to możliwe, aby zapewnić pełną odporność. |
| Błąd We/Wy | Wystąpił błąd tymczasowy dostęp do dysku.<br> <br>**Akcja:** Jeśli to utrzymuje pojawia się, Zastąp dysk, aby zapewnić pełną odporność. |
| Błąd przejściowy | Wystąpił błąd tymczasowy z dysku. Zwykle oznacza to, dysku nie odpowiedział, ale może również oznaczać, że że partycja niewłaściwie został usunięty z napędu bezpośrednich miejsc do magazynowania magazynu. <br> <br>**Akcja:** Jeśli to utrzymuje pojawia się, Zastąp dysk, aby zapewnić pełną odporność. |
| Nieprawidłowy czas oczekiwania | Dysk jest czasami nie odpowiada i jest wyświetlane oznaki awarii.<br> <br>**Akcja:** Jeśli to utrzymuje pojawia się, Zastąp dysk, aby zapewnić pełną odporność. |
| Usuwanie z puli | Usługa Azure Stack jest w trakcie usuwania dysku z puli magazynu.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć usuwanie dysku, a potem sprawdź stan.<br>Jeżeli pozostanie w stanie, skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. |
|  |  |
| Począwszy od trybu konserwacji | Usługa Azure Stack jest w trakcie umieszczenie dysk w trybie konserwacji. Jest to sytuacja tymczasowa — dysk wkrótce powinien być w stanie trybu konserwacji w.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć proces, a potem sprawdź stan. |
| W trybie konserwacji | Dysk jest w trybie konserwacji, zatrzymaniu operacji odczytu i zapisu z dysku. Zwykle oznacza to, że usługi Azure Stack zadania takie jak PNU lub FRU działa dysku. Jednak administrator może również umieścić dysk w trybie konserwacji.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack na zakończenie zadania administracji, a potem sprawdzić stan.<br>Jeżeli pozostanie w stanie, skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. |
|  |  |
| Zatrzymywanie trybu konserwacji | Usługa Azure Stack jest w trakcie przeniesienie dysku do trybu online. Jest to sytuacja tymczasowa — dysk powinien wkrótce dostępne w inny stan - najlepiej działa prawidłowo.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć proces, a potem sprawdź stan. |

 

### <a name="drive-health-state-unhealthy"></a>Stan kondycji dysku: W złej kondycji

Dysku w stan złej kondycji nie może obecnie zapisywana lub dostępne.

| Stan operacyjny | Opis |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Podziel | Dysk ma zostały oddzielone od puli.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. Jeśli musisz użyć tego dysku, Usuń dysk z systemu, upewnij się, że nie ma żadnych przydatne danych na dysku, usunięcia danych z dysku, a następnie odłącz i ponownie podłącz dysk. |
| Nie można jej użyć | Dysk fizyczny jest objęte kwarantanną, ponieważ nie jest obsługiwany przez dostawcę rozwiązania. Obsługiwane są tylko te dyski, zostaną zatwierdzone dla rozwiązania, które mają odpowiedni dysk oprogramowania układowego.<br> <br>**Akcja:** Zamień dysku na dysk zawierający zatwierdzonych producenta i numeru modelu rozwiązania. |
| Nieodświeżone metadane | Dysk zastępczy był wcześniej używany i może zawierać dane z systemu nieznany magazynu. Dysk jest objęte kwarantanną.        <br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. Jeśli musisz użyć tego dysku, Usuń dysk z systemu, upewnij się, że nie ma żadnych przydatne danych na dysku, usunięcia danych z dysku, a następnie odłącz i ponownie podłącz dysk. |
| Nierozpoznane metadane | Nierozpoznane metadane na dysku, co zwykle oznacza, że na dysku jest metadanych z inną pulę na nim.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. Jeśli musisz użyć tego dysku, Usuń dysk z systemu, upewnij się, że nie ma żadnych przydatne danych na dysku, usunięcia danych z dysku, a następnie odłącz i ponownie podłącz dysk. |
| Uszkodzony nośnik | Dysk nie powiodło się i nie będą już używane przez miejsca do magazynowania.<br> <br>**Akcja:** Wymiana stacji dysków tak szybko, jak to możliwe, aby zapewnić pełną odporność. |
| Awaria sprzętowa urządzenia | Wystąpił błąd sprzętowy na tym dysku. <br> <br>**Akcja:** Wymiana stacji dysków tak szybko, jak to możliwe, aby zapewnić pełną odporność. |
| Aktualizacja oprogramowania układowego | Usługa Azure Stack jest aktualizowanie oprogramowania układowego na dysku. Jest to stan tymczasowy, który zazwyczaj trwa on krócej niż minutę i w którym czasu innych dysków w puli, zapewnić obsługę wszystkich operacji odczytu i zapisu.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack ukończyć aktualizację, a potem sprawdź stan. |
| Uruchamianie | Dysk jest przygotowywanie do wykonania operacji. Powinna to być tymczasowa — po wykonaniu tych czynności dysku należy przejść do innego stanu operacyjnego.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć operację, a potem sprawdź stan. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Ze względu na dysku puli

Niektórych dysków nie są gotowe do przeniesienia w puli magazynu usługi Azure Stack. Znajdziesz się, dlaczego dysk nie kwalifikuje się buforowanie, analizując CannotPoolReason własności dysku. W poniższej tabeli przedstawiono nieco bardziej szczegółowo w każdym z powodów.

| Przyczyna | Opis |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sprzęt nie jest zgodne | Dysk nie znajduje się na liście modeli zatwierdzonego magazynu określony za pomocą usługi kondycji.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. |
| Oprogramowanie układowe nie są zgodne | Oprogramowanie układowe na fizycznym dysku nie ma na liście zatwierdzonych oprogramowania układowego za pomocą usługi kondycji.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. |
| Używane przez klaster | Dysk jest obecnie używany przez klaster trybu Failover.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. |
| Nośnik wymienny | Dysk jest klasyfikowana jako dysk wymienny. <br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. |
| Zła kondycja | Dysk nie jest w dobrej kondycji i może być konieczne, można zastąpić.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. |
| Niewystarczającej pojemności | Istnieją partycje zajmowały ilość wolnego miejsca na dysku.<br> <br>**Akcja:** Wymiana stacji dysków przy użyciu nowego dysku. Jeśli musisz użyć tego dysku, Usuń dysk z systemu, upewnij się, że nie ma żadnych przydatne danych na dysku, usunięcia danych z dysku, a następnie odłącz i ponownie podłącz dysk. |
| Weryfikacja w toku | Usługa kondycji jest sprawdzanie, jeśli dysk lub oprogramowania układowego na dysku jest zatwierdzona do użycia.<br> <br>**Akcja:** Poczekaj, aż usługi Azure Stack zakończyć proces, a potem sprawdź stan. |
| Sprawdzenie poprawności nie powiodło się | Usługa kondycji nie można sprawdzić Jeśli dysk lub oprogramowania układowego na dysku jest zatwierdzona do użycia.<br> <br>**Akcja:** Skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. |
| Offline | Dysk jest w trybie offline. <br> <br>**Akcja:** Skontaktuj się z pomocą techniczną. Przed rozpoczęciem proces zbierania plików dziennika przy użyciu wskazówek z https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Następny krok

[Zarządzanie pojemnością magazynu](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 