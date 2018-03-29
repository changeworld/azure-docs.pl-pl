---
title: Rozszerzenie maszyny wirtualnej OMS Azure dla systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż agenta pakietu OMS na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: danis
ms.openlocfilehash: 5c0b7224d0f534661950117813e1a4f348810853
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="oms-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej OMS dla systemu Linux

## <a name="overview"></a>Przegląd

Analiza dzienników zapewnia możliwości korygowania monitorowania, alertów i alertów w chmurze i lokalnych zasobów. Agent pakietu OMS rozszerzenie maszyny wirtualnej dla systemu Linux publikowania i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta pakietu OMS na maszynach wirtualnych platformy Azure i rejestrowania maszyn wirtualnych w istniejącym obszarem roboczym analizy dzienników. Ten dokument zawiera szczegóły dotyczące obsługiwanych platform, konfiguracji i opcje wdrażania dla OMS rozszerzenia maszyny wirtualnej systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Można uruchomić rozszerzenia Agent pakietu OMS, względem tych dystrybucje systemu Linux.

| Dystrybucja | Wersja |
|---|---|
| CentOS Linux | 5, 6 i 7 |
| Oracle Linux | 5, 6 i 7 |
| Red Hat Enterprise Linux Server | 5, 6 i 7 |
| Debian GNU/Linux | 6, 7 i 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 i 12 |

### <a name="agent-and-vm-extension-version"></a>Wersja agenta i rozszerzenia maszyny Wirtualnej
Poniższa tabela zawiera mapowania wersji wirtualna OMS rozszerzenia i Agent pakietu OMS pakietu dla każdej wersji. Łącze do informacji o wersji wersji pakietu OMS agent jest dołączony. Informacje o wersji zawierają szczegółowe informacje o poprawki i nowe funkcje dostępne w wersji danego agenta.  

| Wersja rozszerzenia maszyny Wirtualnej systemu Linux OMS | Wersja pakietu OMS agenta | 
|--------------------------------|--------------------------|
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Centrum zabezpieczeń Azure automatycznie udostępnia agent pakietu OMS i łączy go do obszaru roboczego analizy dzienników domyślny utworzony przez ASC w Twojej subskrypcji platformy Azure. Jeśli korzystasz z Centrum zabezpieczeń Azure, nie należy uruchamiać kroków w tym dokumencie. Dzięki temu zastępuje skonfigurowanego obszaru roboczego i przerywa połączenie z Centrum zabezpieczeń Azure.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenia Agent pakietu OMS dla systemu Linux wymaga, aby docelowa maszyna wirtualna jest połączony z Internetem. 

## <a name="extension-schema"></a>Rozszerzenie schematu

Następujące JSON zawiera schemat rozszerzenia Agent pakietu OMS. Rozszerzenie wymaga identyfikator i klucz obszaru roboczego z obszaru roboczego analizy dzienników docelowym; te wartości mogą być [odnaleźć w obszarze roboczym analizy dzienników](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) w portalu Azure. Ponieważ klucz obszaru roboczego powinien być traktowany jako dane poufne, powinny być przechowywane w chronionej konfiguracji. Dane Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest szyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że **workspaceId** i **workspaceKey** jest rozróżniana wielkość liter.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.4 |
| workspaceId (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyny Wirtualnej platformy Azure można wdrożyć przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealne w przypadku wdrażania maszyn wirtualnych, które wymagają konfiguracji wdrożenia post, takich jak dołączania do analizy dzienników. Przykładowy szablon usługi Resource Manager zawierający rozszerzenia maszyny Wirtualnej agenta pakietu OMS można znaleźć w [Azure Szybki Start galerii](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczony w katalogu głównego lub najwyższego poziomu szablonu usługi Resource Manager JSON. Umieszczanie konfiguracji JSON ma wpływ na wartość nazwy zasobów i typu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasoby podrzędne](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

W poniższym przykładzie założono, że rozszerzenia maszyny Wirtualnej jest zagnieżdżona zasobu maszyny wirtualnej. Podczas zagnieżdżania rozszerzenia zasobu, JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Podczas umieszczania rozszerzenia JSON w elemencie głównym szablonu, nazwy zasobu zawiera odwołanie do nadrzędnego maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.4",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Wdrożenia usługi Azure CLI

Interfejsu wiersza polecenia Azure może służyć do wdrożenia rozszerzenia maszyny Wirtualnej agenta pakietu OMS na istniejącej maszyny wirtualnej. Zastąp *workspaceId* i *workspaceKey* z tymi z obszaru roboczego analizy dzienników. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i obsługa techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożenia rozszerzenia może zostać pobrany z portalu Azure i przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenie jest zarejestrowane w następującym pliku:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 9 | Włącz o nazwie przedwcześnie | [Zaktualizuj agenta systemu Linux Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) do najnowszej dostępnej wersji. |
| 10 | Maszyna wirtualna jest już połączona z obszaru roboczego analizy dzienników | Aby połączyć maszynę Wirtualną do obszaru roboczego określonej w schemacie rozszerzenia, stopOnMultipleConnections ma wartość false w publicznych Ustawienia tej właściwości lub Usuń. Tej maszyny Wirtualnej pobiera rozliczane po dla każdego obszaru roboczego jest połączony. |
| 11 | Nieprawidłowy konfiguracji do rozszerzenia | Postępuj zgodnie z powyższych przykładach można ustawić wartości wszystkich właściwości niezbędne do wdrożenia. |
| 12 | Menedżer pakietów dpkg jest zablokowany. | Upewnij się, wszystkie dpkg operacje aktualizacji na komputerze zostało ukończone, a następnie spróbuj ponownie. |
| 20 | Błąd instalacji pakietu SCX |
| 51 | To rozszerzenie nie jest obsługiwana w systemie operacyjnym maszyny Wirtualnej | |
| 55 | Nie można nawiązać połączenia z usługą Microsoft Operations Management Suite | Sprawdź, czy system ma dostęp do Internetu lub że podano prawidłowy serwer proxy HTTP. Ponadto sprawdź poprawność identyfikator obszaru roboczego. |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w [przewodnik rozwiązywania problemów OMS agenta dla Linux](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ekspertów platformy Azure na [fora MSDN Azure i przepełnienie stosu](https://azure.microsoft.com/en-us/support/forums/). Alternatywnie można pliku zdarzenia pomocy technicznej platformy Azure. Przejdź do [witrynę pomocy technicznej platformy Azure](https://azure.microsoft.com/en-us/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z platformy Azure obsługuje, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/en-us/support/faq/).
