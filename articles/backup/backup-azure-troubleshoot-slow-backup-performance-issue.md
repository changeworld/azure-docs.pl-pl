---
title: Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
description: Zawiera wskazówki dotyczące rozwiązywania problemów ułatwiające zdiagnozowanie przyczyny problemów z wydajnością Azure Backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 3a39d39412c8b64d1851ea0fc9511d116f3b232a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465337"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów ułatwiające zdiagnozowanie przyczyny niskiej wydajności kopii zapasowych plików i folderów w przypadku korzystania z Azure Backup. W przypadku tworzenia kopii zapasowej plików przy użyciu agenta Azure Backup proces tworzenia kopii zapasowej może trwać dłużej niż oczekiwano. Ta wartość opóźnienia może być spowodowana przez jedną lub więcej z następujących czynności:

* [Na komputerze, na którym jest tworzona kopia zapasowa, istnieją wąskie gardła wydajności.](#cause1)
* [Inny proces lub oprogramowanie antywirusowe zakłóca proces Azure Backup.](#cause2)
* [Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure.](#cause3)  
* [Wykonujesz kopię zapasową dużej liczby plików (miliony).](#cause4)

Przed rozpoczęciem rozwiązywania problemów zalecamy pobranie i zainstalowanie [najnowszego agenta Azure Backup](https://aka.ms/azurebackup_agent). Udostępniamy częste aktualizacje agenta kopii zapasowej, aby rozwiązać różne problemy, dodać funkcje i zwiększyć wydajność.

Zdecydowanie zalecamy także przejrzenie [często zadawanych pytań dotyczących usługi Azure Backup](backup-azure-backup-faq.md) , aby upewnić się, że nie występują żadne typowe problemy z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: Wąskie gardła wydajności na komputerze
Wąskie gardła na komputerze, którego kopia zapasowa jest tworzona, mogą spowodować opóźnienia. Na przykład komputer może odczytywać lub zapisywać dane na dysku albo dostępną przepustowość do przesyłania danych za pośrednictwem sieci, mogą spowodować wąskie gardła.

System Windows udostępnia wbudowane narzędzie, które nosi nazwę [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (PerfMon) do wykrywania tych wąskich gardeł.

Poniżej przedstawiono niektóre liczniki wydajności i zakresy, które mogą być przydatne w diagnozowaniu wąskich gardeł dla optymalnych kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny) — czas bezczynności (%) |• 100% bezczynności do 50% bezczynności = w dobrej kondycji</br>• 49% bezczynności do 20% bezczynności = ostrzeżenie lub monitor</br>• 19% bezczynności do 0% bezczynności = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny)--% AVG. Dysk w s odczyt lub zapis |• 0,001 MS do 0,015 MS = dobra kondycja</br>• 0,015 od MS do 0,025 MS = ostrzeżenie lub monitor</br>• 0,026 MS lub dłużej = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny) — bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 żądań przez ponad 6 minut |
| Pamięć — Bajty puli niestronicowanej |• Mniej niż 60% używanej puli = dobra kondycja<br>• 61% do 80% używanej puli = ostrzeżenie lub monitor</br>• Większe niż 80% zużywane przez pulę = krytyczne lub poza specyfikacją |
| Pamięć — bajty w puli stronicowanej |• Mniej niż 60% używanej puli = dobra kondycja</br>• 61% do 80% używanej puli = ostrzeżenie lub monitor</br>• Większe niż 80% zużywane przez pulę = krytyczne lub poza specyfikacją |
| Pamięć — dostępne megabajty |• 50% dostępnej wolnej pamięci lub więcej = dobra kondycja</br>• 25% dostępnej wolnej pamięci = monitor</br>• 10% dostępnej wolnej pamięci = ostrzeżenie</br>• Mniej niż 100 MB lub 5% dostępnej wolnej pamięci = krytyczne lub poza specyfikacją |
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

author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
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
| Processor--\%Processor Time (all instances) |• Mniej niż 60% zużyte = w dobrej kondycji</br>• od 61% do 90% zużyte = monitor lub przestroga</br>• od 91% do 100% zużyte = krytyczny |

> [!NOTE]
> Jeśli określisz, że infrastruktura jest przyczynaa, zalecamy regularne defragmentowanie dysków w celu zapewnienia lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: Inny proces lub oprogramowanie antywirusowe zakłócające działanie Azure Backup
Zaobserwowano kilka wystąpień, w których inne procesy w systemie Windows mają negatywny wpływ na wydajność procesu agenta Azure Backup. Na przykład, jeśli używasz agenta Azure Backup i innego programu do tworzenia kopii zapasowych danych lub jeśli oprogramowanie antywirusowe jest uruchomione i ma blokadę plików, których kopia zapasowa ma zostać utworzona, wiele blokad dla plików może spowodować rywalizację. W takiej sytuacji tworzenie kopii zapasowej może zakończyć się niepowodzeniem lub zadanie może trwać dłużej niż oczekiwano.

Najlepszym zaleceniem w tym scenariuszu jest wyłączenie innego programu do tworzenia kopii zapasowych, aby sprawdzić, czy czas tworzenia kopii zapasowej dla agenta Azure Backup ulega zmianie. Zwykle w celu zapewnienia, że wiele zadań tworzenia kopii zapasowych nie jest uruchomionych w tym samym czasie, wystarczy, aby zapobiec wpływowi ich na siebie nawzajem.

W przypadku programów antywirusowych zaleca się wykluczenie następujących plików i lokalizacji:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services Agent \ foldery
* Lokalizacja zapasowa (jeśli nie używasz lokalizacji standardowej)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Agent kopii zapasowej uruchomiony na maszynie wirtualnej platformy Azure
Jeśli na maszynie wirtualnej jest uruchomiony agent kopii zapasowej, wydajność będzie wolniejsza niż podczas uruchamiania na komputerze fizycznym. Jest to oczekiwane ze względu na ograniczenia liczby IOPS.  Można jednak zoptymalizować wydajność, przełączając dyski danych, których kopie zapasowe są tworzone w usłudze Azure Premium Storage. Pracujemy nad rozwiązaniem tego problemu, a poprawka będzie dostępna w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: Tworzenie kopii zapasowej dużej liczby (miliony) plików
Przeniesienie dużej ilości danych zajmie więcej czasu niż przeniesienie mniejszej ilości danych. W niektórych przypadkach czas tworzenia kopii zapasowej jest związany z nie tylko rozmiarem danych, ale również liczbą plików lub folderów. Jest to szczególnie ważne, gdy tworzona jest kopia zapasowa milionów małych plików (kilka bajtów do kilku kilobajtów).

Takie zachowanie występuje, ponieważ podczas tworzenia kopii zapasowej danych i przechodzenia do platformy Azure, platforma Azure jednocześnie wykazuje pliki. W niektórych rzadkich scenariuszach operacja katalogu może trwać dłużej niż oczekiwano.

Następujące wskaźniki mogą pomóc zrozumieć wąskie gardło i odpowiednio wykonać kolejne kroki:

* **Interfejs użytkownika pokazuje postęp transferu danych**. Dane nadal są transferowane. Przepustowość sieci lub rozmiar danych może powodować opóźnienia.
* **Interfejs użytkownika nie pokazuje postępu transferu danych**. Otwórz dzienniki zlokalizowane w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, a następnie sprawdź wpis FileProvider:: EndData w dziennikach. Ten wpis oznacza, że proces transferu danych zakończył pracę i jest wykonywane działanie katalogu. Nie Anuluj zadań tworzenia kopii zapasowej. Zamiast tego poczekaj chwilę na zakończenie operacji katalogu. Jeśli problem będzie się powtarzać, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#create/Microsoft.Support).
