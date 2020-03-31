---
title: Rozwiązywanie problemów z artefaktami w laboratoriach devtest platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy występujące podczas stosowania artefaktów na maszynie wirtualnej usługi Azure DevTest Labs.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456976"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Rozwiązywanie problemów podczas stosowania artefaktów na maszynie wirtualnej usługi Azure DevTest Labs
Stosowanie artefaktów na maszynie wirtualnej może zakończyć się niepowodzeniem z różnych powodów. W tym artykule znajdziesz kilka metod ułatwiające identyfikację możliwych przyczyn.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami w dziedzinie laboratoriów DevTest Labs (DTL) platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną.   

> [!NOTE]
> Ten artykuł dotyczy maszyn wirtualnych systemu Windows i innych niż Windows. Chociaż istnieją pewne różnice, zostaną one wyraźnie wywołane w tym artykule.

## <a name="quick-troubleshooting-steps"></a>Szybkie kroki rozwiązywania problemów
Sprawdź, czy maszyna wirtualna jest uruchomiona. DevTest Labs wymaga, aby maszyna wirtualna była uruchomiona i że [agent maszyny wirtualnej platformy Microsoft Azure (Agent maszyny wirtualnej)](../virtual-machines/extensions/agent-windows.md) jest zainstalowany i gotowy.

> [!TIP]
> W **witrynie Azure portal**przejdź do strony **Zarządzanie artefaktami** dla maszyny Wirtualnej, aby sprawdzić, czy maszyna wirtualna jest gotowa do zastosowania artefaktów. U góry tej strony zostanie wyświetlona wiadomość. 
> 
> Za pomocą **programu Azure PowerShell**, sprawdź flagę **canApplyArtifacts**, który jest zwracany tylko po rozwinięciu operacji GET. Zobacz następujące przykładowe polecenie:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Sposoby rozwiązywania problemów 
Można rozwiązywać problemy z maszynami wirtualnymi utworzonymi przy użyciu laboratoriów DevTest i modelu wdrażania Menedżera zasobów przy użyciu jednej z następujących metod:

- **Witryna Azure portal** — świetnie, jeśli chcesz szybko uzyskać wizualną wskazówkę, co może być przyczyną problemu.
- **Azure PowerShell** — jeśli masz komfort z monitem programu PowerShell, szybko zapytanie devtest labs zasobów przy użyciu poleceń cmdlet azure powershell.

> [!TIP]
> Aby uzyskać więcej informacji na temat sposobu przeglądania wykonywania artefaktów w ramach maszyny Wirtualnej, zobacz [Diagnozowanie błędów artefaktów w laboratorium.](devtest-lab-troubleshoot-artifact-failure.md)

## <a name="symptoms-causes-and-potential-resolutions"></a>Objawy, przyczyny i potencjalne rozdzielczości 

### <a name="artifact-appears-to-hang"></a>Artefakt wydaje się zawiesić   
Artefakt wydaje się zawieszać do czasu wygaśnięcia wstępnie zdefiniowanego limitu czasu, a artefakt jest oznaczony jako **Nieudany**.

Gdy artefakt wydaje się zawiesić, najpierw określ, gdzie utknął. Artefakt może być zablokowany w dowolnym z następujących kroków podczas wykonywania:

- **Podczas pierwszego żądania**. DevTest Labs tworzy szablon usługi Azure Resource Manager, aby zażądać użycia rozszerzenia skryptu niestandardowego (CSE). W związku z tym w tle jest wyzwalane wdrożenie grupy zasobów. Gdy występuje błąd na tym poziomie, można uzyskać szczegółowe informacje w **dziennikach aktywności** grupy zasobów dla danej maszyny Wirtualnej.  
    - Dostęp do dziennika aktywności można uzyskać z paska nawigacyjnego strony maszyny Wirtualnej laboratorium. Po wybraniu go zostanie wyświetlony wpis **do stosowania artefaktów do maszyny wirtualnej** (jeśli operacja zastosuj artefakty została wyzwolona bezpośrednio) lub **Dodaj lub zmodyfikuj maszyny wirtualne** (jeśli stosowana operacja artefaktów była częścią procesu tworzenia maszyny wirtualnej).
    - Poszukaj błędów pod tymi wpisami. Czasami błąd nie zostanie odpowiednio oznaczony i będziesz musiał zbadać każdy wpis.
    - Podczas badania szczegółów każdego wpisu, upewnij się, aby przejrzeć zawartość ładunku JSON. W dolnej części tego dokumentu może pojawić się błąd.
- **Podczas próby wykonania artefaktu**. Może to być spowodowane problemami z siecią lub pamięcią masową. Zobacz odpowiednią sekcję w dalszej części tego artykułu, aby uzyskać szczegółowe informacje. Może się to również zdarzyć ze względu na sposób, w jaki skrypt jest autorem. Przykład:
    - Skrypt programu PowerShell ma **parametry obowiązkowe**, ale jeden nie może przekazać wartość do niego, albo dlatego, że użytkownik może pozostawić puste, lub dlatego, że nie masz domyślną wartość właściwości w artifactfile.json pliku definicji. Skrypt zostanie zawisły, ponieważ oczekuje na dane wejściowe użytkownika.
    - Skrypt programu PowerShell **wymaga danych wejściowych użytkownika** w ramach wykonywania. Skrypty muszą być zapisywane do pracy po cichu bez konieczności interwencji użytkownika.
- **Agent maszyn wirtualnych trwa długo, aby być gotowym**. Po pierwszym uruchomieniu maszyny Wirtualnej lub gdy rozszerzenie skryptu niestandardowego jest po raz pierwszy zainstalowany do obsługi żądania zastosowania artefaktów, maszyna wirtualna może wymagać uaktualnienia agenta maszyny Wirtualnej lub czekać na agenta maszyny Wirtualnej do zainicjowania. Może istnieć usługi, od których zależy agent maszyny Wirtualnej, które zajmują dużo czasu, aby zainicjować. W takich przypadkach zobacz [Usługi Azure Virtual Machine Agent omówienie](../virtual-machines/extensions/agent-windows.md) dalszych rozwiązywania problemów.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Aby sprawdzić, czy artefakt wydaje się zawiesić z powodu skryptu

1. Zaloguj się do danej maszyny wirtualnej.
2. Skopiuj skrypt lokalnie na maszynie wirtualnej lub znajdź go na maszynie wirtualnej w obszarze `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`. Jest to lokalizacja, w której są pobierane skrypty artefaktów.
3. Korzystając z wiersza polecenia z podwyższonym poziomem uprawnień, należy wykonać skrypt lokalnie, podając te same wartości parametrów, które spowodowały problem.
4. Określ, czy skrypt cierpi na niechciane zachowanie. Jeśli tak, zażądaj aktualizacji artefaktu (jeśli pochodzi on z publicznego repozytorium); lub dokonać poprawek samodzielnie (jeśli pochodzisz z prywatnego repozytorium).

> [!TIP]
> Możesz rozwiązać problemy z artefaktami hostowanymi w naszym [publicznym repozytorium](https://github.com/Azure/azure-devtestlab) i przesłać zmiany do naszego przeglądu i zatwierdzenia. Zobacz sekcję **Składki** w dokumencie [README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Aby uzyskać informacje na temat pisania własnych artefaktów, zobacz [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) dokumentu.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Aby sprawdzić, czy artefakt wydaje się zawiesić z powodu agenta maszyny Wirtualnej:
1. Zaloguj się do danej maszyny wirtualnej.
2. Za pomocą Eksploratora plików przejdź do **pozycji C:\WindowsAzure\logs**.
3. Znajdź i otwórz plik **WaAppAgent.log**.
4. Poszukaj wpisów, które pokazują, kiedy agent maszyny Wirtualnej rozpoczyna się i po zakończeniu inicjowania (oznacza to, że wysyłane jest pierwsze bicie serca). Faworyzuj nowsze wpisy lub w szczególności te w okresie, w którym występuje problem.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    W tym przykładzie widać, że czas rozpoczęcia agenta maszyny Wirtualnej trwało 10 minut i 20 sekund, ponieważ zostało wysłane bicie pulsu. Przyczyną w tym przypadku była usługa OOBE, która zajmuje dużo czasu.

> [!TIP]
> Aby uzyskać ogólne informacje na temat rozszerzeń platformy Azure, zobacz [rozszerzenia i funkcje maszyn wirtualnych platformy Azure.](../virtual-machines/extensions/overview.md)

## <a name="storage-errors"></a>Błędy magazynu
DevTest Labs wymaga dostępu do konta magazynu laboratorium, który jest tworzony do buforowania artefaktów. Gdy DevTest Labs stosuje artefakt, odczyta konfigurację artefaktu i jego pliki ze skonfigurowanych repozytoriów. Domyślnie DevTest Labs konfiguruje dostęp do **publicznego repozytorium artefaktów**.

W zależności od konfiguracji maszyny Wirtualnej może nie mieć bezpośredniego dostępu do tego repozytorium. W związku z tym zgodnie z projektem DevTest Labs buforuje artefakty na koncie magazynu, który jest tworzony podczas pierwszego zainicjowania laboratorium.

Jeśli dostęp do tego konta magazynu jest zablokowany w jakikolwiek sposób, jak wtedy, gdy ruch jest zablokowany z maszyny Wirtualnej do usługi Azure Storage, może pojawić się błąd podobny do następującego:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Powyższy błąd pojawi się w sekcji **Komunikat o wdrożeniu** na stronie **Wyniki artefaktu** w obszarze **Zarządzanie artefaktami**. Pojawi się również w **dziennikach aktywności** w grupie zasobów danej maszyny wirtualnej.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Aby upewnić się, że komunikacja z usługą Azure Storage nie jest blokowana:

- **Sprawdź, czy nie ma dodatkowych grup zabezpieczeń sieciowych**. Może się okazać, że dodano zasady subskrypcji, w których sieciowe sieciowe są automatycznie konfigurowane we wszystkich sieciach wirtualnych. Miałoby to również wpływ na domyślną sieć wirtualną laboratorium, jeśli jest używana, lub inną sieć wirtualną skonfigurowaną w laboratorium, używaną do tworzenia maszyn wirtualnych.
- **Sprawdź domyślne konto magazynu laboratorium** (czyli pierwsze konto magazynu utworzone podczas tworzenia laboratorium, którego nazwa zwykle zaczyna się od litery "a" i kończy się liczbą wielocyfrową, czyli\<labname\>#).
    1. Przejdź do grupy zasobów laboratorium.
    2. Znajdź zasób **typu konto magazynu**, którego nazwa jest zgodna z konwencją.
    3. Przejdź do strony konta magazynu o nazwie **Zapory i sieci wirtualne**.
    4. Upewnij się, że jest ustawiona na **Wszystkie sieci**. Jeśli **zaznaczona** jest opcja Wybrane sieci, upewnij się, że sieci wirtualne laboratorium używane do tworzenia maszyn wirtualnych są dodawane do listy.

Aby uzyskać bardziej szczegółowe rozwiązywanie problemów, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../storage/common/storage-network-security.md).

> [!TIP]
> **Sprawdź reguły sieciowej grupy zabezpieczeń**. Sprawdź [przepływ ip,](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) aby potwierdzić, że reguła w sieciowej grupie zabezpieczeń blokuje ruch do lub z maszyny wirtualnej. Można również przejrzeć reguły skutecznej grupy zabezpieczeń, aby upewnić się, że istnieje reguła **zezwalania na** sieciowe. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z przepływem ruchu maszyn wirtualnych przy użyciu skutecznych reguł zabezpieczeń](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Inne źródła błędów
Istnieją inne rzadziej możliwe źródła błędu. Upewnij się, aby ocenić każdy, aby zobaczyć, czy dotyczy sprawy. Oto jeden z nich: 

- **Wygasły token dostępu osobistego dla repozytorium prywatnego**. Po wygaśnięciu artefakt nie zostanie wyświetlony na liście, a wszystkie skrypty, które odwołują się do artefaktów z repozytorium z wygasłym tokenem dostępu prywatnego, zostaną odpowiednio awaryjne.

## <a name="next-steps"></a>Następne kroki
Jeśli żaden z tych błędów nie wystąpił i nadal nie można zastosować artefakty, można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc **techniczną**.

