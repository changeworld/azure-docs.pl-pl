---
title: Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup
description: Zawiera wskazówki dotyczące rozwiązywania problemów, dzięki którym możesz diagnozować problemy z wydajnością usługi Azure Backup przyczyną
services: backup
author: genlin
manager: cshepard
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f24a60ab9bdcf1231085de4edeeb89ce1edf4e80
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337633"
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
| Procesor —\%czas procesora (wszystkie wystąpienia) |• Mniej niż 60% używane = dobra kondycja</br>• od 61 – 90% używane = monitora lub Uwaga</br>• od 91 – 100% używane = krytyczne |

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
