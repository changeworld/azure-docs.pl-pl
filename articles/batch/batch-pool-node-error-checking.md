---
title: Sprawdzanie błędów puli i węzła usługi Batch
description: Błędy do wyszukania oraz sposoby unikania podczas tworzenia pul i węzłów
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435215"
---
# <a name="checking-for-pool-and-node-errors"></a>Sprawdzanie błędów puli i węzła

Podczas tworzenia i zarządzania pule usługi Batch, istnieją operacje wykonywane od razu wiąże się z asynchroniczne operacje, które nie są bezpośrednim, uruchomione w tle i może potrwać kilka minut.

Wykrywanie błędów dla operacji, które zaczynają obowiązywać natychmiast jest proste, ponieważ wszelkie błędy zostaną zwrócone bezpośrednio przez interfejs API, interfejs wiersza polecenia lub interfejsu użytkownika.

W tym artykule opisano operacji w tle, które mogą być wykonywane dla pul i węzłów puli — Określa, jak można wykryć błędy i jak można uniknąć błędów.

## <a name="pool-errors"></a>Błędy puli

### <a name="resize-timeout-or-failure"></a>Limit czasu zmiany rozmiaru lub niepowodzenia

Podczas tworzenia nowej puli lub zmienianiu rozmiaru istniejącej puli docelowej liczby węzłów jest określony.  Operacja zostanie zakończona natychmiast, ale rzeczywisty przydział nowych węzłów i usuwanie istniejących węzłów odbywa się w tle za pośrednictwem co może potrwać kilka minut.  Przekroczono limit czasu zmiany rozmiaru jest określony w [tworzenie](https://docs.microsoft.com/rest/api/batchservice/pool/add) lub [rozmiar](https://docs.microsoft.com/rest/api/batchservice/pool/resize) interfejsu API — Jeśli docelowa liczba węzłów nie można uzyskać w określonym przedziale czasu zmiany rozmiaru, następnie operacja zostanie zatrzymana z pulą, przechodząc do stanu stabilnego i wystąpiły błędy zmiany rozmiaru.

Przekroczono limit czasu zmiany rozmiaru jest zgłaszany przez [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) właściwość ostatnia ocena zawiera jeden lub więcej błędów, które wystąpiły.

Typowe przyczyny przekroczenia limitu czasu zmiany rozmiaru, obejmują:
- Limit czasu zmiany rozmiaru jest za krótkie
  - Domyślny limit czasu jest 15 minut, która jest zwykle dużo czasu dla węzłów puli mają być przydzielane lub usunięte.
  - Podczas przydzielania dużej liczby węzłów (ponad 1000 węzłów za pomocą obrazu portalu Marketplace) lub ponad 300 węzłów na podstawie niestandardowego obrazu, podczas tworzenia puli lub zmiany rozmiaru, zaleca się 30-minutowy limit czasu.
- Limit przydziału rdzeni niewystarczające
  - Konto usługi batch ma limit przydziału liczby rdzeni, które jest przydzielany dla wszystkich pul.  Batch nie będzie przydzielanie węzłów, po przekroczeniu tego limitu przydziału.  Limit przydziału rdzeni [można zwiększyć](https://docs.microsoft.com/azure/batch/batch-quota-limit) aby umożliwić większą liczbę węzłów do przydzielenia.
- Za mało podsieci adresów IP podczas [puli znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsieci sieci wirtualnej musi mieć wystarczającą liczbę nieprzypisanych adresów IP do przydzielenia w węzłach puli żądanej, w przeciwnym razie nie może zostać utworzone węzły.
- Za mało zasobów po [puli znajduje się w sieci wirtualnej](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Zasoby, takie jak równoważenia obciążenia, publiczne adresy IP i sieciowymi grupami zabezpieczeń są tworzone w subskrypcji użytej do utworzenia konta usługi Batch.  Limity przydziału subskrypcji dla tych zasobów musi być wystarczająca.
- W przypadku pul dużych przy użyciu niestandardowego obrazu maszyny Wirtualnej
  - Pul dużych, za pomocą obrazów niestandardowych można przydzielić i zmienianie rozmiaru przekroczeń limitu czasu trwa dłużej może wystąpić.  Limity i zaleceń dotyczących konfiguracji znajdują się w [określonego artykułu](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Błędy automatycznego skalowania

Zamiast jawne ustawienie docelowej liczby węzłów dla puli w tworzenie puli lub zmień rozmiar, liczbę węzłów w puli mogą być skalowane automatycznie.  [Formułę skalowania automatycznego można tworzyć pulę](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), które zostanie obliczone w regularnych odstępach czasu można skonfigurować do ustawiania docelowa liczba węzłów w puli.  Następujące rodzaje problemów może wystąpić i wykryte:

- Obliczania automatycznego skalowania może zakończyć się niepowodzeniem.
- Wynikowy operacji zmiany rozmiaru może zakończyć się niepowodzeniem i limitu czasu.
- Może to być problem z formuły automatycznego skalowania, co prowadzi do węzła niepoprawne wartości docelowych, przy użyciu rozmiaru pracy lub przekracza limit czasu.

Informacje o ostatniego obliczania automatycznego skalowania są uzyskiwane przy użyciu [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) właściwość, która zgłasza na czas oceny, wartości oraz wynik oceny oraz wszelkie błędy, przeprowadzania oceny.

Informacje o wszystkich ocen automatycznie jest przechwytywany przez [puli rozmiar zdarzenie ukończenia](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Usuwanie

Przy założeniu, że ma węzłów w puli, pula usunąć wyniki operacji w węzłach usuwany po pierwsze, a następnie sam obiekt puli.  Może upłynąć kilka minut, aż węzły puli do usunięcia.

[Puli stanu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) zostanie ustawiony na usunięcie, podczas procesu usuwania.  Aplikacja wywołująca może wykryć, jeśli usunięcie puli jest zbyt długiego czasu za pomocą właściwości 'state' i "stateTransitionTime".

## <a name="pool-compute-node-errors"></a>Błędy węzłów obliczeniowych w puli

Węzły można pomyślnie przydzielone w puli, ale różnych problemów może prowadzić do węzły są w złej kondycji i nie można używać.  Po węzły są przydzielane w puli, są naliczane opłaty, i dlatego jest ważne, aby wykrywać problemy, aby uniknąć płacenia za węzły, których nie można używać.

### <a name="start-task-failure"></a>Niepowodzenie zadania uruchamiania

Opcjonalny [zadanie podrzędne uruchamiania](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) może zostać określony dla puli.  Podobnie jak w przypadku dowolnego zadania podrzędnego, należy określić wiersz polecenia i pliki zasobów można pobrać z magazynu.  Zadanie podrzędne uruchamiania jest określona dla puli, ale uruchomić w każdym węźle — po rozpoczęciu każdy węzeł, a następnie uruchom to zadanie podrzędne uruchamiania.  Dodatkowe właściwości [zadanie podrzędne uruchamiania](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), "waitForSuccess", określa, czy usługi Batch należy oczekiwać zadania podrzędnego uruchamiania do pomyślnego ukończenia przed zaplanowaniem jakichkolwiek zadań do węzła.

Jeśli określona konfiguracja zadania uruchamiania oczekiwania na pomyślne zakończenie zadania uruchamiania nie powiedzie się, węzeł nie będzie można korzystać i będą nadal powodować Naliczanie opłat.

Błędy zadań uruchamiania mogą zostać wykryte za pomocą [wynik](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) i [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) właściwości najwyższego poziomu [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) właściwość węzła.

Zadanie uruchamiania nie powiodło się prowadzi do węzła [stanu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) zostanie ustawiona "starttaskfailed", ale tylko wtedy, jeśli ustawiono "waitForSuccess" na "true".

Podobnie jak w przypadku dowolnego zadania podrzędnego, może wystąpić wiele przyczyn niepowodzenia zadania rozpoczęcia.  Aby rozwiązać problemy, stdout, stderr i wszystkie pliki dzienników dotyczących konkretnych zadań dalsze powinno być zaznaczone.

### <a name="application-package-download-failure"></a>Błąd pobierania pakietu aplikacji

Jeden lub więcej pakietów aplikacji można opcjonalnie określony dla puli, za pomocą plików określony pakiet pobierania na każdym węźle i nieskompresowanym formatem po rozpoczęciu węzła, ale zanim zadania są zaplanowane.  Jest to często używa się wiersza polecenia zadania uruchamiania w połączeniu za pomocą pakietów aplikacji, aby skopiować pliki do innej lokalizacji lub uruchom Instalatora, na przykład.

Błąd podczas pobierania i zdekompresować pakietu aplikacji, które będą zgłaszane przez węzeł [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) właściwości.  Stan węzła zostanie ustawiony "bezużyteczne".

### <a name="node-in-unusable-state"></a>Węzeł w stanie uniemożliwiającym jego używanie

[Stan węzła](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) można ustawić do bezużyteczne wielu powodów.  "Stanie się bezużyteczny," nie może zostać zaplanowane zadania w węźle, ale węzeł będą nadal powodować Naliczanie opłat.

Usługi Batch będą zawsze próbować odzyskać bezużyteczne węzłów, ale odzyskiwania może być lub może nie być możliwe, w zależności od przyczyny.

W przypadku, gdy można ustalić przyczynę, zostanie on zgłoszony przez węzeł [błędy](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) właściwości.

Kilka innych przykładów przyczyny "bezużyteczne" węzłów:

- Nieprawidłowy niestandardowy obraz; nie przygotowane poprawnie, na przykład.
- Błąd infrastruktury lub uaktualnienie niskiego poziomu, co prowadzi do podstawowej maszyny Wirtualnej zostały przeniesione; Batch zostanie przywrócona do działania węzła.

### <a name="node-agent-log-files"></a>Plików dziennika agenta węzła

Jeśli zachodzi konieczność skontaktuj się z działem pomocy technicznej dotyczącej problemu węzła puli, można uzyskać pliki dziennika z proces agenta usługi Batch, który działa w każdym węźle puli.  W witrynie Azure portal, programu Batch Explorer można przekazać plików dziennika dla węzła lub [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Przekazywanie i zapisywanie plików dziennika może być bardzo przydatne jako węzeł lub puli można usunąć tak, aby zmniejszyć koszty związane z uruchomionych węzłach.

## <a name="next-steps"></a>Kolejne kroki

Upewnij się, że aplikacja została zaimplementowana, kompleksowej kontroli błędów, szczególnie w przypadku operacji asynchronicznych, dzięki czemu problemy mogą szybko wykryte i zdiagnozować.
