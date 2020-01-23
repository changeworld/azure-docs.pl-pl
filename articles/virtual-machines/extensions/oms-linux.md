---
title: Azure Monitor rozszerzenie maszyny wirtualnej dla systemu Linux
description: Wdróż agenta usługi Log Analytics na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
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
ms.date: 08/06/2019
ms.author: akjosh
ms.openlocfilehash: a431ae8130e258664328fa32a364eb76c28ea811
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544739"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Azure Monitor rozszerzenie maszyny wirtualnej dla systemu Linux

## <a name="overview"></a>Przegląd

Dzienniki Azure Monitor udostępniają funkcje monitorowania, alertów i korygowania alertów w ramach zasobów w chmurze i lokalnych. Rozszerzenie maszyny wirtualnej Log Analytics Agent dla systemu Linux jest opublikowany i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyn wirtualnych do istniejącego obszaru roboczego usługi Log Analytics. Ten dokument zawiera szczegółowe informacje o obsługiwanych platformach, konfiguracjach i opcjach wdrażania dla rozszerzenia maszyny wirtualnej Azure Monitor dla systemu Linux.

>[!NOTE]
>W ramach ciągłego przejście z programu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor OMS Agent for Windows lub Linux zostanie określone jako funkcja agentów usługi Log Analytics dla Windows i usługi Log Analytics agent dla systemu Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Aby uzyskać szczegółowe informacje o obsługiwanych dystrybucjach systemu Linux, zapoznaj się z artykułem [Omówienie agenta log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Wersja agenta i rozszerzenia maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersji rozszerzenia maszyny wirtualnej Azure Monitor i pakietu agenta Log Analytics dla każdej wersji. Link do wersji agenta usługi Log Analytics w wersji pakietu jest dołączony. Informacje o wersji zawierają szczegółowe informacje na temat poprawki i nowe funkcje dostępne w wersji danego agenta.  

| Wersja rozszerzenia maszyny wirtualnej z systemem Azure Monitor Linux | Wersja pakietu agenta analizy dziennika | 
|--------------------------------|--------------------------|
| 1.12.15 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
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

Usługa Azure Security Center automatycznie aprowizuje agenta usługi Log Analytics i łączy ją z obszarem roboczym usługi Log Analytics domyślny utworzony przez usługę ASC w Twojej subskrypcji platformy Azure. Jeśli używasz usługi Azure Security Center nie należy uruchamiać za pomocą procedury w tym dokumencie. Wykonanie tej czynności powoduje zastąpienie skonfigurowany obszar roboczy co spowoduje przerwanie połączenia z usługą Azure Security Center.

### <a name="internet-connectivity"></a>Łączność z Internetem

Rozszerzenie Log Analytics Agent dla systemu Linux wymaga, że docelowej maszyny wirtualnej jest połączony z Internetem. 

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia Log Analytics Agent. Rozszerzenie wymaga Identyfikatora obszaru roboczego i klucz obszaru roboczego w obszarze roboczym usługi Log Analytics docelowym; te wartości mogą być [znalezione w obszarze roboczym usługi Log Analytics](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) w witrynie Azure portal. Ponieważ klucz obszaru roboczego, powinny być traktowane jako poufne dane, powinny być przechowywane w chronionym ustawienia konfiguracji. Dane platformy Azure ustawienia rozszerzenia chronione maszyny Wirtualnej jest zaszyfrowany i odszyfrowane tylko na docelowej maszynie wirtualnej. Należy pamiętać, że **workspaceId** i **klucz workspaceKey** jest rozróżniana wielkość liter.

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
>Powyższego schematu przyjęto założenie, że zostaną umieszczone na poziomie głównym szablonu. Jeśli umieścisz wewnątrz zasobu maszyny wirtualnej w szablonie, `type` i `name` właściwości, należy zmienić, zgodnie z opisem [w dół](#template-deployment).
>

### <a name="property-values"></a>Wartości właściwości

| Nazwa | Wartość / przykład |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem podczas wdrażania co najmniej jednej maszyny wirtualnej, która wymaga konfiguracji po wdrożeniu, takiej jak dołączanie do dzienników Azure Monitor. Przykładowy szablon Menedżer zasobów zawierający rozszerzenie maszyny wirtualnej agenta Log Analytics można znaleźć w [galerii szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/templates/child-resource-name-type.md). 

W poniższym przykładzie założono, że rozszerzenie maszyny Wirtualnej jest zagnieżdżona w obrębie zasobu maszyny wirtualnej. Zagnieżdżanie rozszerzenia zasobu, za pomocą pliku JSON jest umieszczany w `"resources": []` obiektu maszyny wirtualnej.

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

Podczas umieszczania rozszerzenia JSON w katalogu głównym szablonu, nazwa zasobu zawiera odwołanie do nadrzędnej maszyny wirtualnej, a typ odzwierciedla zagnieżdżonych.  

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

## <a name="azure-cli-deployment"></a>Wdrażania interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia Log Analytics Agent VM do istniejącej maszyny wirtualnej. Zastąp wartość *myWorkspaceKey* poniżej kluczem obszaru roboczego i wartość *myWorkspaceId* identyfikatorem obszaru roboczego. Te wartości można znaleźć w obszarze roboczym Log Analytics w obszarze Azure Portal *Ustawienia zaawansowane*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Rozwiązywanie problemów i pomocy technicznej

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Dane dotyczące stanu wdrożeń rozszerzenia można pobrać z witryny Azure portal i za pomocą wiersza polecenia platformy Azure. Aby wyświetlić stan wdrożenia rozszerzeń dla danej maszyny Wirtualnej, uruchom następujące polecenie, używając wiersza polecenia platformy Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Dane wyjściowe wykonywania rozszerzenia jest rejestrowany w następującym pliku:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Kody błędów i ich znaczenie

| Kod błędu | Znaczenie | Możliwe działania |
| :---: | --- | --- |
| 9 | Wywołuje się przedwcześnie enable | [Aktualizacja agenta systemu Linux platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) do najnowszej dostępnej wersji. |
| 10 | Maszyna wirtualna jest już połączona z obszarem roboczym usługi Log Analytics | Aby połączyć maszynę Wirtualną do obszaru roboczego, określona w schemacie rozszerzenia, ustaw stopOnMultipleConnections na wartość false w ustawieniach publicznych tej właściwości lub Usuń. Ta maszyna wirtualna jest naliczana po dla każdego obszaru roboczego jest połączony. |
| 11 | Nieprawidłowa konfiguracja dostarczane do rozszerzenia | Postępuj zgodnie z powyższych przykładach można ustawić wartości wszystkich właściwości niezbędne do wdrożenia. |
| 17 | Zaloguj się niepowodzenia instalacji pakietu analizy | 
| 19 | Niepowodzenia instalacji pakietu OMI | 
| 20 | Niepowodzenia instalacji pakietu SCX |
| 51 | To rozszerzenie nie jest obsługiwane na system operacyjny maszyny Wirtualnej | |
| 55 | Nie można nawiązać połączenia z usługą Azure Monitor lub brakuje wymaganych pakietów lub Menedżer pakietów serwerach dpkg jest zablokowany| Upewnij się, że system ma dostęp do Internetu lub czy podano prawidłowy serwer proxy HTTP. Ponadto sprawdź poprawność identyfikator obszaru roboczego i sprawdź, czy są zainstalowane narzędzia programu curl i tar. |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć na [przewodnik rozwiązywania problemów programu Log Analytics Agent for Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
