---
title: Znane problemy i Podręcznik rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Listę znanych problemów i przewodnik rozwiązywania problemów z
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: dc57509475634b6a8038179dbb205533c3ea9d99
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35938453"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Usługi Azure Machine Learning Workbench — znane problemy i Podręcznik rozwiązywania problemów 
Ten artykuł ułatwia znajdowanie i poprawianie błędów lub błędy w ramach korzystania z aplikacji Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Znajdź numer kompilacji aplikacji Workbench
Podczas komunikowania się z zespołem pomocy technicznej, należy dołączyć numer kompilacji aplikacji Workbench. W Windows, można znaleźć numer kompilacji, klikając **pomocy** menu i wybrać **Azure ML Workbench**. W systemie macOS, możesz kliknąć **aplikacji Azure ML Workbench** menu i wybrać **Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>MSDN Forum usługi Machine Learning
Mamy Forum MSDN, możesz zadać pytania. Zespół pracujący nad produktem, które aktywnie monitoruje forum. Forum, adres URL jest [ https://aka.ms/azureml-forum ](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Zbierz informacje diagnostyczne
Czasami może być przydatne Jeśli podasz informacje diagnostyczne podczas pytania o pomoc. Oto, miejsca zamieszkania pliki dziennika:

### <a name="installer-log"></a>Dziennika Instalatora
Jeśli napotkasz problem podczas instalacji Instalator pliki dziennika znajdują się w tym miejscu:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Można skompresować się zawartość tych katalogów i Wyślij do nas, diagnostyki.

### <a name="workbench-desktop-app-log"></a>Dziennik aplikacji klasycznej aplikacji Workbench
Jeśli masz problemy z zalogowaniem lub jeśli wystąpiła awaria pulpitu Workbench, można znaleźć w tym miejscu pliki dziennika:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Można skompresować się zawartość tych katalogów i Wyślij do nas, diagnostyki.

### <a name="experiment-execution-log"></a>Dziennik wykonywania eksperymentów
Jeśli konkretnego skryptu zakończy się niepowodzeniem podczas przesyłania z aplikacji klasycznej, spróbuj ponownie przesłać go za pomocą interfejsu wiersza polecenia `az ml experiment submit` polecenia. To powinien zapewnić pełną treść błędu w formacie JSON, a co najważniejsze zawiera **identyfikator operacji** wartość. Wyślij do nas w tym pliku JSON **identyfikator operacji** i firma Microsoft może pomóc w diagnozowaniu. 

Jeśli konkretnego skryptu zakończy się pomyślnie w przesyłania, ale nie powiedzie się podczas wykonywania, powinno wyświetlić **identyfikator uruchomienia** do identyfikowania danego uruchomienia. Można spakować odpowiednich plików dziennika przy użyciu następującego polecenia:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` Polecenie generuje `diagnostics.zip` pliku w folderze głównym projektu. SPAKOWANY pakiet zawiera całego folderu projektu w stanie w czasie, które wykonano, a także informacje o rejestrowaniu. Pamiętaj usunąć poufne informacje, których nie chcesz do uwzględnienia przed wysłaniem nam pliku diagnostyki.

## <a name="send-us-a-frown-or-a-smile"></a>Wyślij grymas niezadowolenia (lub uśmiech)

Podczas pracy w aplikacji Azure ML Workbench możesz wysłać nam grymas niezadowolenia (lub uśmiech), klikając ikony buźki w lewym dolnym rogu powłoka aplikacji. Opcjonalnie można podać swój adres e-mail (dzięki czemu będzie można ponownie do Ciebie), i/lub zrzut ekranu przedstawiający bieżącego stanu. 

## <a name="known-service-limits"></a>Limity usługi znane
- Maksymalny dopuszczalny rozmiar folderu projektu: 25 MB.
    >[!NOTE]
    >To ograniczenie nie ma zastosowania do `.git`, `docs` i `outputs` folderów. Te nazwy folderów jest rozróżniana wielkość liter. Jeśli pracujesz z dużymi plikami, zapoznaj się [wprowadzeniem trwałych zmian i postępowanie z dużymi plikami](../desktop-workbench/how-to-read-write-files.md).

- Maksymalny dopuszczalny czas wykonywania eksperymentu: 7 dni

- Maksymalny rozmiar pliku śledzonych `outputs` folderu po uruchomieniu: 512 MB
  - Oznacza to, jeśli skrypt generuje plik przekracza 512 MB w folderze danych wyjściowych, go nie są zbierane istnieje. Jeśli pracujesz z dużymi plikami, zapoznaj się [wprowadzeniem trwałych zmian i postępowanie z dużymi plikami](../desktop-workbench/how-to-read-write-files.md).

- Klucze SSH są nieobsługiwane, łącząc się z komputera zdalnego lub klaster Spark za pośrednictwem protokołu SSH. Tryb tylko do nazwy użytkownika/hasła jest obecnie obsługiwane.

- Korzystając z klastrów HDInsight, jako cel obliczenia, należy użyć w niej usługi Azure blob jako magazynu głównego. Za pomocą usługi Azure Data Lake Storage nie jest obsługiwana.

- Klastrowanie przekształcenia tekstu nie są obsługiwane na komputerze Mac.

- Biblioteka biblioteki jest obsługiwana tylko w systemach Windows i Linux (w kontenerach platformy Docker). Nie jest obsługiwana w systemie macOS.

- Notesy Jupyter notebook mają limit maksymalnego rozmiaru w większych niż 5 MB, podczas otwierania je z aplikacji Workbench. Możesz otworzyć dużych notesów z interfejsu wiersza polecenia za pomocą polecenia "az ml notesu start" i komórki czyste dane wyjściowe Zmniejsz rozmiar pliku.

## <a name="cant-update-workbench"></a>Nie można zaktualizować aplikacji Workbench
Gdy dostępna jest nowa aktualizacja, strony głównej aplikacji Workbench wyświetla komunikat z informacją o nowych aktualizacji. Powinien zostać wyświetlony wskaźnik aktualizacji znajdujących się w lewym dolnym rogu aplikacji na ikonę dzwonka. Kliknij pozycję wskaźnika i wykonaj instrukcje kreatora Instalatora, aby zainstalować aktualizację. 

![Aktualizowanie obrazu](./media/known-issues-and-troubleshooting-guide/update.png)

Jeśli nie zostanie wyświetlone powiadomienie, spróbuj ponownie uruchomić aplikację. Jeśli nadal nie widzisz powiadomienie o aktualizacji po ponownym uruchomieniu, może istnieć kilka przyczyn.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Uruchamiasz aplikacji Workbench przy użyciu skrótu przypiętych na pasku zadań
Może być już zainstalowano aktualizację. Jednak skrót przypiętych wciąż wskazuje starej bity na dysku. Można to sprawdzić, przechodząc do `%localappdata%/AmlWorkbench` folder i zobaczyć, czy masz najnowszą wersję na nim zainstalowane i sprawdź właściwość przypiętych skrótów, aby zobaczyć, gdzie wskazuje. Zweryfikowane, po prostu usunąć stare skrótu, uruchom aplikację Workbench w menu Start i opcjonalnie utworzyć nowy skrót przypiętych na pasku zadań.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Zainstalowano aplikację Workbench, korzystając z linku "Zainstaluj aplikację Azure ML Workbench" w Windows DSVM
Niestety jest nie łatwe poprawki na tym typie. Należy wykonać poniższe kroki, aby usunąć zainstalowane usługi bits i Pobierz najnowszą wersję Instalatora do zainstalowania nowej aplikacji Workbench: 
   - Usuń folder `C:\Users\<Username>\AppData\Local\amlworkbench`
   - Usuń skrypt `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Usuń skrót, który uruchamia powyższy skrypt
   - Pobierz Instalatora https://aka.ms/azureml-wb-msi i ponowne zainstalowanie.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Zablokowane na ekranie "Sprawdzanie konta eksperymentowania" po zalogowaniu
Po zalogowaniu się w aplikacji Workbench może zatrzymywane w pustego ekranu z komunikatem przedstawiający "Sprawdzanie konta eksperymentowania" kółko obrotowych. Aby rozwiązać ten problem, wykonaj następujące czynności:
1. Zamykanie aplikacji
2. Usuń następujący plik:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Uruchom ponownie aplikację.

## <a name="cant-delete-experimentation-account"></a>Nie można usunąć konta eksperymentowania
Można użyć interfejsu wiersza polecenia, można usunąć konta eksperymentowania, ale należy usunąć podrzędnych obszary robocze i projekty podrzędne w tych obszarach roboczych podrzędnych najpierw. W przeciwnym razie zostanie wyświetlony błąd "nie można usunąć zasobów przed usunięciem zagnieżdżonymi zasobami."

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <workspace name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <workspace name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Można również usunąć te projekty i obszarami roboczymi z poziomu aplikacji Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Nie można otworzyć pliku, jeśli projekt w usłudze OneDrive
Jeśli masz systemu Windows 10 Fall Creators Update, a projekt jest tworzony w folderze lokalnym mapowane do usługi OneDrive, może się okazać, że nie można otworzyć dowolny plik w aplikacji Workbench. Jest to spowodowane usterkę, wynikające z aktualizacji Fall Creators Update, który powoduje, że kod node.js, aby zakończyć się niepowodzeniem w folderze usługi OneDrive. Błąd problem zostanie rozwiązany wkrótce przez usługę Windows update, ale do tego czasu, nie twórz projekty w folderze usługi OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nazwa pliku zbyt długo na Windows
Jeśli używasz aplikacji Workbench w Windows, może wystąpić domyślny limit długości nazwy maksymalnej pliku 260 znaków, które można przedstawić jako błąd "system nie może odnaleźć określonej ścieżki". Można zmodyfikować ustawienie klucza rejestru, aby zezwolić na znacznie dłużej nazwa ścieżki pliku. Przegląd [w tym artykule](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) więcej informacji na temat sposobu ustawiania _MAX_PATH_ klucza rejestru.

## <a name="interrupt-cli-execution-output"></a>Przerwanie dane wyjściowe wykonania interfejsu wiersza polecenia
Jeśli uruchamiał eksperymentowania uruchamiane przy użyciu `az ml experiment submit` lub `az ml notebook start` i chcesz przerwać dane wyjściowe: 
- Na Windows użyj kombinacji klawiszy Ctrl-Break z klawiatury
- W systemie macOS użyj klawiszy Ctrl-C.

Należy pamiętać, to tylko przerywa strumienia wyjściowego, w oknie interfejsu wiersza polecenia. Go nie faktycznie Zatrzymuje zadanie, które jest wykonywane. Aby anulować bieżące zadanie, należy użyć `az ml experiment cancel -r <run_id> -t <target name>` polecenia.

W przypadku komputerów Windows za pomocą klawiatury, które nie mają klucza podziału możliwe alternatywy obejmują Fn B, Ctrl-Fn-B lub Fn + Esc. W dokumentacji dostawcy sprzętu dla określonej kombinacji klawiszy.

## <a name="docker-error-read-connection-refused"></a>Błąd platformy docker "odczytu: odmowa połączenia"
Podczas wykonywania względem lokalny kontener platformy Docker, czasem może zostać wyświetlony następujący błąd: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Możesz ją naprawić, zmieniając serwer DNS platformy Docker z `automatic` stałą wartość `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Usuń błąd wykonywania maszyny Wirtualnej "nie istnieje tty"
Podczas wykonywania względem kontenera platformy Docker na komputerze zdalnym z systemem Linux, może wystąpić następujący komunikat o błędzie:
```
sudo: no tty present and no askpass program specified.
``` 
Może to nastąpić, jeśli zmiany hasła głównego maszyny Wirtualnej z systemem Ubuntu Linux za pomocą witryny Azure portal. 

Usługa Azure Machine Learning Workbench wymaga sudo bez hasła dostępu do uruchamiania na hostach zdalnych. Najprostszym sposobem wykonania tego zadania jest użycie _visudo_ do edycji następującego pliku (razie można utworzyć pliku nie istnieje):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Ważne jest, aby edytować plik za pomocą _visudo_ a nie inne polecenie. _Program visudo_ automatycznie składni sprawdza, czy wszystkie pliki konfiguracji "sudo" i błędu, aby wygenerować plik sudo poprawnych składniowo może zablokować dostęp "sudo".

Wstaw następujący wiersz na końcu pliku:

```
username ALL=(ALL) NOPASSWD:ALL
```

Gdzie _username_ to nazwa aplikacji usługi Azure Machine Learning Workbench będzie używać do logowania się do hosta zdalnego.

Wiersz musi być umieszczone po #includedir "/ etc/sudoers.d", w przeciwnym razie może być zastąpiona przez inną regułę.

W przypadku bardziej skomplikowanych konfigurację pliku sudo, możesz "sudo" na dokumentacji Ubuntu dostępne tutaj: https://help.ubuntu.com/community/Sudoers

Błąd powyżej również może wystąpić, jeśli nie używasz maszyny Wirtualnej systemu Linux Ubuntu na platformie Azure jako element docelowy wykonywania. Obsługiwany jest tylko maszyn wirtualnych z systemem Ubuntu Linux dla wykonania zdalnego. 

## <a name="vm-disk-is-full"></a>Dysk maszyny Wirtualnej jest pełny
Domyślnie podczas tworzenia nowej maszyny Wirtualnej systemu Linux na platformie Azure, otrzymasz 30 GB dysku systemu operacyjnego. Aparat platformy docker, domyślnie używa tego samego dysku, przeciągnij w dół obrazów i uruchomionych kontenerów. To zapełnić dysku systemu operacyjnego i zostanie wyświetlony błąd "Maszyny Wirtualnej dysk jest pełny" wystąpi.

Szybka poprawka jest aby usunąć wszystkie obrazy platformy Docker, które nie są już używane. Polecenie Docker właśnie to robi. (Oczywiście masz SSH z maszyną wirtualną w celu wykonania polecenia platformy Docker z poziomu powłoki bash.)

```
$ docker system prune -a
```

Można również dodać dysk z danymi i skonfigurować aparat platformy Docker do korzystania z dysku danych do przechowywania obrazów. Oto [sposób dodawania dysku z danymi](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Następnie możesz [zmiany, gdzie Docker przechowywane obrazy](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Ewentualnie można rozszerzyć dysk systemu operacyjnego, a nie trzeba w ogóle Konfiguracja aparatu Docker. Oto [jak rozszerzyć dysk systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Udostępnianie dysku C na Windows
Jeśli wykonujesz w lokalnym kontenerze platformy Docker na Windows ustawienia `sharedVolumes` do `true` w `docker.compute` plik `aml_config` może poprawić wydajność wykonywania. To wymaga jednak udostępniać dysku C w _Docker for Windows narzędzie_. Jeśli nie jest możliwe udostępnianie dysku C, skorzystaj z następujących porad:

* Sprawdź, udostępnianie na dysku C, za pomocą Eksploratora plików
* Otwórz ustawienia karty sieciowej i odinstalowywanie i ponowna instalacja "Udostępnianie plików i drukarek w sieciach Microsoft Networks" dla vEthernet
* Otwórz ustawienia platformy docker i udostępnić dysku C z w ramach ustawień platformy docker
* Zmiany hasła Windows mają wpływ na udostępnianie. Otwórz Eksplorator plików, a następnie ponownie udostępnić dysku C i wprowadź nowe hasło.
* Może również wystąpić problem z zaporą, podczas próby udostępnienia na dysku C przy użyciu platformy Docker. To [wpis w witrynie Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) mogą być pomocne.
* Podczas udostępniania dysku C przy użyciu poświadczeń domeny, udostępnianie może przestać działać w sieciach, w których kontroler domeny nie jest dostępny (na przykład sieci domowej, publicznych sieci Wi-Fi itp.). Aby uzyskać więcej informacji, zobacz [ten wpis](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Można także uniknąć problem z udostępnianiem, mała wydajność, koszty, ustawiając `sharedVolumne` do `false` w `docker.compute` pliku.

## <a name="wipe-clean-workbench-installation"></a>Czyszczenie czystą instalację aplikacji Workbench
Zazwyczaj nie trzeba to zrobić. Jednak w przypadku, gdy należy je wyczyścić czysta instalacja, poniżej przedstawiono kroki:

- W systemie Windows:
  - Najpierw upewnij się, że używasz _apletu Dodaj lub usuń programy_ aplet _Panelu sterowania_ do usunięcia _Azure Machine Learning Workbench_ wejścia aplikacji.  
  - Następnie możesz pobrać i uruchomić jeden z następujących skryptów:
    - [Skrypt wiersza polecenia Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Skrypt programu Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Może być konieczne uruchomienie `Set-ExecutionPolicy Unrestricted` w oknie PowerShell uprawnień podwyższonego poziomu uprawnień, zanim będzie można uruchomić skryptu.)
- W systemie macOS:
  - Wystarczy pobrać i uruchomić [powłoki skryptu powłoki systemowej macOS](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Uczenie Maszynowe systemu Azure przy użyciu lokalizacji python innego niż Azure ML zainstalowane środowisko python
Ze względu na ostatnie zmiany w aplikacji Azure Machine Learning Workbench użytkownicy mogą zauważyć, że przebiegów lokalnych może nie wskazywać już zainstalowany przez aplikację Azure ML Workbench środowiska python. Może się tak zdarzyć, jeśli użytkownik ma inne środowisko python zainstalowana na komputerze, a ścieżka "Python" jest ustawiona do punktu w tym środowisku. Aby można było używać aplikacji Azure ML Workbench zainstalowane środowisko Python, wykonaj następujące kroki:
- Przejdź do pliku local.compute w folderze aml_config w katalogu głównym projektu.
- Zmiana zmiennej "pythonLocation", aby wskazywała ścieżkę fizyczną aplikacji Azure ML workbench zainstalowane środowisko python. Tę ścieżkę można uzyskać na dwa sposoby:
    - Lokalizacji języka python w usłudze Azure ML znajduje się w temacie %localappdata%\AmlWorkbench\python\python.exe
    - można otwierać cmd z aplikacji Azure ML Workbench, wpisz języka python w wierszu polecenia, importowanie sys.exe, uruchom sys.executable i uzyskać ścieżkę, w tym miejscu. 



## <a name="some-useful-docker-commands"></a>Kilka przydatnych poleceń platformy Docker

Poniżej przedstawiono kilka przydatnych poleceń platformy Docker:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
