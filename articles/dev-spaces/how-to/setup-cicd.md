---
title: Używanie ciągłej integracji/ciągłego wdrażania w ramach usługi Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Szybkie tworzenie w środowisku Kubernetes za pomocą kontenerów i mikrousług na platformie Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 01e1401c5054eb56d4e2313b5e03ce5a36d1b301
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704065"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Ciągła Integracja/ciągłe dostarczanie za pomocą usługi Azure Dev miejsca do magazynowania

Ten artykuł przeprowadzi Cię przez skonfigurowanie ciągłej integracji/ciągłego wdrażania (CI/CD) do usługi Azure Kubernetes Service (AKS) ze spacjami Dev włączone. Ciągła Integracja/ciągłe dostarczanie do usługi AKS zezwala na aktualizacje aplikacji do automatycznego wdrożenia zawsze wtedy, gdy kod zatwierdzone są wypychane do repozytorium źródłowym. Przy użyciu ciągłej integracji/ciągłego wdrażania w połączeniu ze spacjami Dev włączone klastra przydaje się, ponieważ może prowadzić aktualne dla zespołu do pracy z linii bazowej w aplikacji.

![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](../media/common/ci-cd-simple.png)

Chociaż w tym artykule opisano DevOps platformy Azure, te same pojęcia dotyczą będzie systemów ciągłej integracji/ciągłego Dostarczania, takich jak Jenkins, TeamCity, itp.

## <a name="prerequisites"></a>Wymagania wstępne
* [Klastrze platformy Azure Kubernetes Service (AKS) z włączoną miejsca do magazynowania Azure Dev](../get-started-netcore.md)
* [Usługa Azure Dev miejsca do magazynowania zainstalować interfejs wiersza polecenia](upgrade-tools.md)
* [Usługa Azure DevOps organizacji projektu](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Usługa Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Usługa Azure Container Registry [konta administratora](../../container-registry/container-registry-authentication.md#admin-account) dostępnych szczegółów
* [Autoryzuj klastra usługi AKS do ściągania z usługi Azure Container Registry](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Pobierz przykładowy kod
Ze względu na czas Utwórz rozwidlenie repozytorium GitHub przykładowy kod. Przejdź do https://github.com/Azure/dev-spaces i wybierz **rozwidlenia**. Po zakończeniu procesu rozwidlenia **klonowania** wersji rozwidlonego repozytorium lokalnie. Domyślnie _wzorca_ będzie można wyewidencjonować gałęzi, ale dołączyliśmy pewne zmiany zaoszczędzić czas w _azds_updates_ gałęzi, która powinna być również przesyłanych podczas swojego rozwidlenia. _Azds_updates_ gałąź zawiera aktualizacje, poprosimy o ręcznym wprowadzeniu w sekcji samouczka Dev miejsca do magazynowania, a także niektóre wstępnie przygotowane pliki YAML i JSON upraszczające wdrożenia systemu ciągłej integracji/ciągłego wdrażania. Możesz użyć polecenia, takie jak `git checkout -b azds_updates origin/azds_updates` wyewidencjonować _azds_updates_ gałęzi w repozytorium lokalnym.

## <a name="dev-spaces-setup"></a>Ustawienia tworzenia miejsca do magazynowania
Utwórz nowy obszar o nazwie _dev_ przy użyciu `azds space select` polecenia. _Dev_ miejsca używaną przez potok ciągłej integracji/ciągłego Dostarczania do wypychania zmian w kodzie. Zostanie również służyć do tworzenia _podrzędnych miejsca do magazynowania_ na podstawie _dev_.

```cmd
azds space select -n dev
```

Po wyświetleniu monitu o wybranie nadrzędnej przestrzeni deweloperskiej wybierz pozycję _\<none\>_ .

Po utworzeniu obszaru dev, należy określić sufiks hosta. Użyj `azds show-context` polecenia w celu wyświetlenia sufiks hosta kontrolera danych przychodzących usługi Azure Dev miejsca do magazynowania.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

W powyższym przykładzie sufiks hosta jest _fedcba098.eus.azds.io_. Ta wartość jest używana później, podczas tworzenia definicji wydania.

_Dev_ miejsca będzie zawsze zawierać najnowszy stan repozytorium planu bazowego, dzięki czemu deweloperzy mogą tworzyć _podrzędnych miejsca do magazynowania_ z _dev_ do testowania ich izolowane zmiany w kontekście większej aplikacji. To pojęcie została omówiona bardziej szczegółowo w samouczkach Dev miejsca do magazynowania.

## <a name="creating-the-build-definition"></a>Tworzenie definicji kompilacji
Otwórz projekt zespołu DevOps platformy Azure w przeglądarce sieci web i przejdź do _potoki_ sekcji. Najpierw kliknij zdjęcie w profilu w prawym górnym rogu witryny DevOps platformy Azure, otwórz okienko funkcje wersji zapoznawczej i wyłączyć _YAML nowy potok tworzenia środowiska_:

![Okienko funkcje wersji zapoznawczej otwarcia](../media/common/preview-feature-open.png)

Możliwość wyłączenia:

![Tworzenie nowego potoku YAML opcji środowiska korzystania](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> DevOps platformy Azure _YAML nowy potok tworzenia środowiska_ funkcji (wersja zapoznawcza) jest w konflikcie z tworzenia wstępnie zdefiniowanych tworzenie potoków w tej chwili. Należy wyłączyć ją teraz w celu wdrożenia nasz potok kompilacji wstępnie zdefiniowane.

W _azds_updates_ gałęzi uwzględniliśmy prosty [YAML potoku Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) definiujący kroków kompilacji, wymaganych do *mywebapi* i *webfrontend* .

W zależności od języka, który został wybrany potok YAML zostało zaewidencjonowane w ścieżce podobne do: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Aby utworzyć potok z tego pliku:
1. Na stronie głównej projektu DevOps, przejdź do potoków > kompilacji.
1. Wybierz opcję, aby utworzyć **New** tworzenie potoku.
1. Wybierz **GitHub** jako źródło, autoryzować przy użyciu konta usługi GitHub, jeśli to konieczne i wybierz pozycję _azds_updates_ gałęzi z wersją rozwidlonego repozytorium Przykładowa aplikacja dev-miejsca do magazynowania.
1. Wybierz **konfiguracji jako kodu**, lub **YAML**, jako szablonu.
1. Teraz jest wyświetlana strona konfiguracji dla potoku kompilacji. Jak wspomniano powyżej, przejdź do ścieżki charakterystyczny dla **ścieżka pliku YAML** przy użyciu **...**  przycisku. Na przykład `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Przejdź do **zmienne** kartę.
1. Ręcznie Dodaj _dockerId_ jako zmienna, która jest nazwą użytkownika Twojej [konta administratora usługi Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Wymienione w sekcji wymagania wstępne artykułu)
1. Ręcznie Dodaj _dockerPassword_ jako zmienna, która jest hasło usługi [konta administratora usługi Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Pamiętaj określić _dockerPassword_ jako poufne (na przykład, wybierając ikonę blokady) ze względów bezpieczeństwa.
1. Wybierz **Zapisz k & olejką**.

Masz teraz to rozwiązanie ciągłej integracji, które automatycznie utworzy *mywebapi* i *webfrontend* żadnych aktualizacji wypchnięte do _azds_updates_ gałęzi rozwidlenia w serwisie GitHub. Możesz sprawdzić, przechodząc do witryny Azure portal, wybierając usługi Azure Container Registry i przeglądanie zostały wypchnięte obrazów platformy Docker **repozytoriów** kartę. Może potrwać kilka minut, zanim obrazów do tworzenia i są wyświetlane w rejestrze kontenera.

![Usługa Azure Container Registry repozytoriów](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Tworzenie definicji wydania

1. Na stronie głównej projektu DevOps, przejdź do potoków > wersje
1. Jeśli pracujesz w całkowicie nowy projekt DevOps, która jeszcze nie zawiera definicji wydania, należy najpierw utworzyć definicję wydania pusty, przed kontynuowaniem. Opcja importowania nie są wyświetlane w interfejsie użytkownika do momentu istniejącej definicji wydania.
1. Po lewej stronie, kliknij przycisk **+ nowy** przycisk, a następnie kliknij przycisk **zaimportować potoku**.
1. Kliknij przycisk **Przeglądaj** i wybierz `samples/release.json` z projektu.
1. Kliknij przycisk **OK**. Powiadomienia w okienku potok został załadowany ze strony edytowania definicji wersji. Należy zauważyć, że są także niektóre czerwony ikonami ostrzeżeń, wskazując szczegółowe informacje specyficzne dla klastra, które nadal muszą być skonfigurowane.
1. W lewym okienku potoku, kliknij przycisk **Dodawanie artefaktu** bąbelkowych.
1. W **źródła** listy rozwijanej wybierz kompilację potoku utworzone wcześniej.
1. Dla **domyślną wersję**, wybierz **najnowsze z gałęzi domyślnej potoku kompilacji z tagami**.
1. Pozostaw **tagi** puste.
1. Ustaw **alias źródła** do `drop`. **Alias źródła** wartość jest używana przez zadania wydania, więc musi być ustawiona.
1. Kliknij przycisk **Dodaj**.
1. Teraz kliknij ikonę pioruna na nowo utworzony `drop` źródła artefaktu, jak pokazano poniżej:

    ![Wersja artefaktu ciągłe wdrażanie konfiguracji](../media/common/release-artifact-cd-setup.png)
1. Włącz **wyzwalacz ciągłego wdrażania**.
1. Umieść kursor nad **zadania** karcie obok **potoku** i kliknij przycisk _dev_ edytować _dev_ etap zadania.
1. Sprawdź **usługi Azure Resource Manager** jest zaznaczany w **typu połączenia.** i trzech kontrolek list rozwijanych wyróżniony czerwonym kolorem: ![Instalator definicji wydania](../media/common/release-setup-tasks.png)
1. Wybierz subskrypcję platformy Azure, której korzystasz z usługi Azure Dev miejsca do magazynowania. Również może być konieczne kliknij **Autoryzuj**.
1. Wybierz grupę zasobów i klastra, który jest używany z usługi Azure Dev miejsca do magazynowania.
1. Kliknij pozycję **zadania agenta**.
1. Wybierz **hostowanych 1604 Ubuntu** w obszarze **puli agenta**.
1. Umieść kursor nad **zadania** selektora u góry strony, kliknij przycisk _prod_ edytować _prod_ etap zadania.
1. Sprawdź **usługi Azure Resource Manager** jest zaznaczany w **typu połączenia.** i wybierz subskrypcję platformy Azure, grupę zasobów i klastra, który jest używany z usługi Azure Dev miejsca do magazynowania.
1. Kliknij pozycję **zadania agenta**.
1. Wybierz **hostowanych 1604 Ubuntu** w obszarze **puli agenta**.
1. Kliknij przycisk **zmienne** kartę, aby zaktualizować zmienne dla danej wersji.
1. Zaktualizuj wartość **DevSpacesHostSuffix** z **UPDATE_ME** do sieci sufiksów hosta. Sufiks hosta jest wyświetlany po uruchomieniu `azds show-context` wcześniej polecenia.
1. Kliknij przycisk **Zapisz** w prawym górnym rogu, a **OK**.
1. Kliknij przycisk **+ wydanie** (obok przycisku Zapisz), a **utworzyć wydanie**.
1. W obszarze **artefaktów**, sprawdź, czy jest wybrany najnowszą kompilację z potokiem kompilacji.
1. Kliknij przycisk **Utwórz**.

Procesu tworzenia wersji automatyczne rozpocznie teraz, wdrażanie *mywebapi* i *webfrontend* wykresów Kubernetes klastra w systemie _dev_ miejsca najwyższego poziomu. Możesz monitorować postęp wydania DevOps platformy Azure w portalu sieci web:

1. Przejdź do **wersji** sekcji **potoki**.
1. Kliknij potok wydania dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść kursor nad **dev** pole w obszarze **etapów** i kliknij przycisk **dzienniki**.

Wydanie odbywa się po ukończeniu wszystkich zadań.

> [!TIP]
> Jeśli danej wersji zakończy się niepowodzeniem z komunikatem o błędzie, takich jak *uaktualnienia nie powiodła się: Upłynął limit czasu oczekiwania dla warunku*, spróbuj sprawdzania zasobników w klastrze [za pomocą pulpitu nawigacyjnego rozwiązania Kubernetes](../../aks/kubernetes-dashboard.md). Jeśli widzisz zasobników kończą się niepowodzeniem na początek z komunikatów o błędach, takie jak *nie można ściągnąć obraz "azdsexample.azurecr.io/mywebapi:122": błąd rpc: kod = nieznany desc = odpowiedzi na błąd z demona: Pobierz https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: nieautoryzowanych: wymagane jest uwierzytelnianie*, może to być spowodowane klastra nie został autoryzowany do ściągania z usługi Azure Container Registry. Upewnij się, że zostały wykonane [autoryzować klastra usługi AKS do ściągania z usługi Azure Container Registry](../../container-registry/container-registry-auth-aks.md) wymagań wstępnych.

Masz teraz pełni zautomatyzowanego potoku ciągłej integracji/ciągłego Dostarczania do rozwidlenia serwisu GitHub, miejsca do magazynowania Dev przykładowych aplikacji. Zawsze możesz zatwierdzać i wypychać kod, potok kompilacji będzie Zbuduj i Wypchnij *mywebapi* i *webfrontend* obrazy do wystąpienia usługi ACR niestandardowych. Następnie potok wydania wdroży wykresu Helm dla każdej aplikacji do _dev_ miejsca w klastrze z obsługą tworzenia miejsca do magazynowania.

## <a name="accessing-your-dev-services"></a>Uzyskiwanie dostępu do Twojego _dev_ usług
Po wdrożeniu _dev_ wersję *webfrontend* można uzyskać dostęp za pomocą publicznego adresu URL, takich jak: `http://dev.webfrontend.fedcba098.eus.azds.io`. Możesz znaleźć ten adres URL, uruchamiając `azds list-uri` polecenia: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Do wdrożenia produkcyjnego

Ręczne podwyższenie poziomu określonej wersji, aby _prod_ przy użyciu systemu ciągłej integracji/ciągłego Dostarczania utworzonych w tym samouczku:
1. Przejdź do **wersji** sekcji **potoki**.
1. Kliknij potok wydania dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść kursor nad **prod** pole w obszarze **etapów** i kliknij przycisk **Wdróż**.
    ![Przenieś do produkcji](../media/common/prod-promote.png)
1. Umieść kursor nad **prod** ponownie pole w obszarze **etapów** i kliknij przycisk **dzienniki**.

Wydanie odbywa się po ukończeniu wszystkich zadań.

_Prod_ etapu potoku CI/CD używa modułu równoważenia obciążenia zamiast kontrolera transferu danych przychodzących Dev miejsca do magazynowania umożliwia dostęp do _prod_ usług. Usług wdrożonych w _prod_ etapu są dostępne jako adresów IP zamiast nazwy DNS. W środowisku produkcyjnym można utworzyć własny kontroler danych przychodzących do hostowania usług na podstawie konfiguracji DNS.

Aby określić adres IP usługi webfrontend, kliknij polecenie **drukowanie webfrontend publiczny adres IP** krok, aby rozszerzyć dane wyjściowe dziennika. Użyj adresu IP wyświetlane w dzienniku danych wyjściowych, aby uzyskać dostęp do **webfrontend** aplikacji.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentacja dev miejsca do magazynowania w środowisku produkcyjnym
Mimo że Instrumentacji Dev miejsca do magazynowania zostały zaprojektowane _nie_ aby utrudniać normalnego działania aplikacji, zaleca się uruchamiania obciążeń produkcji w przestrzeni nazw Kubernetes, która nie jest włączona przy użyciu standardowego miejsca do magazynowania. Przy użyciu tego typu przestrzeni nazw Kubernetes oznacza, że możesz albo utworzyć sieci produkcyjnych przestrzeni nazw za pomocą `kubectl` interfejsu wiersza polecenia lub system ciągłej integracji/ciągłego wdrażania może tworzyć je podczas pierwszego wdrażania narzędzia Helm. _Wybieranie_ lub w przeciwnym razie Tworzenie miejsca, przy użyciu spacji Dev narzędzi doda Instrumentacji Dev miejsca do magazynowania do tego obszaru nazw.

Poniżej przedstawiono przykład struktury przestrzeni nazw, który obsługuje rozwój funkcji, środowiska "deweloperskie" _i_ produkcji, wszystko w jednym klastrze platformy Kubernetes:

![Przykład struktury przestrzeni nazw](../media/common/cicd-namespaces.png)

> [!Tip]
> Jeśli już utworzono `prod` miejsca, i czy po prostu jak celu wykluczenia go ze Instrumentacji Dev miejsca do magazynowania (bez usuwania go!), możesz to zrobić za pomocą następującego polecenia interfejsu wiersza polecenia deweloperów miejsca do magazynowania:
>
> `azds space remove -n prod --no-delete`
>
> Może być konieczne usunięcie wszystkich zasobników w `prod` przestrzeni nazw po wykonaniu tego, dzięki czemu można odtworzyć bez Instrumentacji Dev miejsca do magazynowania.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat tworzenia zespołu za pomocą usługi Azure Dev miejsca do magazynowania](../team-development-netcore.md)