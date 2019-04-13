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
keywords: 'Docker, Kubernetes, Azure, usługi AKS, usłudze Azure Kubernetes Service, kontenerów, narzędzia Helm, usługa siatki, routing siatki usługi, narzędzia kubectl, k8s '
ms.openlocfilehash: 16b33203099765633d6bc5992fdc266aa1f28a26
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548784"
---
# <a name="troubleshooting-guide"></a>Przewodnik rozwiązywania problemów

Ten przewodnik zawiera informacje o typowych problemów, które mogą mieć w przypadku korzystania z usługi Azure Dev miejsca do magazynowania.

Jeśli masz problem podczas korzystania z usługi Azure Dev miejsca do magazynowania, należy utworzyć [problemu w repozytorium Azure Dev miejsca do magazynowania w witrynie GitHub](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Włączanie rejestrowania szczegółowe

Do rozwiązywania problemów z bardziej efektywne, może pomóc tworzyć bardziej szczegółowych dzienników do przeglądu.

Rozszerzenie programu Visual Studio można ustawić `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` zmiennej środowiskowej 1. Pamiętaj ponownie uruchomić program Visual Studio dla zmiennej środowiskowej zaczęły obowiązywać. Po włączeniu szczegółowe dzienniki są zapisywane w swojej `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` katalogu.

W interfejsie wiersza polecenia, użytkownik może zapewniać dane wyjściowe informacji podczas wykonywania polecenia przy użyciu `--verbose` przełącznika. Można również przeglądać szczegółowe dzienniki w `%TEMP%\Azure Dev Spaces`. Na komputerze Mac, można znaleźć katalogu TEMP, uruchamiając `echo $TMPDIR` z okna terminalu. Na komputerze z systemem Linux katalogu TEMP jest zazwyczaj `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Debugowanie usług z wieloma wystąpieniami

Obecnie działa usługa Azure Dev miejsca do magazynowania najlepsze podczas debugowania pojedynczego wystąpienia lub zasobników. Plik azds.yaml zawiera ustawienie, *replicaCount*, który wskazuje liczbę zasobników, uruchomieniami Kubernetes dla usługi. Jeśli zmienisz replicaCount skonfigurować aplikację do uruchamiania wielu zasobnikach dla danej usługi, debuger dołącza do pierwszego zasobnik, gdy alfabetycznym. Debuger dołącza do różnych pod podczas odtwarzania oryginalnego zasobników, przez co nieoczekiwane zachowanie.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Błąd "Nie można utworzyć kontroler Azure Dev miejsca do magazynowania"

Można napotkać ten błąd, gdy coś pójdzie nie tak z tworzeniem kontrolera. Jeśli jest to błąd przejściowy, Usuń i Utwórz ponownie kontrolera, aby rozwiązać ten problem.

### <a name="try"></a>Wypróbuj:

Aby usunąć kontroler, należy użyć wiersza polecenia platformy Azure Dev miejsca do magazynowania. Nie jest możliwe tylko w programie Visual Studio lub usłudze Cloud Shell. Aby zainstalować interfejs wiersza polecenia AZDS, najpierw zainstaluj wiersza polecenia platformy Azure, a następnie uruchom następujące polecenie:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A następnie uruchom to polecenie, aby usunąć kontrolera:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Ponowne tworzenie kontrolera może odbywać się z interfejsu wiersza polecenia lub programu Visual Studio. Postępuj zgodnie z instrukcjami w samouczkach tak, jakby uruchamiania po raz pierwszy.


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
Pojawi się *nie można uruchomić usługi* błąd podczas korzystania z pliku Dockerfile wieloetapowych. W takiej sytuacji pełne dane wyjściowe zawiera następujący tekst:

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

Ten błąd występuje, ponieważ węzłów AKS uruchomić starszej wersji platformy docker, który nie obsługuje wieloetapowych kompilacji. Aby uniknąć wieloetapowych kompilacji, należy zmodyfikować z pliku Dockerfile.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Ponowne uruchomienie usługi po ponownego utworzenia kontrolera
Pojawi się *nie można uruchomić usługi* wystąpił błąd podczas próby ponownego uruchomienia usługi po usunięte i tworzony ponownie kontroler Azure Dev miejsca do magazynowania, skojarzone z tym klastrem. W takiej sytuacji pełne dane wyjściowe zawiera następujący tekst:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Ten błąd występuje, ponieważ usunięcie kontrolera Dev miejsca do magazynowania nie powoduje usunięcia usług zainstalowanych wcześniej przez tego kontrolera. Ponowne tworzenie kontrolera, a następnie próbując do uruchamiania usług przy użyciu nowego kontrolera nie działa, ponieważ stare usługi wciąż znajdują się w miejscu.

Aby rozwiązać ten problem, należy użyć `kubectl delete` polecenie, aby ręcznie usunąć stary usługi z klastrem, należy ponownie uruchomić Dev miejsca do magazynowania, aby zainstalować nowych usług.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Rozpoznawanie nazw DNS nie powiedzie się publiczny adres URL skojarzony z usługą Dev miejsca do magazynowania

Można skonfigurować publiczny punkt końcowy adres URL usługi, określając `--public` przełączyć się do `azds prep` polecenia albo zaznaczyć `Publicly Accessible` pole wyboru w programie Visual Studio. Publiczna nazwa DNS jest automatycznie rejestrowane po uruchomieniu usługi w Dev miejsca do magazynowania. Jeśli ta nazwa DNS nie jest zarejestrowany, zostanie wyświetlony *nie można wyświetlić strony* lub *nie można nawiązać połączenia z witryny* błędu w przeglądarce sieci web podczas nawiązywania połączenia publicznego adresu URL.

### <a name="try"></a>Wypróbuj:

Służy następujące polecenie, aby wyświetlić listę wszystkich adresów URL skojarzonych z usługami Dev miejsca do magazynowania:

```cmd
azds list-uris
```

Jeśli adres URL znajduje się w *oczekujące* stanu, oznacza to, czy Dev miejsca do magazynowania nadal oczekuje na rejestrację DNS do wykonania. Czasami zajmuje kilka minut, zanim rejestracji w celu ukończenia. Miejsca do magazynowania dev otwiera również tunel localhost dla każdej usługi, którego można użyć podczas oczekiwania na rejestrację serwera DNS.

Jeśli adres URL pozostaje w *oczekujące* stanu na więcej niż 5 minut, może to oznaczać problem z zewnętrznego zasobnika DNS, który tworzy publiczny punkt końcowy lub zasobnika kontrolera ruch przychodzący serwera nginx, który uzyskuje publiczny punkt końcowy. Można użyć następujących poleceń, można usunąć tych zasobników. AKS odtworzy automatycznie usunięte zasobników.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Błąd "Wymagane narzędzia i konfiguracje są niedostępne"

Ten błąd może wystąpić przy uruchamianiu programu VS Code: "[Azure Dev miejsca do magazynowania] narzędzi i konfiguracji, aby kompilować i debugować,"[Nazwa projektu]"Brak wymaganych."
Ten błąd oznacza, że tego azds.exe nie znajduje się w zmiennej środowiskowej PATH, jak pokazano w programie VS Code.

### <a name="try"></a>Wypróbuj:

Uruchom program VS Code z poziomu wiersza polecenia, w którym zmiennej środowiskowej PATH jest prawidłowo.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Błąd "wymagane narzędzia do tworzenia i debugowania"projectname"są nieaktualne".

Zostanie wyświetlony ten błąd, w programie Visual Studio Code, jeśli ma nowszą wersję rozszerzeń programu VS Code dla usługi Azure Dev miejsca do magazynowania, ale starszą wersję interfejsu wiersza polecenia Azure Dev miejsca do magazynowania.

### <a name="try"></a>Spróbuj

Pobierz i zainstaluj najnowszą wersję interfejsu wiersza polecenia Azure Dev miejsca do magazynowania:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Błąd "azds" nie jest rozpoznawana jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy
 
Można napotkać ten błąd, jeśli azds.exe nie jest zainstalowane lub prawidłowo skonfigurowane.

### <a name="try"></a>Wypróbuj:

1. Sprawdź %ProgramFiles%/Microsoft lokalizacji SDKs\Azure\Azure Dev miejsca do magazynowania interfejs wiersza polecenia (wersja zapoznawcza) dla azds.exe. Jeśli jest określony, dodanie jej do zmiennej środowiskowej PATH.
2. Jeśli nie zainstalowano azds.exe, uruchom następujące polecenie:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Ostrzeżenie "pliku Dockerfile nie można wygenerować z powodu nieobsługiwany język"
Usługa Azure Dev do magazynowania zapewnia macierzystą obsługę języka C# i Node.js. Po uruchomieniu *azds prep* w katalogu, zawierającego kod napisany w jednym z tych języków, Azure Dev miejsca do magazynowania automatycznie utworzy odpowiedni plik Dockerfile za Ciebie.

Można nadal używać usługi Azure Dev miejsca do magazynowania przy użyciu kodu napisanego w innych językach, ale należy ręcznie utworzyć plik Dockerfile przed uruchomieniem *azds się* po raz pierwszy.

### <a name="try"></a>Wypróbuj:
Jeśli aplikacja został napisany w języku, że Azure Dev miejsca do magazynowania nie obsługuje natywnie, musisz podać odpowiednie Dockerfile, aby utworzyć obraz kontenera uruchomienia kodu. Środowisko docker zawiera [listę najlepsze rozwiązania dotyczące zapisywania plików Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) i [odwołanie do pliku Dockerfile](https://docs.docker.com/engine/reference/builder/) napisać plik Dockerfile, który odpowiada Twoim potrzebom, może pomóc.

Po utworzeniu odpowiedni plik Dockerfile w miejscu, możesz kontynuować uruchamianie *azds się* do uruchamiania aplikacji w usłudze Azure Dev spacjach.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Błąd "nadrzędne błąd połączenia lub odłącz/reset przed nagłówki"
Podczas próby uzyskania dostępu do usługi, zostanie wyświetlony ten błąd. Na przykład, gdy nastąpi przejście do adresu URL usługi, w przeglądarce. 

### <a name="reason"></a>Przyczyna 
Port kontenera nie jest dostępna. Ten problem może wystąpić, ponieważ: 
* Kontener trwa nadal trwa utworzeniu i wdrożeniu. Ten problem może wystąpić, jeśli uruchomisz `azds up` lub uruchom debuger, a następnie spróbuj dostępu do kontenera zanim została pomyślnie wdrożona.
* Konfiguracja portów nie jest spójność w ramach usługi _pliku Dockerfile_, narzędzia Helm i wszelki kod serwera, który otwiera port.

### <a name="try"></a>Wypróbuj:
1. Jeśli kontener jest właśnie utworzone/wdrażane, możesz odczekaj 2 – 3 sekund, a następnie spróbuj ponownie uzyskać dostęp do usługi. 
1. Sprawdź konfigurację portu. Numery określony port powinien być **identyczne** we wszystkich następujące zasoby:
    * **Dockerfile:** Określony przez `EXPOSE` instrukcji.
    * **[Narzędzia Helm](https://docs.helm.sh):** Określony przez `externalPort` i `internalPort` wartości dla usługi (często znajduje się w `values.yml` pliku),
    * Wszystkie porty są otwarte w kodzie aplikacji, na przykład w środowisku Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nie można odnaleźć pliku konfiguracji
Możesz uruchomić `azds up` i występuje następujący błąd: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Przyczyna
Należy uruchomić `azds up` z katalogu głównego kodu chcesz uruchomić, a następnie należy zainicjować folder kodu do uruchomienia przy użyciu usługi Azure Dev miejsca do magazynowania.

### <a name="try"></a>Wypróbuj:
1. Zmień bieżący katalog do folderu głównego zawierającego kod w języku usługi. 
1. Jeśli nie masz _azds.yaml_ pliku w folderze kodu uruchamiania `azds prep` wygenerować zasoby platformy Docker, Kubernetes i usługi Azure Dev miejsca do magazynowania.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Błąd: "Program potoków"azds"nieoczekiwanie zakończył działanie z kodem 126."
Uruchamianie debugera programu VS Code czasami może spowodować błąd.

### <a name="try"></a>Wypróbuj:
1. Zamknij i otwórz ponownie program VS Code.
2. Ponownie naciśnij klawisz F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Błąd "Nie można odnaleźć rozszerzenia debugera dla typu: coreclr" debugowania
Uruchamianie debugera programu VS Code zgłasza błąd: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Przyczyna
Nie masz rozszerzenia kodu programu VS dla języka C# zainstalowane na komputerze deweloperskim. C# Rozszerzenie zawiera obsługę debugowania dla platformy .NET Core (CoreCLR).

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

### <a name="reason"></a>Przyczyna 
Kontekst kompilacji jest domyślnie na poziomie projektu/usługi, w związku z tym projektu biblioteki, którego używasz nie można odnaleźć.

### <a name="try"></a>Wypróbuj:
Co trzeba zrobić:
1. Modyfikowanie _azds.yaml_ pliku, aby ustawić kontekstu kompilacji na poziomie rozwiązania.
2. Modyfikowanie _pliku Dockerfile_ i _Dockerfile.develop_ pliki do odwoływania się do projektu (_.csproj_) pliki poprawnie, względem nowej kompilacji kontekstu.
3. Miejsce _.dockerignore_ pliku obok pliku .sln i modyfikować zgodnie z potrzebami.

Na przykład można znaleźć https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Błąd autoryzacji "Microsoft.DevSpaces/register/action"
Potrzebujesz *właściciela* lub *Współautor* dostępu w Twojej subskrypcji platformy Azure do zarządzania usługi Azure Dev miejsca do magazynowania. Może zostać wyświetlony ten błąd, jeśli próbujesz Zarządzanie Dev miejsca do magazynowania, a nie masz *właściciela* lub *Współautor* dostęp do skojarzonej subskrypcji platformy Azure.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Przyczyna
Wybranej subskrypcji platformy Azure nie został zarejestrowany `Microsoft.DevSpaces` przestrzeni nazw.

### <a name="try"></a>Wypróbuj:
Dostęp właściciela lub współautora do subskrypcji platformy Azure można uruchomić następujące polecenie z wiersza polecenia platformy Azure, aby ręcznie zarejestrować `Microsoft.DevSpaces` przestrzeni nazw:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Limit czasu tworzenia miejsca do magazynowania w *oczekiwania na kompilację obrazu kontenera...*  kroku przy użyciu wirtualnych węzłów AKS

### <a name="reason"></a>Przyczyna
Limit czasu występuje, gdy próbują użyć standardowego miejsca do magazynowania, aby uruchomić to usługa, która jest skonfigurowana do uruchamiania [AKS wirtualnego węzła](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Miejsca do magazynowania dev aktualnie nie obsługuje kompilowania lub debugowania usług wirtualnych węzłów.

Jeśli uruchamiasz `azds up` z `--verbose` przełącznika lub Włącz pełne rejestrowanie w programie Visual Studio, zobacz dodatkowe szczegóły:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Powyższe polecenie pokazuje, że zasobnika usługi został przypisany do *wirtualnego node-aci-linux*, który jest węzłem wirtualnym.

### <a name="try"></a>Wypróbuj:
Aktualizowanie wykresu Helm dla usługi usunąć wszystkie *nodeSelector* i/lub *tolerations* wartości, które usługa do uruchamiania na wirtualny węzeł. Te wartości są zazwyczaj zdefiniowane na wykresie `values.yaml` pliku.

Można nadal używać klastra usługi AKS z funkcją wirtualnych węzłów włączone, jeśli usługa, którą chcesz kompilacji/debug za pośrednictwem standardowego miejsca do magazynowania działa w węźle maszyny Wirtualnej. To jest domyślna konfiguracja.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>"Błąd: nie można znaleźć pod tiller gotowe" podczas uruchamiania Dev miejsca do magazynowania

### <a name="reason"></a>Przyczyna
Ten błąd występuje, jeśli klient narzędzia Helm już nie może komunikować się pod Tiller uruchomionych w klastrze.

### <a name="try"></a>Wypróbuj:
Ponowne uruchamianie węzłów agenta w klastrze zwykle rozwiązuje ten problem.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Serwer proxy usługi Azure Dev miejsca do magazynowania może zakłócać innych zasobników w miejsce dev

### <a name="reason"></a>Przyczyna
Po włączeniu Dev miejsca do magazynowania w przestrzeni nazw w klastrze AKS dodatkowy kontener o nazwie _mindaro proxy_ jest instalowany na każdym zasobników działającym wewnątrz tego obszaru nazw. Ten kontener przechwytuje wywołania usług pod, który jest dołączony do funkcji tworzenia zespołu miejsca do magazynowania Dev; jednak może to zakłócać pewne usługi działające w tych zasobników. Jest on znany kolidować z zasobników systemem pamięć podręczna systemu Azure dla usługi Redis, powodując błędy połączeń i błędów w komunikacie główny/podrzędny.

### <a name="try"></a>Wypróbuj:
Dotyczy zasobników można przenieść do przestrzeni nazw w ramach klastra, który wykonuje _nie_ zawierać spacji Dev włączone. Pozostała część aplikacji, mogą w dalszym ciągu uruchamiać wewnątrz przestrzeni nazw z obsługą tworzenia miejsca do magazynowania. Dev miejsca do magazynowania nie spowoduje zainstalowania _mindaro proxy_ kontenerów wewnątrz spacje innych deweloperów włączone obszary nazw.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Usługa Azure Dev spacje wydają się użyć Mój istniejący plik Dockerfile do utworzenia kontenera

### <a name="reason"></a>Przyczyna
Azure Dev do magazynowania można skonfigurować, aby wskazać konkretną _pliku Dockerfile_ w projekcie. Jeśli wydaje się nie używa usługi Azure Dev miejsca do magazynowania _pliku Dockerfile_ oczekujesz, że tworzenie kontenerów, konieczne może być jawnie Poinformuj miejsca do magazynowania Azure Dev które pliku Dockerfile do użycia. 

### <a name="try"></a>Wypróbuj:
Otwórz _azds.yaml_ pliku tego Dev Azure miejsca do magazynowania wygenerowanymi w projekcie. Użyj *konfiguracje -> opracowywanie -> kompilacja -> plik dockerfile* dyrektywy wskaż plik Dockerfile, którego chcesz użyć:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Błąd "wewnętrzny Obejrzyj nie powiodło się: Obejrzyj ENOSPC" podczas dołączania, debugowania aplikacji Node.js

### <a name="reason"></a>Przyczyna

Węzeł uruchomiony zasobnik z aplikacją Node.js próbujesz połączyć się za pomocą debugera przekroczyła *fs.inotify.max_user_watches* wartości. W niektórych przypadkach [wartość domyślną *fs.inotify.max_user_watches* może być zbyt mała, aby obsłużyć dołączanie debugera bezpośrednio do zasobnika](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Spróbuj
Tymczasowe obejście tego problemu jest zwiększenie wartości *fs.inotify.max_user_watches* w każdym węźle w klastrze i ponownie uruchomić ten węzeł, aby zmiany zaczęły obowiązywać.

## <a name="new-pods-are-not-starting"></a>Nowych zasobników nie jest uruchamiany.

### <a name="reason"></a>Przyczyna

Inicjator Kubernetes nie można zastosować PodSpec dla nowych zasobników z powodu zmiany uprawnień RBAC do *administratora klastra* rolę w klastrze. Nowych zasobników może być również nieprawidłowa PodSpec, na przykład konto usługi, skojarzone z zasobnik już nie istnieje. Aby wyświetlić zasobników, które znajdują się w *oczekujące* stanów spowodowanych przez problem inicjatora, użyj `kubectl get pods` polecenia:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Ten problem może mieć wpływ na zasobników w *wszystkie przestrzenie nazw* w klastrze, w tym przestrzenie nazw, w którym nie włączono usługi Azure Dev miejsca do magazynowania.

### <a name="try"></a>Spróbuj

[Aktualizowanie interfejsu wiersza polecenia miejsca do magazynowania dla deweloperów do najnowszej wersji](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) , a następnie usunięcie *azds InitializerConfiguration* od administratora usługi Azure Dev miejsca do magazynowania:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

Po usunięciu *azds InitializerConfiguration* od administratora usługi Azure Dev miejsca do magazynowania, należy użyć `kubectl delete` do usunięcia wszelkich zasobników w *oczekujące* stanu. Gdy wszystkie oczekujące zasobników zostały usunięte, Wdróż ponownie zasobników.

W przypadku nowych zasobników nadal są zablokowane w *oczekujące* stanu po ponownego wdrożenia, użyj `kubectl delete` do usunięcia wszelkich zasobników w *oczekujące* stanu. Gdy wszystkie oczekujące zasobników zostały usunięte, usunąć kontroler z klastra i ponownie ją zainstaluj:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Po ponownym zainstalowaniu kontrolera sieci, należy ponownie wdrożyć zasobników.