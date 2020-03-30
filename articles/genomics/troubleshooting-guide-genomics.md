---
title: Przewodnik rozwiązywania problemów
titleSuffix: Microsoft Genomics
description: Dowiedz się więcej o strategiach rozwiązywania problemów związanych z korzystaniem z programu Microsoft Genomics, w tym komunikatów o błędach i rozwiązywania ich problemów.
keywords: rozwiązywanie problemów, błąd, debugowanie
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73485997"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Oto kilka wskazówek dotyczących rozwiązywania problemów dotyczących niektórych typowych problemów, które mogą wystąpić podczas korzystania z usługi Microsoft Genomics, MSGEN.

 Aby zapoznać się z często zadawanymi pytaniami, które nie są związane z rozwiązywaniem problemów, zobacz [Często zadawane pytania](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Krok 1: Zlokalizuj kody błędów skojarzone z przepływem pracy

Komunikaty o błędach skojarzone z przepływem pracy można zlokalizować, korzystając z:

1. Korzystanie z wiersza polecenia i wpisywanie`msgen status`
2. Badanie zawartości standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Korzystanie z wiersza polecenia`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Istnieją trzy wymagane argumenty:

* ADRES URL — podstawowy identyfikator URI dla interfejsu API
* KEY - klucz dostępu do twojego konta Genomics
    * Aby znaleźć adres URL i klucz, przejdź do witryny Azure portal i otwórz stronę konta Microsoft Genomics. W nagłówku **Zarządzanie** wybierz pozycję **Klawisze programu Access**. Tam znajdziesz zarówno adres URL interfejsu API, jak i klucze dostępu.

  
* ID - identyfikator przepływu pracy
    * Aby znaleźć typ identyfikatora `msgen list` przepływu pracy w poleceniu. Zakładając, że plik konfiguracyjny zawiera adres URL i klucze dostępu i znajduje się w tym samym miejscu co msgen exe, polecenie będzie wyglądać następująco: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Dane wyjściowe z tego polecenia będą wyglądać następująco:
        
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
  >  Alternatywnie można dołączyć ścieżkę do pliku konfiguracyjnego zamiast bezpośrednio wpisywać adres URL i klucz. Jeśli te argumenty zostaną uwzględnione w wierszu polecenia oraz w pliku konfiguracyjnym, pierwszeństwo mają argumenty wiersza polecenia.  

W przypadku pliku o identyfikatorze przepływu pracy 1001 i pliku config.txt umieszczonego w tej samej ścieżce co plik wykonywalny msgen polecenie będzie wyglądać następująco:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. Zbadaj zawartość standardoutput.txt 
Locate the output container for the workflow in question. MSGEN tworzy `[workflowfilename].logs.zip` folder po każdym wykonaniu przepływu pracy. Rozpaj folder, aby wyświetlić jego zawartość:

* outputFileList.txt - lista plików wyjściowych wyprodukowanych podczas przepływu pracy
* standarderror.txt - ten plik jest pusty.
* standardoutput.txt - rejestruje wszystkie komunikaty o stanie najwyższego poziomu, w tym błędy, które wystąpiły podczas uruchamiania przepływu pracy.
* Pliki dziennika GATK - wszystkie `logs` inne pliki w folderze

Aby rozwiązać problem, sprawdź zawartość standardoutput.txt i zanotuj wszystkie komunikaty o błędach, które się pojawią.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Krok 2: Wypróbuj zalecane kroki dla typowych błędów

W tej sekcji pokrótce przedstawiono typowe błędy wyjściowe przez usługę Microsoft Genomics (msgen) i strategie, których można użyć do ich rozwiązania. 

Usługa Microsoft Genomics (msgen) może zgłaszać następujące dwa rodzaje błędów:

1. Wewnętrzne błędy usługi: Błędy, które są wewnętrzne dla usługi, które mogą nie być rozwiązane przez naprawienie parametrów lub plików wejściowych. Czasami ponowne złożenie pliku roboczego może naprawić te błędy.
2. Błędy wejściowe: Błędy, które można rozwiązać przy użyciu poprawnych argumentów lub ustalania formatów plików.

### <a name="1-internal-service-errors"></a>1. Wewnętrzne błędy serwisowe

Błąd usługi wewnętrznej nie jest możliwe do podjęcia działań przez użytkownika. Możesz ponownie przesłać przepływ pracy, ale jeśli to nie zadziała, skontaktuj się z pomocą techniczną microsoft genomics

| Komunikat o błędzie                                                                                                                            | Zalecane kroki rozwiązywania problemów                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Wystąpił błąd wewnętrzny. Spróbuj ponownie przesłać przepływ pracy. Jeśli ten błąd jest ponownie widoczny, skontaktuj się z pomocą techniczną firmy Microsoft Genomics, aby uzyskać pomoc | Prześlij ponownie przepływ pracy. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft Genomics, aby uzyskać pomoc, ponieważ problem będzie się powtarzał, tworząc [bilet](file-support-ticket-genomics.md )pomocy technicznej. |

### <a name="2-input-errors"></a>2. Błędy wejściowe

Te błędy są możliwe do podjęcia działań przez użytkownika. Na podstawie typu pliku i kodu błędu usługa Microsoft Genomics wyprowadza różne kody błędów. Postępuj zgodnie z zalecanymi krokami rozwiązywania problemów wymienionymi poniżej.

| Typ pliku | Kod błędu | Komunikat o błędzie                                                                           | Zalecane kroki rozwiązywania problemów                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Dowolne          | 701        | Read [readId] ma podstawy [numberOfBases], ale limit to [maxReadLength]           | Najczęstszą przyczyną tego błędu jest uszkodzenie pliku prowadzące do łączenia dwóch odczytów. Sprawdź pliki wejściowe. |
| Bam          | 200        |   Nie można odczytać pliku '[yourFileName]'.                                                                                       | Sprawdź format pliku BAM. Prześlij ponownie przepływ pracy za pomocą prawidłowo sformatowanego pliku.                                                                           |
| Bam          | 201        |  Nie można odczytać pliku BAM [File_name].                                                                                      |Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| Bam          | 202        | Nie można odczytać pliku BAM [File_name]. Plik zbyt mały i brakujący nagłówek.                                                                                        | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| Bam          | 203        |   Nie można odczytać pliku BAM [File_name]. Nagłówek pliku był uszkodzony.                                                                                      |Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                           |
| Bam          | 204        |    Nie można odczytać pliku BAM [File_name]. Nagłówek pliku był uszkodzony.                                                                                     | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                           |
| Bam          | 205        |    Nie można odczytać pliku BAM [File_name]. Nagłówek pliku był uszkodzony.                                                                                     | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| Bam          | 206        |   Nie można odczytać pliku BAM [File_name]. Nagłówek pliku był uszkodzony.                                                                                      | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| Bam          | 207        |  Nie można odczytać pliku BAM [File_name]. Plik obcięty w pobliżu odsunięcia [przesunięcie].                                                                                       | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                            |
| Bam          | 208        |   Nieprawidłowy plik BAM. ReadID [Read_Id] nie ma sekwencji w pliku [File_name].                                                                                      | Sprawdź format pliku BAM.  Prześlij przepływ pracy z poprawnie sformatowanym plikiem.                                                                             |
| FASTQ (W TYM, ŻE)        | 300        |  Nie można odczytać pliku FASTQ. [File_name] nie kończy się na nowej linii.                                                                                     | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                           |
| FASTQ (W TYM, ŻE)        | 301        |   Nie można odczytać pliku FASTQ [File_name]. Rekord FASTQ jest większy niż rozmiar buforu przy przesunięciu: [_offset]                                                                                      | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                         |
| FASTQ (W TYM, ŻE)        | 302        |     Błąd składni FASTQ. Plik [File_name] ma pustą linię.                                                                                    | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                         |
| FASTQ (W TYM, ŻE)        | 303        |       Błąd składni FASTQ. Plik[File_name] ma nieprawidłowy znak początkowy przy przesunięciu: [_offset], typ wiersza: [line_type], znak: [_char]                                                                                  | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                         |
| FASTQ (W TYM, ŻE)        | 304      |  Błąd składni FASTQ przy identyfikatorze readID [_ReadID].  Pierwszy odczyt partii nie ma readID kończącego się na /1 w pliku [File_name]                                                                                       | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                         |
| FASTQ (W TYM, ŻE)        | 305        |  Błąd składni FASTQ przy identyfikatorze readID [_readID]. Drugi odczyt partii nie ma readID kończącego się na /2 w pliku [File_name]                                                                                      | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                          |
| FASTQ (W TYM, ŻE)        | 306        |  Błąd składni FASTQ przy identyfikatorze readID [_ReadID]. Pierwszy odczyt pary nie ma identyfikatora, który kończy się na /1 w pliku [File_name]                                                                                       | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                          |
| FASTQ (W TYM, ŻE)        | 307        |   Błąd składni FASTQ przy identyfikatorze readID [_ReadID]. ReadID nie kończy się na /1 lub/2. Pliku [File_name] nie można użyć jako sparowanego pliku FASTQ.                                                                                      |Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                          |
| FASTQ (W TYM, ŻE)        | 308        |  Błąd odczytu FASTQ. Odczyty obu końców zareagowały inaczej. Czy wybrałeś odpowiednie pliki FASTQ?                                                                                       | Popraw format pliku FASTQ i prześlij przepływ pracy ponownie.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Krok 3: Skontaktuj się z pomocą techniczną microsoft genomics

Jeśli nadal masz błędy w zadaniach lub masz inne pytania, skontaktuj się z pomocą techniczną microsoft genomics z witryny Azure portal. Dodatkowe informacje na temat przesyłania wniosku o pomoc techniczną można znaleźć [tutaj](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak rozwiązywać typowe problemy z usługą Microsoft Genomics i rozwiązywać je. Aby uzyskać więcej informacji i bardziej ogólne często zadawane pytania, zobacz [Często zadawane pytania](frequently-asked-questions-genomics.md). 
