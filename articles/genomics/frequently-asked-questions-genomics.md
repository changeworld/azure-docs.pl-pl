---
title: Często zadawane pytania — często zadawane pytania
titleSuffix: Microsoft Genomics
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące korzystania z usługi Microsoft Genomics, w tym informacje techniczne, umowę SLA i rozliczenia.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: troubleshooting
ms.date: 12/07/2017
ms.openlocfilehash: e8806bc4f761214e6740a22093b7e18030fdf881
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986040"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: typowe pytania

W tym artykule wymieniono najważniejsze zapytania, które mogą być powiązane z Microsoft Genomics. Aby uzyskać więcej informacji na temat usługi Microsoft Genomics, zobacz [co to jest Microsoft Genomics?](overview-what-is-genomics.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz nasze [wskazówki dotyczące rozwiązywania problemów](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Jak mogę uruchomić przepływy pracy GATK4 na Microsoft Genomics?
W pliku config. txt usługi Microsoft Genomics Określ process_name do `gatk4`. Pamiętaj, że opłaty są naliczane według zwykłych stawek za rozliczanie.

## <a name="how-do-i-enable-output-compression"></a>Jak mogę włączyć kompresji wyjściowej?
Można skompresować dane wyjściowe VCF lub gvcf za pomocą opcjonalnego argumentu kompresji danych wyjściowych. Jest to równoznaczne z uruchomieniem `-bgzip`, a następnie `-tabix` w danych wyjściowych VCF lub gvcf, aby utworzyć pliki `.gz` (bgzip Output) i `.tbi` (Tabix Output). `bgzip` kompresuje plik VCF lub gvcf, a `tabix` tworzy indeks dla skompresowanego pliku. Argument jest wartością logiczną, która jest domyślnie ustawiona na `false` dla danych wyjściowych VCF i `true` domyślnie dla danych wyjściowych gcvf. Aby użyć w wierszu polecenia, określ `-bz` lub `--bgzip-output` jako `true` (Run bgzip and Tabix) lub `false`. Aby użyć tego argumentu w pliku config. txt, należy dodać do pliku `bgzip_output: true` lub `bgzip_output: false`.

## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co to jest umowa SLA dla Microsoft Genomics?
Gwarantujemy, że przez 99,9% czasu usługa Microsoft Genomics będzie dostępna do odbierania żądań interfejsu API przepływu pracy. Aby uzyskać więcej informacji, zobacz [Umowa SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak użycie Microsoft Genomics jest widoczne na rachunku?
Microsoft Genomics weksle na podstawie liczby przetworzonych gigabaz na przepływ pracy. Aby uzyskać więcej informacji, zobacz [Cennik](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Gdzie można znaleźć listę wszystkich możliwych poleceń i argumentów dla klienta `msgen`?
Pełną listę dostępnych poleceń i argumentów można uzyskać, uruchamiając `msgen help`. Jeśli nie podano żadnych dalszych argumentów, zostanie wyświetlona lista dostępnych sekcji pomocy, jeden dla każdego z `submit`, `list`, `cancel`i `status`. Aby uzyskać pomoc dotyczącą określonego polecenia, wpisz `msgen help command`; na przykład `msgen help submit` zawiera listę wszystkich opcji przesyłania.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Jakie są najczęściej używane polecenia dla klienta `msgen`?
Najczęściej używane polecenia są argumentami dla klienta `msgen` są następujące: 

 |**Polecenie**          |  **Opis pola** |
 |:--------------------|:-------------         |
 |`list`               |Zwraca listę przesłanych zadań. Dla argumentów, zobacz `msgen help list`.  |
 |`submit`             |Przesyła żądanie przepływu pracy do usługi. Dla argumentów, zobacz `msgen help submit`.|
 |`status`             |Zwraca stan przepływu pracy określonego przez `--workflow-id`. Zobacz również `msgen help status`. |
 |`cancel`             |Wysyła żądanie anulowania przetwarzania przepływu pracy określonego przez `--workflow-id`. Zobacz również `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Gdzie można uzyskać wartość dla `--api-url-base`?
Przejdź do Azure Portal i Otwórz stronę konta genomika. W obszarze nagłówek **zarządzania** wybierz pozycję **klucze dostępu**. W tym miejscu znajduje się adres URL interfejsu API i klucze dostępu.

## <a name="where-do-i-get-the-value-for---access-key"></a>Gdzie można uzyskać wartość dla `--access-key`?
Przejdź do Azure Portal i Otwórz stronę konta genomika. W obszarze nagłówek **zarządzania** wybierz pozycję **klucze dostępu**. W tym miejscu znajduje się adres URL interfejsu API i klucze dostępu.

## <a name="why-do-i-need-two-access-keys"></a>Dlaczego są potrzebne dwa klucze dostępu?
Wymagane są dwa klucze dostępu, jeśli chcesz je zaktualizować (wygenerować ponownie) bez przerywania korzystania z usługi. Na przykład jeśli chcesz zaktualizować pierwszy klucz, wszystkie Nowe przepływy pracy powinny używać drugiego klucza. Następnie poczekaj na zakończenie wszystkich przepływów pracy przy użyciu pierwszego klucza przed zaktualizowaniem pierwszego klucza.

## <a name="do-you-save-my-storage-account-keys"></a>Czy chcesz zapisać klucze konta magazynu?
Klucz konta magazynu służy do tworzenia krótkoterminowych tokenów dostępu dla usługi Microsoft Genomics do odczytywania plików wejściowych i zapisywania plików wyjściowych. Domyślny czas trwania tokenu to 48 godzin. Czas trwania tokenu można zmienić za pomocą opcji `-sas/--sas-duration` polecenia Prześlij; wartość jest w godzinach.

## <a name="what-genome-references-can-i-use"></a>Których odwołań genomów mogę używać?

Te odwołania są obsługiwane:

 |Informacje ogólne              | Wartość `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (bez analizy Alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak mogę formatowanie moich argumentów wiersza polecenia jako pliku konfiguracji? 

msgen rozumie pliki konfiguracyjne w następującym formacie:
* Wszystkie opcje są podane jako pary klucz-wartość z wartościami oddzielonymi od kluczy przez dwukropek.
  Biały znak jest ignorowany.
* Wiersze zaczynające się od `#` są ignorowane.
* Każdy argument wiersza polecenia w długim formacie można przekonwertować na klucz, oddzielając jego wiodące myślniki i zastępując łączniki między wyrazami znakami podkreślenia. Poniżej przedstawiono kilka przykładów konwersji:

  |Argument wiersza polecenia            | Wiersz pliku konfiguracji |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key: klucz*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Microsoft Genomics, Skorzystaj z następujących zasobów:
- Zacznij od uruchomienia pierwszego przepływu pracy za pomocą usługi Microsoft Genomics. [Uruchamianie przepływu pracy za pomocą usługi Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Prześlij własne dane do przetwarzania przez usługę Microsoft Genomics: [parowanie FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [wielu FASTQ lub BAM](quickstart-input-multiple.md) 

