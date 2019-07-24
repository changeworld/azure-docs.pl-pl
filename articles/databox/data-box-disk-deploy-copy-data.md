---
title: Samouczek do kopiowania danych do Azure Data Box Disk | Microsoft Docs
description: Z tego samouczka dowiesz się, jak skopiować dane na urządzenie Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 336cc7dae00d06e38e4be8671f1cb11ed73e5edc
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414646"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Samouczek: kopiowanie danych na urządzenie Azure Data Box Disk i ich weryfikacja

W tym samouczku opisano, jak skopiować dane z komputera hosta, a następnie wygenerować sumy kontrolne, aby sprawdzić integralność danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kopiowanie danych na urządzenie Data Box Disk
> * Weryfikacja danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:
- Ukończono [Samouczek: instalowanie i konfigurowanie urządzenia Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- Twoje dyski są odblokowane i podłączone do komputera klienckiego.
- Na komputerze klienckim używanym do kopiowania danych na dyski musi być uruchomiony [obsługiwany system operacyjny](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Upewnij się, że typ magazynu wybrany na potrzeby danych jest jednym z [obsługiwanych typów magazynu](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Zapoznaj [się z limitami dysku zarządzanego w obszarze limity rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Kopiowanie danych na dyski

Przed skopiowaniem danych na dyski należy zapoznać się z następującymi kwestiami:

- Twoim obowiązkiem jest zapewnienie, że dane zostały skopiowane do folderów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob zostały skopiowane do folderu BlockBlob. Jeśli format danych nie pasuje do folderu (typu magazynu), na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
- Podczas kopiowania danych upewni się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Azure storage and Data Box Disk limits (Ograniczenia usług Azure Storage i Data Box Disk)](data-box-disk-limits.md).
- Jeśli dane przekazywane przy użyciu usługi Data Box Disk będą jednocześnie przekazywane przez inne aplikacje, poza usługą Data Box Disk, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.

   > [!IMPORTANT]
   >  W przypadku określenia dysków zarządzanych jako jednego z lokalizacji docelowych magazynu podczas tworzenia kolejności, ma zastosowanie następująca sekcja.

- Można mieć tylko jeden dysk zarządzany o danej nazwie w grupie zasobów we wszystkich pretworzonych folderach i we wszystkich Data Box Disk. Oznacza to, że wirtualne dyski twarde przekazane do folderów pretworzonych powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa nie jest zgodna z już istniejącym dyskiem zarządzanym w grupie zasobów. Jeśli wirtualne dyski twarde mają takie same nazwy, tylko jeden dysk VHD jest konwertowany na dysk zarządzany o tej nazwie. Pozostałe wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob do konta magazynu przemieszczania.
- Zawsze Kopiuj wirtualne dyski twarde do jednego z utworzonych folderów. W przypadku kopiowania dysków VHD poza tymi folderami lub w utworzonym folderze wirtualne dyski twarde są przekazywane do konta usługi Azure Storage jako stronicowe obiekty blob i dyski niezarządzane.
- Do tworzenia dysków zarządzanych można przekazać tylko stałe wirtualne dyski twarde. Dynamiczne dyski VHD, różnicowe dyski VHD lub pliki VHDX nie są obsługiwane.


Aby podłączyć urządzenia Data Box Disk do komputera i skopiować na nie dane, wykonaj poniższe czynności.

1. Wyświetl zawartość odblokowanego dysku. Lista pretworzonych folderów i podfolderów w stacji jest różna w zależności od opcji wybranych podczas umieszczania kolejności Data Box Disk.

    |Wybrane miejsce docelowe magazynu  |Typ konta magazynu|Typ konta magazynu przemieszczania |Foldery i podfoldery  |
    |---------|---------|---------|------------------|
    |Konto magazynu     |GPv1 lub GPv2                 | Nie dotyczy | BlockBlob <br> PageBlob <br> AzureFile        |
    |Konto magazynu     |Konto magazynu obiektów BLOB         | Nie dotyczy | BlockBlob        |
    |Dyski zarządzane     |Nie dotyczy | GPv1 lub GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Konto magazynu <br> Dyski zarządzane     |GPv1 lub GPv2 | GPv1 lub GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Konto magazynu <br> Dyski zarządzane    |Konto magazynu obiektów BLOB | GPv1 lub GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Poniżej przedstawiono przykład zrzutu ekranu zamówienia, w którym określono konto magazynu GPv2:

    ![Zawartość stacji dysków](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Skopiuj dane, które muszą zostać zaimportowane jako blokowe obiekty blob w folderze do *BlockBlob* . Podobnie skopiuj dane, takie jak VHD/VHDX, do folderu *PageBlob* i danych w folderze do *AzureFile* .

    Dla każdego podfolderu w folderach BlockBlob i PageBlob zostanie utworzony kontener na koncie usługi Azure Storage. Wszystkie pliki w folderach BlockBlob i PageBlob zostaną skopiowane do domyślnego kontenera `$root` na koncie usługi Azure Storage. Pliki w kontenerze `$root` są zawsze przekazywane jako blokowe obiekty blob.

   Skopiuj pliki do folderu w folderze *AzureFile* . Podfolder w folderze *AzureFile* tworzy element przestawny. Pliki skopiowane bezpośrednio do folderu *AzureFile* nie powiodą się i są przekazywane jako blokowe obiekty blob.

    Jeśli w katalogu głównym istnieją jakiekolwiek pliki lub foldery, należy przenieść je do innego folderu przed rozpoczęciem kopiowania danych.

    > [!IMPORTANT]
    > Wszystkie kontenery, obiekty blob i nazwy plików powinny być zgodne z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). W przypadku niezgodności z tymi regułami przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

3. Podczas kopiowania plików upewnij się, że pliki nie przekraczają ~ 4,7 TiB dla blokowych obiektów blob, ~ 8 TiB dla stronicowych obiektów blob i ~ 1 TiB dla Azure Files. 
4. Możesz skopiować dane, przeciągając je i upuszczając w Eksploratorze plików. Możesz też skopiować dane za pomocą dowolnego narzędzia kopiowania danych zgodnego z SMB, na przykład narzędzia Robocopy. Aby rozpocząć wiele zadań kopiowania, użyj następującego polecenia narzędzia Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Parametry i opcje tego polecenia przedstawiono w poniższej tabeli:
    
    |Parametry/opcje  |Opis |
    |--------------------|------------|
    |Source            | Określa ścieżkę do katalogu źródłowego.        |
    |Miejsce docelowe       | Określa ścieżkę do katalogu docelowego.        |
    |/E                  | Kopiuje podkatalogi, włącznie z pustymi katalogami. |
    |/MT[:N]             | Tworzy kopie wielowątkowe zawierające N wątków, gdzie N to liczba całkowita od 1 do 128. <br>Domyślna wartość N to 8.        |
    |® \<N >             | Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania. Domyślna wartość N to 1 000 000 (milion ponownych prób).        |
    |/W: \<N >             | Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach. Domyślna wartość N to 30 (30 sekund oczekiwania).        |
    |/NFL                | Określa, że nazwy plików nie mają być rejestrowane w dzienniku.        |
    |/NDL                | Określa, że nazwy katalogów nie mają być rejestrowane w dzienniku.        |
    |/FFT                | Zakłada czas plików w formacie FAT (dokładność do dwóch sekund).        |
    |/Log:\<plik dziennika >     | Zapisuje dane wyjściowe stanu w pliku dziennika (zastępuje istniejący plik dziennika).         |

    Można użyć równolegle wielu dysków, uruchamiając wiele zadań na każdym z nich.

6. Gdy zadanie będzie w toku, sprawdzaj stan kopiowania. Poniższy przykład przedstawia dane wyjściowe polecenia kopiowania plików na urządzenie Data Box Disk za pomocą narzędzia Robocopy.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych.

    |    Platforma    |    Przeważnie małe pliki < 512 KB                           |    Przeważnie średnie pliki od 512 KB do 1 MB                      |    Przeważnie duże pliki > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 sesje narzędzia Robocopy* <br> 16 wątków na sesję    |    2 sesje narzędzia Robocopy* <br> 16 wątków na sesję    |    2 sesje narzędzia Robocopy* <br> 16 wątków na sesję    |
    
    **Każda sesja narzędzia Robocopy może mieć maksymalnie 7000 katalogów i 150 milionów plików.*
    
    >[!NOTE]
    > Sugerowane powyżej parametry są oparte na środowisku używanym do testowania przeprowadzanego we własnym zakresie.
    
    Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

6. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki dziennika, które pomogą w rozwiązywaniu problemów. Pliki dziennika znajdują się w lokalizacji określonej w narzędziu Robocopy.
 
### <a name="split-and-copy-data-to-disks"></a>Dzielenie i kopiowanie danych na dyski

Ta opcjonalna procedura może być używana w przypadku korzystania z wielu dysków i dużego zestawu danych, który należy podzielić i skopiować na wszystkie dyski. Narzędzie do dzielenia skopiowanych dysków Data Box pomaga w dzieleniu i kopiowaniu danych na komputerze z systemem Windows.

>[!IMPORTANT]
> Narzędzie do dzielenia skopiowanych dysków Data Box przeprowadza również walidację danych. Jeśli używasz narzędzia do dzielenia skopiowanych dysków Data Box do kopiowania danych, możesz pominąć [krok walidacji](#validate-data).
> Narzędzie Split Copy nie jest obsługiwane w przypadku dysków zarządzanych.

1. Upewnij się, że narzędzie do dzielenia skopiowanych dysków Data Box zostało pobrane i wyodrębnione w folderze lokalnym na komputerze z systemem Windows. To narzędzie zostało pobrane podczas pobierania zestawu narzędzi Data Box Disk dla systemu Windows.
2. Otwórz Eksploratora plików. Zanotuj literę dysku źródła danych i litery dysków przypisane do usługi Data Box Disk. 

     ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Określ źródło danych do skopiowania. Na przykład w tym przypadku:
    - Zidentyfikowano następujące dane blokowego obiektu blob.

         ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Zidentyfikowano następujące dane stronicowego obiektu blob.

         ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Przejdź do folderu, w którym wyodrębniono oprogramowanie. Znajdź plik `SampleConfig.json` w tym folderze. Jest to plik tylko do odczytu, który można modyfikować i zapisywać.

   ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Zmodyfikuj plik `SampleConfig.json`.
 
   - Podaj nazwę zadania. Spowoduje to utworzenie folderu w usłudze Data Box Disk, który w ostateczności stanie się kontenerem na koncie usługi Azure Storage powiązanym z tymi dyskami. Nazwa zadania musi być zgodna z konwencjami nazewnictwa kontenera platformy Azure. 
   - Podaj ścieżkę źródłową, zapisując format ścieżki w pliku `SampleConfigFile.json`. 
   - Wprowadź litery dysku odpowiadające dyskom docelowym. Dane zostaną pobrane ze ścieżki źródłowej i skopiowane na wiele dysków.
   - Podaj ścieżkę dla plików dziennika. Domyślnie są one wysyłane do bieżącego katalogu, w którym znajduje się plik `.exe`.

     ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Aby zweryfikować format pliku, przejdź do narzędzia `JSONlint`. Zapisz plik jako `ConfigFile.json`. 

     ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Otwórz okno wiersza polecenia. 

8. Uruchom plik `DataBoxDiskSplitCopy.exe`. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Naciśnij klawisz Enter, aby kontynuować wykonywanie skryptu.

    ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. W czasie dzielenia i kopiowania zestawu danych zostanie wyświetlone podsumowanie sesji kopiowania przekazane przez narzędzie do dzielenia skopiowanych dysków. Poniżej pokazano przykładowe dane wyjściowe.

    ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Upewnij się, że dane zostały rozdzielone między dyski docelowe. 
 
    ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Jeśli sprawdzisz zawartość dysku `n:`, zobaczysz, że dwa tworzone podfoldery odpowiadają formatowi danych blokowego obiektu blob i stronicowego obiektu blob.
    
     ![Dzielenie skopiowanych danych](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Jeśli sesja kopiowania zakończy się niepowodzeniem, użyj następującego polecenia, aby odzyskać dane i wznowić operację:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Jeśli widzisz błędy za pomocą narzędzia Split Copy, przejdź do sekcji jak [rozwiązywać problemy](data-box-disk-troubleshoot-data-copy.md)z wydzielonymi narzędziami kopiowania.

Po zakończeniu kopiowania danych możesz przejść do walidowania danych. Jeśli użyto narzędzia do dzielenia skopiowanych plików, pomiń walidację (przeprowadza ją również narzędzie do dzielenia skopiowanych plików) i przejdź do następnego samouczka.


## <a name="validate-data"></a>Sprawdzanie poprawności danych

Jeśli nie użyto narzędzia do dzielenia skopiowanych plików, musisz przeprowadzić walidację danych. Aby zweryfikować dane, wykonaj następujące czynności:

1. Uruchom polecenie `DataBoxDiskValidation.cmd` w celu zweryfikowania sumy kontrolnej w folderze *DataBoxDiskImport* na dysku.
    
    ![Dane wyjściowe narzędzia walidacji dysków Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Wybierz odpowiednią opcję. **Zalecamy, aby zawsze przy wykonywaniu walidacji plików i generowaniu sum kontrolnych wybierać opcję 2**. Czas wykonywania tej operacji zależy od rozmiaru danych. Po zakończeniu wykonywania skryptu zamknij okno polecenia. Jeśli podczas walidacji i generowania sumy kontrolnej wystąpi błąd, zostanie wyświetlone powiadomienie i podany link do dzienników błędów.

    ![Dane wyjściowe sumy kontrolnej](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Zresetuj narzędzie między dwoma uruchomieniami.
    > - Użyj opcji 1, jeśli obsługujesz duży zestaw danych zawierający małe pliki (~ KB/s). Ta opcja powoduje tylko walidowanie plików, ponieważ generowanie sumy kontrolnej może zająć bardzo dużo czasu i spowodować spadek wydajności.

3. Jeśli używasz wielu dysków, uruchom to polecenie dla każdego dysku.

Jeśli widzisz błędy podczas walidacji, zobacz [Rozwiązywanie problemów z błędami walidacji](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Kopiowanie danych na urządzenie Data Box Disk
> * Weryfikowanie integralności danych

Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box Disk i zweryfikować przekazanie danych na platformę Azure.

> [!div class="nextstepaction"]
> [Wysyłka zwrotna urządzenia Azure Data Box do firmy Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-disks"></a>Kopiowanie danych na dyski

Wykonaj następujące kroki, aby nawiązać połączenie i skopiować dane z komputera do Data Box Disk.

1. Wyświetl zawartość odblokowanego dysku. Lista pretworzonych folderów i podfolderów w stacji jest różna w zależności od opcji wybranych podczas umieszczania kolejności Data Box Disk.
2. Skopiuj dane do folderów, które odpowiadają odpowiedniemu formatowi danych. Na przykład skopiuj dane bez struktury do folderu *BlockBlob* folderu, VHD lub VHDX do folderu *PageBlob* i plików do *AzureFile*. Jeśli format danych nie jest zgodny z odpowiednim folderem (typ magazynu), to w późniejszym kroku dane zostaną przekazane do platformy Azure.

    - Dla każdego podfolderu w folderach BlockBlob i PageBlob zostanie utworzony kontener na koncie usługi Azure Storage. Wszystkie pliki w folderach *BlockBlob* i *PageBlob* są kopiowane do domyślnego kontenera $root w ramach konta usługi Azure Storage. 
    - Wszystkie pliki w kontenerze $root są zawsze przekazywane jako blokowe obiekty blob.
    - Skopiuj pliki do folderu w folderze *AzureFile* . Podfolder w folderze *AzureFile* tworzy element przestawny. Pliki skopiowane bezpośrednio do folderu *AzureFile* nie powiodą się i są przekazywane jako blokowe obiekty blob.
    - Jeśli w katalogu głównym istnieją jakiekolwiek pliki lub foldery, należy przenieść je do innego folderu przed rozpoczęciem kopiowania danych.
    - Jeśli zamówienie ma Managed Disks jako jeden z miejsc docelowych magazynu, zobacz Konwencje nazewnictwa dla [dysków zarządzanych](data-box-disk-limits.md#managed-disk-naming-conventions).

    > [!IMPORTANT]
    > Wszystkie kontenery, obiekty blob i pliki powinny być zgodne z [konwencjami nazewnictwa platformy Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) i [limitami rozmiaru obiektów platformy Azure](data-box-disk-limits.md#azure-object-size-limits). Jeśli te reguły lub limity nie są przestrzegane, przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

3. Użyj funkcji przeciągania i upuszczania za pomocą Eksploratora plików lub dowolnego narzędzia do kopiowania plików zgodnego z protokołem SMB, takiego jak Robocopy, aby skopiować dane. Można inicjować wiele zadań kopiowania przy użyciu następującego polecenia:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki dziennika, które pomogą w rozwiązywaniu problemów. Pliki dziennika znajdują się w lokalizacji określonej w narzędziu Robocopy.

Użyj opcjonalnej procedury [dzielenia i kopiowania](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) w przypadku korzystania z wielu dysków i posiadania dużego zestawu danych, który musi być podzielony i kopiowany na wszystkich dyskach.

## <a name="validate-data"></a>Sprawdzanie poprawności danych

Aby sprawdzić dane, wykonaj następujące czynności.

1. Uruchom polecenie `DataBoxDiskValidation.cmd` w celu zweryfikowania sumy kontrolnej w folderze *DataBoxDiskImport* na dysku.
2. Użyj opcji 2, aby zweryfikować pliki i wygenerować sumy kontrolne. Czas wykonywania tej operacji zależy od rozmiaru danych. Jeśli podczas walidacji i generowania sumy kontrolnej wystąpi błąd, zostanie wyświetlone powiadomienie i podany link do dzienników błędów.

    Jeśli widzisz błędy podczas walidacji, zobacz [Rozwiązywanie problemów z błędami walidacji](data-box-disk-troubleshoot.md).

::: zone-end
