---
title: 'Microsoft Genomics: przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft'
titleSuffix: Azure
description: Dowiedz się więcej na temat strategii rozwiązywania problemów
keywords: Rozwiązywanie problemów, błąd, debugowania
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 78084e6beac7b390b1ea1afe888030c5224856b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790508"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Oto kilka wskazówki dotyczące rozwiązywania problemów dla niektórych typowych problemów, które mogą występować podczas korzystania z usługi Microsoft Genomics modułu MSGEN.

 Często zadawane pytania niezwiązane z rozwiązywania problemów można znaleźć [często zadawane pytania dotyczące](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Znajdź kody błędów skojarzone z przepływem pracy

Możesz znaleźć skojarzone z przepływem pracy przez komunikaty o błędach:

1. Przy użyciu wiersza polecenia i wpisując frazę  `msgen status`
2. Badanie zawartość standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Przy użyciu wiersza polecenia `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Istnieją trzy wymagane argumenty:

* Adresa URL – podstawowy identyfikator URI dla interfejsu API
* KLUCZ — klucz dostępu dla konta usługi Genomics
    * Aby znaleźć adres URL i klucz, przejdź do witryny Azure portal i otwórz strony swojego konta Microsoft Genomics. W obszarze **zarządzania** nagłówka, wybierz **klucze dostępu**. Tam możesz znaleźć adres URL interfejsu API i kluczy dostępu.

  
* ID — identyfikator przepływu pracy
    * Aby znaleźć danego typu Identyfikator przepływu pracy w `msgen list` polecenia. Zakładając, że plik konfiguracji zawiera adres URL i klucze dostępu i znajduje się w tej samej lokalizacji co modułu msgen plików wykonywalnych, polecenie będzie wyglądać następująco: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Dane wyjściowe tego polecenia będzie wyglądać następująco:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Alternatywnie możesz dołączyć ścieżkę do pliku konfiguracji zamiast bezpośrednio wprowadzać adres URL i klucz. Jeśli te argumenty zostaną umieszczone w wierszu polecenia, a także plik konfiguracji, argumenty wiersza polecenia będą miały pierwszeństwo.  

Przepływ pracy o identyfikatorze 1001 i plik config.txt znajduje się w tej samej ścieżce jako modułu msgen pliku wykonywalnego polecenie będzie wyglądać następująco:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Sprawdź zawartość standardoutput.txt 
Znajdź kontenera danych wyjściowych dla danego przepływu pracy. Tworzy modułu MSGEN, `[workflowfilename].logs.zip` folderu po każdego wykonania przepływu pracy. Rozpakuj folder, aby wyświetlić jego zawartość:

* outputFileList.txt — lista pliki wyjściowe utworzone podczas przepływu pracy
* StandardError.txt — ten plik jest pusty.
* StandardOutput.txt — rejestruje wszystkie komunikaty o stanie najwyższego poziomu, w tym błędy, które wystąpiły podczas uruchamiania przepływu pracy.
* Plików — wszystkie pliki w dziennika GATK `logs` folderu

Na potrzeby rozwiązywania problemów Sprawdź zawartość standardoutput.txt i zanotuj wszystkie komunikaty o błędach są wyświetlane.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2: Wypróbuj zalecane czynności dla typowych błędów

W tej sekcji przedstawiono pokrótce typowych błędów danych wyjściowych przez usługę Microsoft Genomics (modułu msgen) i strategie, których można użyć, aby je rozwiązać. 

Usługa Microsoft Genomics (modułu msgen) może zgłosić następujących dwóch rodzajów błędów:

1. Błędy usługi wewnętrznej: Błędy, które są wewnętrzne w usłudze, które mogą nie zostać rozpoznane przez ustalenie parametrów ani plików wejściowych. Czasami Trwa ponowne przesyłanie przepływu pracy może rozwiązać te błędy.
2. Błędy wejścia: Błędy, które można rozwiązać za pomocą poprawne argumenty lub ustalania formatów plików.

### <a name="1-internal-service-errors"></a>1. Błędy usługi wewnętrznej

Wystąpił wewnętrzny błąd usługi nie jest użytkownikiem informacje z możliwością działania. Można ponownie przesłać przepływ pracy, ale jeśli to nie pomoże, skontaktuj się z działem pomocy technicznej Microsoft Genomics

| Komunikat o błędzie                                                                                                                            | Zalecane kroki rozwiązywania problemów                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wystąpił błąd wewnętrzny. Wypróbuj, trwa ponowne przesyłanie przepływu pracy. Jeśli zostanie wyświetlony ten błąd pomocy technicznej usługi Microsoft Genomics poproś o pomoc | Ponownie Prześlij przepływ pracy. Skontaktuj się z Microsoft Genomics Obsługa pomocy, jeśli problem będzie się powtarzać, tworząc zgłoszenie [biletu](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Błędy wejścia

Te błędy są informacje z możliwością działania użytkownika. Na podstawie typu pliku oraz kodu błędu, usługi Microsoft Genomics generuje kody błędów distinct. Wykonaj zalecane kroki rozwiązywania problemów wymienione poniżej.

| Typ pliku | Kod błędu | Komunikat o błędzie                                                                           | Zalecane kroki rozwiązywania problemów                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Dowolne          | 701        | Przeczytaj [readId] ma podstaw [numberOfBases], ale limit [maxReadLength]           | Najbardziej typową przyczyną tego błędu jest uszkodzenie pliku, co prowadzi do łączenia dwóch operacji odczytu. Sprawdź pliki wejściowe. |
| BAM          | 200        |   Nie można odczytać pliku "[yourFileName]".                                                                                       | Sprawdź format plik BAM. Prześlij przepływ pracy ponownie poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 201        |  Nie można odczytać plik BAM [nazwa_pliku].                                                                                      |Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 202        | Nie można odczytać plik BAM [nazwa_pliku]. Zbyt małe i brak nagłówka pliku.                                                                                        | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 203        |   Nie można odczytać plik BAM [nazwa_pliku]. Nagłówek pliku został uszkodzony.                                                                                      |Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 204        |    Nie można odczytać plik BAM [nazwa_pliku]. Nagłówek pliku został uszkodzony.                                                                                     | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                           |
| BAM          | 205        |    Nie można odczytać plik BAM [nazwa_pliku]. Nagłówek pliku został uszkodzony.                                                                                     | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 206        |   Nie można odczytać plik BAM [nazwa_pliku]. Nagłówek pliku został uszkodzony.                                                                                      | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 207        |  Nie można odczytać plik BAM [nazwa_pliku]. Plik obcięte w pobliżu przesunięcia [przesunięcie].                                                                                       | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                            |
| BAM          | 208        |   Nieprawidłowy plik BAM. ReadID [Read_Id] nie ma żadnych sekwencji w pliku [nazwa_pliku].                                                                                      | Sprawdź format plik BAM.  Przesyłanie przepływu pracy przy użyciu poprawnie sformatowanym plikiem.                                                                             |
| FASTQ        | 300        |  Nie można odczytać plików FASTQ. [Nazwa_pliku] nie kończy się znakiem nowego wiersza.                                                                                     | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                           |
| FASTQ        | 301        |   Nie można odczytać plików FASTQ [nazwa_pliku]. Rekord plików FASTQ jest większy niż rozmiar buforu w przesunięciu: [_offset]                                                                                      | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                         |
| FASTQ        | 302        |     Błąd składniowy plików FASTQ. Plik [nazwa_pliku] ma pusty wiersz.                                                                                    | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                         |
| FASTQ        | 303        |       Błąd składniowy plików FASTQ. Plik [nazwa_pliku] zawiera nieprawidłowy znak początkowy przesunięciem: typ wiersza [_offset]: [line_type] znaków: [_char]                                                                                  | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                         |
| FASTQ        | 304      |  Błąd składniowy FASTQ readID [_ReadID].  Odczyt pierwszej partii nie ma readID końcówce /1 w pliku [nazwa_pliku]                                                                                       | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                         |
| FASTQ        | 305        |  Błąd składniowy FASTQ readID [_readID]. Odczyt drugiej partii nie ma readID końcówce /2 w pliku [nazwa_pliku]                                                                                      | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                          |
| FASTQ        | 306        |  Błąd składniowy FASTQ readID [_ReadID]. Przeczytaj pierwszą pary nie ma Identyfikatora, który kończy się /1 w pliku [nazwa_pliku]                                                                                       | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                          |
| FASTQ        | 307        |   Błąd składniowy FASTQ readID [_ReadID]. ReadID nie kończy się znakiem /1 lub / 2. Plik [nazwa_pliku] nie może być używany jako sparowanych plików FASTQ.                                                                                      |Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                          |
| FASTQ        | 308        |  Błąd odczytu plików FASTQ. Odczyty obu końcach odpowiedziała inaczej. Został wybrany poprawny plików FASTQ?                                                                                       | Popraw format plików FASTQ i ponownie Prześlij przepływ pracy.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: Skontaktuj się z działem pomocy technicznej usługi Microsoft Genomics

Jeśli nadal występują błędy zadań lub jeśli masz inne pytania, skontaktuj się z pomocą techniczną usługi Microsoft Genomics w witrynie Azure portal. Dodatkowe informacje na temat przesłać żądanie pomocy technicznej można znaleźć [tutaj](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób rozwiązać typowe problemy związane z usługą Microsoft Genomics. Aby uzyskać więcej informacji i bardziej ogólnych — często zadawane pytania, zobacz [często zadawane pytania dotyczące](frequently-asked-questions-genomics.md). 
