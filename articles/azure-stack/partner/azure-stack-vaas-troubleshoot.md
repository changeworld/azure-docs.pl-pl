---
title: Rozwiązywanie problemów z usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z weryfikacji jako usługa dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: fedfd7f83a35398586734fa647751e537b850bf8
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481200"
---
# <a name="troubleshoot-validation-as-a-service"></a>Rozwiązywanie problemów z weryfikacji jako usługa

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Poniżej przedstawiono typowe problemy, niezwiązanych ze sobą na wersji oprogramowania i sposoby ich rozwiązywania.

## <a name="local-agent"></a>Agent lokalny

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>W portalu jest wyświetlany w trybie debugowania lokalnego agenta

Jest to prawdopodobnie, ponieważ agent nie może przesyłać pulsów do usługi z powodu niestabilne połączenie. Puls jest wysyłane co pięć minut. Jeśli usługa nie otrzyma pulsu przez 15 minut, usługa uwzględnia agenta jako nieaktywne i testy zostaną zaplanowane już na nim. Sprawdź komunikat o błędzie w *Agenthost.log* plików znajdujących się w katalogu, którym agent został uruchomiony.

> [!Note]
> Wszystkie testy, które już uruchomione na agencie, będą nadal działać, ale jeśli pulsu nie jest przywracane przed zakończeniem testu, a następnie agenta zakończy się niepowodzeniem zaktualizować stan testu lub przekazywania dzienników. Test zawsze będzie widoczny jako **systemem** i trzeba będzie można anulować.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Proces agenta na komputerze został zamknięty podczas wykonywania testu. Czego można oczekiwać?

Jeśli proces agenta jest zamknięta ungracefully na przykład, ponownym maszynę, proces zabite (CTRL + C w oknie agenta jest traktowane jako łagodne zamykanie), a następnie testu, w którym był uruchomiony na niej będą nadal wyświetlane jako **systemem**. Jeśli agent jest ponownie uruchamiany, a następnie agenta spowoduje zaktualizowanie stanu testu **anulowane**. Jeśli agent nie zostanie ponownie uruchomiony, a następnie test pojawi się jako **systemem** i musi ręcznie anulować testu.

> [!Note]
> Testy w przepływie pracy są planowane do uruchomienia po kolei. **Oczekujące** testy nie będą są wykonywane aż do testów w **systemem** stanów w tej samej po ukończeniu przepływu pracy.

## <a name="vm-images"></a>Obrazy maszyn wirtualnych

### <a name="handle-slow-network-connectivity"></a>Obsługa łączności wolnej sieci

Możesz pobrać obraz PIR w udziale w lokalnym centrum danych. A następnie można sprawdzić obrazu.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Pobierz obraz PIR do lokalnego udziału w przypadku powolnego ruchu sieciowego

1. Pobierz narzędzia AzCopy z: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Wyodrębnij AzCopy.zip i przejdź do katalogu zawierającego AzCopy.exe

3. Otwórz środowiska Windows PowerShell z podwyższonym poziomem uprawnień wiersza. Uruchom następujące polecenia:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare jest ścieżką udziału lub ścieżkę lokalną.

#### <a name="verifying-pir-image-file-hash-value"></a>Weryfikowanie wartość skrótu pliku obrazu PIR

Możesz użyć **Get-HashFile** polecenia cmdlet, aby uzyskać wartość skrótu repozytorium obrazów publiczne pobrane pliki obrazów, aby sprawdzić integralność obrazów.

| Nazwa pliku | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Błąd występuje, gdy przekazywanie obrazu maszyny Wirtualnej w `VaaSPreReq` skryptu

Najpierw sprawdź, czy środowisko jest w dobrej kondycji:

1. Z Menedżer DVM / przejdź do pola, sprawdź, czy można pomyślnie zalogować się do portalu administracyjnego przy użyciu poświadczeń administratora.
1. Upewnij się, że nie ma żadnych alertów lub ostrzeżenia.

Jeśli środowisko jest w dobrej kondycji, ręcznie przekazać 5 obrazów maszyn wirtualnych wymagana do uruchamiania testów VaaS:

1. Zaloguj się jako administrator usługi do portalu administracyjnego. Można znaleźć w portalu administracyjnym adres URL z ONZ magazynu lub sygnatury informacji o pliku. Aby uzyskać instrukcje, zobacz [parametrów środowiska](azure-stack-vaas-parameters.md#environment-parameters).
1. Wybierz **więcej usług** > **dostawców zasobów** > **obliczenia** > **obrazów maszyn wirtualnych**.
1. Wybierz **+ Dodaj** znajdujący się u góry **obrazów maszyn wirtualnych** bloku.
1. Modyfikowanie lub Sprawdź wartości pola dla pierwsze obraz maszyny Wirtualnej:
    > [!IMPORTANT]
    > Nie wszystkie ustawienia domyślne są odpowiednie dla istniejącego elementu portalu Marketplace.

    | Pole  | Wartość  |
    |---------|---------|
    | Wydawca | MicrosoftWindowsServer |
    | Oferta | WindowsServer |
    | Typ systemu operacyjnego | Windows |
    | SKU | 2012-R2-Datacenter |
    | Wersja | 1.0.0 |
    | Identyfikator URI obiektu Blob dysku systemu operacyjnego | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Wybierz przycisk **Utwórz**.
1. Powtórz dla pozostałych obrazów maszyn wirtualnych.

Właściwości wszystkich 5 obrazów maszyn wirtualnych są następujące:

| Wydawca  | Oferta  | Typ systemu operacyjnego | SKU | Wersja | Identyfikator URI obiektu Blob dysku systemu operacyjnego |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Kolejne kroki

- Przegląd [informacje o wersji do weryfikacji jako usługa](azure-stack-vaas-release-notes.md) dotyczące wprowadzania zmian w najnowszych wersjach.