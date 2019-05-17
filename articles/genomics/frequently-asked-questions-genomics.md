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
ms.openlocfilehash: 2bcbf9d145d9b8b5a3c42893235906d24516405c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792632"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft Genomics: Często zadawane pytania

W tym artykule wymieniono najpopularniejsze zapytania, które być może masz dotyczące Microsoft Genomics. Aby uzyskać więcej informacji na temat usługi Microsoft Genomics, zobacz [co to jest Microsoft Genomics?](overview-what-is-genomics.md). Aby uzyskać więcej informacji na temat rozwiązywania problemów, zobacz nasze [przewodnik rozwiązywania problemów](troubleshooting-guide-genomics.md). 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>Co to jest wspieranie GATK 4 usługi Microsoft Genomics?
Do 30 czerwca 2019 usługi Microsoft Genomics oferuje 20 przebiegów WGS z GATK4 bez ponoszenia kosztów. Aby uczestniczyć w rejestrze tej oferty [tutaj](https://aka.ms/msgatk4). 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>Jakie są typowe problemy, które można napotkać podczas uruchamiania Microsoft Genomics service GATK4 promocji
Poniżej przedstawiono listę typowych błędów, które mogą wystąpić i ich zalecane rozwiązania:

| **Komunikat**                                                                                                                                                                                    | **Przyczyna**                                                                                                    | **Rozdzielczość**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `gatk4-promo` nie włączono dla swojego konta. Aby uzyskać więcej informacji zobacz https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | Próbujesz uruchomić GATK4 przepływy pracy z usługą Microsoft Genomics bez aktywowany.       | Odwiedź stronę [tutaj](https://aka.ms/msgatk4) umożliwiający aktywację konta usługi. Należy pamiętać, że wersja próbna wygaśnie 30 czerwca 2019 r. Nie można aktywować konto dla przebiegów promocyjne po tej dacie. |
| Dziękujemy za podjęcie próby `gatk4-promo`. Okres próbny zakończył się. Aby uzyskać więcej informacji https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | Wersja próbna GATK4 wygasł na koniec roku kalendarzowego i próby wywołania `gatk4-promo` nazwa_procesu.  | Przełącz parametru nazwa_procesu, `gatk4`, zamiast `gatk4-promo`. To jest wersja gatk4 oficjalne, a przepływ pracy zostanie naliczona, jeśli użyjesz tego parametru.                                         |
| Dziękujemy za podjęcie próby `gatk4-promo` wykorzystano wszystkie przydzielone przebiegów. Aby uzyskać więcej informacji zobacz https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | Pomyślnie przesłano wszystkie swoje 20 promocyjne działa przez GATK4.                               | Prześlij wszystkie nowe gatk4 działa z wartością argumentu nazwa_procesu `gatk4` zamiast `gatk4-promo`. Przepływ pracy zostanie naliczony podczas korzystania z tego parametru.                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>Można uruchomić przepływy pracy GATK4 usłudze Microsoft Genomics bez rejestrowania się w celu promowania GATK4?
Tak, w pliku config.txt usługi Microsoft Genomics, określ nazwa_procesu do `gatk4`. Należy zauważyć, że opłata zostanie naliczona zgodnie z regularnymi rozliczeń i uruchamia 20 bezpłatnej nie dotyczą konta usługi Microsoft Genomics.



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

 |Odwołanie              | Wartość `-pa/--process-args` |
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
  |`-u/--api-url-base https://url`  | *api_url_base:https://url*    |
  |`-k/--access-key KEY`            | *access_key:key*              |      
  |`-pa/--process-args R=B37m1`     | *process_args:R-b37m1*        |  

## <a name="next-steps"></a>Kolejne kroki

Użyj następujących zasobów, aby rozpocząć pracę z usługą Microsoft Genomics:
- Rozpoczynanie pracy przez uruchomienie pierwszego przepływu pracy za pomocą usługi Microsoft Genomics. [Uruchamianie przepływu pracy za pośrednictwem usługi Microsoft Genomics](quickstart-run-genomics-workflow-portal.md)
- Przesyłanie danych do przetwarzania przez usługę Microsoft Genomics: [sparowane pliki FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [wiele plików FASTQ lub BAM](quickstart-input-multiple.md) 

