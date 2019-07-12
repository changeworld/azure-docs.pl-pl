---
title: 'Microsoft Genomics: Często zadawane pytania — często zadawane pytania | Dokumentacja firmy Microsoft'
titleSuffix: Azure
description: Odpowiedzi na typowe pytania klientów zapytaj o Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: d36a2c6379a95cc67a55c2cc266ced94b4a0179a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672229"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Często zadawane pytania

W tym artykule wymieniono najpopularniejsze zapytania, które być może masz dotyczące Microsoft Genomics. Aby uzyskać więcej informacji na temat usługi Microsoft Genomics, zobacz [co to jest Microsoft Genomics?](overview-what-is-genomics.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz nasze [przewodnik rozwiązywania problemów](troubleshooting-guide-genomics.md). 


## <a name="how-do-i-run-gatk4-workflows-on-microsoft-genomics"></a>Jak uruchomić GATK4 przepływów pracy w usłudze Microsoft Genomics?
W pliku config.txt usługi Microsoft Genomics, należy określić nazwa_procesu do `gatk4`. Należy pamiętać, że opłata zostanie naliczona zgodnie z regularnymi rozliczeń.


## <a name="what-is-the-sla-for-microsoft-genomics"></a>Co to jest umowa SLA dla usługi Microsoft Genomics?
Firma Microsoft gwarantuje 99,9% czasu usługa Microsoft Genomics będą dostępne do odbierania żądań interfejsu API w przepływie pracy. Aby uzyskać więcej informacji, zobacz [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/).

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Jak użycie Microsoft Genomics widoczne na rachunku?
Microsoft Genomics są naliczane na podstawie liczby gigabaz przetworzonych w ramach przepływu pracy. Aby uzyskać więcej informacji, zobacz [ceny](https://azure.microsoft.com/pricing/details/genomics/).


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>Gdzie mogę znaleźć listę wszystkich możliwych poleceń i argumentów `msgen` klienta?
Możesz uzyskać pełną listę dostępnych poleceń i argumentów, uruchamiając `msgen help`. Jeśli nie inne argumenty są podane pokazuje listę dostępnych pomocy sekcje, jednej dla każdego z `submit`, `list`, `cancel`, i `status`. Aby uzyskać pomoc dotyczącą określonego polecenia, wpisz `msgen help command`, na przykład `msgen help submit` zawiera listę wszystkich opcji przesyłania danych.

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>Co to są najczęściej używane polecenia dla `msgen` klienta?
Najczęściej używane polecenia są argumenty `msgen` klienta obejmują: 

 |**Polecenie**          |  **Opis pola** |
 |:--------------------|:-------------         |
 |`list`               |Zwraca listę zadań, które zostały przesłane. Dla argumentów, zobacz `msgen help list`.  |
 |`submit`             |Przesyła żądanie do usługi przepływu pracy. Dla argumentów, zobacz `msgen help submit`.|
 |`status`             |Zwraca stan przepływu pracy, określony przez `--workflow-id`. Zobacz też `msgen help status`. |
 |`cancel`             |Wysyła żądanie anulowania przetwarzania przepływu pracy, określony przez `--workflow-id`. Zobacz też `msgen help cancel`. |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>Gdzie uzyskać wartość `--api-url-base`?
Przejdź do witryny Azure portal i otwórz strony swojego konta usługi Genomics. W obszarze **zarządzania** nagłówka, wybierz **klucze dostępu**. Tam możesz znaleźć adres URL interfejsu API i kluczy dostępu.

## <a name="where-do-i-get-the-value-for---access-key"></a>Gdzie uzyskać wartość `--access-key`?
Przejdź do witryny Azure portal i otwórz strony swojego konta usługi Genomics. W obszarze **zarządzania** nagłówka, wybierz **klucze dostępu**. Tam możesz znaleźć adres URL interfejsu API i kluczy dostępu.

## <a name="why-do-i-need-two-access-keys"></a>Dlaczego potrzebne dwa klucze dostępu?
W przypadku, gdy chcesz zaktualizować (Generuj ponownie), muszą się dwa klucze dostępu do nich bez przerywania użycia usługi. Na przykład jeśli chcesz zaktualizować pierwszy klucz, powinny mieć drugi klucz użyty wszystkich nowych przepływów pracy. Zaczekaj, aż wszystkie przepływy pracy przy użyciu pierwszy klucz, aby zakończyć przed zaktualizowaniem pierwszy klucz.

## <a name="do-you-save-my-storage-account-keys"></a>Czy zapisać Moje klucze konta magazynu?
Klucz konta magazynu jest używane do tworzenia krótkoterminowych tokenów dostępu dla usługi Microsoft Genomics odczytywać pliki wejściowe i zapisywać pliki wyjściowe. Domyślny czas trwania tokenu wynosi 48 godzin. Czas trwania tokenu, można zmienić za pomocą `-sas/--sas-duration` opcji polecenia Prześlij; wartość w godzinach.

## <a name="what-genome-references-can-i-use"></a>Jakie genomu odwołuje się do czego służy?

Te odwołania są obsługiwane:

 |Tematy pomocy              | Wartość `-pa/--process-args` |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38 (żadnej analizy alt) | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>Jak sformatować Moje argumenty wiersza polecenia jako plik konfiguracji? 

modułu msgen rozumie pliki konfiguracyjne w następującym formacie:
* Wszystkie opcje są dostarczane jako pary klucz wartość z wartościami z kluczy rozdzielone średnikiem.
  Odstęp zostanie zignorowany.
* Wiersze rozpoczynające się znakiem `#` są ignorowane.
* Którykolwiek z argumentów wiersza polecenia w formacie długim można przekonwertować z kluczem przez obcięcie jej wiodące kresek i zastępowanie łączniki między wyrazami znakami podkreślenia. Poniżej przedstawiono kilka przykładów konwersji:

  |argument wiersza polecenia            | Wiersz w pliku konfiguracji |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | *api_url_base: https://url*    |
  |`-k/--access-key KEY`            | *access_key:key*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Następne kroki

Użyj następujących zasobów, aby rozpocząć pracę z usługą Microsoft Genomics:
- Rozpoczynanie pracy przez uruchomienie pierwszego przepływu pracy za pomocą usługi Microsoft Genomics. [Uruchamianie przepływu pracy za pośrednictwem usługi Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Przesyłanie danych do przetwarzania przez usługę Microsoft Genomics: [sparowane pliki FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [wiele plików FASTQ lub BAM](quickstart-input-multiple.md) 

