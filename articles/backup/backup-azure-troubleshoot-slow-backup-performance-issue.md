---
title: Rozwiązywanie problemów z powolne kopii zapasowej plików i folderów w usłudze Kopia zapasowa Azure | Dokumentacja firmy Microsoft
description: Zawiera wskazówki dotyczące rozwiązywania problemów, aby ułatwić diagnozowanie przyczyn problemów z wydajnością kopia zapasowa Azure
services: backup
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.assetid: e379180a-db13-4e0c-90e4-28e5dd6f5b14
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6ed1e2d7bfc08afe135cb85995fdebaa30202c23
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, aby ułatwić diagnozowanie przyczynę niską wydajność tworzenia kopii zapasowej plików i folderów podczas korzystania z usługi Kopia zapasowa Azure. Gdy agenta kopii zapasowej Azure umożliwia tworzenie kopii zapasowych plików, proces tworzenia kopii zapasowej może trwać dłużej, niż oczekiwano. To opóźnienie może być spowodowany przez jedną lub więcej z następujących czynności:

* [Na komputerze, na którym jest tworzona kopia zapasowa, brak wąskich gardeł wydajności.](#cause1)
* [Inny proces lub oprogramowanie antywirusowe jest zakłócać proces tworzenia kopii zapasowej Azure.](#cause2)
* [Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure (VM).](#cause3)  
* [W przypadku tworzenia kopii zapasowej dużą liczbą plików (w milionach).](#cause4)

Przed rozpoczęciem rozwiązywania problemów, firma Microsoft zaleca pobraniu i zainstalowaniu [najnowsza wersja agenta usługi Kopia zapasowa Azure](http://aka.ms/azurebackup_agent). Wykonujemy częste aktualizacje agenta kopii zapasowej, aby rozwiązać problemy z różnych, Dodaj funkcje i zwiększyć wydajność.

Ponadto zdecydowanie zaleca się przejrzenie [usługi Kopia zapasowa Azure — często zadawane pytania](backup-azure-backup-faq.md) się upewnić, że nie występują pewne typowe problemy z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: Wąskich gardeł wydajności na komputerze
Wąskie gardła na komputerze, na którym jest tworzona kopia zapasowa może powodować opóźnienia. Na przykład komputera możliwość odczytu lub zapisu na dysku lub dostępną przepustowość do przesyłania danych za pośrednictwem sieci, może spowodować wąskich gardeł.

System Windows oferuje wbudowane narzędzie, które jest nazywany [monitora wydajności](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon), aby wykryć te wąskich gardeł.

Poniżej przedstawiono niektóre liczniki wydajności i zakresy, które mogą pomóc w zdiagnozowaniu wąskich gardeł optymalne kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny)--% bezczynności |• 100% w stanie bezczynności do 50% bezczynności = w dobrej kondycji</br>• 49% bezczynności 20% bezczynności = ostrzeżenia lub monitora</br>• 19% bezczynności 0% bezczynności = krytyczne lub specyfikacji z |
| Dysk logiczny (dysk fizyczny)--% średni S odczytu lub zapisu na dysku |• 0,001 ms 0,015 MS = w dobrej kondycji</br>• 0,015 ms 0,025 MS = ostrzeżenia lub monitora</br>• 0.026 ms lub już = krytyczne lub specyfikacji z |
| Dysk logiczny (dysk fizyczny) — bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 żądań przez więcej niż 6 minut |
| Pamięć — Bajty z systemem innym niż puli stronicowanej |• Mniej niż 60% puli używane = w dobrej kondycji<br>• 61% do 80% puli używane = ostrzeżenia lub monitora</br>• Większa niż 80% puli używane = krytyczne lub specyfikacji z |
| Pamięć — Bajty w puli stronicowanej |• Mniej niż 60% puli używane = w dobrej kondycji</br>• 61% do 80% puli używane = ostrzeżenia lub monitora</br>• Większa niż 80% puli używane = krytyczne lub specyfikacji z |
| Pamięć — dostępnego w megabajtach |• 50% pamięci lub więcej = w dobrej kondycji</br>• 25% pamięci = monitora</br>• 10% pamięci = ostrzeżenie</br>• Mniej niż 100 MB lub 5% wolnej pamięci = krytyczne lub specyfikacji z |
| Procesor —\%czas procesora (wszystkie wystąpienia) |• Mniej niż 60% używane = w dobrej kondycji</br>• od 61 do 90% używane = Monitor lub Uwaga</br>• od 91 do 100% używane = krytyczne |

> [!NOTE]
> Jeśli okaże się, że infrastruktura jest dziedziczonej z istotnymi elementami, firma Microsoft zaleca defragmentację dysków regularnie w celu zapewnienia lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: Inny proces lub zakłócać kopia zapasowa Azure oprogramowanie antywirusowe
Firma Microsoft przedstawiono kilka wystąpień, gdy inne procesy w systemie Windows ma negatywny wpływ na wydajność procesu agenta usługi Kopia zapasowa Azure. Na przykład używania zarówno agenta usługi Kopia zapasowa Azure, jak i inny program, aby utworzyć kopię zapasową danych lub oprogramowanie antywirusowe jest uruchomiona, jest zablokowany na plikach do wykonania kopii zapasowej wielokrotność blokuje na plików może spowodować rywalizacji. W takiej sytuacji kopii zapasowej może zakończyć się niepowodzeniem lub zadanie może trwać dłużej, niż oczekiwano.

Najlepsze w tym scenariuszu zaleca się wyłączyć kopii zapasowej program aby zobaczyć, czy zmiany czasu tworzenia kopii zapasowych agenta kopii zapasowej Azure. Zwykle upewniając się, że wiele zadań tworzenia kopii zapasowej nie są uruchomione w tym samym czasie jest wystarczające, aby nie wpływały na siebie.

Programy antywirusowe zaleca się, można wykluczyć następujące pliki i lokalizacje:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folderów
* Pliki tymczasowe lokalizacji (Jeśli nie używasz standardowego lokalizacji)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Kopia zapasowa agenta uruchomionego na maszynie wirtualnej platformy Azure
Jeśli korzystasz z agenta kopii zapasowej na maszynie Wirtualnej, wydajność będzie mniejsza niż po uruchomieniu go na komputerze fizycznym. Oczekiwano z powodu ograniczeń IOPS.  Jednak aby zoptymalizować wydajność przełączając dysków z danymi, które są tworzona kopia zapasowa Azure Premium Storage. Pracujemy nad rozwiązywania tego problemu i poprawki będą dostępne w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: Tworzenie kopii zapasowej dużą liczbą plików (w milionach)
Przenoszenie dużej ilości danych będzie trwało dłużej niż przenoszenie mniejsza ilość danych. W niektórych przypadkach czas tworzenia kopii zapasowej dotyczy nie tylko rozmiar danych, ale liczba plików lub folderów. Jest to szczególnie istotne, gdy tworzona jest kopia zapasowa miliony małych plików (w bajtach kilka do kilku kilobajtów).

Dzieje się tak, ponieważ w przypadku tworzenia kopii zapasowej danych i przenoszenia go do platformy Azure, Azure jest jednocześnie skatalogowania plików. W niektórych scenariuszach rzadkich operacji katalogu może potrwać dłużej niż oczekiwano.

Następujących wskaźników ułatwią zrozumienie wąskie gardło i odpowiednio działają w następnych krokach:

* **Interfejs użytkownika jest wyświetlany postęp do transferu danych**. Nadal transferu danych. Przepustowość sieci lub rozmiar danych mogą być przyczyną opóźnień.
* **Interfejs użytkownika nie jest wyświetlany postęp do transferu danych**. Otwórz dzienniki w Agent\Temp usług odzyskiwania Azure C:\Microsoft lokalizacji, a następnie wyszukaj polecenie wpisu FileProvider::EndData w dziennikach. Ten wpis oznacza, że Zakończono transfer danych jest przeprowadzana operacja katalogu. Nie Anuluj zadania tworzenia kopii zapasowej. Zamiast tego należy zaczekać nieco dłużej na zakończenie operacji katalogu. Jeśli problem będzie się powtarzać, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/#create/Microsoft.Support).
