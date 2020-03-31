---
title: Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux
description: Wdrażanie agenta usługi Log Analytics na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250558"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux

## <a name="overview"></a>Omówienie

Dzienniki usługi Azure Monitor udostępnia funkcje monitorowania, alertów i korygowania alertów w zasobach w chmurze i lokalnie. Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Linux jest publikowane i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyny wirtualne w istniejącym obszarze roboczym usługi Log Analytics. Ten dokument zawiera szczegółowe informacje na temat obsługiwanych platform, konfiguracji i opcji wdrażania rozszerzenia maszyny wirtualnej usługi Log Analytics dla systemu Linux.

>[!NOTE]
>W ramach trwającego przejścia z pakietu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor agent pakietu OMS dla systemu Windows lub Linux będzie określany jako agent analizy dzienników dla systemu Windows i agenta analizy dzienników dla systemu Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Szczegółowe informacje na temat obsługiwanych dystrybucji systemu Linux można znaleźć w [artykule — omówienie agenta usługi Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Wersja rozszerzenia agenta i maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny Wirtualnej usługi Log Analytics i pakietu agenta usługi Log Analytics dla każdej wersji. Łącze do informacji o wersji dla wersji pakietu agenta usługi Log Analytics jest dołączony. Informacje o wersji zawierają szczegółowe informacje na temat poprawek błędów i nowych funkcji dostępnych dla danej wersji agenta.  

| Wersja rozszerzenia maszyny Wirtualnej systemu Log Analytics dla systemu Linux | Wersja pakietu agenta usługi Log Analytics Agent | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
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

Usługa Azure Security Center automatycznie udostępnia agentowi usługi Log Analytics i łączy go z domyślnym obszarem roboczym usługi Log Analytics utworzonym przez asc w ramach subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center, nie należy uruchamiać kroków w tym dokumencie. W ten sposób zastępuje skonfigurowany obszar roboczy i przerywa połączenie z usługą Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie agenta analizy dziennika dla systemu Linux wymaga, aby docelowa maszyna wirtualna była połączona z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia agenta usługi Log Analytics. Rozszerzenie wymaga identyfikatora obszaru roboczego i klucza obszaru roboczego z docelowego obszaru roboczego usługi Log Analytics; te wartości można [znaleźć w obszarze roboczym usługi Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) w witrynie Azure portal. Ponieważ klucz obszaru roboczego powinien być traktowany jako poufne dane, powinien być przechowywany w konfiguracji ustawień chronionych. Dane ustawień chronionych przez rozszerzenie maszyny Wirtualnej platformy Azure są szyfrowane i odszyfrowywane tylko na docelowej maszynie wirtualnej. Należy zauważyć, **że identyfikator workspaceId** i **klawisz workspaceKey** są rozróżniane.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Schemat powyżej zakłada, że zostanie on umieszczony na poziomie głównym szablonu. Jeśli umieścisz go wewnątrz zasobu maszyny `type` wirtualnej w szablonie, właściwości i `name` powinny zostać zmienione, zgodnie z opisem w [dalszej.](#template-deployment)

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / Przykład |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| wydawca | Monitorowanie usługi Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux ( OmsAgentForLinux ) |
| typHandlerVersion | 1.7 |
| obszar roboczyId (np. | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrożyć za pomocą szablonów usługi Azure Resource Manager. Szablony są idealne podczas wdrażania co najmniej jednej maszyny wirtualnej, które wymagają konfiguracji po wdrożeniu, takich jak dołączanie do dzienników usługi Azure Monitor. Przykładowy szablon Menedżera zasobów zawierający rozszerzenie maszyny Wirtualnej agenta usługi Log Analytics można znaleźć w [Galerii Szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguracja JSON dla rozszerzenia maszyny wirtualnej może być zagnieżdżona wewnątrz zasobu maszyny wirtualnej lub umieszczona na głównym lub najwyższym poziomie szablonu JSON Menedżera zasobów. Położenie konfiguracji JSON wpływa na wartość nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustawianie nazwy i typu zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie przyjęto założenie, że rozszerzenie maszyny wirtualnej jest zagnieżdżone wewnątrz zasobu maszyny wirtualnej. Podczas zagnieżdżania zasobu rozszerzenia `"resources": []` JSON jest umieszczany w obiekcie maszyny wirtualnej.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla konfigurację zagnieżdżoną.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Wdrożenie interfejsu wiersza polecenia platformy Azure

Interfejsu wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia usługi Log Analytics Agent VM do istniejącej maszyny wirtualnej. Zastąp poniższą wartość *myWorkspaceKey* kluczem obszaru roboczego i wartością *myWorkspaceId* identyfikatorem obszaru roboczego. Te wartości można znaleźć w obszarze roboczym usługi Log Analytics w witrynie Azure portal w obszarze *Ustawienia zaawansowane*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzeń można pobrać z witryny Azure portal i przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonania rozszerzenia są rejestrowane w następującym pliku:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenia

| Kod błędu | Znaczenie | Możliwe działanie |
| :---: | --- | --- |
| 9 | Włącz wywołane przedwcześnie | [Zaktualizuj agenta systemu Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) do najnowszej dostępnej wersji. |
| 10 | Maszyna wirtualna jest już połączona z obszarem roboczym usługi Log Analytics | Aby połączyć maszynę wirtualną z obszarem roboczym określonym w schemacie rozszerzenia, ustaw stopOnMultipleConnections na false w ustawieniach publicznych lub usuń tę właściwość. Ta maszyna wirtualna jest rozliczana raz za każdy obszar roboczy, z który jest połączony. |
| 11 | Nieprawidłowa config podana do rozszerzenia | Postępuj zgodnie z poprzednimi przykładami, aby ustawić wszystkie wartości właściwości niezbędne do wdrożenia. |
| 17 | Błąd instalacji pakietu usługi Log Analytics | 
| 19 | Awaria instalacji pakietu OMI | 
| 20 | Awaria instalacji pakietu SCX |
| 51 | To rozszerzenie nie jest obsługiwane w systemie operacyjnym maszyny Wirtualnej | |
| 55 | Nie można połączyć się z usługą Usługi Azure Monitor lub brak pakietów wymaganych lub menedżer pakietów dpkg jest zablokowany| Sprawdź, czy system ma dostęp do Internetu lub czy został dostarczony prawidłowy serwer proxy HTTP. Ponadto sprawdź poprawność identyfikatora obszaru roboczego i sprawdź, czy są zainstalowane narzędzia curl i tar. |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć w Przewodniku rozwiązywania problemów z programem [Log Analytics-Agent-for-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję Uzyskaj pomoc techniczną. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
