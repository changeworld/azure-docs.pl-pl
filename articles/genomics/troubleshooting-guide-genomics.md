---
title: 'Microsoft genomika: przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft'
titleSuffix: Azure
description: Dowiedz się więcej o strategie rozwiązywania problemów
keywords: Rozwiązywanie problemu dotyczącego błędu, debugowania
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
Tym omówieniu przedstawiono strategie, aby rozwiązać typowe problemy podczas korzystania z usługi Microsoft Genomics. Ogólne — często zadawane pytania dla [często zadawane pytania](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Jak sprawdzić Mój stan zadania?
Sprawdź stan przepływu pracy, przez wywołanie metody `msgen status` z wiersza polecenia, jak pokazano. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Istnieją trzy wymaganych argumentów:
* Adres URL — podstawowy identyfikator URI dla interfejsu API
* KLUCZ — klucz dostępu dla konta genomika. 
* ID — identyfikator przepływu pracy

Znajdowanie adresu URL i klucza, przejdź do portalu Azure i Otwórz stronę genomika konta. W obszarze **zarządzania** nagłówek, wybierz **klucze dostępu**. Możesz znaleźć zarówno adres URL interfejsu API i kluczy dostępu.

Alternatywnie możesz podać ścieżkę do pliku konfiguracji zamiast bezpośrednio wprowadzania adresu URL i klucza. Należy pamiętać, że jeśli w wierszu polecenia, a także plik konfiguracji tych argumentów, argumenty wiersza polecenia będzie mieć pierwszeństwo. 

Po wywołaniu `msgen status`, przyjazny komunikat będą wyświetlane, opisujące, czy przepływ pracy zakończyło się pomyślnie lub podając przyczyny niepowodzenia zadania. 


## <a name="get-more-information-about-my-workflow-status"></a>Uzyskaj więcej informacji o Mój stan przepływu pracy

Aby uzyskać więcej informacji na temat dlaczego zadania może nie zakończyło się pomyślnie, można sprawdzić pliki dziennika utworzone podczas przepływu pracy. W kontenerze Twoje dane wyjściowe, powinna zostać wyświetlona `[youroutputfilename].logs.zip` folderu.  Rozpakować tego folderu, zostaną wyświetlone następujące elementy:

* outputFileList.txt — lista pliki wyjściowe tworzone podczas przepływu pracy
* StandardError.txt — ten plik jest pusty.
* StandardOutput.txt — zawiera rejestrowania najwyższego poziomu przepływu pracy. 
* Pliki — wszystkie pliki w dziennika GATK `logs` folderu

`standardoutput.txt` Plik jest dobrym miejscem do uruchomienia określić, dlaczego przepływu pracy nie powiodła się, ponieważ zawiera więcej informacji niskiego poziomu przepływu pracy. 

## <a name="common-issues-and-how-to-resolve-them"></a>Typowe problemy i ich rozwiązania
W tej sekcji przedstawiono krótkie typowe problemy i ich rozwiązania.

### <a name="fastq-files-are-unmatched"></a>Pliki Fastq są niezgodne
Pliki Fastq tylko powinny różnić się końcowe /1 lub /2 w identyfikatorze próbki. Niedopasowane pliki FASTQ przypadkowo zostały przesłane, może zostać wyświetlone następujące komunikaty o błędach podczas wywoływania metody `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Aby rozwiązać ten problem, przejrzyj, jeśli pliki fastq przesłane do przepływu pracy są faktycznie dopasowane zestawu. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Błąd podczas przekazywania pliku .bam. Istnieje już obiekt blob danych wyjściowych, a opcja zastępowania została ustawiona na False.
Jeśli zostanie wyświetlony komunikat o błędzie następujących, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, folder wyjściowy zawiera już plik wyjściowy o takiej samej nazwie.  Usuń istniejący plik wyjściowy lub Włącz opcję zastępowania w pliku konfiguracji. Następnie prześlij ponownie przepływ pracy.

### <a name="when-to-contact-microsoft-genomics-support"></a>Kiedy należy się z pomocą techniczną firmy Microsoft Genomics
Jeśli widzisz następujące komunikaty o błędach, wystąpił błąd wewnętrzny. 

* `Error locating input files on worker machine`
* `Process management failure`

Spróbuj ponownie przesłać do przepływu pracy. Jeśli nadal błędami zadania lub jakichkolwiek innych pytań, się z pomocą techniczną Microsoft genomika z portalu Azure.

![Skontaktuj się z pomocą techniczną w portalu Azure](./media/troubleshooting-guide/genomics-contact-support.png "się z pomocą techniczną w portalu Azure")

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób rozwiązywania i rozwiązywania typowych problemów z usługą Microsoft Genomics. Aby uzyskać więcej informacji i bardziej ogólne — często zadawane pytania, zobacz [często zadawane pytania](frequently-asked-questions-genomics.md). 