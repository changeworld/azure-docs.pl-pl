---
title: Rozszerzenie maszyny wirtualnej platformy Azure Monitor dla systemu Linux | Dokumentacja firmy Microsoft
description: Wdróż agenta usługi Log Analytics na maszynie wirtualnej systemu Linux przy użyciu rozszerzenia maszyny wirtualnej.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/06/2019
ms.author: roiyz
ms.openlocfilehash: 8b24af016349db0fcfb4106a1e69da395e3d0150
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755156"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Rozszerzenie maszyny wirtualnej platformy Azure Monitor dla systemu Linux

## <a name="overview"></a>Omówienie

Dzienniki platformy Azure Monitor zapewnia możliwości korygowania monitorowania, alertów i alertów w zasoby w chmurze i lokalnych. Rozszerzenie maszyny wirtualnej Log Analytics Agent dla systemu Linux jest opublikowany i obsługiwane przez firmę Microsoft. Rozszerzenie instaluje agenta usługi Log Analytics na maszynach wirtualnych platformy Azure i rejestruje maszyn wirtualnych do istniejącego obszaru roboczego usługi Log Analytics. W tym dokumencie przedstawiono obsługiwanych platform, konfiguracji i opcje wdrażania usługi Azure Monitor rozszerzenie maszyny wirtualnej dla systemu Linux.

>[!NOTE]
>W ramach ciągłego przejście z programu Microsoft Operations Management Suite (OMS) do usługi Azure Monitor OMS Agent for Windows lub Linux zostanie określone jako funkcja agentów usługi Log Analytics dla Windows i usługi Log Analytics agent dla systemu Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="operating-system"></a>System operacyjny

Rozszerzenia Log Analytics Agent mogą być uruchamiane względem tych dystrybucje systemu Linux.

| Dystrybucja | Wersja |
|---|---|
| CentOS Linux | 6 — x86/x64 64 i 7 (x 64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 i 7 — x86/x64 64 |
| Red Hat Enterprise Linux Server | 6 — x86/x64 64 i 7 (x 64) |
| Debian GNU/Linux | 8 i 9 — x86/x64 64 |
| Ubuntu | 14.04 LTS — x86/x64 64, 16.04 LTS — x86/x64 64 i 18.04 LTS (x64) |
| SUSE Linux Enterprise Server | 12 (x 64) |

>[!NOTE]
>Niższa niż wersja biblioteki OpenSSL 1.x nie jest obsługiwane na dowolnej platformie i wersji 1.10 jest obsługiwana tylko na platformach x86_64 (64-bitowe).  
>

### <a name="agent-prerequisites"></a>Wymagania wstępne dotyczące agenta

W poniższej tabeli wymieniono pakiety wymagane dla obsługiwanych dystrybucjach systemu Linux, które agent zostanie zainstalowany na.

|Wymagany pakiet |Opis |Minimalna wersja |
|-----------------|------------|----------------|
|Glibc |    Biblioteka C GNU | 2.5-12 
|Openssl    | Biblioteki OpenSSL | 1.0.x lub 1.1.x |
|Curl | Klient sieci web programu cURL | 7.15.5 |
|Ctypes języka Python | | 
|PAM | Podłączane moduły uwierzytelniania | | 

>[!NOTE]
>Rsyslog lub syslog-ng są wymagane do zbierania komunikaty dziennika systemowego. Demon syslog domyślne w wersji 5 (sysklog) w wersji w systemie Red Hat Enterprise Linux, CentOS i Oracle Linux nie jest obsługiwana dla zbierania zdarzeń dziennika systemu. Do zbierania danych z serwera syslog z tej wersji tych dystrybucji, demona rsyslog powinien być zainstalowany i skonfigurowany tak, aby zastąpić sysklog.

### <a name="agent-and-vm-extension-version"></a>Wersja agenta i rozszerzenia maszyny Wirtualnej
Poniższa tabela zawiera mapowanie wersję rozszerzenia maszyny Wirtualnej platformy Azure Monitor i pakietu agenta usługi Log Analytics dla każdej wersji. Link do wersji agenta usługi Log Analytics w wersji pakietu jest dołączony. Informacje o wersji zawierają szczegółowe informacje na temat poprawki i nowe funkcje dostępne w wersji danego agenta.  

| Wersja rozszerzenia usługi Azure Monitor maszyny Wirtualnej z systemem Linux | Wersja pakietu agenta analizy dziennika | 
|--------------------------------|--------------------------|
|1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
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

| Name (Nazwa) | Wartość / przykład |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| Wydawcy | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| Identyfikator obszaru roboczego (np.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| klucz workspaceKey (np.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu

Rozszerzenia maszyn wirtualnych platformy Azure można wdrażać przy użyciu szablonów usługi Azure Resource Manager. Szablony są idealnym rozwiązaniem w przypadku wdrażania maszyn wirtualnych, które wymagają konfiguracji po wdrożeniu, takich jak dołączanie do usługi Azure Monitor dzienniki. Przykładowy szablon usługi Resource Manager, który uwzględnia również rozszerzenie Log Analytics Agent VM znajduje się na [w galerii platformy Azure Szybki Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

Konfiguracji JSON dla rozszerzenia maszyny wirtualnej mogą być zagnieżdżone wewnątrz zasobu maszyny wirtualnej lub umieszczone w katalogu głównego lub najwyższego poziomu szablon JSON usługi Resource Manager. Położenie konfiguracji JSON ma wpływ na wartości nazwy i typu zasobu. Aby uzyskać więcej informacji, zobacz [Ustaw nazwę i typ zasobów podrzędnych](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

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

Interfejs wiersza polecenia platformy Azure może służyć do wdrożenia rozszerzenia Log Analytics Agent VM do istniejącej maszyny wirtualnej. Zastąp *workspaceId* i *klucz workspaceKey* osobom z obszaru roboczego usługi Log Analytics. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
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
| 55 | Nie można nawiązać połączenia z usługą Azure Monitor lub wymagane pakiety serwerach lub brakuje Menedżera pakietów jest zablokowany| Upewnij się, że system ma dostęp do Internetu lub czy podano prawidłowy serwer proxy HTTP. Ponadto sprawdź poprawność identyfikator obszaru roboczego i sprawdź, czy są zainstalowane narzędzia programu curl i tar. |

Dodatkowe informacje dotyczące rozwiązywania problemów można znaleźć na [przewodnik rozwiązywania problemów programu Log Analytics Agent for Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Pomoc techniczna

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz Uzyskaj pomoc techniczną. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
