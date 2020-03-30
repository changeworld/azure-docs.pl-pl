---
title: Rozwiązywanie problemów
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Dowiedz się, jak rozwiązywać typowe problemy i rozwiązywać je podczas włączania i korzystania z usługi Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
ms.openlocfilehash: c12dfd385962d8dd7de8239a0d4ecd46746499c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239765"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Rozwiązywanie problemów z usługą Azure Dev Spaces

Ten przewodnik zawiera informacje dotyczące typowych problemów, które mogą wystąpić podczas korzystania z usługi Azure Dev Spaces.

Jeśli masz problem podczas korzystania z usługi Azure Dev Spaces, utwórz [problem w repozytorium Usługi Azure Dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby skuteczniej rozwiązywać problemy, może pomóc w tworzeniu bardziej szczegółowych dzienników do przeglądu.

Dla rozszerzenia programu Visual `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` Studio ustaw zmienną środowiskową na 1. Pamiętaj, aby ponownie uruchomić program Visual Studio, aby zmienna środowiskowa została uwzględnina. Po włączeniu szczegółowe dzienniki są `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` zapisywane w katalogu.

W wierszu polecenia można wyprowadzić więcej informacji `--verbose` podczas wykonywania polecenia za pomocą przełącznika. Można również przeglądać bardziej szczegółowe `%TEMP%\Azure Dev Spaces`dzienniki w . Na komputerze Mac katalog *TEMP* można znaleźć, uruchamiając `echo $TMPDIR` je z okna terminala. Na komputerze z *TEMP* systemem Linux katalog `/tmp`TEMP jest zwykle . Ponadto sprawdź, czy rejestrowanie jest włączone w [pliku konfiguracyjnym interfejsu wiersza polecenia platformy Azure](/cli/azure/azure-cli-configuration?view=azure-cli-latest#cli-configuration-values-and-environment-variables).

Usługa Azure Dev Spaces działa również najlepiej podczas debugowania pojedynczego wystąpienia lub zasobnika. Plik `azds.yaml` zawiera *ustawienie, replicaCount*, który wskazuje liczbę zasobników, które kubernetes działa dla usługi. Jeśli zmienisz *replicaCount,* aby skonfigurować aplikację do uruchamiania wielu zasobników dla danej usługi, debuger dołącza do pierwszego zasobnika, gdy jest wymieniony alfabetycznie. Debuger dołącza do innego zasobnika, gdy oryginalny zasobnik odtwarza, co może spowodować nieoczekiwane zachowanie.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Typowe problemy podczas włączania usługi Azure Dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Błąd "Nie można utworzyć kontrolera usługi Azure Dev Spaces"

Ten błąd może zostać wyświetlony, gdy coś pójdzie nie tak z utworzeniem kontrolera. Jeśli jest to błąd przejściowy, usuń i ponownie stwórz kontroler, aby go naprawić.

Można również spróbować usunąć kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Użyj interfejsu wiersza polecenia azure dev spaces, aby usunąć kontroler. Nie można usunąć kontrolera z programu Visual Studio. Nie można również zainstalować interfejsu wiersza polecenia usługi Azure Dev Spaces w usłudze Azure Cloud Shell, aby nie można usunąć kontrolera z usługi Azure Cloud Shell.

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia azure dev spaces, możesz najpierw zainstalować go za pomocą następującego polecenia, a następnie usunąć kontroler:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Ponownetworzenie kontrolera można wykonać za ć w interfejsie wiersza polecenia lub programu Visual Studio. Zobacz [tworzenie zespołu](quickstart-team-development.md) lub [rozwijanie za pomocą programów szybki startowych .NET Core,](quickstart-netcore-visualstudio.md) aby zapoznać się z przykładami.

### <a name="controller-create-failing-because-of-controller-name-length"></a>Niepowodzenie tworzenia kontrolera z powodu długości nazwy kontrolera

Nazwa kontrolera usługi Azure Dev Spaces nie może być dłuższa niż 31 znaków. Jeśli nazwa kontrolera przekracza 31 znaków po włączeniu przestrzeni deweloperskich w klastrze AKS lub utworzeniu kontrolera, zostanie wyświetlony błąd. Przykład:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

Aby rozwiązać ten problem, należy utworzyć kontroler o alternatywnej nazwie. Przykład:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Włączanie funkcji Miejsca dewelopera w przypadku niepowodzenia podczas dodawania pul węzłów systemu Windows do klastra AKS

Obecnie usługa Azure Dev Spaces jest przeznaczona do uruchamiania tylko w zasobnikach i węzłach systemu Linux. Jeśli masz klaster AKS z pulą węzłów systemu Windows, należy się upewnić, że zasobniki usługi Azure Dev Spaces są zaplanowane tylko w węzłach systemu Linux. Jeśli zasobnik usługi Azure Dev Spaces jest zaplanowane do uruchomienia w węźle systemu Windows, ten zasobnik nie rozpocznie się i włączenie dev spaces zakończy się niepowodzeniem.

Aby rozwiązać ten problem, [dodaj skazę](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do klastra AKS, aby upewnić się, że zasobniki systemu Linux nie są zaplanowane do uruchomienia w węźle systemu Windows.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Błąd "Znaleziono nieskażone węzły systemu Linux w stanie Gotowy w klastrze. Musi istnieć co najmniej jeden nieskażony węzeł Linuksa w stanie Gotowy, aby wdrożyć zasobników w obszarze nazw "azds".

Usługa Azure Dev Spaces nie może utworzyć kontrolera w klastrze usługi AKS, ponieważ nie można znaleźć nieskażonego węzła w stanie *Gotowy* do zaplanowania zasobników. Usługa Azure Dev Spaces wymaga co najmniej jednego węzła systemu Linux w stanie *Gotowy,* który umożliwia planowanie zasobników bez określania tolerancji.

Aby rozwiązać ten problem, [należy zaktualizować konfigurację splint](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) w klastrze AKS, aby upewnić się, że co najmniej jeden węzeł systemu Linux umożliwia planowanie zasobników bez określania tolerancji. Upewnij się również, że co najmniej jeden węzeł systemu Linux, który umożliwia planowanie zasobników bez określania tolerancji, jest w stanie *Gotowy.* Jeśli węzeł zajmuje dużo czasu, aby osiągnąć *stan Gotowości,* można spróbować ponownie uruchomić węzeł.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Błąd "Azure Dev Spaces CLI nie został poprawnie zainstalowany" podczas uruchamiania az aks use-dev-spaces

Aktualizacja interfejsu wiersza polecenia azure dev spaces zmieniła ścieżkę instalacji. Jeśli używasz wersji interfejsu wiersza polecenia platformy Azure wcześniej niż 2.0.63, może zostać wyświetlony ten błąd. Aby wyświetlić wersję interfejsu wiersza polecenia platformy Azure, użyj programu `az --version`.

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Pomimo komunikatu o `az aks use-dev-spaces` błędzie podczas uruchamiania z wersją interfejsu wiersza polecenia platformy Azure przed 2.0.63, instalacja zakończy się pomyślnie. Możesz nadal używać `azds` bez żadnych problemów.

Aby rozwiązać ten problem, zaktualizuj instalację [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) do 2.0.63 lub nowszej. Ta aktualizacja rozwiąże komunikat o `az aks use-dev-spaces`błędzie wyświetlany podczas uruchamiania . Alternatywnie można nadal używać bieżącej wersji interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia azure dev spaces.

### <a name="error-unable-to-reach-kube-apiserver"></a>Błąd "Nie można skontaktować się z serwerem kube-apiserver"

Ten błąd może wystąpić, gdy usługa Azure Dev Spaces nie może połączyć się z serwerem interfejsu API klastra AKS. 

Jeśli dostęp do serwera interfejsu API klastra AKS jest zablokowany lub jeśli dla klastra AKS włączono [zakresy autoryzowanych adresów IP serwera interfejsu API,](../aks/api-server-authorized-ip-ranges.md) należy również [utworzyć](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) lub [zaktualizować](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) klaster, aby [zezwolić na dodatkowe zakresy oparte na regionie](https://github.com/Azure/dev-spaces/tree/master/public-ips).

Upewnij się, że serwer interfejsu API jest dostępny, uruchamiając polecenia kubectl. Jeśli serwer interfejsu API jest niedostępny, skontaktuj się z pomocą techniczną usługi AKS i spróbuj ponownie, gdy serwer interfejsu API działa.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Typowe problemy podczas przygotowywania projektu dla usługi Azure Dev Spaces

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Ostrzeżenie "Nie można wygenerować pliku dockerfile z powodu nieobsługiwałego języka"
Usługa Azure Dev Spaces zapewnia natywną obsługę języka C# i node.js. Po uruchomieniu `azds prep` w katalogu z kodem napisanym w jednym z tych języków usługa Azure Dev Spaces automatycznie tworzy odpowiedni plik Dockerfile.

Nadal można używać usługi Azure Dev Spaces z kodem napisanym w innych językach, `azds up` ale musisz ręcznie utworzyć plik Dockerfile przed uruchomieniem po raz pierwszy.

Jeśli aplikacja jest napisana w języku, który nie obsługuje usługi Azure Dev Spaces natywnie, należy podać odpowiedni plik Dockerfile do tworzenia obrazu kontenera z uruchomionym kodem. Docker zawiera [listę najlepszych rozwiązań dotyczących pisania dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) i [odwołania dockerfile,](https://docs.docker.com/engine/reference/builder/) które mogą pomóc w pisaniu Dockerfile, który odpowiada Twoim potrzebom.

Po uzyskaniu odpowiedniego pliku Dockerfile w `azds up` miejscu, można uruchomić do uruchomienia aplikacji w usłudze Azure Dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Typowe problemy podczas uruchamiania lub zatrzymywania usług za pomocą usługi Azure Dev Spaces

### <a name="error-config-file-not-found"></a>Błąd "Nie znaleziono pliku konfiguracyjnego:"

Podczas `azds up`pracy może pojawić się ten błąd. Oba `azds up` `azds prep` i muszą być uruchamiane z katalogu głównego projektu, który chcesz uruchomić w przestrzeni deweloperów.

Aby rozwiązać ten problem:
1. Zmień bieżący katalog na folder główny zawierający kod usługi. 
1. Jeśli nie masz pliku _azds.yaml_ w folderze `azds prep` kodu, uruchom do generowania zasobów Platformy Docker, Kubernetes i Azure Dev Spaces.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Limit czasu w "Oczekiwanie na kompilację obrazu kontenera..." krok z węzłami wirtualnymi AKS

Ten limit czasu występuje podczas próby uruchomienia usługi skonfigurowanej do uruchamiania w [węźle wirtualnym usługi AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal)za pomocą funkcji Dev Spaces. Przestrzenie deweloperskie obecnie nie obsługują tworzenia lub debugowania usług w węzłach wirtualnych.

Jeśli uruchomisz `azds up` `--verbose` za pomocą przełącznika lub włączysz pełne rejestrowanie w programie Visual Studio, zostaną wyświetlona dodatkowa szczegółowość:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Powyższe polecenie pokazuje, że zasobnik usługi został przypisany do *virtual-node-aci-linux*, który jest węzłem wirtualnym.

Aby rozwiązać ten problem, zaktualizuj wykres Helm dla usługi, aby usunąć wszystkie *nodeSelector* lub *wartości tolerancji,* które umożliwiają uruchomienie usługi w węźle wirtualnym. Wartości te są zazwyczaj definiowane `values.yaml` w pliku wykresu.

Nadal można użyć klastra AKS z włączoną funkcją węzłów wirtualnych, jeśli usługa, którą chcesz utworzyć lub debugować za pośrednictwem funkcji Dev Spaces, jest uruchamiana w węźle maszyny Wirtualnej. Uruchamianie usługi ze spacjami deweloperów w węźle maszyny Wirtualnej jest konfiguracją domyślną.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Błąd "nie można znaleźć gotowego zasobnika kultywatystw" podczas uruchamiania Dev Spaces

Ten błąd występuje, jeśli klient Helm nie może już rozmawiać z zasobnikiem tiller uruchomiony w klastrze.

Aby rozwiązać ten problem, uruchom ponownie węzły agenta w klastrze.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Błąd "release\<azds-\>-\<identifier\>-\<spacename\> servicename\<failed: services ' servicename\>' \<already\>exists" lub "Pull access denied for servicename , repozytorium nie istnieje lub może wymagać 'logowania docker'"

Te błędy mogą wystąpić, jeśli mieszasz uruchamianie `helm install` `helm upgrade`bezpośrednich `helm delete`poleceń Helm (takich `azds up` jak `azds down`, , lub ) z poleceniami Dev Spaces (takimi jak i ) wewnątrz tej samej przestrzeni deweloperskiej. Występują one, ponieważ Dev Spaces ma własne wystąpienie Tiller, które powoduje konflikt z własnym wystąpieniem Tiller uruchomionym w tej samej przestrzeni deweloperów.

Dobrze jest używać zarówno poleceń Helm, jak i dev spaces względem tego samego klastra AKS, ale każdy obszar nazw z obsługą obszarów deweloperskich powinien używać jednego lub drugiego.

Załóżmy na przykład, że polecenie Helm służy do uruchamiania całej aplikacji w nadrzędnej przestrzeni deweloperskiej. Można utworzyć przestrzenie deweloperów podrzędnych poza tym elementem nadrzędnym, użyć funkcji Dev Spaces do uruchamiania poszczególnych usług wewnątrz przestrzeni deweloperskich podrzędnych i testować usługi razem. Gdy wszystko będzie gotowe do zaewidencjonowania zmian, użyj polecenia Helm, aby wdrożyć zaktualizowany kod w nadrzędnym miejscu deweloperskim. Nie używaj `azds up` do uruchamiania zaktualizowanej usługi w nadrzędnym miejscu deweloperskim, ponieważ spowoduje to konflikt z usługą początkowo uruchomioną przy użyciu helma.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Istniejący plik dockerfile nie jest używany do tworzenia kontenera

Usługi Azure Dev Spaces można skonfigurować tak, aby wskazywały określony _plik dockerfile_ w projekcie. Jeśli okaże się, że usługa Azure Dev Spaces nie używa _pliku Dockerfile,_ którego oczekujesz do utworzenia kontenerów, może być konieczne jawne poinformowanie usługi Azure Dev Spaces, którego pliku Dockerfile użyć. 

Aby rozwiązać ten problem, otwórz plik _azds.yaml,_ który usługa Azure Dev Spaces wygenerowała w projekcie. Aktualizuj *konfiguracje: opracowanie: kompilacja: dockerfile,* aby wskazać plik dockerfile, którego chcesz użyć. Przykład:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Błąd "nieautoryzowane: wymagane uwierzytelnianie" podczas próby użycia obrazu platformy Docker z rejestru prywatnego

Używasz obrazu platformy Docker z rejestru prywatnego, który wymaga uwierzytelniania.

Aby rozwiązać ten problem, można zezwolić na tworzenie przestrzeni do uwierzytelniania i ściągania obrazów z tego prywatnego rejestru przy użyciu [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Aby użyć imagePullSecrets, [należy utworzyć klucz tajny Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) w obszarze nazw, w którym używasz obrazu. Następnie podaj sekret jako imagePullSecret w `azds.yaml`.

Poniżej znajduje się przykład określania imagePullSecrets w `azds.yaml`.

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Ustawienie imagePullSecrets `azds.yaml` w zastąpi imagePullSecrets określonych `values.yaml`w .

### <a name="error-service-cannot-be-started"></a>Błąd "Nie można uruchomić usługi".

Ten błąd może zostać wyświetlony, gdy nie można uruchomić kodu usługi. Przyczyną jest często w kodzie użytkownika. Aby uzyskać więcej informacji diagnostycznych, włącz bardziej szczegółowe rejestrowanie podczas uruchamiania usługi.

W wierszu polecenia `--verbose` użyj, aby włączyć bardziej szczegółowe rejestrowanie. Można również określić format `--output`wyjściowy za pomocą programu . Przykład:

```cmd
azds up --verbose --output json
```

W programie Visual Studio:

1. **Otwórz opcje >** i w obszarze Projekty i **rozwiązania**wybierz pozycję **Buduj i uruchamiaj**.
2. Zmień ustawienia **pełnej szczegółowości danych wyjściowych kompilacji projektu MSBuild** na **Szczegółowe** lub **Diagnostyczne**.

    ![Zrzut ekranu przedstawiający okno dialogowe Opcje narzędzi](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Ponowne uruchamianie usługi po ponownym utworzeniu kontrolera

Nie *można uruchomić usługi* błąd podczas próby ponownego uruchomienia usługi po usunięciu, a następnie ponownie utworzyć kontroler azure dev spaces skojarzony z tym klastrem. W tej sytuacji pełne dane wyjściowe zawiera następujący tekst:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ten błąd występuje, ponieważ usunięcie kontrolera miejsca dewelopera nie powoduje usunięcia usług wcześniej zainstalowanych przez ten kontroler. Ponowne tworzenie kontrolera, a następnie próba uruchomienia usług przy użyciu nowego kontrolera kończy się niepowodzeniem, ponieważ stare usługi są nadal w miejscu.

Aby rozwiązać ten problem, użyj `kubectl delete` polecenia, aby ręcznie usunąć stare usługi z klastra, a następnie ponownie uruchomić dev spaces, aby zainstalować nowe usługi.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Błąd "Nie można uruchomić usługi". podczas korzystania z wieloetapowych plików dockerfiles

Nie *można uruchomić usługi* błąd podczas korzystania z wieloetapowego pliku dockerfile. W tej sytuacji pełne dane wyjściowe zawiera następujący tekst:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Ten błąd występuje, ponieważ usługa Azure Dev Spaces obecnie nie obsługuje kompilacji wieloetapowych. Aby uniknąć kompilacji wieloetapowych, przepisz plik Dockerfile.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Ruch sieciowy nie jest przekazytryowany do klastra AKS podczas podłączania komputera deweloperskiego

Podczas korzystania z [usługi Azure Dev Spaces do łączenia klastra AKS z komputerem deweloperskim,](how-to/connect.md)może wystąpić problem, w którym ruch sieciowy nie jest przekazywany między komputerem deweloperskim a klastrem AKS.

Podczas podłączania komputera deweloperskiego do klastra AKS usługa Azure Dev Spaces przekazuje ruch sieciowy między klastrem AKS a komputerem deweloperskim, modyfikując plik komputera deweloperskiego. `hosts` Usługa Azure Dev Spaces `hosts` tworzy wpis z adresem usługi Kubernetes, którą zastępujesz jako nazwę hosta. Ten wpis jest używany z przekazywania portów do bezpośredniego ruchu sieciowego między komputerem deweloperskim i klastra AKS. Jeśli usługa na komputerze deweloperskim powoduje konflikt z portem usługi Kubernetes, którą zastępujesz, usługa Azure Dev Spaces nie może przesyłać dalej ruchu sieciowego dla usługi Kubernetes. Na przykład usługa *Windows BranchCache* jest zwykle powiązana z *wartością 0.0.0.0:80*, co spowoduje konflikt dla portu 80 na wszystkich lokalnych usługach IP.

Aby rozwiązać ten problem, należy zatrzymać wszystkie usługi lub procesy, które są w konflikcie z portem usługi Kubernetes, którą próbujesz zastąpić. Można użyć narzędzi, takich jak *netstat,* aby sprawdzić, jakie usługi lub procesy na komputerze deweloperskim są w konflikcie.

Na przykład, aby zatrzymać i wyłączyć usługę *Windows BranchCache:*
* Uruchom `services.msc` z wiersza polecenia.
* Kliknij prawym przyciskiem myszy *usługę BranchCache* i wybierz polecenie *Właściwości*.
* Kliknij *przycisk Zatrzymaj*.
* Opcjonalnie można go wyłączyć, ustawiając *typ uruchamiania* na *Wyłączone*.
* Kliknij przycisk *OK*.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Typowe problemy przy użyciu programu Visual Studio i kodu programu Visual Studio w miejscach deweloperów platformy Azure

### <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Brakuje wymaganych narzędzi i konfiguracji"

Ten błąd może wystąpić podczas uruchamiania programu VS Code: "[Azure Dev Spaces] Wymagane narzędzia i konfiguracje do tworzenia i debugowania '[nazwa projektu]'.
Błąd oznacza, że program azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak widać w programie VS Code.

Spróbuj uruchomić program VS Code z wiersza polecenia, w którym zmienna środowiskowa PATH jest ustawiona poprawnie.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Błąd "Wymagane narzędzia do tworzenia i debugowania 'projectname' są nieaktualne."

Ten błąd jest widoczny w programie Visual Studio Code, jeśli masz nowszą wersję rozszerzenia kodu VS dla usługi Azure Dev Spaces, ale starszą wersję interfejsu wiersza polecenia azure dev spaces.

Spróbuj pobrać i zainstalować najnowszą wersję interfejsu wiersza polecenia usługi Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Błąd: "Nie można odnaleźć rozszerzenia debugera dla typu:coreclr"

Ten błąd może wystąpić podczas uruchamiania debugera kodu programu Visual Studio. Rozszerzenie kodu programu VS dla języka C# może nie być zainstalowane na komputerze deweloperskim. Rozszerzenie języka C# obejmuje obsługę debugowania dla .NET Core (CoreCLR).

Aby rozwiązać ten problem, zainstaluj [rozszerzenie kodu VS dla języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Błąd "Typ debugowania skonfigurowany 'coreclr' nie jest obsługiwany"

Ten błąd może wystąpić podczas uruchamiania debugera kodu programu Visual Studio. Rozszerzenie kodu programu VS dla usługi Azure Dev Spaces może nie być zainstalowane na komputerze deweloperskim.

Aby rozwiązać ten problem, należy zainstalować [rozszerzenie kodu VS dla usługi Azure Dev Spaces](get-started-netcore.md).

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Błąd "Nieprawidłowa wartość 'cwd' '/src'. System nie może odnaleźć określonego pliku." lub "launch: program '/src/[ścieżka do projektu binarnego]' nie istnieje"

Ten błąd może wystąpić podczas uruchamiania debugera kodu programu Visual Studio. Domyślnie rozszerzenie kodu VS `src` używa jako katalogu roboczego dla projektu w kontenerze. Jeśli zaktualizowano, `Dockerfile` aby określić inny katalog roboczy, może pojawić się ten błąd.

Aby rozwiązać ten problem, zaktualizuj `launch.json` plik pod `.vscode` podkatalogiem folderu projektu. Zmień `configurations->cwd` dyrektywę, aby wskazać ten `WORKDIR` sam katalog, co `Dockerfile`zdefiniowany w projekcie . Może być również konieczne `configurations->program` zaktualizowanie dyrektywy, jak również.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd "Program potoku 'azds' zakończył się nieoczekiwanie z kodem 126."

Ten błąd może wystąpić podczas uruchamiania debugera kodu programu Visual Studio.

Aby rozwiązać ten problem, zamknij i otwórz ponownie visual studio code. Uruchom ponownie debuger.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Błąd "Zegarek wewnętrzny nie powiódł się: obejrzyj program ENOSPC" podczas podłączania debugowania do aplikacji Node.js

Ten błąd występuje, gdy węzeł z zasobnikiem z aplikacją Node.js, do której próbujesz dołączyć za pomocą debugera, przekroczył wartość *fs.inotify.max_user_watches.* W niektórych przypadkach [domyślna wartość *pliku fs.inotify.max_user_watches* może być zbyt mała, aby obsłużyć dołączanie debugera bezpośrednio do zasobnika](https://github.com/Azure/AKS/issues/772).

Tymczasowe obejście tego problemu polega na zwiększeniu wartości *pliku fs.inotify.max_user_watches* w każdym węźle w klastrze i ponownym uruchomieniu tego węzła, aby zmiany zostały wprowadzone.

## <a name="other-common-issues"></a>Inne typowe problemy

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Błąd "azds" nie jest rozpoznawany jako polecenie wewnętrzne lub zewnętrzne, działający program lub plik wsadowy

Ten błąd może `azds.exe` się zdarzyć, jeśli nie jest zainstalowany lub skonfigurowany poprawnie.

Aby rozwiązać ten problem:

1. Sprawdź lokalizację %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev `azds.exe`Spaces CLI dla . Jeśli istnieje, dodaj tę lokalizację do zmiennej środowiskowej PATH.
2. Jeśli `azds.exe` nie jest zainstalowany, uruchom następujące polecenie:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Błąd autoryzacji "Microsoft.DevSpaces/register/action"

Aby zarządzać usługami Azure Dev Spaces, musisz mieć dostęp do usługi *Owner* lub *Contributor* w ramach subskrypcji platformy Azure. Jeśli próbujesz zarządzać przestrzeniami deweloperów i nie masz dostępu *właściciela* lub *współautora* do skojarzonej subskrypcji platformy Azure, może zostać wyświetlony błąd autoryzacji. Przykład:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Aby rozwiązać ten problem, przy użyciu konta z *właścicielem* lub *współautorem* dostępu do subskrypcji platformy Azure, ręcznie zarejestrować obszar `Microsoft.DevSpaces` nazw:

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nowe zasobniki nie zaczynają

Inicjator Kubernetes nie można zastosować PodSpec dla nowych zasobników ze względu na zmiany uprawnień RBAC do roli *administratora klastra* w klastrze. Nowy zasobnik może również mieć nieprawidłowy PodSpec, na przykład konto usługi skojarzone z zasobnikiem już nie istnieje. Aby wyświetlić zasobników, które są w stanie *oczekiwania* z `kubectl get pods` powodu problemu inicjatora, należy użyć polecenia:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ten problem może mieć wpływ na zasobników we *wszystkich obszarach nazw* w klastrze, w tym obszarów nazw, w których usługa Azure Dev Spaces nie jest włączona.

Aby rozwiązać ten problem, [zaktualizuj interfejs wiersza polecenia dev spaces do najnowszej wersji,](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) a następnie usuń *konfigurację azds InitializerConfiguration* z kontrolera azure dev spaces:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Po usunięciu *azds InitializerConfiguration* z kontrolera usługi `kubectl delete` Azure Dev Spaces, użyj do usunięcia wszelkich zasobników w stanie *oczekujące.* Po usunięciu wszystkich oczekujących zasobników ponownie rozmieszczaj strąki.

Jeśli nowe zasobniki są nadal zablokowane w stanie *oczekujące* `kubectl delete` po ponowne wdrożenie, należy użyć do usunięcia wszelkich zasobników w stanie *oczekujące.* Po usunięciu wszystkich oczekujących zasobników usuń kontroler z klastra i zainstaluj go ponownie:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po ponownym zainstalowaniu kontrolera ponownie wdrożyć zasobników.

### <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nieprawidłowe uprawnienia RBAC do wywoływania kontrolera przestrzeni deweloperskich i interfejsów API

Użytkownik uzyskujący dostęp do kontrolera usługi Azure Dev Spaces musi mieć dostęp do odczytu *kubeconfig* administratora w klastrze AKS. Na przykład to uprawnienie jest dostępne we [wbudowanej roli administratora klastra usługi Azure Kubernetes](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Użytkownik uzyskujący dostęp do kontrolera usługi Azure Dev Spaces musi również mieć rolę *RBAC współautora* lub *właściciela* dla kontrolera. Więcej informacji na temat aktualizowania uprawnień użytkownika do klastra AKS można znaleźć [tutaj](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aby zaktualizować rolę RBAC użytkownika dla kontrolera:

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do grupy zasobów zawierającej kontroler, który jest zwykle taki sam jak klaster AKS.
1. Włącz pole wyboru *Pokaż ukryte typy.*
1. Kliknij kontroler.
1. Otwórz okienko *Kontrola dostępu (IAM).*
1. Kliknij kartę *Przypisania ról.*
1. Kliknij *pozycję Dodaj,* a następnie *dodaj przypisanie roli*.
    * W przypadku *opcji Rola*wybierz *opcję Współautor* lub *Właściciel*.
    * Aby *przypisać dostęp do*, wybierz *użytkownika, grupę lub jednostkę usługi Azure AD*.
    * W *polu Wybierz*wyszukaj użytkownika, którego chcesz nadać uprawnienia.
1. Kliknij przycisk *Zapisz*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Rozpoznawanie nazw DNS kończy się niepowodzeniem dla publicznego adresu URL skojarzonego z usługą Miejsca dewelopera

Można skonfigurować publiczny punkt końcowy adresu URL dla `--enable-ingress` usługi, `azds prep` określając przełącznik do `Publicly Accessible` polecenia lub zaznaczając pole wyboru w programie Visual Studio. Publiczna nazwa DNS jest automatycznie rejestrowana po uruchomieniu usługi w miejscach deweloperskich. Jeśli ta nazwa DNS nie jest *zarejestrowana, nie można wyświetlić strony* lub nie można uzyskać błędu *witryny* w przeglądarce internetowej podczas łączenia się z publicznym adresem URL.

Aby rozwiązać ten problem:

* Sprawdź stan wszystkich adresów URL skojarzonych z usługami Dev Spaces:

  ```console
  azds list-uris
  ```

* Jeśli adres URL znajduje się w stanie *Oczekujące,* miejsca deweloperów nadal czekają na zakończenie rejestracji DNS. Czasami rejestracja zajmuje kilka minut. Dev Spaces otwiera również tunel localhost dla każdej usługi, którego można używać podczas oczekiwania na rejestrację DNS.
* Jeśli adres URL pozostaje w stanie *Oczekujące* dłużej niż 5 minut, może to oznaczać problem z zewnętrznego zasobnika DNS, który tworzy publiczny punkt końcowy lub zasobnika kontrolera nginx, który uzyskuje publiczny punkt końcowy. Użyj następujących poleceń, aby usunąć te zasobniki i zezwolić aksowi na ich automatyczne odtworzenie:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "błąd połączenia nadrzędnego lub odłączenie/zresetowanie przed nagłówkami"

Ten błąd może wystąpić podczas próby uzyskania dostępu do usługi. Na przykład, gdy przejdziesz do adresu URL usługi w przeglądarce. Ten błąd oznacza, że port kontenera nie jest dostępny. Może to z przyczyn:

* Kontener jest nadal w trakcie budowy i wdrażania. Ten problem może wystąpić `azds up` po uruchomieniu lub uruchomieniu debugera, a następnie spróbuj uzyskać dostęp do kontenera, zanim pomyślnie wdrożony.
* Konfiguracja portu nie jest spójna w pliku _dockerfile,_ wykresie helm i każdym kodzie serwera, który otwiera port.

Aby rozwiązać ten problem:

1. Jeśli kontener jest w trakcie tworzenia/wdrażania, można odczekać 2-3 sekundy i spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu w następujących zasobach:
    * **[Wykres steru:](https://docs.helm.sh)** `service.port` Określony `deployment.containerPort` przez i w values.yaml szkieletu przez `azds prep` polecenie.
    * Wszystkie porty otwierane w kodzie aplikacji, na przykład w pliku Node.js:`var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Nie można odnaleźć nazwy typu lub obszaru nazw "MyLibrary"

Nie można odnaleźć używanego projektu biblioteki. W obszarze deweloperów kontekst kompilacji jest domyślnie na poziomie projektu/usługi.  

Aby rozwiązać ten problem:

1. Zmodyfikuj plik, `azds.yaml` aby ustawić kontekst kompilacji na poziomie rozwiązania.
2. Zmodyfikuj `Dockerfile` pliki i `Dockerfile.develop` pliki, `.csproj`aby odwołać się do plików projektu, na przykład poprawnie względem nowego kontekstu kompilacji.
3. Dodaj `.dockerignore` w tym samym katalogu `.sln` co plik.
4. W `.dockerignore` razie potrzeby zaktualizuj dodatkowe wpisy.

Przykład można znaleźć [tutaj](https://github.com/sgreenmsft/buildcontextsample).

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Skalowanie w poziomie zasobnika nie działa w przestrzeni deweloperów

Po uruchomieniu usługi w przestrzeni dev, zasobnik tej usługi jest [wstrzykiwany z dodatkowych kontenerów do instrumentacji](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) i wszystkie kontenery w zasobniku muszą mieć limity zasobów i żądania ustawione dla skalowania automatycznego zasobu poziomego.

Aby rozwiązać ten problem, zastosuj żądanie zasobu i ograniczyć do wstrzykniętych kontenerów Dev Spaces. Żądania zasobów i limity mogą być stosowane do wstrzykniętego `azds.io/proxy-resources` kontenera (devspaces-proxy) przez dodanie adnotacji do specyfikacji zasobu. Wartość powinna być ustawiona na obiekt JSON reprezentujący sekcję zasobów specyfikacji kontenera dla serwera proxy.

Poniżej znajduje się przykład adnotacji zasobów proxy, która ma być zastosowana do specyfikacji zasobnika.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Włączanie obszarów deweloperskich platformy Azure w istniejącym obszarze nazw z uruchomionymi zasobnikami

Być może masz istniejący klaster AKS i obszar nazw z uruchomionymi zasobnikami, w których chcesz włączyć usługi Azure Dev Spaces.

Aby włączyć usługi Azure Dev Spaces w istniejącym obszarze `use-dev-spaces` nazw `kubectl` w klastrze AKS, uruchom i użyj, aby ponownie uruchomić wszystkie zasobników w tym obszarze nazw.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Po ponownym uruchomieniu zasobników można rozpocząć korzystanie z istniejącego obszaru nazw za pomocą usługi Azure Dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Włączanie usługi Azure Dev Spaces w klastrze AKS z ograniczonym ruchem wychodzącym dla węzłów klastra

Aby włączyć usługi Azure Dev Spaces w klastrze AKS, dla którego ruch wychodzący z węzłów klastra jest ograniczony, należy zezwolić na następujące nazwy FQDN:

| Nazwa FQDN                                    | Port      | Użycie      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | Protokół HTTPS:443 | Aby wyciągnąć linux alpine i inne obrazy Azure Dev Spaces |
| gcr.io | HTTP:443 | Aby wyciągnąć helm / tiller obrazy|
| storage.googleapis.com | HTTP:443 | Aby wyciągnąć helm / tiller obrazy|
| azds-<guid>. <location>azds.io .azds.io | Protokół HTTPS:443 | Aby komunikować się z usługami zaplecza usługi Azure Dev Spaces dla kontrolera. Dokładną nazwy FQDN można znaleźć w "dataplaneFqdn"\.w %USERPROFILE% azds\settings.json|

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Błąd "Nie można \<odnaleźć klastra klastra\> w identyfikatorze \<\>subskrypcji"

Ten błąd może wystąpić, jeśli plik kubeconfig jest przeznaczone dla innego klastra lub subskrypcji niż próbujesz użyć z narzędzi po stronie klienta usługi Azure Dev Spaces. Narzędzie po stronie klienta usługi Azure Dev Spaces replikuje zachowanie *kubectl*, który używa [jednego lub więcej plików kubeconfig](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) do wybierania i komunikowania się z klastrem.

Aby rozwiązać ten problem:

* Służy `az aks use-dev-spaces -g <resource group name> -n <cluster name>` do aktualizowania bieżącego kontekstu. To polecenie umożliwia również usługi Azure Dev Spaces w klastrze AKS, jeśli nie jest jeszcze włączona. Alternatywnie można użyć `kubectl config use-context <cluster name>` do aktualizacji bieżącego kontekstu.
* Służy `az account show` do pokazywania bieżącej subskrypcji platformy Azure, na którą kierowano reklamy i sprawdzania, czy jest poprawna. Możesz zmienić kierowaną subskrypcję `az account set`za pomocą programu .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Błąd podczas używania funkcji Odstępy deweloperów po obróceniu certyfikatów usługi AKS

Po [obróceniu certyfikatów w klastrze AKS](../aks/certificate-rotation.md)niektóre operacje, takie jak `azds space list` i `azds up` zakończy się niepowodzeniem. Należy również odświeżyć certyfikaty na kontrolerze usługi Azure Dev Spaces po obróceniu certyfikatów w klastrze.

Aby rozwiązać ten problem, upewnij się, że *kubeconfig* ma zaktualizowane certyfikaty, `az aks get-credentials` a następnie uruchom `azds controller refresh-credentials` polecenie. Przykład:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
