---
title: Uruchamianie fabryki obrazów z usługi Azure DevOps w laboratoriach usługi Azure DevTest
description: W tym artykule opisano wszystkie przygotowania potrzebne do uruchomienia fabryki obrazów z usługi Azure DevOps (dawniej Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758686"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Uruchamianie fabryki obrazów z usługi Azure DevOps
W tym artykule opisano wszystkie przygotowania potrzebne do uruchomienia fabryki obrazów z usługi Azure DevOps (dawniej Visual Studio Team Services).

> [!NOTE]
> Każdy silnik aranżacji będzie działać! Usługa Azure DevOps nie jest obowiązkowa. Fabryka obrazów jest uruchamiana przy użyciu skryptów programu Azure PowerShell, dzięki czemu można ją uruchomić ręcznie, przy użyciu harmonogramu zadań systemu Windows, innych systemów ciągłej integracji/ciągłego wdrażania i tak dalej.

## <a name="create-a-lab-for-the-image-factory"></a>Tworzenie laboratorium dla fabryki obrazów
Pierwszym krokiem w konfigurowaniu fabryki obrazów jest utworzenie laboratorium w laboratorium usługi Azure DevTest Labs. To laboratorium jest laboratorium fabryki obrazów, gdzie tworzymy maszyny wirtualne i zapisujemy niestandardowe obrazy. To laboratorium jest uważane za część całego procesu fabryki obrazu. Po utworzeniu laboratorium należy zapisać nazwę, ponieważ będzie potrzebna później.

## <a name="scripts-and-templates"></a>Skrypty i szablony
Następnym krokiem w przyjęciu fabryki obrazów dla zespołu jest zrozumienie, co jest dostępne. Skrypty i szablony fabryki obrazów są dostępne publicznie w [devtest labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Oto zarys utworów:

- Fabryka obrazów. Jest to folder główny.
    - Konfiguracji. Wejścia do fabryki obrazów
        - GoldenImages. Ten folder zawiera pliki JSON reprezentujące definicje obrazów niestandardowych.
        - Labs.json. Plik, w którym zespoły zarejestrują się, aby otrzymywać określone obrazy niestandardowe.
- Skrypty. Silnik fabryki obrazu.

Artykuły w tej sekcji zawierają więcej szczegółów na temat tych skryptów i szablonów.

## <a name="create-an-azure-devops-team-project"></a>Tworzenie projektu zespołowego programu Azure DevOps
Usługa Azure DevOps umożliwia przechowywanie kodu źródłowego, uruchom program Azure PowerShell w jednym miejscu. Możesz zaplanować biegi cykliczne, aby obrazy były aktualne. Istnieją dobre możliwości rejestrowania wyników w celu zdiagnozowania wszelkich problemów.  Korzystanie z usługi Azure DevOps nie jest wymagane jednak można użyć dowolnej wiązki wiązki/aparatu, który można połączyć się z platformą Azure i można uruchomić program Azure PowerShell.

Jeśli masz istniejące konto devops lub projekt, którego chcesz użyć, pomiń ten krok.

Aby rozpocząć, utwórz bezpłatne konto w usłudze Azure DevOps. Odwiedź https://www.visualstudio.com/ i wybierz **Pobierz za darmo** bezpośrednio w obszarze Azure **DevOps** (dawniej VSTS). Musisz wybrać unikatową nazwę konta i upewnij się, że chcesz zarządzać kodem za pomocą Git. Po utworzeniu zapisz adres URL w projekcie zespołowym. Oto przykładowy adres `https://<accountname>.visualstudio.com/MyFirstProject`URL: .

## <a name="check-in-the-image-factory-to-git"></a>Zamelduj się w fabryce obrazów w Git
Wszystkie powershell, szablony i konfiguracja dla fabryki obrazów znajdują się w [publicznej repozytorium DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Najszybszym sposobem, aby uzyskać kod do nowego projektu zespołowego jest zaimportowanie repozytorium. Spowoduje to pobranie całego repozytorium DevTest Labs (dzięki czemu otrzymasz dodatkowe dokumenty i przykłady).

1. Odwiedź projekt Programu Azure DevOps utworzony w poprzednim kroku (adres URL wygląda jak **https:\//\<accountname>.visualstudio.com/MyFirstProject**).
2. Wybierz **pozycję Importuj repozytorium**.
3. Wprowadź **adres URL klonowania** repozytorium `https://github.com/Azure/azure-devtestlab`DevTest Labs: .
4. Wybierz pozycję **Import**.

    ![Importowanie repo Git](./media/set-up-devops-lab/import-git-repo.png)

Jeśli zdecydujesz się sprawdzić tylko dokładnie to, co jest potrzebne (pliki fabryki obrazu), wykonaj kroki [tutaj,](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) aby sklonować repozytorium Git i wypchnąć tylko pliki znajdujące się w **katalogu skryptów / ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Tworzenie kompilacji i łączenie się z platformą Azure
W tym momencie masz pliki źródłowe przechowywane w repozytorium Git w usłudze Azure DevOps. Teraz należy skonfigurować potok, aby uruchomić program Azure PowerShell. Istnieje wiele opcji, aby wykonać te kroki. W tym artykule używasz definicji kompilacji dla uproszczenia, ale współpracuje z DevOps Build, DevOps Release (pojedyncze lub wiele środowisk), innych aparatów wykonywania, takich jak Harmonogram zadań systemu Windows lub inne wiązki, które można wykonać programu Azure PowerShell.

> [!NOTE]
> Jeden ważny punkt, aby pamiętać, że niektóre pliki programu PowerShell zająć dużo czasu, aby uruchomić, gdy istnieje wiele (10+) obrazów niestandardowych do utworzenia. Free hostowane DevOps Build / Release agentów mają limit czasu 30 min, więc nie można korzystać z wolnego hostowanego agenta po rozpoczęciu tworzenia wielu obrazów. To wyzwanie limitu czasu dotyczy niezależnie od uprzęży, które zdecydujesz się użyć, dobrze jest wcześniej sprawdzić, czy można rozszerzyć typowe limity czasu dla skryptów programu Azure PowerShell długo działające. W przypadku usługi Azure DevOps można użyć płatnych agentów hostowanych lub użyć własnego agenta kompilacji.

1. Aby rozpocząć, wybierz pozycję **Konfiguruj kompilację** na stronie głównej projektu DevOps:

    ![Przycisk Kompilacja instalacji](./media/set-up-devops-lab/setup-build-button.png)
2. Określ **nazwę** kompilacji (na przykład: Tworzenie i dostarczanie obrazów do laboratoriów DevTest).
3. Wybierz **pustą** definicję kompilacji i wybierz pozycję **Zastosuj,** aby utworzyć kompilację.
4. Na tym etapie można wybrać **hostowane** dla agenta kompilacji.
5. **Zapisz** definicję kompilacji.

    ![Definicja kompilacji](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurowanie zmiennych kompilacji
Aby uprościć parametry wiersza polecenia, hermetyzuj wartości kluczy, które napędzają fabrykę obrazu do zestawu zmiennych kompilacji. Wybierz kartę **Zmienne,** a zobaczysz listę kilku zmiennych domyślnych. Oto lista zmiennych, które należy wprowadzić w usłudze Azure DevOps:


| Nazwa zmiennej | Wartość | Uwagi |
| ------------- | ----- | ----- |
| Lokalizacja konfiguracji | /Skrypty/ImageFactory/Konfiguracja | Jest to pełna ścieżka w repozytorium do folderu **Konfiguracja.** Jeśli zaimportowano całe repozytorium powyżej, wartość po lewej stronie jest poprawna. W przeciwnym razie zaktualizuj, aby wskazać lokalizację konfiguracji. |
| Nazwa pliku DevTestLabName | MyImageFactory (Faktor MyImageFactory) | Nazwa laboratorium w laboratorium Azure DevTest Labs używane jako fabryka do tworzenia obrazów. Jeśli go nie masz, utwórz go. Upewnij się, że laboratorium jest w tej samej subskrypcji, że punkt końcowy usługi ma dostęp do. |
| ImageRetention (Dokręć obrazu) | 1 | Liczba obrazów, które chcesz zapisać każdego typu. Ustaw wartość domyślną na 1. |
| Hasło na maszynie | ******* | Wbudowane hasło konta administratora dla maszyn wirtualnych. Jest to konto przejściowe, więc upewnij się, że jest bezpieczne. Wybierz małą ikonę kłódki po prawej stronie, aby upewnić się, że jest to bezpieczny ciąg znaków. |
| Nazwa użytkownika maszyny | ImageFactoryUser | Wbudowana nazwa konta administratora dla maszyn wirtualnych. Jest to konto przejściowe. |
| Minuty czasu standardowego | 30 | Limit czasu, który powinniśmy poczekać na regularne operacje na platformie Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Identyfikator subskrypcji, w którym istnieje laboratorium i do którego punkt końcowy usługi ma dostęp. |
| Rozmiar maszyny Wirtualnej | Standardowa_A3 | Rozmiar maszyny wirtualnej do użycia w kroku **Utwórz.** Maszyny wirtualne utworzone są przejściowe. Rozmiar musi być ten, który jest [włączony dla laboratorium](devtest-lab-set-lab-policy.md). Potwierdź, że jest wystarczająco dużo [przydziału rdzeni subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Tworzenie zmiennych](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Następnym krokiem jest skonfigurowanie jednostki usługi. Jest to tożsamość w usłudze Azure Active Directory, która umożliwia agentowi kompilacji DevOps do działania na platformie Azure w imieniu użytkownika. Aby go skonfigurować, zacznij od dodania pierwszego kroku kompilacji programu Azure PowerShell.

1. Wybierz **pozycję Dodaj zadanie**.
2. Wyszukaj **program Azure PowerShell**.
3. Po jego znalezieniu wybierz pozycję **Dodaj,** aby dodać zadanie do kompilacji. Po wykonaniu tej tej funkcji po stronie zostanie wyświetlone po lewej stronie, zgodnie z dodanym.

![Konfigurowanie kroku programu PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

Najszybszym sposobem skonfigurowania jednostki usługi jest umożliwienie deweloperom platformy Azure wykonania tego za nas.

1. Wybierz właśnie dodane **zadanie.**
2. W przypadku **typu połączenia platformy Azure**wybierz pozycję Azure Resource **Manager**.
3. Wybierz **łącze Zarządzaj,** aby skonfigurować jednostkę usługi.

Aby uzyskać więcej informacji, zobacz ten [wpis w blogu](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Po wybraniu **łącza Zarządzaj** wylądujesz we właściwym miejscu w DevOps (drugi zrzut ekranu we wpisie w blogu), aby skonfigurować połączenie z platformą Azure. Podczas konfigurowania tej opcji należy wybrać **punkt końcowy usługi Usługi Usługi Usługi Azure Resource Manager.**

## <a name="complete-the-build-task"></a>Wykonaj zadanie kompilacji
Jeśli wybierzesz zadanie kompilacji, zobaczysz wszystkie szczegóły w prawym okienku, które powinny zostać wypełnione.

1. Najpierw nazwij zadanie kompilacji: **Tworzenie maszyn wirtualnych**.
2. Wybierz **jednostkę usługi utworzoną** przez wybranie **usługi Azure Resource Manager**
3. Wybierz **punkt końcowy usługi**.
4. W przypadku **ścieżki skryptu**wybierz **... (wielokropek)** po prawej stronie.
5. Przejdź do skryptu **MakeGoldenImageVMs.ps1.**
6. Parametry skryptu powinny wyglądać następująco:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Ukończ definicję kompilacji](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Kolejka kompilacji
Sprawdźmy, czy wszystko jest poprawnie skonfigurowane, ustawiając w kolejce nową kompilację. Gdy kompilacja jest uruchomiona, przełącz się do [witryny Azure portal](https://portal.azure.com) i wybierz **wszystkie maszyny wirtualne** w laboratorium fabryki obrazów, aby potwierdzić, że wszystko działa poprawnie. Powinny zostać wyświetlene trzy maszyny wirtualne utworzone w laboratorium.

![Maszyny wirtualne w laboratorium](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Następne kroki
Pierwszy krok w konfigurowaniu fabryki obrazów na podstawie usługi Azure DevTest Labs został ukończony. W następnym artykule z serii otrzymasz te maszyny wirtualne uogólnione i zapisane w obrazach niestandardowych. Następnie, masz je dystrybuowane do wszystkich innych laboratoriów. Zobacz następny artykuł z serii: [Zapisywanie obrazów niestandardowych i rozpowszechnianie w wielu laboratoriach](image-factory-save-distribute-custom-images.md).
