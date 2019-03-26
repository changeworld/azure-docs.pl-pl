---
title: Uruchom fabrykę obrazu z DevOps platformy Azure w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć fabrykę danych obrazu niestandardowego w usłudze Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 62cb8222b592660f2e7ab32d438fd4073246ee50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439909"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Uruchom fabrykę obrazu z DevOps platformy Azure
W tym artykule opisano wszystkie czynności, które są potrzebne do uruchomienia fabrycznie obrazu z DevOps platformy Azure (dawniej Visual Studio Team Services).

> [!NOTE]
> Każdy silnik orchestration będzie działać! DevOps platformy Azure nie jest obowiązkowe. Fabryka obrazu jest uruchamiany, za pomocą skryptów programu Azure PowerShell, dzięki czemu może być uruchamiane ręcznie, za pomocą harmonogramu zadań systemu Windows, inne systemy ciągłej integracji/ciągłego wdrażania i tak dalej.

## <a name="create-a-lab-for-the-image-factory"></a>Tworzenie laboratorium na potrzeby fabrycznie obrazu
Pierwszym krokiem w procesie konfigurowania fabryki obrazu jest tworzenie laboratorium w usłudze Azure DevTest Labs. To laboratorium stanowi laboratorium fabryki obraz miejscu tworzenia maszyn wirtualnych i zapisywanie obrazów niestandardowych. W tym laboratorium jest traktowany jako część całego procesu fabrycznie obrazu. Po utworzeniu laboratorium, upewnij się zapisać nazwę, ponieważ będzie on potrzebny później.

## <a name="scripts-and-templates"></a>Skrypty i szablony
Następnym krokiem w przyjmowaniu fabrycznie obrazu dla Twojego zespołu jest zrozumienie, jakie opcje są dostępne. Obraz fabryki skryptów i szablonów są dostępne publicznie w [repozytorium GitHub usługi DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Poniżej przedstawiono zarys elementy:

- Fabryka obrazu. Jest folder główny. 
    - Konfiguracja. Dane wejściowe do fabryki obrazu
        - GoldenImages. Ten folder zawiera pliki w formacie JSON, które reprezentują definicje obrazów niestandardowych.
        - Labs.json. Plik, w którym zespoły Zarejestruj, aby otrzymywać określonych niestandardowych obrazów.
- Skrypty. Aparat fabryki obrazu.

Artykuły w tej sekcji Podaj więcej szczegółów na temat tych skryptów i szablonów. 

## <a name="create-an-azure-devops-team-project"></a>Tworzenie projektu zespołu DevOps platformy Azure
DevOps platformy Azure umożliwiają przechowywanie kodu źródłowego, uruchamiać program Azure PowerShell w jednym miejscu. Można zaplanować cykliczne przebiegów, aby zapewnić aktualność obrazów. Ma dobrej funkcje służące do rejestrowania wyników do diagnozowania problemów.  Za pomocą usługi Azure DevOps jednak nie jest to wymagane, można użyć dowolnego kontroler/aparat, który umożliwia połączenie z platformy Azure i można uruchomić programu Azure PowerShell.

Jeśli masz istniejące konto DevOps lub projektu, którego chcesz użyć, Pomiń ten krok.

Aby rozpocząć, Utwórz bezpłatne konto w DevOps platformy Azure. Odwiedź stronę https://www.visualstudio.com/ i wybierz **Rozpocznij za darmo** tuż poniżej **DevOps platformy Azure** (dawniej VSTS). Należy wybrać unikatową nazwę konta i upewnij się, że chce zarządzać kodem przy użyciu narzędzia Git. Po utworzeniu, Zapisz adres URL do projektu zespołowego. Poniżej przedstawiono przykładowy adres URL: https://<accountname>.visualstudio.com/MyFirstProject.

## <a name="check-in-the-image-factory-to-git"></a>Sprawdź w fabryce obrazu do usługi Git
Wszystkie środowiska PowerShell, szablony i konfiguracji fabryki obrazu znajdują się w [publicznego repozytorium GitHub laboratoria DevTest](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Najszybszym sposobem uzyskania kodu do nowego projektu zespołowego jest importowanie repozytorium. Spowoduje to pobranie całego repozytorium w usłudze DevTest Labs (dzięki czemu zapewnisz sobie dodatkowych dokumentację i przykłady). 

1. Odwiedź stronę projektu DevOps platformy Azure, który został utworzony w poprzednim kroku (adres URL wygląda **https://<accountname>.visualstudio.com/MyFirstProject**).
2. Wybierz **zaimportować repozytorium**.
3. Wprowadź **adres URL klonowania** dla repozytorium usługi DevTest Labs: `https://github.com/Azure/azure-devtestlab`.
4. Wybierz **importu**.

    ![Importowanie repozytorium Git](./media/set-up-devops-lab/import-git-repo.png)

Jeśli zdecydujesz się jedynie zaewidencjonować dokładnie co jest potrzebne (pliki obrazów fabryki), postępuj zgodnie z instrukcjami [tutaj](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) klonowanie repozytorium Git do wypychania plików znajdujących się w **skryptów/ImageFactory** katalogu.

## <a name="create-a-build-and-connect-to-azure"></a>Tworzenie kompilacji i łączenie z platformą Azure
W tym momencie masz pliki źródłowe, przechowywane w repozytorium Git w DevOps platformy Azure. Teraz musisz skonfigurować potok do uruchamiania programu Azure PowerShell. Dostępnych jest wiele opcji, aby wykonać te kroki. W tym artykule możesz użyć definicji kompilacji, dla uproszczenia, ale działa z kompilacją metodyki DevOps, DevOps wersji (jednego lub wielu środowiskach), innymi aparatami wykonania, takie jak Harmonogram zadań Windows lub inne kontroler, która umożliwia wykonanie programu Azure PowerShell.

> [!NOTE]
> Ważnym punktem należy pamiętać, że niektóre pliki programu PowerShell długo trwać do uruchamiania, gdy istnieje wiele (10 i nowsze) niestandardowych obrazów, aby utworzyć. Bezpłatne hostowanych agentów kompilacji/wydania DevOps mają limit czasu równy 30 minut, więc nie można użyć bezpłatnego hostowany agent, po rozpoczęciu tworzenia wielu obrazów. Ten limit czasu żądania ma zastosowanie do dowolnych zdecydujesz się używać kontroler, warto na początku należy sprawdzić, rozszerzyć typowych limitów czasu dla długotrwałych skryptów programu Azure PowerShell. W przypadku DevOps platformy Azure możesz użyć płatnych hostowanych agentów lub użyć własnego agenta kompilacji.

1. Aby rozpocząć, wybierz opcję **Ustaw kompilację** na stronie głównej projektu DevOps:

    ![Konfigurowanie przycisku kompilacji](./media/set-up-devops-lab/setup-build-button.png)
2. Określ **nazwa** kompilacji (na przykład: Twórz i dostarczaj obrazów użytkownik usługi DevTest Labs). 
3. Wybierz **pusty** definicji kompilacji, a następnie wybierz pozycję **Zastosuj** do tworzenia kompilacji. 
4. Na tym etapie możesz wybrać **hostowana** dla agenta kompilacji. 
5. **Zapisz** definicji kompilacji.

    ![Definicja kompilacji](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurowanie zmiennych kompilacji
Aby uprościć parametry wiersza polecenia, hermetyzacji wartości klucza, które dysku fabrycznie obrazu do zestawu zmiennych kompilacji. Wybierz **zmienne** kartę, aby zobaczyć listę kilku zmiennych domyślne. Poniżej przedstawiono listę zmiennych, które można wprowadzać w DevOps platformy Azure:


| Nazwa zmiennej | Wartość | Uwagi |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Jest to pełna ścieżka w repozytorium, aby **konfiguracji** folderu. Jeśli zaimportowano całego repozytorium, powyższe wartości po lewej stronie jest poprawna. W przeciwnym razie aktualizacji, aby wskazać lokalizację konfiguracji. |
| DevTestLabName | MyImageFactory | Nazwa laboratorium w usłudze Azure DevTest Labs używana jako fabryka do tworzenia obrazów. Jeśli nie masz, utwórz je. Upewnij się, że laboratorium znajduje się w tej samej subskrypcji, punkt końcowy usługi z dostępem do. |
| ImageRetention | 1 | Liczba obrazów, które chcesz zapisać każdego typu. Ustaw wartość domyślną 1. |
| MachinePassword | ******* | Hasło konta administratora wbudowane dla maszyn wirtualnych. To jest konto przejściowy, dlatego upewnij się, że jest bezpieczne. Wybierz mała ikona blokady po prawej stronie, aby upewnić się, że jest ciągiem bezpiecznym. |
| MachineUserName | ImageFactoryUser | Nazwa użytkownika konta administratora wbudowane dla maszyn wirtualnych. To jest konto przejściowy. |
| StandardTimeoutMinutes | 30 | Limit czasu, w których firma Microsoft ma oczekiwać regularnych operacjach platformy Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Identyfikator subskrypcji, w którym istnieje laboratorium i punkt końcowy usługi z dostępem do. |
| VMSize | Standardowa_A3 | Rozmiar maszyny wirtualnej na potrzeby **Utwórz** kroku. Maszyny wirtualne tworzone są przejściowe. Rozmiar musi być to [włączone dla laboratorium](devtest-lab-set-lab-policy.md). Upewnij się, że jest wystarczająco dużo [limit przydziału rdzeni subskrypcji](../azure-subscription-service-limits.md). 

![Tworzenie zmiennych](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Następnym krokiem jest do skonfigurowania nazwy głównej usługi. Jest to tożsamość w usłudze Azure Active Directory, umożliwiająca DevOps agenta kompilacji, działają na platformie Azure w imieniu użytkownika. Aby ustawić go tak, rozpoczynać się dodanie pierwszy Azure PowerShell krok kompilacji.

1. Wybierz **Dodaj zadanie**.
2. Wyszukaj **programu Azure PowerShell**. 
3. Po znalezieniu go wybrać **Dodaj** można dodać zadania do kompilacji. Gdy to zrobisz, zobaczysz zadania są wyświetlane po lewej stronie w miarę dodawania.

![Konfigurowanie programu PowerShell krok](./media/set-up-devops-lab/set-up-powershell-step.png)

Najszybszym sposobem konfigurowania jednostki usługi jest umożliwiające DevOps platformy Azure to dla nas. 

1. Wybierz **zadań** właśnie dodałeś.
2. Aby uzyskać **typu połączenia platformy Azure**, wybierz **usługi Azure Resource Manager**. 
3. Wybierz **Zarządzaj** link, aby skonfigurować nazwę główną usługi. 

Aby uzyskać więcej informacji, zobacz ten [wpis w blogu](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Po wybraniu **Zarządzaj** link, zostanie wyświetlona w odpowiednim miejscu w infrastrukturze DevOps (drugi zrzut ekranu: wpis w blogu) Aby skonfigurować połączenie z platformą Azure. Upewnij się wybrać **punkt końcowy usługi Resource Manager platformy Azure** podczas konfigurowania.

## <a name="complete-the-build-task"></a>Wykonanie zadania kompilacji
Jeśli wybierzesz zadanie kompilacji, zobaczysz wszystkie szczegółowe informacje w okienku po prawej stronie, które powinno być wypełnione. 

1. Po pierwsze nadaj nazwę zadaniu kompilacji: **Tworzenie maszyn wirtualnych**. 
2. Wybierz **nazwy głównej usługi** utworzone przez wybranie **usługi Azure Resource Manager**
3. Wybierz **punktu końcowego usługi**. 
4. Aby uzyskać **ścieżka skryptu**, wybierz opcję **... (wielokropek)**  po prawej stronie.
5. Przejdź do **MakeGoldenImageVMs.ps1** skryptu. 
6. Parametry skryptu powinny wyglądać następująco: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Wykonaj definicji kompilacji](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Dodaj kompilację do kolejki
Teraz Sprawdź, czy wszystko jest poprawnie skonfigurowany, kolejkowania nowej kompilacji. Gdy kompilacja jest uruchomiona, przełącz się do [witryny Azure portal](https://portal.azure.com) i wybierz **wszystkie maszyny wirtualne** w środowisku laboratoryjnym fabrycznie obrazu, aby upewnić się, że wszystko działa poprawnie. Powinien pojawić się trzy maszyny wirtualne utworzone w środowisku laboratoryjnym.

![Maszyny wirtualne w laboratorium](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Kolejne kroki 
Pierwszym krokiem w procesie konfigurowania fabryki obrazu, oparte na usłudze Azure DevTest Labs jest pełny. W następnym artykule z tej serii otrzymasz te maszyny wirtualne uogólnione i zapisane w niestandardowych obrazów. W efekcie masz ich dystrybuowane do wszystkich innych laboratoriów. Następny artykuł z serii: [Zapisywanie niestandardowych obrazów i dystrybucji do wielu labs](image-factory-save-distribute-custom-images.md).
