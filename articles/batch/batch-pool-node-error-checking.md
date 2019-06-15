---
title: Sprawdź błędy puli i węzła — Azure Batch
description: Błędy do wyszukania oraz sposoby unikania ich podczas tworzenia pul i węzłów
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 05/28/2019
ms.topic: conceptual
ms.openlocfilehash: b0a9d04fccce7ccbacb700f7af5126c6ae05140a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357758"
---
# <a name="check-for-pool-and-node-errors"></a>Sprawdź błędy puli i węzła

Tworzenie i Zarządzanie pulami usługi Azure Batch, niektóre operacje warstw natychmiast. Jednak niektóre operacje są asynchroniczne i uruchamiania w tle. One może potrwać kilka minut.

Wykrywanie błędów dla operacji, które zaczynają obowiązywać natychmiast jest bardzo proste, ponieważ wszystkie błędy są natychmiast zwracane przez interfejs API, interfejs wiersza polecenia lub interfejsu użytkownika.

W tym artykule opisano operacje w tle, które mogą wystąpić dla pul i węzłów puli. Określa, jak wykrywać i uniknąć awarii.

## <a name="pool-errors"></a>Błędy puli

### <a name="resize-timeout-or-failure"></a>Limit czasu zmiany rozmiaru lub niepowodzenia

Podczas tworzenia nowej puli, lub zmienianiu rozmiaru istniejącej puli, możesz określić docelowej liczby węzłów.  Operacja kończy się natychmiast, ale rzeczywisty przydział nowych węzłów i usuwanie istniejących węzłów może potrwać kilka minut.  Określ limit czasu zmiany rozmiaru w [tworzenie](https://docs.microsoft.com/rest/api/batchservice/pool/add) lub [rozmiar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) interfejsu API. Jeśli partii nie może uzyskać docelowej liczby węzłów w trakcie okresu czasu zmiany rozmiaru, zatrzymuje operację. Puli przechodzi w stan stały, i raporty, Zmień rozmiar błędy.

[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) właściwość w najnowszej wersji ewaluacyjnej raporty z limitem czasu zmiany rozmiaru i wyświetla wszystkie błędy, które wystąpiły.

Typowe przyczyny przekroczenia limitu czasu zmiany rozmiaru, obejmują:

- Limit czasu zmiany rozmiaru jest za krótkie
  - W większości przypadków domyślna wartość limitu czasu 15 minut jest wystarczająco długo, aby węzły puli mają być przydzielane lub usunięte.
  - Jeśli alokowaniu dużą liczbę węzłów, zaleca się ustawienie limitu czasu zmiany rozmiaru do 30 minut. Na przykład podczas możesz teraz zmiany rozmiaru do więcej niż 1000 węzłów z obrazu w portalu Azure Marketplace lub do ponad 300 węzłów za pomocą niestandardowego obrazu maszyny Wirtualnej.
- Limit przydziału rdzeni niewystarczające
  - Konto usługi Batch ma ograniczoną liczbę rdzeni, które można przydzielić we wszystkich pul. Batch zatrzymuje się przydzielanie węzłów, po przekroczeniu tego limitu przydziału. Możesz [może zwiększyć](https://docs.microsoft.com/azure/batch/batch-quota-limit) limit przydziału rdzeni, dzięki czemu tej partii można przydzielić więcej węzłów.
- Za mało podsieci adresów IP podczas [puli znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsieci sieci wirtualnej musi mieć wystarczającą liczbę nieprzypisanych adresów IP, aby przydzielić do każdego węzła w żądanej puli. W przeciwnym razie nie można utworzyć węzłów.
- Za mało zasobów po [puli znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Zasoby, takie jak moduły równoważenia obciążenia, publiczne adresy IP i sieciowe grupy zabezpieczeń można utworzyć w tej samej subskrypcji co konto usługi Batch. Sprawdź, czy limity przydziału subskrypcji są wystarczające dla tych zasobów.
- Duże pule z niestandardowych obrazów maszyn wirtualnych
  - Pule dużych, korzystających z niestandardowych obrazów maszyn wirtualnych może trwać dłużej można przydzielić i zmienianie rozmiaru przekroczeń limitu czasu może wystąpić.  Zobacz [użyć obrazu niestandardowego do tworzenia puli maszyn wirtualnych](https://docs.microsoft.com/azure/batch/batch-custom-images) uzyskać zalecenia dotyczące limitów i konfiguracji.

### <a name="automatic-scaling-failures"></a>Automatyczne skalowanie błędów

Można również ustawić usługi Azure Batch do automatycznego dowolnego skalowania liczby węzłów w puli. Zdefiniuj parametry dla [formułę dla puli automatycznego skalowania](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Usługa Batch używa formuły do okresowo ocenia liczby węzłów w puli i ustaw nowy numer docelowej. Mogą wystąpić następujące problemy:

- Automatyczne skalowanie oceny zakończy się niepowodzeniem.
- Wynikowy operacji zmiany rozmiaru zakończy się niepowodzeniem i limitu czasu.
- Problem z formułę skalowania automatycznego prowadzi do węzła niepoprawne wartości docelowych. Zmiana rozmiaru działa lub przekroczeniem limitu czasu.

Można uzyskać informacji na temat ostatnia ocena skalowania automatycznego za pomocą [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) właściwości. Ta właściwość raporty czas oceny, wartości i wynik oraz wszelkie błędy wydajności.

[Puli rozmiar zdarzenie ukończenia](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) znajdują się informacje dotyczące wszystkich ocen.

### <a name="delete"></a>Usuwanie

Po usunięciu puli, która zawiera węzły pierwszej partii usuwa węzłów. Następnie usuwa sam obiekt puli. Może upłynąć kilka minut, aż węzły puli do usunięcia.

Batch ustawia [puli stanu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) do **usuwanie** podczas procesu usuwania. Aplikacja wywołująca może wykryć, jeśli usunięcie puli jest zbyt długiego czasu za pomocą **stanu** i **stateTransitionTime** właściwości.

## <a name="pool-compute-node-errors"></a>Błędy węzłów obliczeniowych w puli

Nawet wtedy, gdy usługi Batch są przydzielane pomyślnie węzłów w puli, może spowodować różne problemy z niektóre węzły w złej kondycji i korzystanie z niej. Te węzły są naliczane opłaty. Jest ważne, aby wykrywać problemy, aby nie płacić węzłów uniemożliwiającym jego używanie.

### <a name="start-task-failure"></a>Niepowodzenie zadania uruchamiania

Możesz chcieć określić opcjonalny [zadanie podrzędne uruchamiania](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) dla puli. Podobnie jak w przypadku dowolnego zadania podrzędnego, można użyć wiersza polecenia i pliki zasobów, można pobrać z magazynu. Zadanie podrzędne uruchamiania jest uruchamiane dla każdego węzła, po została uruchomiona. **WaitForSuccess** właściwość określa, czy usługi Batch oczekuje na zadanie uruchamiania zakończy się pomyślnie przed jego harmonogramy wszystkich zadań do węzła.

Co zrobić, jeśli skonfigurowano węzeł, który ma czekać na ukończenie zadania pomyślne uruchomienie, ale rozpoczęcia zadań kończy się niepowodzeniem? W takim przypadku węzła nie nadaje się, ale nadal spowoduje naliczenie opłat.

Można wykryć niepowodzeń zadań uruchamiania, za pomocą [wynik](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) i [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) właściwości najwyższego poziomu [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) właściwość węzła.

Zadanie uruchamiania nie powiodło się również powoduje, że usługi Batch ustawić węzeł [stanu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) do **starttaskfailed** Jeśli należy ustawić **waitForSuccess** do **true**.

Podobnie jak w przypadku dowolnego zadania podrzędnego, może wystąpić wiele przyczyn niepowodzenia zadania rozpoczęcia.  Aby rozwiązać problemy, sprawdź stdout, stderr i wszystkie pliki dalsze dzienników dotyczących konkretnych zadań.

### <a name="application-package-download-failure"></a>Błąd pobierania pakietu aplikacji

Można określić co najmniej jeden pakiet aplikacji dla puli. Usługa Batch pobiera pliki określonego pakietu do każdego węzła i dekompresuje pliki po uruchomieniu węzła, ale zanim zadania są zaplanowane. Jest to często używa się wiersza polecenia zadania uruchamiania w połączeniu za pomocą pakietów aplikacji. Na przykład, aby skopiować pliki do innej lokalizacji lub do uruchomienia Instalatora.

Węzeł [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) właściwość zgłasza błąd do pobrania i zdekompresować pakietu aplikacji. Batch ustawia stan węzła **bezużyteczne**.

### <a name="container-download-failure"></a>Błąd pobierania kontenera

Możesz określić jedno lub więcej odwołań kontenera w puli. Batch pobierze określonego kontenerów do każdego węzła. Węzeł [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) właściwość zgłasza błąd pobierania kontenera i ustawia stan węzła **bezużyteczne**.

### <a name="node-in-unusable-state"></a>Węzeł w stanie uniemożliwiającym jego używanie

Usługa Azure Batch może ustawić [stan węzła](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) do **bezużyteczne** wielu powodów. Ze stanem węzła równa **bezużyteczne**, nie można zaplanować zadania w węźle, ale nadal wiąże opłaty.

Węzły w **unsuable**, ale bez [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) stan oznacza, że partii nie może komunikować się z maszyną Wirtualną. W tym przypadku wsadowego zawsze próbuje odzyskać maszynę Wirtualną. Batch nie podejmie automatycznie próbę odzyskania sprawności maszyn wirtualnych, których nie można zainstalować pakiety aplikacji lub kontenerów, nawet jeśli ich stan to **bezużyteczne**.

Jeśli usługi Batch można określić przyczyny, węzeł [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) właściwość zgłasza go.

Dodatkowe przykłady przyczyny **bezużyteczne** węzłów obejmują:

- Niestandardowy obraz maszyny Wirtualnej jest nieprawidłowy. Na przykład, nie został poprawnie przygotowany obraz.

- Maszyna wirtualna zostanie przeniesiona z powodu awarii infrastruktury lub uaktualnienie niskiego poziomu. Batch odzyskuje węzła.

- Obraz maszyny Wirtualnej został wdrożony na sprzęcie, który go nie obsługuje. Na przykład "HPC" obrazu maszyny Wirtualnej uruchomionej na sprzęcie innych niż HPC. Na przykład, może uruchomić obraz CentOS HPC [maszyna wirtualna Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) maszyny Wirtualnej.

- Maszyny wirtualne znajdują się w [sieci wirtualnej platformy Azure](batch-virtual-network.md), i został zablokowany ruch do portów klucza.

### <a name="node-agent-log-files"></a>Plików dziennika agenta węzła

Proces agenta usługi Batch, który działa w każdym węźle puli może zapewnić pliki dziennika, które mogą być przydatne, jeśli zachodzi potrzeba kontaktu z działu pomocy technicznej problem z węzłem puli. Pliki dziennika dla węzła można przekazać za pośrednictwem witryny Azure portal, programu Batch Explorer lub [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Jest to przydatne do przekazywania i zapisywać pliki dziennika. Potem można usunąć węzła lub puli, aby zmniejszyć koszty związane z uruchomionych węzłach.

## <a name="next-steps"></a>Kolejne kroki

Sprawdź, czy ustawiono aplikacji do implementowania kontroli błędów kompleksowe, szczególnie w przypadku operacji asynchronicznych. Może okazać się niezbędne do niezwłocznego wykrywanie i diagnozowanie problemów.
