---
title: Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
description: Zawiera wskazówki dotyczące rozwiązywania problemów, dzięki którym możesz diagnozować problemy z wydajnością usługi Azure Backup przyczyną
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
ms.openlocfilehash: 1bc9c7b4f6e2a4f2a7c712d966caac74b73518df
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565659"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, dzięki którym możesz diagnozować przyczyny niskiej wydajności tworzenia kopii zapasowych plików i folderów, podczas korzystania z usługi Azure Backup. Gdy używasz agenta usługi Azure Backup do tworzenia kopii zapasowych plików, proces tworzenia kopii zapasowej może potrwać dłużej niż oczekiwano. To opóźnienie może być spowodowany przez jedną lub więcej z następujących czynności:

* [Na komputerze, na którym jest tworzona kopia zapasowa znajdują się wąskie gardła wydajności.](#cause1)
* [Inny proces lub program antywirusowy zakłóca jest z procesem kopia zapasowa Azure.](#cause2)
* [Agenta usługi Backup jest uruchomiona na maszynie wirtualnej platformy Azure (VM).](#cause3)  
* [Wykonujesz kopie zapasowe dużą liczbą plików (w milionach).](#cause4)

Przed rozpoczęciem rozwiązywania problemów, firma Microsoft zaleca pobierzesz i zainstalujesz [najnowszą wersję agenta usługi Kopia zapasowa Azure](https://aka.ms/azurebackup_agent). Ułatwiamy częste aktualizacje agenta kopii zapasowej do rozwiązywania różnych problemów, Dodaj funkcje i zwiększyć wydajność.

Ponadto zdecydowanie zalecamy przejrzenie [— często zadawane pytania dla usługi Azure Backup](backup-azure-backup-faq.md) się upewnić, że nie występują dowolne typowe problemy z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: Wąskie gardła wydajności na komputerze
Wąskie gardła na komputerze, na którym jest tworzona kopia zapasowa może powodować opóźnienia. Na przykład zdolność komputera do odczytu lub zapisu na dysku lub dostępną przepustowość do przesyłania danych za pośrednictwem sieci, może spowodować wąskie gardła.

Windows oferuje wbudowane narzędzie, które jest wywoływane [monitora wydajności](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon), aby wykryć te wąskich gardeł.

Poniżej przedstawiono niektóre liczniki wydajności i zakresy, które mogą być pomocne podczas diagnozowania wąskie gardła dla optymalnego tworzenia kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny) — % bezczynności |• 100% bezczynności (%) do 50% bezczynne = dobra kondycja</br>• 49% bezczynności (%) do 20% bezczynne = ostrzeżenie lub Monitor</br>• 19% bezczynności (%) na 0% bezczynne = o znaczeniu krytycznym lub poza Specyfikacja |
| Dysk logiczny (dysk fizyczny) — średni % Dysku w s Odczyt lub zapis |• 0,001 ms 0,015 MS = dobra kondycja</br>• 0,015 ms 0,025 MS = ostrzeżenie lub Monitor</br>• 0.026 ms lub już = o znaczeniu krytycznym lub poza Specyfikacja |
| Dysk logiczny (dysk fizyczny) — bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 żądań przez więcej niż 6 minut |
| Pamięć — Bajty innej puli stronicowanej |• Mniej niż 60% puli używane = dobra kondycja<br>• 61% do 80% puli używane = ostrzeżenie lub Monitor</br>• Większa niż 80% puli używane = o znaczeniu krytycznym lub poza Specyfikacja |
| Pamięć — Bajty w puli stronicowanej |• Mniej niż 60% puli używane = dobra kondycja</br>• 61% do 80% puli używane = ostrzeżenie lub Monitor</br>• Większa niż 80% puli używane = o znaczeniu krytycznym lub poza Specyfikacja |
| Pamięć — dostępne (MB) |• 50% pamięci lub bardziej = dobra kondycja</br>• 25% pamięci = Monitor</br>• 10% wolnej pamięci dostępne = ostrzeżenie</br>• Mniej niż 100 MB lub 5% wolnej pamięci dostępne = o znaczeniu krytycznym lub poza Specyfikacja |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Procesor —\`czas procesora (wszystkie wystąpienia)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Mniej niż 60% używane = dobra kondycja</br>• od 61 – 90% używane = monitora lub Uwaga</br>• od 91 – 100% używane = krytyczne |

> [!NOTE]
> Jeśli okaże się, że infrastruktura jest przyczyna nadmiernego, firma Microsoft zaleca defragmentację dysków regularnie w celu zapewnienia lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: Inny proces lub program antywirusowy zakłóca za pomocą usługi Azure Backup
Zobaczyliśmy, kilka wystąpień, w którym inne procesy w systemie Windows ma negatywny wpływ na wydajność procesu agenta usługi Kopia zapasowa Azure. Na przykład jeśli używasz agenta usługi Azure Backup i inny program do tworzenia kopii zapasowych lub jeśli oprogramowanie antywirusowe jest uruchomiona, jest zablokowany na plikach do wykonania kopii zapasowej wielokrotność blokady na pliki może spowodować rywalizacji o zasoby. W takiej sytuacji kopii zapasowej może zakończyć się niepowodzeniem lub zadania może trwać dłużej, niż oczekiwano.

Najlepsze w tym scenariuszu zaleca się wyłączyć kopii zapasowej program aby zobaczyć, czy zmiany godziny tworzenia kopii zapasowych agenta usługi Azure Backup. Zazwyczaj upewniając się, że wiele zadań tworzenia kopii zapasowej nie są uruchomione w tym samym czasie jest wystarczające, aby nie wpływały na siebie nawzajem.

Programy antywirusowe zaleca się wykluczenie następujące pliki i lokalizacje:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folderów
* Lokalizacja plików tymczasowych (Jeśli nie używasz standardowa)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Agent kopii zapasowych działa na maszynie wirtualnej platformy Azure
Jeśli korzystasz z agenta kopii zapasowej na maszynie Wirtualnej, wydajność będzie przebiegać wolniej niż po uruchomieniu go na komputerze fizycznym. Jest to oczekiwane ze względu na ograniczenia operacji We/Wy.  Możesz jednak zoptymalizować wydajność, przełączając dysków z danymi, które tworzona jest kopia zapasowa Azure Premium Storage. Pracujemy nad rozwiązaniem tego problemu i poprawka będzie dostępna w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: Tworzenie kopii zapasowej dużą liczbą plików (w milionach)
Przenoszenie dużych ilości danych będzie trwać dłużej niż przenoszenie mniejszej ilości danych. W niektórych przypadkach czas wykonywania kopii zapasowych dotyczy nie tylko rozmiar danych, ale liczba plików lub folderów. Jest to szczególnie istotne w przypadku, gdy tworzona jest kopia zapasowa milionów małych plików (w bajtach kilka do kilku kilobajtów).

Dzieje się tak, ponieważ jest tworzenie kopii zapasowych danych i przenosząc ją na platformie Azure, Azure jest jednocześnie katalogowanie plików. W niektórych przypadkach rzadkich operacji katalogu może potrwać dłużej niż oczekiwano.

Następujących wskaźników mogą pomóc Ci zrozumieć wąskie gardło i odpowiednio działają na następne kroki:

* **Interfejs użytkownika jest wyświetlany postęp za transfer danych**. Dane są wciąż przesyłane. Przepustowość sieci lub większy niż rozmiar danych mogą być przyczyną opóźnień.
* **Interfejs użytkownika nie jest wyświetlany postęp dla transferu danych**. Otwórz dzienniki znajdujące się na C:\Program Files\Microsoft Azure Recovery Services Agent\Temp i sprawdź, czy wpis FileProvider::EndData w dziennikach. Ten wpis oznacza, że Zakończono transfer danych, a operacja katalogu. Nie Anuluj zadania tworzenia kopii zapasowej. Zamiast tego należy zaczekać nieco dłużej na zakończenie operacji katalogu. Jeśli problem będzie się powtarzać, skontaktuj się z [pomocy technicznej platformy Azure](https://portal.azure.com/#create/Microsoft.Support).
