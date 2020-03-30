---
title: Często zadawane pytania - CZĘSTO ZADAWANE PYTANIA
titleSuffix: Microsoft Genomics
description: Uzyskaj odpowiedzi na często zadawane pytania związane z korzystaniem z usługi Microsoft Genomics, w tym informacje techniczne, umowy SLA i rozliczenia.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986040"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Często zadawane pytania

W tym artykule wymieniono najważniejsze zapytania, które mogą być związane z microsoft genomics. Aby uzyskać więcej informacji na temat usługi Microsoft Genomics, zobacz [Co to jest Microsoft Genomics?](overview-what-is-genomics.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz nasz [Przewodnik rozwiązywania problemów](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Jak uruchomić przepływy pracy GATK4 w programie Microsoft Genomics?
W pliku config.txt usługi Microsoft Genomics określ process_name `gatk4`do . Pamiętaj, że opłata będzie naliczona według regularnych stawek rozliczeniowych.

## <a name="how-do-i-enable-output-compression"></a>Jak włączyć kompresję wyjściową?
Można skompresować wyjście vcf lub gvcf za pomocą opcjonalnego argumentu kompresji wyjściowej. Jest to równoważne `-bgzip` `-tabix` z uruchamianiem, po którym następuje `.gz` wyjście vcf lub `.tbi` gvcf, do produkcji (wyjście bgzip) i (wyjście tabix). `bgzip`kompresuje plik vcf lub gvcf i `tabix` tworzy indeks dla skompresowanego pliku. Argument jest wartością logiczną, `false` która jest domyślnie ustawiona `true` na wyjście vcf i domyślnie dla danych wyjściowych gcvf. Aby użyć w wierszu `-bz` `--bgzip-output` polecenia, należy określić lub `true` jako `false`(uruchom bgzip i tabix) lub . Aby użyć tego argumentu w pliku config.txt, dodaj `bgzip_output: true` lub `bgzip_output: false` do pliku.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co to jest umowy SLA dla microsoft genomics?
Gwarantujemy, że 99,9% czasu usługi Microsoft Genomics będzie dostępne do odbierania żądań interfejsu API przepływu pracy. Aby uzyskać więcej informacji, zobacz [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>W jaki sposób użycie programu Microsoft Genomics jest wyświetlane na moim rachunku?
Microsoft Genomics rachunki na podstawie liczby gigabases przetworzonych na przepływ pracy. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Gdzie można znaleźć listę wszystkich możliwych poleceń `msgen` i argumentów dla klienta?
Pełną listę dostępnych poleceń i argumentów `msgen help`można uzyskać, uruchamiając plik . Jeśli nie podano żadnych dalszych argumentów, zostanie wyświetlona lista `submit` `list`dostępnych `cancel`sekcji `status`pomocy, po jednym dla każdego z , , i . Aby uzyskać pomoc dotyczącą określonego `msgen help command`polecenia, wpisz ; na przykład `msgen help submit` wyświetla listę wszystkich opcji przesyłania.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jakie są najczęściej używane polecenia dla `msgen` klienta?
Najczęściej używane polecenia są argumenty dla `msgen` klienta obejmują: 

 |**Polecenie**          |  **Opis pola** |
 |:--------------------|:-------------         |
 |`list`               |Zwraca listę przesłanych zadań. Aby uzyskać `msgen help list`argumenty, zobacz .  |
 |`submit`             |Przesyła żądanie przepływu pracy do usługi. Aby uzyskać `msgen help submit`argumenty, zobacz .|
 |`status`             |Zwraca stan przepływu pracy określony `--workflow-id`przez program . Zobacz też `msgen help status`. |
 |`cancel`             |Wysyła żądanie anulowania przetwarzania przepływu pracy `--workflow-id`określonego przez program . Zobacz też `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Gdzie mogę uzyskać `--api-url-base`wartość?
Przejdź do witryny Azure portal i otwórz stronę konta Genomics. W nagłówku **Zarządzanie** wybierz pozycję **Klawisze programu Access**. Tam znajdziesz zarówno adres URL interfejsu API, jak i klucze dostępu.

## <a name="where-do-i-get-the-value-for---access-key"></a>Gdzie mogę uzyskać `--access-key`wartość?
Przejdź do witryny Azure portal i otwórz stronę konta Genomics. W nagłówku **Zarządzanie** wybierz pozycję **Klawisze programu Access**. Tam znajdziesz zarówno adres URL interfejsu API, jak i klucze dostępu.

## <a name="why-do-i-need-two-access-keys"></a>Dlaczego potrzebuję dwóch kluczy dostępu?
Potrzebne są dwa klucze dostępu w przypadku, gdy chcesz zaktualizować (ponownie wygenerować) je bez przerywania korzystania z usługi. Na przykład, jeśli chcesz zaktualizować pierwszy klucz, należy mieć wszystkie nowe przepływy pracy użyć drugiego klucza. Następnie poczekaj na zakończenie wszystkich przepływów pracy przy użyciu pierwszego klucza przed zaktualizowaniem pierwszego klucza.

## <a name="do-you-save-my-storage-account-keys"></a>Czy zapisujesz klucze mojego konta magazynu?
Klucz konta magazynu służy do tworzenia tokenów dostępu krótkoterminowego dla usługi Microsoft Genomics do odczytu plików wejściowych i zapisu plików wyjściowych. Domyślny czas trwania tokenu wynosi 48 godzin. Czas trwania tokenu można `-sas/--sas-duration` zmienić za pomocą opcji polecenia submit; wartość jest w godzinach.

## <a name="what-genome-references-can-i-use"></a>Jakich odniesień do genomu mogę użyć?

Odwołania te są obsługiwane:

 |Tematy pomocy              | Wartość`-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (bez analizy alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak sformatować argumenty wiersza polecenia jako plik konfiguracyjny? 

msgen rozumie pliki konfiguracyjne w następującym formacie:
* Wszystkie opcje są dostarczane jako pary klucz-wartość z wartościami oddzielonymi od kluczy dwukropkiem.
  Odstęp jest ignorowany.
* Wiersze `#` zaczynające się od są ignorowane.
* Dowolny argument wiersza polecenia w formacie długim można przekonwertować na klucz, usuwając jego kreski wiodące i zastępując kreski między wyrazami podkreśleniami. Oto kilka przykładów konwersji:

  |Argument wiersza polecenia            | Wiersz pliku konfiguracyjnego |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:KLUCZ*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z microsoft genomics, użyj następujących zasobów:
- Rozpocznij od uruchomienia pierwszego przepływu pracy za pośrednictwem usługi Microsoft Genomics. [uruchamianie przepływu pracy za pośrednictwem usługi Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Prześlij własne dane do przetworzenia przez usługę Microsoft Genomics: [sparowane FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ lub BAM](quickstart-input-multiple.md) 

