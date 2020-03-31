---
title: Sprawdzanie błędów puli i węzłów — usługa Azure Batch
description: W tym artykule opisano operacje w tle, które mogą wystąpić, wraz z błędami, aby sprawdzić i jak ich uniknąć podczas tworzenia pul i węzłów.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472949"
---
# <a name="check-for-pool-and-node-errors"></a>Sprawdzanie błędów puli i węzłów

Podczas tworzenia i zarządzania pulami usługi Azure Batch niektóre operacje są natychmiast. Jednak niektóre operacje są asynchroniczne i działają w tle, co zajmuje kilka minut.

Wykrywanie awarii dla operacji, które odbywają się natychmiast jest proste, ponieważ wszelkie błędy są zwracane natychmiast przez interfejs API, interfejs wiersza polecenia lub interfejsu użytkownika.

W tym artykule opisano operacje w tle, które mogą wystąpić dla pul i węzłów puli. Określa, jak można wykryć i uniknąć awarii.

## <a name="pool-errors"></a>Błędy puli

### <a name="resize-timeout-or-failure"></a>Przeliczenie limitu czasu lub błąd rozmiaru

Podczas tworzenia nowej puli lub zmiany rozmiaru istniejącej puli, należy określić docelową liczbę węzłów.  Operacja tworzenia lub ponownego rozmiaru kończy się natychmiast, ale rzeczywista alokacja nowych węzłów lub usunięcie istniejących węzłów może potrwać kilka minut.  Należy określić limit czasu ponownego rozmiaru w interfejsie API [tworzenia](https://docs.microsoft.com/rest/api/batchservice/pool/add) lub [ponownego rozmiaru.](https://docs.microsoft.com/rest/api/batchservice/pool/resize) Jeśli usługa Batch nie może uzyskać docelowej liczby węzłów podczas okresu limitu czasu ponownego rozmiaru, pula przechodzi w stan ustalony i zgłasza błędy rozmiaru.

[Właściwość ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) dla najnowszej oceny wyświetla listę błędów, które wystąpiły.

Typowe przyczyny błędów ponownego rozmiaru obejmują:

- Limit czasu ponownego rozmiaru rozmiaru jest za krótki
  - W większości przypadków domyślny limit czasu 15 minut jest wystarczająco długi, aby węzły puli zostały przydzielone lub usunięte.
  - Jeśli przydzielasz dużą liczbę węzłów, zalecamy ustawienie limitu czasu rozmiaru do 30 minut. Na przykład podczas zmiany rozmiaru do więcej niż 1000 węzłów z obrazu portalu Azure Marketplace lub do ponad 300 węzłów z niestandardowego obrazu maszyny Wirtualnej.
- Niewystarczająca kwota podstawowa
  - Konto usługi Batch jest ograniczona w liczbie rdzeni, które można przydzielić we wszystkich pulach. Partia zatrzymuje przydzielanie węzłów po osiągnięciu tego przydziału. [Można zwiększyć](https://docs.microsoft.com/azure/batch/batch-quota-limit) przydział podstawowy, dzięki czemu usługa Batch może przydzielić więcej węzłów.
- Niewystarczające adresy IP podsieci, gdy [pula znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsieć sieci wirtualnej musi mieć wystarczającą liczbę nieprzypisanych adresów IP, aby przydzielić je do każdego żądanego węzła puli. W przeciwnym razie nie można utworzyć węzłów.
- Niewystarczające zasoby, gdy [pula znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - W tej samej subskrypcji co konto usługi Batch można utworzyć zasoby, takie jak moduły równoważenia obciążenia, publiczne wiadomości IP i sieciowe grupy zabezpieczeń. Sprawdź, czy przydziały subskrypcji są wystarczające dla tych zasobów.
- Duże pule z niestandardowymi obrazami maszyn wirtualnych
  - Duże pule, które używają niestandardowych obrazów maszyn wirtualnych może trwać dłużej, aby przydzielić i zmienić rozmiar limitów czasu może wystąpić.  Aby uzyskać zalecenia dotyczące limitów i konfiguracji, zobacz [Tworzenie puli z Galerią obrazów udostępnionych.](batch-sig-images.md)

### <a name="automatic-scaling-failures"></a>Automatyczne błędy skalowania

Można również ustawić usługę Azure Batch, aby automatycznie skalować liczbę węzłów w puli. Należy zdefiniować parametry [formuły automatycznego skalowania dla puli](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Usługa Batch używa formuły do okresowej oceny liczby węzłów w puli i ustawiania nowego numeru docelowego. Mogą wystąpić następujące typy problemów:

- Automatyczna ocena skalowania kończy się niepowodzeniem.
- Wynikowa operacja ponownego rozmiaru kończy się niepowodzeniem i przesunie się.
- Problem z formułą automatycznego skalowania prowadzi do niepoprawnych wartości docelowych węzła. Rozmiar albo działa lub przesunie się.

Można uzyskać informacje o ostatniej automatycznej oceny skalowania przy użyciu [właściwości autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) Ta właściwość raportuje czas oceny, wartości i wynik oraz wszelkie błędy wydajności.

Rozmiar [puli complete event](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) przechwytuje informacje o wszystkich ocenach.

### <a name="delete"></a>Usuń

Po usunięciu puli zawierającej węzły, najpierw batch usuwa węzły. Następnie usuwa sam obiekt puli. Usunięcie węzłów puli może potrwać kilka minut.

Partia ustawia [stan puli](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) do **usuwania** podczas procesu usuwania. Aplikacja wywołująca może wykryć, czy usuwanie puli trwa zbyt długo przy użyciu **stanu** i **stateTransitionTime** właściwości.

## <a name="pool-compute-node-errors"></a>Błędy węzła obliczeniowego puli

Nawet wtedy, gdy usługa Batch pomyślnie przydziela węzły w puli, różne problemy mogą powodować, że niektóre węzły są w złej kondycji i nie mogą uruchamiać zadań. Te węzły nadal ponoszą opłaty, dlatego ważne jest, aby wykryć problemy, aby uniknąć płacenia za węzły, których nie można użyć. Oprócz typowych błędów węzła, znajomość bieżącego [stanu zadania](/rest/api/batchservice/job/get#jobstate) jest przydatna do rozwiązywania problemów.

### <a name="start-task-failures"></a>Uruchamianie błędów zadań

Można określić opcjonalne [zadanie początkowe](/rest/api/batchservice/pool/add#starttask) dla puli. Podobnie jak w przypadku każdego zadania, można użyć wiersza polecenia i plików zasobów do pobrania z magazynu. Zadanie uruchamiania jest uruchamiane dla każdego węzła po jego uruchomieniu. Właściwość **waitForSuccess** określa, czy usługa Batch czeka, aż zadanie rozpoczęcia zakończy się pomyślnie, zanim zaplanuje wszelkie zadania do węzła.

Co zrobić, jeśli węzeł został skonfigurowany tak, aby czekał na pomyślne rozpoczęcie zadania, ale zadanie startowe nie powiedzie się? W takim przypadku węzeł nie będzie użyteczny, ale nadal będzie ponosić opłaty.

Błędy uruchamiania zadań można wykryć przy użyciu [wyników](/rest/api/batchservice/computenode/get#taskexecutionresult) i [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) właściwości najwyższego poziomu [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) właściwości węzła.

Zadanie uruchomienia nie powiodło się powoduje również, że usługa Batch ustawia [stan](/rest/api/batchservice/computenode/get#computenodestate) węzła na **uruchamianie,** jeśli **waitForSuccess** został ustawiony na **true**.

Podobnie jak w przypadku każdego zadania, może istnieć wiele przyczyn niepowodzenia zadania uruchamiania.  Aby rozwiązać problem, sprawdź stdout, stderr i wszelkie inne pliki dziennika specyficzne dla zadania.

Zadania startowe muszą być ponownie uruchomione, ponieważ jest możliwe, że zadanie startowe jest uruchamiane wiele razy w tym samym węźle; zadanie start jest uruchamiane, gdy węzeł zostanie ponownie zaimagedyz lub ponownie uruchomiony. W rzadkich przypadkach zadanie uruchamiania zostanie uruchomione po zdarzeniu spowodowanym ponownym uruchomieniem węzła, gdzie jeden z systemów operacyjnych lub efemerycznych dysków został ponownie zaimagedyzami, podczas gdy drugi nie był. Ponieważ zadania uruchamiania usługi Batch (podobnie jak wszystkie zadania wsadowe) są uruchamiane z dysku efemerycznego, zwykle nie jest to problem, ale w niektórych przypadkach, gdy zadanie uruchamiania instaluje aplikację na dysku systemu operacyjnego i przechowuje inne dane na dysku efemerycznym, może to spowodować problemy, ponieważ rzeczy są niezsynchronizowane. Odpowiednio chroń aplikację, jeśli używasz obu dysków.

### <a name="application-package-download-failure"></a>Niepowodzenie pobierania pakietu aplikacji

Można określić jeden lub więcej pakietów aplikacji dla puli. Usługa Batch pobiera określone pliki pakietu do każdego węzła i dekompresuje pliki po uruchomieniu węzła, ale przed zaplanowaniem zadań. Często używa się wiersza polecenia zadania startowego w połączeniu z pakietami aplikacji. Na przykład, aby skopiować pliki do innej lokalizacji lub uruchomić instalatora.

Właściwość [błędów węzła](/rest/api/batchservice/computenode/get#computenodeerror) zgłasza niepowodzenie pobierania i odkompresowywać pakiet aplikacji; stan węzła jest ustawiony na **bezużyteczny**.

### <a name="container-download-failure"></a>Błąd pobierania kontenera

Można określić co najmniej jedno odwołanie do kontenera w puli. Partia pobiera określone kontenery do każdego węzła. Właściwość [błędów węzła](/rest/api/batchservice/computenode/get#computenodeerror) zgłasza niepowodzenie pobrania kontenera i ustawia stan węzła **na bezużyteczny.**

### <a name="node-in-unusable-state"></a>Węzeł w stanie bezużytecznym

Usługa Azure Batch może ustawić [stan węzła](/rest/api/batchservice/computenode/get#computenodestate) **bezużyteczny z** wielu powodów. Ze stanem węzła ustawionym na **bezużyteczny,** zadania nie mogą być zaplanowane do węzła, ale nadal wiąże się z opłatami.

Węzły w stanie **bezużytecznym,** ale bez [błędów](/rest/api/batchservice/computenode/get#computenodeerror) oznacza, że usługa Batch nie może komunikować się z maszyną wirtualną. W takim przypadku Batch zawsze próbuje odzyskać maszynę wirtualną. Usługa Batch nie będzie automatycznie próbowała odzyskać maszyn wirtualnych, których nie udało się zainstalować pakietów lub kontenerów aplikacji, nawet jeśli ich stan nie nadaje się do **bezużytecznia.**

Jeśli usługa Batch może określić przyczynę, właściwość [błędów węzła](/rest/api/batchservice/computenode/get#computenodeerror) zgłasza ją.

Dodatkowe przykłady przyczyn **bezużytecznych** węzłów obejmują:

- Niestandardowy obraz maszyny Wirtualnej jest nieprawidłowy. Na przykład obraz, który nie jest odpowiednio przygotowany.

- Maszyna wirtualna jest przenoszona z powodu awarii infrastruktury lub uaktualnienia niskiego poziomu. Partia odzyskuje węzeł.

- Obraz maszyny Wirtualnej został wdrożony na sprzęcie, który go nie obsługuje. Na przykład próbuje uruchomić obraz HPC CentOS na [maszynie wirtualnej Standard_D1_v2.](../virtual-machines/dv2-dsv2-series.md)

- Maszyny wirtualne znajdują się w [sieci wirtualnej platformy Azure,](batch-virtual-network.md)a ruch został zablokowany do kluczowych portów.

- Maszyny wirtualne znajdują się w sieci wirtualnej, ale ruch wychodzący do magazynu platformy Azure jest zablokowany.

- Maszyny wirtualne znajdują się w sieci wirtualnej z konfiguracją DNS klienta, a serwer DNS nie może rozpoznać magazynu platformy Azure.

### <a name="node-agent-log-files"></a>Pliki dziennika agenta węzła

Proces agenta usługi Batch, który działa w każdym węźle puli może dostarczyć pliki dziennika, które mogą być przydatne, jeśli trzeba skontaktować się z pomocą techniczną w sprawie problemu węzła puli. Pliki dziennika dla węzła można przekazać za pośrednictwem portalu Azure, Eksploratora wsadowego lub [interfejsu API.](/rest/api/batchservice/computenode/uploadbatchservicelogs) Warto przekazać i zapisać pliki dziennika. Następnie można usunąć węzeł lub pulę, aby zaoszczędzić koszt uruchomionych węzłów.

### <a name="node-disk-full"></a>Pełny dysk węzła

Tymczasowy dysk dla węzła puli Maszyny Wirtualnej jest używany przez partię dla plików zadań, plików zadań i plików udostępnionych.

- Pliki pakietów aplikacji
- Pliki zasobów zadań
- Pliki specyficzne dla aplikacji pobrane do jednego z folderów wsadowych
- Pliki stdout i stderr dla każdego wykonania aplikacji zadania
- Pliki wyjściowe specyficzne dla aplikacji

Niektóre z tych plików są zapisywane tylko raz podczas tworzenia węzłów puli, takich jak pakiety aplikacji puli lub pliki zasobów uruchamiania puli. Nawet jeśli tylko raz napisane podczas tworzenia węzła, jeśli te pliki są zbyt duże, mogą wypełnić dysk tymczasowy.

Inne pliki są zapisywane dla każdego zadania, które jest uruchamiane w węźle, takich jak stdout i stderr. Jeśli duża liczba zadań uruchamianych w tym samym węźle i/lub pliki zadań są zbyt duże, mogą wypełnić dysk tymczasowy.

Rozmiar dysku tymczasowego zależy od rozmiaru maszyny Wirtualnej. Jedną z kwestii podczas wybierania rozmiaru maszyny Wirtualnej jest zapewnienie, że dysk tymczasowy ma wystarczająco dużo miejsca.

- W witrynie Azure portal podczas dodawania puli można wyświetlić pełną listę rozmiarów maszyn wirtualnych i kolumnę "Rozmiar dysku zasobów".
- Artykuły opisujące wszystkie rozmiary maszyn wirtualnych mają tabele z kolumną "Magazyn tymczasowy"; na przykład [rozmiary zoptymalizowanych pod kątem obliczeń maszyn wirtualnych](/azure/virtual-machines/windows/sizes-compute)

W przypadku plików zapisanych przez każde zadanie można określić czas przechowywania dla każdego zadania, który określa, jak długo pliki zadań są przechowywane przed automatycznym czyszczeniem. Czas przechowywania można zmniejszyć, aby obniżyć wymagania dotyczące magazynu.


Jeśli na dysku tymczasowym zabraknie miejsca (lub jest bardzo blisko wyczerpania miejsca), węzeł zostanie przesunięty do stanu [bezużytecznego](/rest/api/batchservice/computenode/get#computenodestate) i zostanie zgłoszony błąd węzła z informacją, że dysk jest zapełniony.

### <a name="what-to-do-when-a-disk-is-full"></a>Co zrobić, gdy dysk jest zapełniony

Określ, dlaczego dysk jest pełny: Jeśli nie masz pewności, co zajmuje miejsce w węźle, zaleca się zdalne do węzła i zbadać ręcznie, gdzie miejsce poszło. Można również użyć [interfejsu API plików listy wsadowej](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) do sprawdzania plików w folderach zarządzanych usługi Batch (na przykład wyjść zadań). Należy zauważyć, że ten interfejs API zawiera tylko pliki w katalogach zarządzanych partii i jeśli zadania utworzone pliki w innym miejscu nie będą widoczne.

Upewnij się, że wszystkie potrzebne dane zostały pobrane z węzła lub przekazane do magazynu trwałe. Wszystkie łagodzenie problemu pełnego dysku obejmują usunięcie danych w celu zwolnienia miejsca.

### <a name="recovering-the-node"></a>Odzyskiwanie węzła

1. Jeśli pula jest pulą [C.loudServiceConfiguration,](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) można ponownie obrazować węzeł za pośrednictwem [interfejsu API ponownego obrazu usługi Batch](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Spowoduje to wyczyszcenie całego dysku. Ponowne obraz nie jest obecnie obsługiwany dla [pul VirtualMachineConfiguration.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)

2. Jeśli pula jest [konfiguracją VirtualMachineConfiguration,](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)można usunąć węzeł z puli za pomocą [interfejsu API usuwania węzłów](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes). Następnie można ponownie rozwijać pulę, aby zastąpić zły węzeł świeżym.

3.  Usuń stare ukończone zadania lub stare ukończone zadania, których dane zadań nadal są w węzłach. Aby uzyskać wskazówkę, jakie zadania/zadania dane znajdują się w węzłach, które można sprawdzić w [kolekcji RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) w węźle lub [w plikach w węźle](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). Usunięcie zadania spowoduje usunięcie wszystkich zadań w zadaniu, a usunięcie zadań w zadaniu spowoduje wyzwolenie danych w katalogach zadań w węźle do usunięcia, zwalniając w ten sposób miejsce. Po zwolnieniu wystarczającej ilości miejsca, uruchom ponownie węzeł i powinien przenieść się ze stanu "Bezużyteczny" i ponownie w "Bezczynny".

## <a name="next-steps"></a>Następne kroki

Sprawdź, czy aplikacja została ustawiona w celu zaimplementowania kompleksowego sprawdzania błędów, szczególnie w przypadku operacji asynchronicznych. Może być bardzo ważne, aby szybko wykryć i zdiagnozować problemy.
