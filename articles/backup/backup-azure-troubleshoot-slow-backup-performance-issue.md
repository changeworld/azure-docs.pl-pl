---
title: Rozwiązywanie problemów z powolną tworzeniem kopii zapasowych plików i folderów
description: Zawiera wskazówki dotyczące rozwiązywania problemów ułatwiające diagnozowanie przyczyn problemów z wydajnością usługi Azure Backup
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 6c650ee735ffcdd50f4361a867fa592f4965ab68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408695"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które pomagają zdiagnozować przyczynę powolnej wydajności tworzenia kopii zapasowych dla plików i folderów podczas korzystania z usługi Azure Backup. Podczas tworzenia kopii zapasowej plików za pomocą agenta usługi Azure Backup proces tworzenia kopii zapasowej może trwać dłużej niż oczekiwano. Opóźnienie to może być spowodowane przez co najmniej jedną z następujących czynności:

* [Istnieją wąskie gardła wydajności na komputerze, który jest archiwizowana.](#cause1)
* [Inny proces lub oprogramowanie antywirusowe zakłóca proces tworzenia kopii zapasowej platformy Azure.](#cause2)
* [Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure (VM).](#cause3)  
* [Kopie zapasowe dużej liczby (milionów) plików.](#cause4)

Przed rozpoczęciem rozwiązywania problemów zaleca się pobranie i zainstalowanie [najnowszego agenta usługi Azure Backup](https://aka.ms/azurebackup_agent). Często aktualizujemy agenta kopii zapasowej, aby rozwiązać różne problemy, dodać funkcje i zwiększyć wydajność.

Firma Microsoft zdecydowanie zaleca również przejrzenie [często zadawanych pytań dotyczących usługi Azure Backup,](backup-azure-backup-faq.md) aby upewnić się, że nie występują żadne z typowych problemów z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Przyczyna: Zadanie tworzenia kopii zapasowej uruchomione w trybie nieoptymalizowanym

* Agent MARS może uruchomić zadanie tworzenia kopii zapasowej w **trybie zoptymalizowanym** przy użyciu dziennika zmiany numeru USN (numer sekwencyjny aktualizacji) lub **trybu nieoptymalizowanego,** sprawdzając zmiany w katalogach lub plikach, skanując cały wolumin.
* Tryb nieoptymalizowany jest powolny, ponieważ agent musi skanować każdy plik na woluminie i porównywać je z metadanymi, aby określić zmienione pliki.
* Aby to sprawdzić, otwórz **szczegóły zadania** z konsoli agenta MARS i sprawdź stan, aby sprawdzić, czy jest **wyświetlany komunikat Przesyłanie danych (nieoptymalizowanych, może zająć więcej czasu),** jak pokazano poniżej:

    ![Bieganie w trybie nieoptymalizowanym](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Następujące warunki mogą spowodować uruchomienie zadania kopii zapasowej w trybie nieoptymalizowanym:
  * Pierwsza kopia zapasowa (znana również jako replikacja początkowa) będzie zawsze uruchamiana w trybie nieoptymalizowanym
  * Jeśli poprzednie zadanie tworzenia kopii zapasowej nie powiedzie się, następne zaplanowane zadanie tworzenia kopii zapasowej zostanie uruchomione jako nieoptymizowane.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: Wąskie gardła wydajności na komputerze

Wąskie gardła na komputerze, na który jest utworzona kopia zapasowa, mogą powodować opóźnienia. Na przykład zdolność komputera do odczytu lub zapisu na dysku lub dostępna przepustowość do wysyłania danych przez sieć może powodować wąskie gardła.

System Windows udostępnia wbudowane narzędzie o nazwie [Monitor wydajności](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) do wykrywania tych wąskich gardeł.

Oto kilka liczników wydajności i zakresów, które mogą być pomocne w diagnozowaniu wąskich gardeł w celu uzyskania optymalnych kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny)--%bezczynny |* 100% bezczynny do 50% bezczynny = Zdrowy</br>* 49% bezczynnie do 20% bezczynnie = Ostrzeżenie lub Monitor</br>* 19% bezczynności do 0% bezczynności = Krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny)--%Avg. Disk Sec odczyt lub zapis |* 0,001 ms do 0,015 ms = Zdrowy</br>* 0,015 ms do 0,025 ms = Ostrzeżenie lub Monitor</br>* 0,026 ms lub dłużej = Krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny) - bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 próśb na ponad 6 minut |
| Pamięć — pula bajtów niestronicowanych |* Mniej niż 60% spożywanego basenu = Zdrowe<br>* 61% do 80% zużytej puli = Ostrzeżenie lub Monitor</br>* Większa niż 80% puli zużyte = Krytyczne lub Out of Spec |
| Pamięć — stronicowanych bajtów puli |* Mniej niż 60% spożywanego basenu = Zdrowe</br>* 61% do 80% zużytej puli = Ostrzeżenie lub Monitor</br>* Większa niż 80% puli zużyte = Krytyczne lub Out of Spec |
| Pamięć — dostępne megabajty |* 50% wolnej pamięci dostępnej lub więcej = Zdrowy</br>* 25% wolnej pamięci = Monitor</br>* 10% wolnej pamięci = Ostrzeżenie</br>* Mniej niż 100 MB lub 5% wolnej pamięci dostępne = Krytyczne lub Poza specyfikacją |
| Procesor —\%czas procesora (wszystkie wystąpienia) |* Mniej niż 60% zużyte = Zdrowe</br>* 61% do 90% zużyte = Monitor lub przestroga</br>* 91% do 100% zużyte = Krytyczne |

> [!NOTE]
> Jeśli okaże się, że infrastruktura jest winowajcą, zaleca się defragmentacji dysków regularnie dla lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: Inny proces lub oprogramowanie antywirusowe zakłócające usługę Azure Backup

Widzieliśmy kilka wystąpień, w których inne procesy w systemie Windows mają negatywny wpływ na wydajność procesu agenta usługi Azure Backup. Na przykład jeśli używasz agenta usługi Azure Backup i innego programu do tworzenia kopii zapasowych danych lub jeśli oprogramowanie antywirusowe jest uruchomione i ma blokadę plików, które mają być archiwizowane, wiele blokad plików może spowodować rywalizację. W tej sytuacji kopia zapasowa może zakończyć się niepowodzeniem lub zadanie może trwać dłużej niż oczekiwano.

Najlepszym zaleceniem w tym scenariuszu jest wyłączenie innego programu do tworzenia kopii zapasowych, aby sprawdzić, czy zmienia się czas wykonywania kopii zapasowej agenta usługi Azure Backup. Zazwyczaj upewniając się, że wiele zadań tworzenia kopii zapasowych nie są uruchomione w tym samym czasie jest wystarczające, aby zapobiec ich wpływ na siebie nawzajem.

W przypadku programów antywirusowych zaleca się wykluczenie następujących plików i lokalizacji:

* C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure\bin\cbengine.exe jako proces
* C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure\ foldery
* Lokalizacja zdrapania (jeśli nie używasz standardowej lokalizacji)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Agent kopii zapasowej uruchomiony na maszynie wirtualnej platformy Azure

Jeśli korzystasz z agenta kopii zapasowej na maszynie Wirtualnej, wydajność będzie mniejsza niż po uruchomieniu go na komputerze fizycznym. Jest to oczekiwane ze względu na ograniczenia we/wy.  Można jednak zoptymalizować wydajność, przełączając dyski danych, których kopię zapasową jest w usłudze Azure Premium Storage. Pracujemy nad rozwiązaniem tego problemu, a poprawka będzie dostępna w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: Tworzenie kopii zapasowych dużej liczby (milionów) plików

Przenoszenie dużej ilości danych zajmie więcej czasu niż przeniesienie mniejszej ilości danych. W niektórych przypadkach czas tworzenia kopii zapasowej jest związany nie tylko z rozmiarem danych, ale także z liczbą plików lub folderów. Jest to szczególnie ważne, gdy miliony małych plików (kilka bajtów do kilku kilobajtów) są archiwizowane.

To zachowanie występuje, ponieważ podczas tworzenia kopii zapasowej danych i przenoszenia ich na platformę Azure platforma Azure jednocześnie kataloguje pliki. W niektórych rzadkich scenariuszach operacja katalogu może trwać dłużej niż oczekiwano.

Następujące wskaźniki mogą pomóc w zrozumieniu wąskiego gardła i odpowiednio pracować nad kolejnymi krokami:

* **Interfejs użytkownika pokazuje postęp transferu danych**. Dane są nadal przesyłane. Przepustowość sieci lub rozmiar danych może powodować opóźnienia.
* **Interfejs użytkownika nie pokazuje postępu transferu danych**. Otwórz dzienniki znajdujące się pod adresem C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, a następnie sprawdź wpis FileProvider::EndData w dziennikach. Ten wpis oznacza, że transfer danych został zakończony i operacja katalogu ma miejsce. Nie anuluj zadań tworzenia kopii zapasowej. Zamiast tego poczekaj trochę dłużej, aż operacja katalogu zakończy się. Jeśli problem będzie się powtarzał, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#create/Microsoft.Support).

## <a name="next-steps"></a>Następne kroki

* [Typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md)