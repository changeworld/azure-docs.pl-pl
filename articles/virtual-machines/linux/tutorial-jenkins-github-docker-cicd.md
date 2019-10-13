---
title: 'Samouczek: tworzenie potoku programowania na platformie Azure przy użyciu usługi Jenkins | Microsoft Docs'
description: Z tego samouczka dowiesz się, jak utworzyć na platformie Azure maszynę wirtualną usługi Jenkins, która przeprowadza ściąganie z usługi GitHub przy każdym zatwierdzeniu kodu i tworzy nowy kontener platformy Docker w celu uruchomienia aplikacji.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 875285b6a168d9aa9820d660d9c366a36545d319
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299408"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Samouczek: tworzenie infrastruktury programowania na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu usług Jenkins, GitHub i Docker

Aby zautomatyzować fazę kompilacji i testowania podczas tworzenia aplikacji, można użyć potoku ciągłej integracji i wdrażania (CI/CD). W tym samouczku utworzysz potok CI/CD na maszynie wirtualnej platformy Azure. Wykonasz m.in. następujące czynności:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Instalowanie i konfigurowanie usługi Jenkins
> * Tworzenie integracji elementu webhook między usługami GitHub i Jenkins
> * Tworzenie i wyzwalanie zadań kompilacji usługi Jenkins z poziomu zatwierdzeń usługi GitHub
> * Tworzenie obrazu platformy Docker dla aplikacji
> * Sprawdzanie, czy zatwierdzenia GitHub tworzą nowy obraz platformy Docker i aktualizują działającą aplikację

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-jenkins-instance"></a>Tworzenie wystąpienia usługi Jenkins
W poprzednim samouczku dotyczącym [sposobu dostosowywania maszyny wirtualnej systemu Linux przy pierwszym uruchomieniu](tutorial-automate-vm-deployment.md) przedstawiono proces automatyzowania dostosowywania maszyny wirtualnej przy użyciu pliku cloud-init. W tym samouczku plik cloud-init jest używany do instalowania usługi Jenkins i platformy Docker na maszynie wirtualnej. Jenkins to popularny serwer automatyzacji typu open source, który bezproblemowo integruje się z platformą Azure, umożliwiając ciągłą integrację (CI) i ciągłe dostarczanie (CD). Więcej samouczków dotyczących korzystania z usługi można znaleźć w centrum [Usługa Jenkins na platformie Azure](https://docs.microsoft.com/azure/jenkins/).

W bieżącej powłoce utwórz plik o nazwie *cloud-init-jenkins.txt* i wklej poniższą konfigurację. Na przykład utwórz plik w usłudze Cloud Shell, a nie na maszynie lokalnej. Wprowadź `sensible-editor cloud-init-jenkins.txt`, aby utworzyć plik i wyświetlić listę dostępnych edytorów. Upewnij się, że skopiowano cały plik cloud-init chmury, a szczególnie pierwszy wiersz:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Zanim będzie można utworzyć maszynę wirtualną, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroupJenkins* w lokalizacji *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Użyj parametru `--custom-data` do przekazania w pliku konfiguracji cloud-init. Podaj pełną ścieżkę do pliku *cloud-init-jenkins.txt*, jeśli plik został zapisany poza aktualnym katalogiem roboczym.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Utworzenie i skonfigurowanie maszyny wirtualnej może potrwać kilka minut.

Aby zezwolić na ruch internetowy do maszyny wirtualnej, użyj polecenia [az vm open-port](/cli/azure/vm) w celu otwarcia portu *8080* dla ruchu usługi Jenkins i portu *1337* dla aplikacji Node.js używanej do uruchamiania aplikacji przykładowej:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Konfigurowanie usługi Jenkins
Aby uzyskać dostęp do wystąpienia usługi Jenkins, pobierz publiczny adres IP maszyny wirtualnej:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Ze względów bezpieczeństwa musisz wprowadzić początkowe hasło administratora, które jest przechowywane w pliku tekstowym na maszynie wirtualnej, aby rozpocząć instalację usługi Jenkins. Użyj publicznego adresu IP uzyskanego w poprzednim kroku dla protokołu SSH na maszynie wirtualnej:

```bash
ssh azureuser@<publicIps>
```

Użyj polecenia `service`, aby sprawdzić, czy usługa Jenkins jest uruchomiona:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Wyświetl element `initialAdminPassword` dla instalacji usługi Jenkins i skopiuj go:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Jeśli plik nie jest jeszcze dostępny, poczekaj kilka minut, aż plik cloud-init ukończy instalację usługi Jenkins i platformy Docker.

Otwórz przeglądarkę internetową i przejdź do `http://<publicIps>:8080`. Ukończ początkową konfigurację usługi Jenkins w następujący sposób:

- Wybierz pozycję **Wybierz wtyczki do zainstalowania**.
- Wyszukaj pozycję *GitHub* w polu tekstowym u góry. Zaznacz pole wyboru pozycji *GitHub*, a następnie wybierz pozycję **Zainstaluj**.
- Utwórz pierwszego użytkownika administratora. Wprowadź nazwę użytkownika, taką jak **admin**, a następnie podaj bezpieczne hasło. Na koniec wpisz pełną nazwę i adres e-mail.
- Wybierz pozycję **Zapisz i zakończ**.
- Gdy narzędzie Jenkins będzie gotowe, wybierz pozycję **Rozpocznij korzystanie z narzędzia Jenkins**.
  - Jeśli po rozpoczęciu korzystania z narzędzia Jenkins w przeglądarce internetowej zostanie wyświetlona pusta strona, uruchom ponownie usługę Jenkins. Z poziomu sesji protokołu SSH wpisz ciąg `sudo service jenkins restart`, a następnie odśwież przeglądarkę internetową.
- W razie potrzeby zaloguj się do usługi Jenkins przy użyciu utworzonej nazwy użytkownika i hasła.


## <a name="create-github-webhook"></a>Tworzenie elementu webhook GitHub
Aby skonfigurować integrację z usługą GitHub, otwórz [przykładową aplikację Node.js Hello World](https://github.com/Azure-Samples/nodejs-docs-hello-world) z poziomu repozytorium przykładów na platformie Azure. Aby utworzyć rozwidlenie repozytorium na koncie usługi GitHub, wybierz przycisk **Rozwidlenie** w prawym górnym rogu.

Utwórz element webhook wewnątrz utworzonego rozwidlenia:

- Wybierz pozycję **Ustawienia**, a następnie wybierz pozycję **Elementy webhook** po lewej stronie.
- Wybierz pozycję **Dodaj element webhook**, a następnie w polu filtru wprowadź nazwę *Jenkins*.
- W polu **Payload URL** (Adres URL ładunku) wprowadź adres `http://<publicIps>:8080/github-webhook/`. Upewnij się, że wpisano znak /.
- W polu **Content type** (Typ zawartości) wybierz pozycję *application/x-www-form-urlencoded*.
- W obszarze **Which events would you like to trigger this webhook?** (Które zdarzenia mają wyzwalać ten element webhook?) wybierz opcję *Just the push event* (Tylko zdarzenie wypychania).
- Zaznacz pole wyboru **Active** (Aktywne).
- Kliknij przycisk **Add webhook** (Dodaj element webhook).

![Dodawanie elementu webhook GitHub do rozwidlonego repozytorium](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Tworzenie zadania usługi Jenkins
Aby usługa Jenkins odpowiadała w usłudze GitHub na zdarzenie, takie jak zatwierdzenie kodu, należy utworzyć zadanie usługi Jenkins. Użyj adresów URL dla własnego rozwidlenia usługi GitHub.

W witrynie internetowej usługi Jenkins wybierz na stronie głównej pozycję **Utwórz nowe zadania**:

- Wprowadź ciąg *HelloWorld* jako nazwę zadania. Wybierz **Projekt Freestyle**, a następnie wybierz pozycję **OK**.
- W sekcji **Ogólne** wybierz projekt **GitHub** i wprowadź adres URL rozwidlonego repozytorium, taki jak *https://github.com/cynthn/nodejs-docs-hello-world*
- W sekcji **Zarządzanie kodem źródłowym** wybierz pozycję **Git** i wprowadź adres URL *.git* rozwidlonego repozytorium, taki jak *https://github.com/cynthn/nodejs-docs-hello-world.git*
- W sekcji **Kompilowanie wyzwalaczy** wybierz pozycję **Wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITscm**.
- W sekcji **Kompilowanie** wybierz pozycję **Dodaj krok kompilacji**. Wybierz pozycję **Wykonaj powłokę**, a następnie wprowadź `echo "Test"` w oknie wiersza polecenia.
- Wybierz pozycję **Zapisz** w dolnej części okna zadań.


## <a name="test-github-integration"></a>Testowanie integracji z usługą GitHub
Aby przetestować integrację z usługą GitHub przy użyciu usługi Jenkins, zatwierdź zmianę w rozwidleniu. 

Po powrocie do internetowego interfejsu użytkownika usługi GitHub wybierz rozwidlone repozytorium, a następnie wybierz plik **index.js**. Wybierz ikonę ołówka, aby edytować ten plik tak, aby wiersz 6 wyglądał tak:

```javascript
response.end("Hello World!");
```

Aby zatwierdzić zmiany, wybierz przycisk **Zatwierdź zmiany** u dołu.

W usłudze Jenkins nowa kompilacja zaczyna się w sekcji **Historia kompilacji** w lewym dolnym rogu strony zadania. Wybierz link numeru kompilacji i wybierz pozycję **Dane wyjściowe konsoli** po lewej stronie. Możesz wyświetlić kroki, które usługa Jenkins podejmuje, gdy kod jest ściągany z usługi GitHub, a akcja kompilacji generuje komunikat wyjściowy `Test` do konsoli. Za każdym razem, gdy w usłudze GitHub przeprowadzane jest zatwierdzenie, element webhook łączy się z usługą Jenkins i wyzwala w ten sposób nową kompilację.


## <a name="define-docker-build-image"></a>Definiowanie obrazu kompilacji platformy Docker
Aby wyświetlić aplikację Node.js działającą w oparciu o zatwierdzenia GitHub, skompilujmy obraz platformy Docker w celu uruchomienia aplikacji. Obraz jest kompilowany z pliku Dockerfile, który definiuje sposób konfigurowania kontenera uruchamiającego aplikację. 

Z poziomu połączenia protokołu SSH z maszyną wirtualną przejdź do katalogu obszaru roboczego usługi Jenkins o nazwie odpowiadającej zadaniu utworzonemu w poprzednim kroku. W tym przykładzie była to nazwa *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Utwórz plik w bieżącym katalogu obszaru roboczego przy użyciu elementu `sudo sensible-editor Dockerfile` i wklej poniższą zawartość. Upewnij się, że skopiowano cały plik Dockerfile, a szczególnie pierwszy wiersz:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Ten plik Dockerfile używa obrazu podstawowego środowiska Node.js przy użyciu systemu Alpine Linux, uwidacznia port 1337, w którym działa aplikacja Hello World, a następnie kopiuje pliki aplikacji i inicjuje ją.


## <a name="create-jenkins-build-rules"></a>Tworzenie reguł kompilacji usługi Jenkins
W poprzednim kroku utworzono podstawową regułę kompilacji usługi Jenkins, która generuje komunikat wyjściowy w konsoli. Utwórzmy krok kompilacji, aby użyć naszego pliku Dockerfile i uruchomić aplikację.

Po powrocie do wystąpienia usługi Jenkins wybierz zadanie utworzone w poprzednim kroku. Wybierz pozycję **Konfiguruj** po lewej stronie i przewiń w dół do sekcji **Kompilacja**:

- Usuń istniejący krok kompilacji `echo "Test"`. Wybierz czerwony krzyżyk w prawym górnym rogu pola istniejącego kroku kompilacji.
- Wybierz pozycję **Dodaj krok kompilacji**, a następnie wybierz pozycję **Wykonaj powłokę**
- W polu **Polecenie** wprowadź następujące polecenia platformy Docker, a następnie wybierz pozycję **Zapisz**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Kroki procesu kompilacji platformy Docker powodują utworzenie obrazu i oznaczenie go numerem kompilacji usługi Jenkins, dzięki czemu można zachować historię obrazów. Istniejące kontenery z działającą aplikacją są zatrzymywane, a następnie usuwane. Nowy kontener jest następnie uruchamiany przy użyciu obrazu i uruchamia aplikację Node.js w oparciu o najnowsze zatwierdzenia w witrynie GitHub.


## <a name="test-your-pipeline"></a>Testowanie potoku
Aby zobaczyć cały potok w akcji, edytuj ponownie plik *index.js* w rozwidlonym repozytorium GitHub, a następnie wybierz pozycję **Zatwierdź zmianę**. Nowe zadanie zostanie uruchomione w usłudze Jenkins w obrębie elementu webhook powiązanego z usługą GitHub. Utworzenie obrazu platformy Docker i uruchomienie aplikacji w nowym kontenerze trwa kilka sekund.

W razie potrzeby ponownie pobierz publiczny adres IP maszyny wirtualnej:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Otwórz przeglądarkę internetową i wprowadź `http://<publicIps>:1337`. Zostanie wyświetlona aplikacja Node.js odzwierciedlająca najnowsze zatwierdzenia w rozwidleniu GitHub w następujący sposób:

![Uruchamianie aplikacji Node.js](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Teraz wprowadź kolejną modyfikację pliku *index.js* w usłudze GitHub i zatwierdź zmianę. Poczekaj kilka sekund na ukończenie zadania w usłudze Jenkins, a następnie odśwież przeglądarkę internetową, aby wyświetlić zaktualizowaną wersję aplikacji uruchomionej w nowym kontenerze w następujący sposób:

![Uruchamianie aplikacji Node.js po kolejnym zatwierdzeniu GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Następne kroki
W tym samouczku skonfigurowano usługę GitHub do uruchomienia zadania kompilacji usługi Jenkins przy każdym zatwierdzeniu kodu, a następnie wdrożono kontener platformy Docker w celu testowania aplikacji. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej usługi Jenkins
> * Instalowanie i konfigurowanie usługi Jenkins
> * Tworzenie integracji elementu webhook między usługami GitHub i Jenkins
> * Tworzenie i wyzwalanie zadań kompilacji usługi Jenkins z poziomu zatwierdzeń usługi GitHub
> * Tworzenie obrazu platformy Docker dla aplikacji
> * Sprawdzanie, czy zatwierdzenia GitHub tworzą nowy obraz platformy Docker i aktualizują działającą aplikację

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat sposobu integrowania usługi Jenkins z usługami Azure DevOps Services.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji przy użyciu usług Jenkins i Azure DevOps Services](tutorial-build-deploy-jenkins.md)
