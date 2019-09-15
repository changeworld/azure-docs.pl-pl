---
title: Rozwiązywanie problemów
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s '
ms.openlocfilehash: b16a7d874f15747c14df1d728be824fac76de2be
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993960"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Ten przewodnik zawiera informacje o typowych problemów, które mogą mieć w przypadku korzystania z usługi Azure Dev miejsca do magazynowania.

Jeśli wystąpi problem podczas korzystania z Azure Dev Spaces, Utwórz [problem w repozytorium Azure dev Spaces GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Włączanie rejestrowania szczegółowe

Aby bardziej efektywnie rozwiązywać problemy, może to pomóc w tworzeniu bardziej szczegółowych dzienników do przeglądu.

Rozszerzenie programu Visual Studio można ustawić `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` zmiennej środowiskowej 1. Pamiętaj ponownie uruchomić program Visual Studio dla zmiennej środowiskowej zaczęły obowiązywać. Po włączeniu szczegółowe dzienniki są zapisywane w `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalogu.

W interfejsie wiersza polecenia, użytkownik może zapewniać dane wyjściowe informacji podczas wykonywania polecenia przy użyciu `--verbose` przełącznika. Można również przeglądać szczegółowe dzienniki w `%TEMP%\Azure Dev Spaces`. Na komputerze Mac, można znaleźć katalogu TEMP, uruchamiając `echo $TMPDIR` z okna terminalu. Na komputerze z systemem Linux katalogu TEMP jest zazwyczaj `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debugowanie usług z wieloma wystąpieniami

Obecnie Azure Dev Spaces sprawdza się najlepiej podczas debugowania pojedynczego wystąpienia lub pod. Plik azds. YAML zawiera ustawienie *replicaCount*, które wskazuje liczbę uruchomionych dla usługi. Jeśli zmienisz replicaCount w celu skonfigurowania aplikacji tak, aby uruchamiała wiele zasobników dla danej usługi, debuger przyłączy się do pierwszego pod, gdy zostanie wyświetlony alfabetycznie. Debuger dołącza się do innego, pod, kiedy pierwotne odzyskanie, prawdopodobnie wystąpiło nieoczekiwane zachowanie.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Błąd "Nie można utworzyć kontroler Azure Dev miejsca do magazynowania"

### <a name="reason"></a>Reason
Można napotkać ten błąd, gdy coś pójdzie nie tak z tworzeniem kontrolera. Jeśli jest to błąd przejściowy, Usuń i Utwórz ponownie kontroler, aby rozwiązać ten problem.

### <a name="try"></a>Spróbuj

Usuń kontroler:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Aby usunąć kontroler, należy użyć interfejsu wiersza polecenia Azure Dev Spaces. Nie można usunąć kontrolera z programu Visual Studio. Nie można również zainstalować interfejsu wiersza polecenia Azure Dev Spaces w Azure Cloud Shell, dlatego nie można usunąć kontrolera z Azure Cloud Shell.

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia Azure Dev Spaces, możesz go najpierw zainstalować przy użyciu następującego polecenie, a następnie usunąć kontroler:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Ponowne tworzenie kontrolera może odbywać się z interfejsu wiersza polecenia lub programu Visual Studio. Przykłady można znaleźć w temacie [programowanie zespołowe](quickstart-team-development.md) lub opracowywanie za pomocą przewodnika Szybki Start dla [platformy .NET Core](quickstart-netcore-visualstudio.md) .

## <a name="error-service-cannot-be-started"></a>Błąd "nie można uruchomić usługi."

Ten błąd może pojawić się po kodzie usługi nie powiedzie się. Przyczyną jest często w kodzie użytkownika. Aby uzyskać więcej informacji diagnostycznych, należy wprowadzić następujące zmiany w Twoich poleceń i ustawień:

### <a name="try"></a>Wypróbuj:

W wierszu polecenia:

Korzystając z _azds.exe_, użyj--verbose opcji wiersza polecenia i użyj opcji wiersza polecenia dane wyjściowe, aby określić format danych wyjściowych.
 
```cmd
azds up --verbose --output json
```

W programie Visual Studio:

1. Otwórz **Narzędzia > Opcje** i w obszarze **projekty i rozwiązania**, wybierz **kompilowanie i uruchamianie**.
2. Zmień ustawienia **poziom szczegółowości danych wyjściowych kompilacji projektu programu MSBuild** do **szczegółowe** lub **diagnostycznych**.

    ![Zrzut ekranu opcji narzędzi okna dialogowego](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Wieloetapowe pliki Dockerfile:
W przypadku korzystania z wieloetapowej pliku dockerfile *nie można uruchomić usługi* . W tej sytuacji pełne dane wyjściowe zawierają następujący tekst:

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

Ten błąd występuje, ponieważ węzły AKS korzystają ze starszej wersji platformy Docker, która nie obsługuje kompilacji wieloetapowych. Aby uniknąć kompilacji wieloetapowych, napisz ponownie pliku dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Ponowne uruchamianie usługi po ponownym utworzeniu kontrolera
Wystąpił błąd podczas próby ponownego uruchomienia usługi po usunięciu i ponownym utworzeniu kontrolera Azure dev Spaces skojarzonego z tym klastrem. W tej sytuacji pełne dane wyjściowe zawierają następujący tekst:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ten błąd występuje, ponieważ usunięcie kontrolera usługi Dev Spaces nie powoduje usunięcia usług wcześniej zainstalowanych przez ten kontroler. Ponowne tworzenie kontrolera, a następnie próbując do uruchamiania usług przy użyciu nowego kontrolera nie działa, ponieważ stare usługi wciąż znajdują się w miejscu.

Aby rozwiązać ten problem, należy użyć `kubectl delete` polecenia, aby ręcznie usunąć stare usługi z klastra, a następnie ponownie uruchomić obszary deweloperów, aby zainstalować nowe usługi.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Rozpoznawanie nazw DNS nie powiedzie się publiczny adres URL skojarzony z usługą Dev miejsca do magazynowania

Możesz skonfigurować publiczny punkt końcowy adresu URL dla usługi `--public` `Publicly Accessible` , określając przełącznik `azds prep` do polecenia lub zaznaczając pole wyboru w programie Visual Studio. Publiczna nazwa DNS jest automatycznie rejestrowana podczas uruchamiania usługi w obszarze dev Spaces. Jeśli ta nazwa DNS nie jest zarejestrowana, zobaczysz, że *nie można wyświetlić strony* lub *nie można uzyskać dostępu do witryny* w przeglądarce sieci Web podczas łączenia się z publicznym adresem URL.

### <a name="try"></a>Wypróbuj:

Służy następujące polecenie, aby wyświetlić listę wszystkich adresów URL skojarzonych z usługami Dev miejsca do magazynowania:

```cmd
azds list-uris
```

Jeśli adres URL znajduje się w *oczekujące* stanu, oznacza to, czy Dev miejsca do magazynowania nadal oczekuje na rejestrację DNS do wykonania. Czasami zajmuje kilka minut, zanim rejestracji w celu ukończenia. Miejsca do magazynowania dev otwiera również tunel localhost dla każdej usługi, którego można użyć podczas oczekiwania na rejestrację serwera DNS.

Jeśli adres URL pozostaje w stanie *oczekiwania* przez więcej niż 5 minut, może to wskazywać na problem z zewnętrznym systemem DNS, który tworzy publiczny punkt końcowy lub kontroler Nginx transferu danych przychodzących pod warunkiem, że uzyskuje publiczny punkt końcowy. Można użyć następujących poleceń, można usunąć tych zasobników. AKS automatycznie odtworzy usuniętych zasobników.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Wymagane narzędzia i konfiguracje są niedostępne"

Ten błąd może wystąpić przy uruchamianiu programu VS Code: "[Azure Dev miejsca do magazynowania] narzędzi i konfiguracji, aby kompilować i debugować,"[Nazwa projektu]"Brak wymaganych."
Ten błąd oznacza, że tego azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak pokazano w programie VS Code.

### <a name="try"></a>Wypróbuj:

Uruchom program VS Code z poziomu wiersza polecenia, w którym zmiennej środowiskowej PATH jest prawidłowo.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Błąd "wymagane narzędzia do kompilacji i debugowania" ProjectName "są nieaktualne".

Ten błąd jest wyświetlany w Visual Studio Code, jeśli masz nowszą wersję rozszerzenia VS Code dla Azure Dev Spaces, ale starszą wersję interfejsu wiersza polecenia Azure Dev Spaces.

### <a name="try"></a>Spróbuj

Pobierz i zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure Dev Spaces:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Błąd "azds" nie jest rozpoznawana jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy
 
Można napotkać ten błąd, jeśli azds.exe nie jest zainstalowane lub prawidłowo skonfigurowane.

### <a name="try"></a>Wypróbuj:

1. Sprawdź lokalizację% ProgramFiles%/Microsoft SDKs\Azure\Azure dev Spaces CLI dla azds. exe. Jeśli jest określony, dodanie jej do zmiennej środowiskowej PATH.
2. Jeśli nie zainstalowano azds.exe, uruchom następujące polecenie:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Ostrzeżenie "pliku Dockerfile nie można wygenerować z powodu nieobsługiwany język"
Usługa Azure Dev do magazynowania zapewnia macierzystą obsługę języka C# i Node.js. Po uruchomieniu *azds prep* w katalogu, zawierającego kod napisany w jednym z tych języków, Azure Dev miejsca do magazynowania automatycznie utworzy odpowiedni plik Dockerfile za Ciebie.

Nadal możesz używać Azure Dev Spaces z kodem zapisanym w innych językach, ale musisz ręcznie utworzyć pliku dockerfile przed uruchomieniem *azds* po raz pierwszy.

### <a name="try"></a>Wypróbuj:
Jeśli aplikacja jest zapisywana w języku, który Azure Dev Spaces nie obsługuje natywnie, musisz podać odpowiedni pliku dockerfile, aby utworzyć obraz kontenera, w którym działa kod. Platforma Docker zawiera [listę najlepszych rozwiązań dotyczących pisania wieloetapowe dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) i [odwołania pliku dockerfile](https://docs.docker.com/engine/reference/builder/) , które mogą pomóc napisać pliku dockerfile, który odpowiada Twoim potrzebom.

Po utworzeniu odpowiedni plik Dockerfile w miejscu, możesz kontynuować uruchamianie *azds się* do uruchamiania aplikacji w usłudze Azure Dev spacjach.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "nadrzędne błąd połączenia lub odłącz/reset przed nagłówki"
Podczas próby uzyskania dostępu do usługi, zostanie wyświetlony ten błąd. Na przykład, gdy nastąpi przejście do adresu URL usługi, w przeglądarce. 

### <a name="reason"></a>Przyczyna 
Port kontenera nie jest dostępna. Ten problem może wystąpić, ponieważ: 
* Kontener trwa nadal trwa utworzeniu i wdrożeniu. Ten problem może wystąpić, jeśli uruchomisz `azds up` lub uruchom debuger, a następnie spróbuj dostępu do kontenera zanim została pomyślnie wdrożona.
* Konfiguracja portów nie jest spójność w ramach usługi _pliku Dockerfile_, narzędzia Helm i wszelki kod serwera, który otwiera port.

### <a name="try"></a>Wypróbuj:
1. Jeśli kontener jest właśnie utworzone/wdrażane, możesz odczekaj 2 – 3 sekund, a następnie spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu. Określone numery portów powinny być **identyczne** we wszystkich następujących zasobach:
    * **Pliku dockerfile** Określone przez `EXPOSE` instrukcję.
    * **[Wykres Helm](https://docs.helm.sh):** Określone przez `externalPort` wartości i `internalPort` dla usługi `values.yml` (często znajdujące się w pliku),
    * Wszystkie porty są otwarte w kodzie aplikacji, na przykład w środowisku Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nie można odnaleźć pliku konfiguracji
Możesz uruchomić `azds up` i występuje następujący błąd: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Przyczyna
Należy uruchomić `azds up` z katalogu głównego kodu chcesz uruchomić, a następnie należy zainicjować folder kodu do uruchomienia przy użyciu usługi Azure Dev miejsca do magazynowania.

### <a name="try"></a>Wypróbuj:
1. Zmień bieżący katalog do folderu głównego zawierającego kod w języku usługi. 
1. Jeśli nie masz _azds.yaml_ pliku w folderze kodu uruchamiania `azds prep` wygenerować zasoby platformy Docker, Kubernetes i usługi Azure Dev miejsca do magazynowania.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd: "Azds" programu potoku "zakończył się nieoczekiwanie z kodem 126."
Uruchamianie debugera programu VS Code czasami może spowodować błąd.

### <a name="try"></a>Wypróbuj:
1. Zamknij i otwórz ponownie program VS Code.
2. Ponownie naciśnij klawisz F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Błąd "Nie można odnaleźć rozszerzenia debugera dla typu: coreclr" debugowania
Uruchamianie debugera programu VS Code zgłasza błąd: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenia kodu programu VS dla języka C# zainstalowane na komputerze deweloperskim. C# Rozszerzenie obejmuje obsługę debugowania dla platformy .NET Core (CoreCLR).

### <a name="try"></a>Wypróbuj:
Zainstaluj [rozszerzenia kodu programu VS dla języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Błąd debugowania "skonfigurowane debugowania typu"coreclr"nie jest obsługiwany"
Uruchamianie debugera programu VS Code zgłasza błąd: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenie programu VS Code Azure Dev spacje zainstalowany na komputerze deweloperskim.

### <a name="try"></a>Wypróbuj:
Zainstaluj [rozszerzenie programu VS Code dla usługi Azure Dev miejsca do magazynowania](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Debugowanie błąd "nieprawidłowy"cwd"wartość" / src ". System nie może odnaleźć określonego pliku." lub "Uruchom: program"/ src / [ścieżka do pliku binarnego projektu]"nie istnieje."
Uruchamianie debugera programu VS Code zgłasza błąd `Invalid 'cwd' value '/src'. The system cannot find the file specified.` i/lub `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Przyczyna
Domyślnie używa rozszerzenia programu VS Code `src` jako katalog roboczy dla projektu w kontenerze. Jeśli zaktualizowano swoje `Dockerfile` Aby określić inny katalog roboczy, może zostać wyświetlony ten błąd.

### <a name="try"></a>Wypróbuj:
Aktualizacja `launch.json` plik `.vscode` podkatalogu w folderze projektu. Zmiana `configurations->cwd` dyrektywy, aby wskazywała na tym samym katalogu co `WORKDIR` zdefiniowane w swoim projekcie `Dockerfile`. Również może być konieczne zaktualizowanie `configurations->program` również dyrektywę.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Nie można odnaleźć nazwy typu lub przestrzeni nazw "MojaBiblioteka"

### <a name="reason"></a>Reason 
Kontekst kompilacji jest domyślnie na poziomie projektu/usługi, dlatego nie można odnaleźć projektu biblioteki, którego używasz.

### <a name="try"></a>Wypróbuj:
Co trzeba zrobić:
1. Modyfikowanie _azds.yaml_ pliku, aby ustawić kontekstu kompilacji na poziomie rozwiązania.
2. Modyfikowanie _pliku Dockerfile_ i _Dockerfile.develop_ pliki do odwoływania się do projektu ( _.csproj_) pliki poprawnie, względem nowej kompilacji kontekstu.
3. Miejsce _.dockerignore_ pliku obok pliku .sln i modyfikować zgodnie z potrzebami.

Na przykład można znaleźć https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Błąd autoryzacji "Microsoft.DevSpaces/register/action"
Aby zarządzać Azure Dev Spaces, musisz mieć dostęp do *właściciela* lub współautora w ramach subskrypcji platformy Azure. Ten błąd może pojawić się, jeśli próbujesz zarządzać miejscami deweloperskimi, a nie masz dostępu *właściciela* lub współautora do skojarzonej subskrypcji platformy Azure.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
Wybranej subskrypcji platformy Azure nie został zarejestrowany `Microsoft.DevSpaces` przestrzeni nazw.

### <a name="try"></a>Wypróbuj:
Dostęp właściciela lub współautora do subskrypcji platformy Azure można uruchomić następujące polecenie z wiersza polecenia platformy Azure, aby ręcznie zarejestrować `Microsoft.DevSpaces` przestrzeni nazw:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Miejsca deweloperskie przekroczą limit czasu podczas *oczekiwania na kompilację obrazu kontenera...* Step z węzłami WIRTUALNYmi AKS

### <a name="reason"></a>Reason
Ten limit czasu występuje podczas próby użycia obszarów deweloperskich do uruchomienia usługi, która jest skonfigurowana do uruchamiania w [węźle wirtualnym AKS](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Funkcja Spaces dev nie obsługuje obecnie usług kompilowania i debugowania w węzłach wirtualnych.

Jeśli uruchamiasz `azds up` polecenie `--verbose` z przełącznikiem lub włączysz pełne rejestrowanie w programie Visual Studio, zobaczysz dodatkowe szczegóły:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Powyższe polecenie pokazuje, że usługa poniżej została przypisana do *wirtualnego węzła-ACI-Linux*, który jest węzłem wirtualnym.

### <a name="try"></a>Wypróbuj:
Zaktualizuj wykres Helm dla usługi, aby usunąć wszelkie wartości *nodeSelector* i/lub *tolerowania* , które umożliwiają uruchomienie usługi w węźle wirtualnym. Te wartości są zwykle zdefiniowane w `values.yaml` pliku wykresu.

Nadal można korzystać z klastra AKS, który ma włączoną funkcję węzłów wirtualnych, jeśli usługa, którą chcesz skompilować/debugować za pomocą funkcji miejsca do deweloperów, działa w węźle maszyny wirtualnej. Jest to konfiguracja domyślna.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Błąd: nie można znaleźć pod tiller gotowe" podczas uruchamiania Dev miejsca do magazynowania

### <a name="reason"></a>Przyczyna
Ten błąd występuje, jeśli klient narzędzia Helm już nie może komunikować się pod Tiller uruchomionych w klastrze.

### <a name="try"></a>Wypróbuj:
Ponowne uruchamianie węzłów agenta w klastrze zwykle rozwiązuje ten problem.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>"Błąd: Release azds-\<identyfikator\>\<-spacename\>-\<ServiceName nie powiodło się: Services" ServiceName\> \<\>"już istnieje" lub "odmowa dostępu do \<ściągania\>dla ServiceName, repozytorium nie istnieje lub może wymagać" Docker login' "

### <a name="reason"></a>Reason
Te błędy mogą wystąpić, jeśli Mieszasz uruchomione bezpośrednie polecenia Helm (takie `helm install`jak `helm upgrade`, lub `helm delete`) z poleceniami dev Spaces ( `azds up` takimi jak i `azds down`) w tym samym obszarze dev. Są one wykonywane, ponieważ spacje do deweloperów mają własne wystąpienie do przyciągania, które powoduje konflikt z własnym wystąpieniem programu do wykonywania w tym samym obszarze dev.

### <a name="try"></a>Wypróbuj:
Warto używać zarówno poleceń Helm, jak i poleceń dev Spaces z tym samym klastrem AKS, ale każda przestrzeń nazw z włączoną funkcją dev Spaces powinna używać jednego lub drugiego.

Załóżmy na przykład, że używasz polecenia Helm do uruchamiania całej aplikacji w nadrzędnym obszarze dev. Można utworzyć podrzędne miejsca deweloperskie dla tego elementu nadrzędnego, użyć funkcji miejsca do magazynowania w celu uruchomienia poszczególnych usług w podrzędnych miejscach dev i przetestowania usług. Gdy wszystko będzie gotowe do zaewidencjonowania zmian, użyj polecenia Helm w celu wdrożenia zaktualizowanego kodu w nadrzędnym obszarze dev. Nie należy `azds up` używać do uruchamiania zaktualizowanej usługi w nadrzędnym obszarze dev, ponieważ spowoduje to konflikt z usługą uruchomioną początkowo przy użyciu Helm.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Serwer proxy usługi Azure Dev miejsca do magazynowania może zakłócać innych zasobników w miejsce dev

### <a name="reason"></a>Przyczyna
Po włączeniu Dev miejsca do magazynowania w przestrzeni nazw w klastrze AKS dodatkowy kontener o nazwie _mindaro proxy_ jest instalowany na każdym zasobników działającym wewnątrz tego obszaru nazw. Ten kontener przechwytuje wywołania usług w obszarze pod, które są integralną częścią możliwości deweloperskich dla deweloperów. może jednak zakłócać niektóre usługi działające w tych zasobnikach. Wiadomo, że istnieje możliwość zakłócania korzystania z usługi Redis w pamięci podręcznej platformy Azure, co powoduje błędy połączeń i błędy w komunikacji podstawowej/dodatkowej.

### <a name="try"></a>Wypróbuj:
Zależne zasobniki można przenieść do przestrzeni nazw w klastrze, w którym _nie_ włączono miejsc dev. Pozostała część aplikacji może nadal działać w przestrzeni nazw z włączoną funkcją dev Spaces. Dev miejsca do magazynowania nie spowoduje zainstalowania _mindaro proxy_ kontenerów wewnątrz spacje innych deweloperów włączone obszary nazw.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Usługa Azure Dev spacje wydają się użyć Mój istniejący plik Dockerfile do utworzenia kontenera

### <a name="reason"></a>Przyczyna
Azure Dev do magazynowania można skonfigurować, aby wskazać konkretną _pliku Dockerfile_ w projekcie. Jeśli jest wyświetlany Azure Dev Spaces nie używa _pliku dockerfile_ , aby móc kompilować kontenery, może być konieczne jawne poinformowanie Azure dev Spaces, których pliku dockerfile użyć. 

### <a name="try"></a>Wypróbuj:
Otwórz plik _azds. YAML_ , który Azure dev Spaces wygenerowany w projekcie. Użyj opcji *Konfiguracje-> Programowanie-> kompilacja-> pliku dockerfile* , aby wskazać pliku dockerfile, którego chcesz użyć:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Błąd "nie można wykonać monitorowania wewnętrznego: Watch ENOSPC" podczas dołączania debugowania do aplikacji node. js

### <a name="reason"></a>Reason

Węzeł, na którym działa program, z aplikacją Node. js, którą próbujesz dołączyć do debugera, przekroczył wartość *FS. inotify. Max _user_watches* . W niektórych przypadkach [Domyślna wartość *FS. inotify. Max _user_watches* może być zbyt mała, aby można było obsłużyć bezpośrednie dołączenie debugera do poziomu](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Spróbuj
Tymczasowe obejście tego problemu polega na zwiększeniu wartości *FS. inotify. Max _user_watches* na każdym węźle klastra i ponownym uruchomieniu tego węzła, aby zmiany zaczęły obowiązywać.

## <a name="new-pods-are-not-starting"></a>Nowe zasobniki nie są uruchamiane

### <a name="reason"></a>Reason

Inicjator Kubernetes nie może zastosować PodSpec dla nowych, z powodu zmian uprawnień RBAC w roli *administratora klastra* w klastrze. Nowy element pod może również mieć nieprawidłową PodSpec, na przykład konto usługi skojarzone ze znakiem "już nie istnieje". Aby wyświetlić zasobniki, które znajdują się w stanie *oczekiwania* ze względu na problem z `kubectl get pods` inicjatorem, użyj polecenia:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ten problem może mieć wpływ na *wszystkie przestrzenie nazw* w klastrze, w tym przestrzenie nazw, w których Azure dev Spaces nie jest włączona.

### <a name="try"></a>Spróbuj

[Aktualizacja interfejsu wiersza polecenia dev Spaces do najnowszej wersji](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , a następnie usunięcie *azds InitializerConfiguration* z kontrolera Azure dev Spaces:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Po usunięciu *azds InitializerConfiguration* z kontrolera Azure dev Spaces Użyj `kubectl delete` , aby usunąć wszystkie zasobniki w stanie *oczekiwania* . Po usunięciu wszystkich oczekujących zasobników należy ponownie wdrożyć Twoje dane.

Jeśli nowe zasobniki są nadal zablokowane w stanie *oczekiwania* po ponownej instalacji, użyj `kubectl delete` , aby usunąć wszystkie zasobniki w stanie *oczekiwania* . Po usunięciu wszystkich oczekujących zasobników Usuń kontroler z klastra i zainstaluj go ponownie:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po ponownym zainstalowaniu kontrolera ponownie Wdróż swój zasobnik.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Nieprawidłowe uprawnienia RBAC do wywoływania kontrolera i interfejsów API programu dev Spaces

### <a name="reason"></a>Reason
Użytkownik, który uzyskuje dostęp do kontrolera Azure Dev Spaces, musi mieć dostęp do odczytu *kubeconfig* administratora w klastrze AKS. Na przykład to uprawnienie jest dostępne w wbudowanej [roli administratora klastra usługi Kubernetes platformy Azure](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Użytkownik uzyskujący dostęp do kontrolera Azure Dev Spaces musi mieć także rolę RBAC współautora lub *właściciela* dla kontrolera.

### <a name="try"></a>Spróbuj
Więcej szczegółów na temat aktualizowania uprawnień użytkownika do klastra AKS są dostępne [tutaj](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Aby zaktualizować rolę RBAC użytkownika dla kontrolera:

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Przejdź do grupy zasobów zawierającej kontroler, która jest zwykle taka sama jak w przypadku klastra AKS.
1. Włącz pole wyboru *Pokaż ukryte typy* .
1. Kliknij kontroler.
1. Otwórz okienko *Access Control (IAM)* .
1. Kliknij kartę *przypisania ról* .
1. Kliknij przycisk *Dodaj* , a następnie *Dodaj przypisanie roli*.
    * W obszarze *rola* wybierz opcję *współautor* lub *właściciel*.
    * Aby *przypisać dostęp do* wybranych *użytkowników, grup lub nazw podmiotów usługi Azure AD*.
    * Dla *opcji wybierz* wyszukiwanie użytkownika, którym chcesz nadać uprawnienia.
1. Kliknij polecenie *Zapisz*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Tworzenie kontrolera kończy się niepowodzeniem z powodu długości nazwy kontrolera

### <a name="reason"></a>Reason
Nazwa kontrolera Azure Dev Spaces nie może być dłuższa niż 31 znaków. Jeśli nazwa kontrolera przekracza 31 znaków po włączeniu funkcji miejsca do magazynowania w klastrze AKS lub utworzeniu kontrolera, zostanie wyświetlony następujący błąd:

*Nie można utworzyć kontrolera usługi Dev Spaces dla klastra "a-Controller-Name-to-------Long-AKS-Wschodnie": Nazwa kontrolera Azure Dev Spaces "a-nazwa---------------------------------- Ograniczenia zostały naruszone: Nazwy kontrolerów Azure Dev Spaces mogą zawierać maksymalnie 31 znaków*

### <a name="try"></a>Spróbuj

Utwórz kontroler z alternatywną nazwą:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Włączanie funkcji Spaces dev, gdy pule węzłów systemu Windows są dodawane do klastra AKS

### <a name="reason"></a>Reason
Obecnie Azure Dev Spaces jest przeznaczony do uruchamiania tylko w przypadku systemów i węzłów systemu Linux. Jeśli masz klaster AKS z pulą węzłów systemu Windows, musisz upewnić się, że Azure Dev Spaces są planowane tylko w węzłach z systemem Linux. Jeśli Azure Dev Spaces pod zaplanowano uruchomienie w węźle systemu Windows, nie zostanie on uruchomiony i nie będzie można włączyć funkcji tworzenia miejsc deweloperskich.

### <a name="try"></a>Spróbuj
[Dodaj](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) do klastra AKS, aby upewnić się, że w węźle systemu Windows nie zaplanowano uruchamiania systemów Linux.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Błąd "znaleziono brak przygotowanych węzłów systemu Linux w stanie gotowe w klastrze. Do wdrożenia w przestrzeni nazw "azds" musi być co najmniej jeden węzeł systemu Linux, który jest w stanie gotowości.

### <a name="reason"></a>Reason

Azure Dev Spaces nie może utworzyć kontrolera w klastrze AKS, ponieważ nie mógł znaleźć w stanie *gotowym* węzła, który ma zaplanowaną wartość. Azure Dev Spaces wymaga co najmniej jednego węzła systemu Linux w stanie gotowości, który umożliwia planowanie zasobników bez określania tolerowania.

### <a name="try"></a>Spróbuj
[Zaktualizuj konfigurację swojego programu](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) w klastrze AKS, aby upewnić się, że co najmniej jeden węzeł systemu Linux zezwala na planowanie zasobników bez określania tolerowanych danych. Ponadto upewnij się, że co najmniej jeden węzeł systemu Linux, który umożliwia planowanie zasobników bez określania tolerowania, jest w stanie *gotowe* . Jeśli Twój węzeł zajmuje dużo czasu na osiągnięcie stanu gotowości , możesz spróbować ponownie uruchomić węzeł.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Błąd "Azure Dev Spaces interfejs wiersza polecenia nie został poprawnie zainstalowany" podczas uruchamiania`az aks use-dev-spaces`

### <a name="reason"></a>Reason
Aktualizacja interfejsu wiersza polecenia Azure Dev Spaces została zmieniona ścieżka instalacji. Jeśli używasz wersji interfejsu wiersza polecenia platformy Azure starszej niż 2.0.63, może pojawić się ten błąd. Aby wyświetlić wersję interfejsu wiersza polecenia platformy Azure, użyj `az --version`polecenia.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Mimo tego, że podczas uruchamiania `az aks use-dev-spaces` programu z wersją interfejsu wiersza polecenia platformy Azure przed 2.0.63m zostanie wyświetlony komunikat o błędzie, instalacja zakończy się pomyślnie. Można nadal używać `azds` bez żadnych problemów.

### <a name="try"></a>Spróbuj
Zaktualizuj instalację [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) do 2.0.63 lub nowszego. Spowoduje to rozwiązanie komunikatu o błędzie, który pojawia się `az aks use-dev-spaces`podczas uruchamiania. Alternatywnie można nadal używać bieżącej wersji interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia Azure Dev Spaces.


## <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Skalowanie w poziomie nie działa w obszarze dev

### <a name="reason"></a>Reason

Po uruchomieniu usługi w obszarze deweloperskim, ta usługa jest wprowadzana [z dodatkowymi kontenerami dla Instrumentacji](how-dev-spaces-works.md#prepare-your-aks-cluster) , a wszystkie kontenery w miejscu muszą mieć limity zasobów i żądania ustawione na automatyczne skalowanie w poziomie. 


Żądania zasobów i limity można zastosować dla wstrzykniętego kontenera (devspaces-proxy), dodając `azds.io/proxy-resources` adnotację do specyfikacji pod. Wartość powinna być ustawiona na obiekt JSON reprezentujący sekcję zasobów specyfikacji kontenera dla serwera proxy.

### <a name="try"></a>Spróbuj

Poniżej znajduje się przykład adnotacji zasobów serwera proxy, która ma zostać zastosowana do specyfikacji pod.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

## <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Błąd "nieautoryzowane: wymagane uwierzytelnienie" podczas próby użycia obrazu platformy Docker z rejestru prywatnego

### <a name="reason"></a>Reason

Używasz obrazu platformy Docker z rejestru prywatnego wymagającego uwierzytelniania. Można zezwolić na przestrzenie deweloperskie do uwierzytelniania i ściągania obrazów z tego rejestru prywatnego przy użyciu usługi [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets).

### <a name="try"></a>Spróbuj

Aby użyć imagePullSecrets, [Utwórz wpis tajny Kubernetes](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) w przestrzeni nazw, w której jest używany obraz. Podaj klucz tajny jako imagePullSecret w `azds.yaml`.

Poniżej znajduje się przykład określania imagePullSecrets w `azds.yaml`.

```
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
> Ustawienie imagePullSecrets w `azds.yaml` programie spowoduje przesłonięcie imagePullSecrets `values.yaml`określonych w elemencie.
