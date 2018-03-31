---
title: Znane problemy i przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft
description: Listę znanych problemów i przewodnik dotyczący rozwiązywania problemów
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 3699e2a59061d8a2870a263588917268ca504866
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench — znane problemy i przewodnik rozwiązywania problemów 
Ten artykuł ułatwia znajdowanie i poprawić błędy lub błędów napotkanych jako część przy użyciu aplikacji Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Znajdź Workbench numer kompilacji
Podczas komunikowania się z zespołem pomocy technicznej, ważne jest uwzględnienie numeru kompilacji aplikacji Workbench. W systemie Windows, można sprawdzić numer kompilacji, klikając **pomocy** menu i wybierz polecenie **Azure ML narzędzia Workbench**. Na macOS, możesz kliknąć **Azure ML Workbench** menu i wybierz polecenie **Azure ML narzędzia Workbench**.

## <a name="machine-learning-msdn-forum"></a>MSDN Forum uczenia maszynowego
Mamy Forum MSDN ogłaszania pytania. Zespół pracujący nad produktem aktywnie monitoruje forum. Forum adres URL jest [ https://aka.ms/azureml-forum ](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Zbierz informacje diagnostyczne
Czasami może być przydatne, jeśli można podać informacje diagnostyczne, podczas pytania o pomoc. Oto miejsca zamieszkania pliki dziennika:

### <a name="installer-log"></a>Dziennik Instalatora
Jeśli napotkasz problem podczas instalacji, w tym miejscu są pliki dziennika Instalatora:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Możesz zip się zawartość tych katalogów i Wyślij do nas, diagnostyki.

### <a name="workbench-desktop-app-log"></a>Dziennik aplikacji komputerowej Workbench
Jeśli masz problemy z zalogowaniem lub pulpitu Workbench ulegnie awarii, można znaleźć tutaj plików dziennika:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Możesz zip się zawartość tych katalogów i Wyślij do nas, diagnostyki.

### <a name="experiment-execution-log"></a>Dziennik wykonywania eksperymentu
Jeśli konkretnego skryptu zakończy się niepowodzeniem podczas przesyłania z aplikacji komputerowej, spróbuj ponownie przesłać go przy użyciu interfejsu wiersza polecenia `az ml experiment submit` polecenia. To powinien zapewnić pełny komunikat o błędzie w formacie JSON, a przede wszystkim zawiera **identyfikator operacji** wartość. Wyślij nam w tym pliku JSON **identyfikator operacji** i firma Microsoft może pomóc w diagnozowaniu. 

Jeśli pomyślnie przesyłanie konkretnego skryptu, ale kończy się niepowodzeniem podczas wykonywania, należy wydrukować **Uruchom identyfikator** do identyfikowania z określonym programem. Można spakować odpowiednich plików dziennika przy użyciu następującego polecenia:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` Polecenie generuje `diagnostics.zip` pliku w folderze głównym projektu. Pakiet ZIP zawiera folder cały projekt w stanie, w czasie, który została wykonana, oraz informacje o rejestrowaniu. Należy usunąć poufne informacje, których nie chcesz, aby uwzględnić przed wysłaniem nam pliku diagnostyki.

## <a name="send-us-a-frown-or-a-smile"></a>Wyślij niezadowolenie (lub zadowolenie)

Podczas pracy w konsoli usługi Azure ML Workbench można również wysłać nam niezadowolenie (lub zadowolenie), klikając ikonę krój uśmiech w lewym dolnym rogu powłoka aplikacji. Możesz opcjonalnie zawierają Twój adres e-mail (tak, aby firma Microsoft może zająć się), i/lub zrzut ekranu bieżącego stanu. 

## <a name="known-service-limits"></a>Limity znanej usługi
- Maksymalny dozwolony rozmiar folderu projektu: 25 MB.
    >[!NOTE]
    >To ograniczenie nie dotyczy `.git`, `docs` i `outputs` folderów. Te nazwy folderów jest rozróżniana wielkość liter. Jeśli pracujesz z dużymi plikami, zapoznaj się [wprowadzeniem trwałych zmian i transakcji z dużymi plikami](how-to-read-write-files.md).

- Maksymalny dozwolony czas wykonywania eksperymentów: 7 dni

- Maksymalny rozmiar pliku śledzonych w `outputs` folder po Uruchom: 512 MB
  - Oznacza to, czy skrypt tworzy plik przekracza 512 MB w folderze dane wyjściowe, go nie są zbierane istnieje. Jeśli pracujesz z dużymi plikami, zapoznaj się [wprowadzeniem trwałych zmian i transakcji z dużymi plikami](how-to-read-write-files.md).

- Klucze SSH nie są obsługiwane podczas nawiązywania połączenia zdalnego komputera lub klastra Spark za pomocą protokołu SSH. Tryb tylko do nazwy użytkownika i hasła jest obecnie obsługiwane.

- Podczas korzystania z klastra usługi HDInsight, jak obliczeniowy docelowej, należy użyć usługi Azure blob jako podstawowy. Za pomocą usługi Azure Data Lake Storage nie jest obsługiwane.

- Transformacje klastrowania tekstu nie są obsługiwane na komputerach Mac.

- Biblioteka RevoScalePy jest obsługiwana tylko w systemach Windows i Linux (w kontenerach Docker). Nie jest obsługiwana na macOS.

- Notesów Jupyter mają limit maksymalny rozmiar to 5 MB, podczas otwierania je z poziomu aplikacji Workbench. Możesz otworzyć dużych notesów z interfejsu wiersza polecenia przy użyciu polecenia "notesu ml az start", a czystą komórki danych wyjściowych do Zmniejsz rozmiar pliku.

## <a name="cant-update-workbench"></a>Nie można zaktualizować Workbench
Gdy dostępna jest nowa aktualizacja, na stronie głównej aplikacji Workbench wyświetla komunikat informujący o nowych aktualizacji. Powinny pojawić się wskaźnika aktualizacji znajdujących się w lewym dolnym rogu aplikacji ikonę dzwonka. Kliknij badge i użyj Kreatora Instalatora, aby zainstalować aktualizację. 

![Aktualizacja obrazu](./media/known-issues-and-troubleshooting-guide/update.png)

Jeśli nie widzisz powiadomienia, spróbuj ponownie uruchomić aplikację. Jeśli nadal nie widać powiadomienie o aktualizacji po ponownym uruchomieniu, może być kilka przyczyn.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Uruchamiasz Workbench z przypiętych skrót na pasku zadań
Może już zainstalowano aktualizację. Jednak nadal wskazuje przypiętych skrót do starego bitów na dysku. Można to sprawdzić, przechodząc do `%localappdata%/AmlWorkbench` folder i sprawdź, czy masz zainstalowaną najnowszą wersję i sprawdź właściwość przypiętych skrótów, aby zobaczyć, których wskazuje. Zweryfikowane, po prostu usuń stare skrótów, uruchom Workbench z Start menu i opcjonalnie utworzyć nowy skrót przypiętych na pasku zadań.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Zainstalowano Workbench za pomocą łącza "Zainstaluj Workbench uczenie Maszynowe Azure" na DSVM systemu Windows
Niestety jest nie łatwe poprawkę na tym typie. Należy wykonać następujące kroki, aby usunąć zainstalowane usługi bits i Pobierz najnowszą wersję Instalatora do zainstalowania świeża Workbench: 
   - Usuń folder `C:\Users\<Username>\AppData\Local\amlworkbench`
   - Usuń skryptu `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - Usunięcie skrótu z pulpitu, który uruchamia skrypt powyżej
   - Pobierz Instalatora https://aka.ms/azureml-wb-msi i ponowne zainstalowanie.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Zablokowana na ekranie "Sprawdzanie konta eksperymenty" po zalogowaniu
Po zalogowaniu aplikacji Workbench może zatrzymywane w pusty ekran z komunikatem "Sprawdzanie eksperymenty konto" kółkiem Obracająca przedstawiający. Aby rozwiązać ten problem, wykonaj następujące czynności:
1. Zamknięcie aplikacji
2. Usunąć następujący plik:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Uruchom ponownie aplikację.

## <a name="cant-delete-experimentation-account"></a>Nie można usunąć konta eksperymenty
Można użyć interfejsu wiersza polecenia, aby usunąć konto eksperymenty, ale musisz usunąć obszary robocze podrzędnych i projekty podrzędne w tych obszarach roboczych podrzędnych najpierw. W przeciwnym razie zostanie wyświetlony błąd "nie można usunąć zasobów przed usunięciem zagnieżdżonych zasobów."

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Możesz także usunąć projektów i obszarów roboczych z poziomu aplikacji Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Nie można otworzyć pliku, jeśli projekt w usłudze OneDrive
Jeśli masz twórców spadek 10 usługi Windows Update, a projektu jest tworzony w folderze lokalnym zamapowane do usługi OneDrive, może się okazać, że nie można otworzyć dowolny plik w Workbench. Jest to spowodowane usterki wprowadzone przez aktualizację twórców spadek, która powoduje niepowodzenie w folderze OneDrive kodu node.js. Błąd zostanie naprawiony wkrótce przez usługę Windows update, ale do tego czasu, nie twórz projekty w folderze OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nazwa pliku zbyt długo w systemie Windows
Jeśli używasz narzędzia Workbench w systemie Windows może działać na domyślny limit długości nazwy maksymalną pliku 260 znaków, które można powierzchni jako błąd "system nie może odnaleźć określonej ścieżki". Można zmodyfikować ustawienie klucza rejestru, aby umożliwić znacznie dłużej nazwa ścieżki pliku. Przegląd [w tym artykule](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) Aby uzyskać więcej informacji na temat ustawiania _MAX_PATH_ klucza rejestru.

## <a name="interrupt-cli-execution-output"></a>Przerwanie dane wyjściowe wykonania interfejsu wiersza polecenia
Jeśli należy rozpocząć poza eksperymenty uruchomić przy użyciu `az ml experiment submit` lub `az ml notebook start` i chcesz przerwać dane wyjściowe: 
- W systemie Windows, użyj kombinacji klawiszy Ctrl-Break z klawiatury
- Na macOS użyj klawiszy Ctrl-C.

Należy pamiętać, to tylko przerwań strumienia wyjściowego w oknie interfejsu wiersza polecenia. Nie faktycznie Zatrzymuje zadanie, które jest wykonywana. Aby anulować bieżące zadanie, należy użyć `az ml experiment cancel -r <run_id> -t <target name>` polecenia.

Na komputerach z systemem Windows z klawiatury, które nie mają klucz podziału możliwe alternatywne metody obejmują Fn-B, Ctrl-Fn-B lub Fn + Esc. W dokumentacji producenta sprzętu dla określonej kombinacji klawiszy.

## <a name="docker-error-read-connection-refused"></a>Błąd docker "do odczytu: połączenie zostało odrzucone"
Podczas wykonywania przed lokalnego kontenera Docker, czasami może pojawić następujący błąd: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Możesz go rozwiązać, zmieniając Docker serwer DNS z `automatic` do wartości stałej z `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Usuń błąd wykonywania maszyny Wirtualnej "nie istnieje tty"
Podczas wykonywania przed kontener Docker na zdalnym komputerze systemu Linux, może wystąpić następujący komunikat o błędzie:
```
sudo: no tty present and no askpass program specified.
``` 
Może to się zdarzyć, jeśli używasz portalu Azure do zmiany hasła głównego maszyny Wirtualnej systemu Ubuntu Linux. 

Azure Machine Learning Workbench wymaga dostępu sudoers bez hasła do uruchamiania na hostach zdalnych. Najprostszym sposobem, aby to zrobić, jest użycie _visudo_ do edycji następującego pliku (pliku może utworzyć Jeśli istnieje):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Ważne jest, aby edytować plik z _visudo_ i nie innego polecenia. _visudo_ automatycznie sprawdza wszystkie pliki konfiguracji sudo, składni i nie można utworzyć pliku sudoers poprawna składniowo może zablokować użytkownikowi dostęp sudo.

Wstaw następujący wiersz na końcu pliku:

```
username ALL=(ALL) NOPASSWD:ALL
```

Gdzie _username_ jest nazwa usługi Azure Machine Learning Workbench będzie używać do logowania do zdalnego hosta.

Wiersz musi być umieszczony po #includedir "/ etc/sudoers.d", w przeciwnym razie mogą zostać zastąpione przez inną regułę.

Jeśli masz bardziej złożonych konfiguracji sudo można sudo na dokumentacji Ubuntu dostępna tutaj: https://help.ubuntu.com/community/Sudoers

Błąd powyżej również może się zdarzyć, jeśli nie używasz maszyny Wirtualnej systemu Linux Ubuntu na platformie Azure jako element docelowy wykonywania. Obsługiwany jest tylko maszynę Wirtualną z systemem Ubuntu Linux dla wykonania zdalnego. 

## <a name="vm-disk-is-full"></a>Maszyna wirtualna dysk jest pełny
Domyślnie podczas tworzenia nowej maszyny Wirtualnej systemu Linux na platformie Azure, możesz uzyskać 30 GB dysku systemu operacyjnego. Aparatem platformy docker domyślnie używa tego samego dysku ściąganie dół obrazów i uruchomionych kontenerów. To zapełnić dysk systemu operacyjnego i zostanie wyświetlona "Maszyny Wirtualnej dysk jest pełny" błąd, gdy nastąpi.

Aby usunąć wszystkie obrazy Docker, które nie są już używane jest poprawka szybkie. Polecenie Docker jest właśnie tę. (Oczywiście trzeba SSH do maszyny Wirtualnej w celu wykonania polecenia Docker z powłoki bash.)

```
$ docker system prune -a
```

Można także dodać dysk danych i skonfigurować aparatem platformy Docker pod kątem używania dysku danych do przechowywania obrazów. Oto [sposób dodawania dysku danych](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Następnie możesz [zmiany, w którym Docker są przechowywane obrazy](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Lub, można rozszerzyć dysk systemu operacyjnego, a nie masz dostępu do konfiguracji aparatu Docker. Oto [jak rozszerzyć dysk systemu operacyjnego](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

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

## <a name="sharing-c-drive-on-windows"></a>Udostępnianie dysku C w systemie Windows
Jeśli wykonujesz kompilację w kontenerze Docker lokalnego w systemie Windows ustawienia `sharedVolumes` do `true` w `docker.compute` plików w obszarze `aml_config` może poprawić wydajność wykonywania. To wymaga jednak udostępnić dysk C w _Docker — narzędzie Windows_. Jeśli nie jest możliwe udostępnić dysk C, spróbuj następujących wskazówek:

* Sprawdź udostępnianie na dysku C za pomocą Eksploratora plików
* Otwórz ustawienia karty sieciowej i odinstalowywanie i ponowna instalacja "Udostępnianie plików i drukarek w sieciach Microsoft Networks" dla vEthernet
* Otwórz ustawienia docker i udostępniać dysk C z ustawień docker
* Zmiany hasła systemu Windows mają wpływ na udostępnianie. Otwórz okno Eksploratora plików, ponownie dysk C i wprowadź nowe hasło.
* Może również wystąpić problem zapory, podczas próby udostępnienia dysku C z Docker. To [przepełnienie stosu post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) mogą być pomocne.
* Podczas udostępniania dysku C przy użyciu poświadczeń domeny, udostępniania mogą przestać działać w sieciach, w których kontroler domeny nie jest dostępny (na przykład sieci domowej, publicznej sieci Wi-Fi itp.). Aby uzyskać więcej informacji, zobacz [ten wpis](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Można także uniknąć problem z udostępnianiem, w małych wydajności, kosztów, ustawiając `sharedVolumne` do `false` w `docker.compute` pliku.

## <a name="wipe-clean-workbench-installation"></a>Czysta instalacja Workbench czyszczenia
Zazwyczaj nie trzeba to zrobić. Jednak w przypadku, gdy użytkownik musi wyczyszczenie instalacji, poniżej przedstawiono kroki:

- W systemie Windows:
  - Najpierw należy upewnić się, _Dodaj lub usuń programy_ aplet _Panelu sterowania_ usunąć _Azure Machine Learning Workbench_ wejścia aplikacji.  
  - Następnie możesz pobrać i uruchom jednego z poniższych skryptów:
    - [Skrypt wiersza polecenia systemu Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Skrypt programu Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Należy uruchomić `Set-ExecutionPolicy Unrestricted` w oknie programu PowerShell z podniesionymi uprawnieniami uprawnień przed uruchomieniem skryptu.)
- Na macOS:
  - Wystarczy pobrać i uruchomić [skrypt powłoki bash macOS](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Azure ML przy użyciu lokalizacji python innego niż Azure ML zainstalowane środowiska python
Ze względu na zmiany w konsoli usługi Azure Machine Learning Workbench użytkownicy mogą zauważyć, że przebiegów lokalnych może nie wskazywać środowiska python już zainstalowana przez Workbench uczenia Maszynowego Azure. Może się tak zdarzyć, jeśli użytkownik ma inne środowisko python zainstalowanego na komputerze i ścieżki "Python" ma ustawioną wartość wskaż tego środowiska. Aby można było używać usługi Azure ML Workbench zainstalowane środowisko Python, wykonaj następujące kroki:
- Przejdź do pliku local.compute w folderze aml_config w katalogu głównym projektu.
- Zmiana zmiennej "pythonLocation" wskaż ścieżkę fizyczną workbench uczenie Maszynowe Azure zainstalowane środowisko python. Tę ścieżkę można uzyskać na dwa sposoby:
    - Lokalizacja python platformy Azure ML znajduje się w temacie %localappdata%\AmlWorkbench\python\python.exe
    - można otwierać cmd ze środowiska roboczego uczenia Maszynowego Azure, wpisz w wierszu polecenia języka python, zaimportować sys.exe, uruchom sys.executable i pobrać ścieżki stamtąd. 



## <a name="some-useful-docker-commands"></a>Niektóre przydatne polecenia Docker

Oto niektóre przydatne polecenia Docker:

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
