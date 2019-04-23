---
title: Migrowanie do szczegółowej dostępu opartej na rolach dla konfiguracje klastra — Azure HDInsight
description: Więcej informacji na temat zmiany wymagane do migracji do szczegółowej dostępu opartej na rolach w przypadku konfiguracji klastra.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006237"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrowanie do szczegółowej dostępu opartej na rolach w przypadku konfiguracji klastra

Wprowadzamy pewne ważne zmiany do obsługi więcej szczegółową kontrolę dostępu opartej na rolach do uzyskania informacji poufnych. Jako część tych zmian, niektóre **akcji mogą być wymagane** Jeśli używasz jednego z [wpływ na podmioty/scenariusze](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co ulega zmianie?

Wcześniej, wpisów tajnych mogą być uzyskane za pośrednictwem interfejsu API HDInsight przez klaster użytkowników posiadających właściciela, współautora lub czytelnika [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
Idąc dalej, tych kluczy tajnych nie będzie już dostępna dla użytkowników z rolą Czytelnik. Możemy również być Przedstawiamy nową rolę "Operator HDInisght klastra", który ma możliwość pobierania wpisów tajnych, bez konieczności udzielania uprawnień administracyjnych, współautora lub właściciela. Podsumowując:

| Rola                                  | Wcześniej                                                                                       | Teraz       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Czytelnik                                | -Dostęp do odczytu, włącznie z wpisy tajne                                                                   | -Dostęp do odczytu, **z wyłączeniem** wpisów tajnych |           |   |   |
| Klaster HDInsight — Operator<br>(Nową rolę) | ND                                                                                              | — Dostęp odczytu/zapisu, w tym kluczy tajnych         |   |   |
| Współautor                           | — Dostęp odczytu/zapisu, w tym kluczy tajnych<br>-Tworzenie i zarządzanie nimi, wszystkie typy zasobów platformy Azure.     | Bez zmian |
| Właściciel                                 | — Dostęp odczytu/zapisu, w tym kluczy tajnych<br>-Pełny dostęp do wszystkich zasobów<br>-Delegować dostęp do innych osób | Bez zmian |

Aby uzyskać informacje na temat dodawania Operator klastra HDInsight przypisania roli do użytkownika, aby przyznać im odczyt/zapis dostęp do wpisów tajnych klastra, zobacz poniżej sekcji [Dodaj przypisanie roli operatora klastra HDInsight z użytkownikiem](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Dla mnie zagrożeniem te zmiany?

Uwzględnione są następujące jednostki i scenariuszy:

- [INTERFEJS API](#api): Użytkownicy korzystający z `/configurations` lub `/configurations/{configurationName}` punktów końcowych.
- [Narzędzia Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) ___ wersji i poniżej.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) __ wersji i poniżej.
- [Zestaw narzędzi platformy Azure dla środowiska Eclipse](#azure-toolkit-for-eclipse) __ wersji i poniżej.
- [Zestaw SDK dla platformy .NET](#sdk-for-net)
    - [wersji 1.x i 2.x](#versions-1x-and-2x): Użytkownicy korzystający z `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` lub `DisableHttp` metody z klasy ConfigurationsOperationsExtensions.
    - [wersji 3.x i nowsze](#versions-3x-and-up): Użytkownicy korzystający z `EnableHttp`, `DisableHttp`, `Update`, lub `Get` metody z `ConfigurationsOperationsExtensions` klasy.
- [Zestaw SDK dla języka Python](#sdk-for-python): Użytkownicy korzystający z `get` lub `update` metody z klasy ConfigurationsOperations.
- [Zestaw SDK dla języka Java](#sdk-for-java): Użytkownicy korzystający z `update` lub `get` metody z klasy ConfigurationsInner.
- [Zestaw SDK dla języka Go](#sdk-for-go): Użytkownicy korzystający z `Get` lub `Update` metod ze struktury ConfigurationsClient.

Zobacz poniższe sekcje (lub użyj powyższe linki) aby wyświetlić migracji kroki dla danego scenariusza.

### <a name="api"></a>Interfejs API

Następujące interfejsy API zostaną zmienione lub uznane za przestarzałe:

- [**/Configurations/ GET {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (usunięte poufne informacje) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Wcześniej używany do uzyskiwania konfiguracji poszczególnych typów (łącznie z wpisy tajne).
    - To wywołanie interfejsu API zwróci teraz konfiguracji poszczególnych typów przy użyciu kluczy tajnych pominięty. Aby uzyskać wszystkie konfiguracje, w tym wpisów tajnych, użyj nowej [/configurations WPIS]() wywołania. Aby uzyskać tylko ustawienia bramy, należy użyć nowego [/getGatewaySettings WPIS]() wywołania.
- [**Pobierz /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (przestarzałe) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Wcześniej użyty do uzyskania wszystkich konfiguracji (łącznie z wpisy tajne)
    - To wywołanie interfejsu API będą już obsługiwane. Aby uzyskać wszystkie konfiguracje idąc dalej, użyj nowej [/configurations WPIS]() wywołania. Aby uzyskać konfiguracje z parametrami poufnych pominięty, użyj [/configurations/ GET {configurationName}]() wywołania.
- [**WPIS /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (przestarzałe) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Wcześniej używane do aktualizowania poświadczeń bramy.
    - To wywołanie interfejsu API będzie przestarzały i nie jest już obsługiwana. Użyj nowego [/updateGatewaySettings WPIS]() zamiast tego.

Zastępuje następujące interfejsy API zostały dodane:</span>

- **/Configurations WPIS** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Ten interfejs API umożliwia uzyskanie wszystkie konfiguracje, w tym kluczy tajnych.
- **/GetGatewaySettings WPIS** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Ten interfejs API umożliwia uzyskanie ustawień bramy.
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Aby zaktualizować ustawienia bramy (nazwa użytkownika i/lub hasło), należy użyć tego interfejsu API.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Usługa Azure HDInsight Tools for Visual Studio Code

Jeśli używasz wersji 1.1.1 lub starsze, przeprowadź aktualizację do wersji [najnowszą wersję usługi Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) uniknięcie przerw w działaniu.

### <a name="azure-toolkit-for-intellij"></a>Zestaw narzędzi platformy Azure dla środowiska IntelliJ

Jeśli używasz wersji 3.21.0 lub starsze, przeprowadź aktualizację do wersji [najnowszą wersję zestawu narzędzi platformy Azure dla środowiska IntelliJ wtyczki](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) uniknięcie przerw w działaniu.

### <a name="azure-toolkit-for-eclipse"></a>Zestaw narzędzi platformy Azure dla środowiska Eclipse

Jeśli używasz wersji 2019-03-29 lub starsze, przeprowadź aktualizację do najnowszej wersji zestawu narzędzi platformy Azure dla środowiska Eclipse uniknąć przerw w pracy.

### <a name="sdk-for-net"></a>Zestaw SDK środowiska .NET

#### <a name="versions-1x-and-2x"></a>Wersji 1.x i 2.x

Przeprowadź aktualizację do wersji [wersja 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) zestawu HDInsight SDK dla platformy .NET. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- `ClusterOperationsExtensions.GetClusterConfigurations` będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć `ClusterOperationsExtensions.ListConfigurations` przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra.
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` i `DisableHttp` obecnie są przestarzałe. HTTP są teraz zawsze włączone, więc te metody nie są już potrzebne.

#### <a name="versions-3x-and-up"></a>Wersji 3.x i nowsze

Przeprowadź aktualizację do wersji [wersji 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) zestawu HDInsight SDK dla platformy .NET. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć `ConfigurationOperationsExtensions.List` przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) i [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) obecnie są przestarzałe. HTTP są teraz zawsze włączone, więc te metody nie są już potrzebne.

### <a name="sdk-for-python"></a>Zestaw SDK dla środowiska Python

Przeprowadź aktualizację do wersji [wersji 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) zestawu HDInsight SDK dla języka Python. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) jest już przestarzały i został zastąpiony przez [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Zestaw SDK dla języka Java

Przeprowadź aktualizację do wersji [wersji 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) zestawu HDInsight SDK dla języka Java. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć `ConfigurationsInner.list` przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>SDK For Go

Przeprowadź aktualizację do wersji [wersji 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) zestawu HDInsight SDK dla języka Go. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) jest już przestarzały i został zastąpiony przez [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Dodaj przypisanie roli operatora klastra HDInsight z użytkownikiem

Użytkownik mający [Współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) lub [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) roli można przyznać roli operatora klastra HDInsight, aby użytkownicy mają dostęp odczytu/zapisu do wpisów tajnych klaster HDInsight, takie jak brama poświadczeń klastra i klucze konta magazynu.

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem dodania tego przypisania ról jest za pomocą następującego polecenia w interfejsie wiersza polecenia platformy Azure:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> To polecenie musi być uruchamiane przez użytkownika z roli współautora lub właściciela, ponieważ tylko oni może nadać te uprawnienia. `--assignee` Jest adres e-mail użytkownika, któremu chcesz przypisać rolę operatora klastra HDInsight.

Powyższe polecenie daje tę rolę na poziomie subskrypcji. Aby zamiast tego można przydzielić tę rolę na poziomie grupy zasobów, można zmodyfikować polecenie w następujący sposób:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Witryna Azure portal umożliwia również dodawanie przypisania roli operatora klastra HDInsight z użytkownikiem. Można znaleźć w dokumentacji [zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal — Dodawanie przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
