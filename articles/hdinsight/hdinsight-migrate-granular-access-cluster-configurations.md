---
title: Migrowanie do szczegółowej dostępu opartej na rolach dla konfiguracje klastra — Azure HDInsight
description: Więcej informacji na temat zmiany wymagane w ramach migracji do szczegółowej dostępu opartej na rolach w przypadku konfiguracji klastra HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 56ba2dfebeb47f7e12a2693eae443e3c31e2a4dd
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203102"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrowanie do szczegółowego dostępu opartego na rolach w przypadku konfiguracji klastrów

Wprowadzamy pewne ważne zmiany do obsługi więcej szczegółową kontrolę dostępu opartej na rolach do uzyskania informacji poufnych. Jako część tych zmian, niektóre **akcji mogą być wymagane** Jeśli używasz jednego z [wpływ na podmioty/scenariusze](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Co ulega zmianie?

Wcześniej, wpisów tajnych mogą być uzyskane za pośrednictwem interfejsu API HDInsight przez klaster użytkowników posiadających właściciela, współautora lub czytelnika [role RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), tak jakby były dostępne dla każdego z `*/read` uprawnienie nie jest wymagana.
Idąc dalej, uzyskiwanie dostępu do tych kluczy tajnych będzie wymagać `Microsoft.HDInsight/clusters/configurations/*` uprawnienie, co oznacza, nie będzie można do nich przez użytkowników z roli Czytelnik. Klucze tajne są definiowane jako wartości, które mogłyby zostać użyte w celu uzyskania bardziej podwyższonego poziomu dostępu, niż rola użytkownika powinien zezwalać na. Obejmują one wartości, takich jak klaster bramy HTTP poświadczeń, klucze konta magazynu i poświadczenia bazy danych.

Ponadto wprowadzamy nowe [Operator klastra HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) roli, który ma być możliwe do pobierania wpisów tajnych, bez konieczności udzielania uprawnień administracyjnych, współautora lub właściciela. Podsumowując:

| Rola                                  | Wcześniej                                                                                       | Idąc dalej       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Czytelnik                                | -Dostęp do odczytu, włącznie z wpisy tajne                                                                   | -Dostęp do odczytu, **z wyłączeniem** wpisów tajnych |           |   |   |
| Klaster HDInsight — Operator<br>(Nową rolę) | ND                                                                                              | — Dostęp odczytu/zapisu, w tym kluczy tajnych         |   |   |
| Współautor                           | — Dostęp odczytu/zapisu, w tym kluczy tajnych<br>-Tworzenie i zarządzanie nimi, wszystkie typy zasobów platformy Azure.     | Nie wprowadzono zmian |
| Właściciel                                 | — Dostęp odczytu/zapisu, w tym kluczy tajnych<br>-Pełny dostęp do wszystkich zasobów<br>-Delegować dostęp do innych osób | Nie wprowadzono zmian |

Aby uzyskać informacje na temat dodawania Operator klastra HDInsight przypisania roli do użytkownika, aby przyznać im odczyt/zapis dostęp do wpisów tajnych klastra, zobacz poniżej sekcji [Dodaj przypisanie roli operatora klastra HDInsight z użytkownikiem](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Dla mnie zagrożeniem te zmiany?

Uwzględnione są następujące jednostki i scenariuszy:

- [INTERFEJS API](#api): Użytkownicy korzystający z `/configurations` lub `/configurations/{configurationName}` punktów końcowych.
- [Narzędzia Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) wersji 1.1.1 lub niższy.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) wersji 3.20.0 lub niższy.
- [Usługa Azure Data Lake i Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1 w wersji starszej.
- [Zestaw narzędzi platformy Azure dla środowiska Eclipse](#azure-toolkit-for-eclipse) wersji 3.15.0 lub niższy.
- [Zestaw SDK dla platformy .NET](#sdk-for-net)
    - [wersji 1.x i 2.x](#versions-1x-and-2x): Użytkownicy korzystający z `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` lub `DisableHttp` metody z klasy ConfigurationsOperationsExtensions.
    - [wersji 3.x i nowsze](#versions-3x-and-up): Użytkownicy korzystający z `Get`, `Update`, `EnableHttp`, lub `DisableHttp` metody z `ConfigurationsOperationsExtensions` klasy.
- [Zestaw SDK dla języka Python](#sdk-for-python): Użytkownicy korzystający z `get` lub `update` metody z `ConfigurationsOperations` klasy.
- [Zestaw SDK dla języka Java](#sdk-for-java): Użytkownicy korzystający z `update` lub `get` metody z `ConfigurationsInner` klasy.
- [Zestaw SDK dla języka Go](#sdk-for-go): Użytkownicy korzystający z `Get` lub `Update` metody z `ConfigurationsClient` struktury.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) niższej wersji 2.0.0.
Procedurę migracji dla danego scenariusza można znaleźć w poniższych sekcjach (lub za pomocą powyższych linków).

### <a name="api"></a>Interfejs API

Następujące interfejsy API zostaną zmienione lub uznane za przestarzałe:

- [ **/Configurations/ GET {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (usunięte poufne informacje)
    - Wcześniej używany do uzyskiwania konfiguracji poszczególnych typów (łącznie z wpisy tajne).
    - To wywołanie interfejsu API zwróci teraz konfiguracji poszczególnych typów przy użyciu kluczy tajnych pominięty. Aby uzyskać wszystkie konfiguracje, w tym wpisów tajnych, użyj nowe wywołanie /configurations POST. Aby uzyskać tylko ustawienia bramy, należy użyć nowe wywołanie /getGatewaySettings POST.
- [**Pobierz /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (przestarzałe)
    - Wcześniej użyty do uzyskania wszystkich konfiguracji (łącznie z wpisy tajne)
    - To wywołanie interfejsu API będą już obsługiwane. Aby uzyskać wszystkie konfiguracje idąc dalej, użyj nowe wywołanie /configurations POST. Aby uzyskać konfiguracje z parametrami poufnych pominięty, należy użyć wywołania /configurations/ {configurationName} GET.
- [**WPIS /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (przestarzałe)
    - Wcześniej używane do aktualizowania poświadczeń bramy.
    - To wywołanie interfejsu API będzie przestarzały i nie jest już obsługiwana. Zamiast tego użyj nowego /updateGatewaySettings POST.

Zastępuje następujące interfejsy API zostały dodane:</span>

- [ **/Configurations WPIS**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ten interfejs API umożliwia uzyskanie wszystkie konfiguracje, w tym kluczy tajnych.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Ten interfejs API umożliwia uzyskanie ustawień bramy.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Aby zaktualizować ustawienia bramy (nazwa użytkownika i/lub hasło), należy użyć tego interfejsu API.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Usługa Azure HDInsight Tools for Visual Studio Code

Jeśli używasz wersji 1.1.1 lub poniżej, aktualizacja do [najnowszą wersję usługi Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) uniknięcie przerw w działaniu.

### <a name="azure-toolkit-for-intellij"></a>Zestaw narzędzi platformy Azure dla środowiska IntelliJ

Jeśli używasz wersji 3.20.0 lub poniżej, aktualizacja do [najnowszą wersję zestawu narzędzi platformy Azure dla środowiska IntelliJ wtyczki](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) uniknięcie przerw w działaniu.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Usługa Azure Data Lake i Stream Analytics Tools for Visual Studio

Aktualizacja do wersji 2.3.9000.1 lub nowszej [Azure Data Lake i Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) uniknięcie przerw w działaniu.  Aby uzyskać pomoc dotyczącą aktualizacji, zobacz naszą dokumentację [aktualizacji Data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Zestaw narzędzi platformy Azure dla środowiska Eclipse

Jeśli używasz wersji 3.15.0 lub poniżej, aktualizacja do [najnowszą wersję zestawu narzędzi platformy Azure dla środowiska Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) uniknięcie przerw w działaniu.

### <a name="sdk-for-net"></a>Zestaw SDK środowiska .NET

#### <a name="versions-1x-and-2x"></a>Wersji 1.x i 2.x

Aktualizacja do [wersja 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) zestawu HDInsight SDK dla platformy .NET. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- `ClusterOperationsExtensions.GetClusterConfigurations` będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć `ClusterOperationsExtensions.ListConfigurations` przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra.
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` jest już przestarzały i został zastąpiony przez `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` i `DisableHttp` obecnie są przestarzałe. HTTP są teraz zawsze włączone, więc te metody nie są już potrzebne.

#### <a name="versions-3x-and-up"></a>Wersji 3.x i nowsze

Aktualizacja do [wersji 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) lub nowszej zestawu HDInsight SDK dla platformy .NET. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) jest już przestarzały i został zastąpiony przez [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) i [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) obecnie są przestarzałe. HTTP są teraz zawsze włączone, więc te metody nie są już potrzebne.

### <a name="sdk-for-python"></a>Zestaw SDK dla środowiska Python

Aktualizacja do [wersji 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) lub nowszej zestawu HDInsight SDK dla języka Python. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) jest już przestarzały i został zastąpiony przez [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Zestaw SDK dla języka Java

Aktualizacja do [wersji 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) lub nowszej zestawu HDInsight SDK dla języka Java. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) jest już przestarzały i został zastąpiony przez [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK For Go

Aktualizacja do [wersji 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) lub nowszej zestawu HDInsight SDK dla języka Go. Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) będzie **już nie zwracać parametry poufnych** , np. klucze magazynu (lokacja) lub poświadczeń HTTP (bramy).
    - Aby pobrać wszystkie konfiguracje, w tym liter parametrów, należy użyć [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) przyszłości.  Należy pamiętać, że użytkownicy z rolą "Reader" nie będzie można użyć tej metody. To zapewnia precyzyjną kontrolę nad tym, którzy użytkownicy mają dostęp do poufnych informacji do klastra. 
    - Aby pobrać tylko poświadczeń bramy protokołu HTTP, użyj [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) jest już przestarzały i został zastąpiony przez [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight programu PowerShell
Aktualizacja do [Az PowerShell w wersji 2.0.0](https://www.powershellgallery.com/packages/Az) lub nowszej, aby uniknąć przerw w pracy.  Modyfikacje minimalnej ilości kodu mogą być wymagane, jeśli używane są metody wpływ tych zmian.
- `Grant-AzHDInsightHttpServicesAccess` jest już przestarzały i został zastąpiony nowym `Set-AzHDInsightGatewayCredential` polecenia cmdlet.
- `Get-AzHDInsightJobOutput` został zaktualizowany do obsługi szczegółowych dostępu opartej na rolach do klucza magazynu.
    - Nie ma to wpływu na użytkowników mających rolę Operator, Współautor lub Właściciel w klastrze usługi HDInsight.
    - Użytkownicy z rolą czytelnik musi określić `DefaultStorageAccountKey` parametru jawnie.
- `Revoke-AzHDInsightHttpServicesAccess` jest już przestarzały. HTTP są teraz zawsze włączone, więc to polecenie cmdlet nie jest już potrzebny.
 Zobacz [az. Przewodnik po migracji HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) Aby uzyskać więcej informacji.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Dodaj przypisanie roli operatora klastra HDInsight z użytkownikiem

Użytkownik mający [Współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) lub [właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) roli można przypisać [Operator klastra HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ról do użytkowników, których chcesz mieć dostępu do odczytu i zapisu do wrażliwych Wartości konfiguracji klastra HDInsight (na przykład klaster bramy poświadczenia i klucze konta magazynu).

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem dodania tego przypisania ról jest przy użyciu `az role assignemnt create` polecenia w interfejsie wiersza polecenia platformy Azure.

> [!NOTE]
> To polecenie musi być uruchamiane przez użytkownika z roli współautora lub właściciela, ponieważ tylko oni może nadać te uprawnienia. `--assignee` Jest adres e-mail użytkownika, któremu chcesz przypisać rolę operatora klastra HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Przydział roli na poziomie zasobów (klastrowy)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Przydział roli na poziomie grupy zasobów

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Przydział roli na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Witryna Azure portal umożliwia również dodawanie przypisania roli operatora klastra HDInsight z użytkownikiem. Można znaleźć w dokumentacji [zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal — Dodawanie przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
