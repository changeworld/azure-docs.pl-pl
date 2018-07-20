---
title: 'Microsoft Genomics: przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft'
titleSuffix: Azure
description: Dowiedz się więcej na temat strategii rozwiązywania problemów
keywords: Rozwiązywanie problemów, błąd, debugowania
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 9bd1690003fd37b6c2edd0f0421cf8d0e74f8cb5
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39144180"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów
W tym omówieniu opisano strategie, aby rozwiązać typowe problemy podczas korzystania z usługi Microsoft Genomics. Aby uzyskać ogólne — często zadawane pytania, zobacz [często zadawane pytania dotyczące](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Jak sprawdzić stan zadania, Moje?
Sprawdź stan przepływu pracy, wywołując `msgen status` z wiersza polecenia, jak pokazano. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Istnieją trzy wymagane argumenty:
* Adresa URL – podstawowy identyfikator URI dla interfejsu API
* KLUCZ — klucz dostępu dla konta usługi Genomics. 
* ID — identyfikator przepływu pracy

Aby znaleźć adres URL i klucz, przejdź do witryny Azure portal i otwórz strony swojego konta usługi Genomics. W obszarze **zarządzania** nagłówka, wybierz **klucze dostępu**. Tam możesz znaleźć adres URL interfejsu API i kluczy dostępu.

Alternatywnie możesz dołączyć ścieżkę do pliku konfiguracji zamiast bezpośrednio wprowadzać adres URL i klucz. Należy pamiętać, że te argumenty zostaną umieszczone w wierszu polecenia, a także plik konfiguracji, argumenty wiersza polecenia będzie mieć pierwszeństwo. 

Po wywołaniu `msgen status`, przyjazny dla użytkownika komunikat zostanie wyświetlony, opisująca, czy przepływ pracy zakończyło się pomyślnie lub co daje przyczynę niepowodzenia zadania. 


## <a name="get-more-information-about-my-workflow-status"></a>Uzyskaj więcej informacji na temat Mój stan przepływu pracy

Aby uzyskać więcej informacji na temat przyczyny zadanie może nie zakończyły się powodzeniem, możesz eksplorować pliki dziennika generowane podczas przepływu pracy. W Twoim kontenerze danych wyjściowych, powinien zostać wyświetlony `[youroutputfilename].logs.zip` folderu.  Rozpakowywanie tego folderu, pojawią się następujące elementy:

* outputFileList.txt — lista pliki wyjściowe utworzone podczas przepływu pracy
* StandardError.txt — ten plik jest pusty.
* StandardOutput.txt — zawiera rejestrowania najwyższego poziomu przepływu pracy. 
* Plików — wszystkie pliki w dziennika GATK `logs` folderu

`standardoutput.txt` Plik jest dobrym miejscem do uruchomienia można określić, dlaczego przepływu pracy nie powiodła się, ponieważ zawiera więcej informacji niskiego poziomu przepływu pracy. 

## <a name="common-issues-and-how-to-resolve-them"></a>Typowe problemy i ich rozwiązania
W tej sekcji przedstawiono pokrótce typowe problemy i ich rozwiązania.

### <a name="fastq-files-are-unmatched"></a>Pliki Fastq są niezgodne
Pliki Fastq powinien różnią się tylko końcowe /1 lub /2 w identyfikatorze próbki. Jeśli przypadkowo prześlesz niedopasowane plików FASTQ, można napotkać następujące komunikaty o błędach podczas wywoływania `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Aby rozwiązać ten problem, przejrzyj, jeśli pliki fastq przesłane do przepływu pracy są faktycznie dopasowane zestawu. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Błąd podczas przekazywania pliku .bam. Wyjściowy obiekt blob już istnieje, a następnie opcję zastępowania została ustawiona na wartość False.
Jeśli zostanie wyświetlony następujący komunikat o błędzie `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, folder wyjściowy zawiera już plik wyjściowy o takiej samej nazwie.  Usuń istniejący plik danych wyjściowych albo włączyć opcję zastąpienia w pliku konfiguracji. Następnie należy ponownie przesłać przepływ pracy.

### <a name="when-to-contact-microsoft-genomics-support"></a>Kiedy należy się z pomocą techniczną firmy Microsoft Genomics
Jeśli zobaczysz następujące komunikaty o błędach, wystąpił błąd wewnętrzny. 

* `Error locating input files on worker machine`
* `Process management failure`

Spróbuj ponownie przesłać przepływ pracy. Jeśli nadal występują błędy zadań lub jeśli masz inne pytania, skontaktuj się z pomocą techniczną usługi Microsoft Genomics w witrynie Azure portal. Dodatkowe informacje na temat przesłać żądanie pomocy technicznej można znaleźć [tutaj](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób rozwiązać typowe problemy związane z usługą Microsoft Genomics. Aby uzyskać więcej informacji i bardziej ogólnych — często zadawane pytania, zobacz [często zadawane pytania dotyczące](frequently-asked-questions-genomics.md). 
