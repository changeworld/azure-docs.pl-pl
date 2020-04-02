---
title: Szczegółowe konfiguracje klastra usługi Azure HDInsight oparte na rolach
description: Dowiedz się więcej o zmianach wymaganych w ramach migracji do szczegółowego dostępu opartego na rolach dla konfiguracji klastra HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: bb78d84aa0f9a2832b6599edeac9d50e0e226437
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546350"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrowanie do szczegółowego dostępu opartego na rolach w przypadku konfiguracji klastrów

Wprowadzamy kilka ważnych zmian w celu wspierania bardziej szczegółowego dostępu opartego na rolach w celu uzyskania poufnych informacji. W ramach tych zmian niektóre działania mogą być wymagane **do 3 września 2019 r.,** jeśli korzystasz z jednego z [dotkniętych jednostek/scenariuszy.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Co się zmienia?

Wcześniej wpisy tajne można było uzyskać za pośrednictwem interfejsu API HDInsight przez użytkowników klastra posiadających role `*/read` Właściciela, Współautora lub Czytnika [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), ponieważ były one dostępne dla każdego, kto ma to uprawnienie. Wpisy tajne są definiowane jako wartości, które mogą służyć do uzyskania większej liczby podwyższonego poziomu dostępu niż rola użytkownika powinna zezwalać. Obejmują one wartości, takie jak poświadczenia HTTP bramy klastra, klucze kont magazynu i poświadczenia bazy danych.

Począwszy od 3 września 2019 r., `Microsoft.HDInsight/clusters/configurations/action` dostęp do tych wpisów tajnych będzie wymagać uprawnień, co oznacza, że nie mogą być już dostępne dla użytkowników z rolą Czytelnika. Role, które mają to uprawnienie są współautor, właściciel i nowa rola operatora klastra HDInsight (więcej na ten temat poniżej).

Wprowadzamy również nową rolę [operatora klastra HDInsight,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) która będzie mogła pobierać wpisy tajne bez uzyskania uprawnień administracyjnych współautora lub właściciela. Podsumowując:

| Rola                                  | Wcześniej                                                                                       | Idąc dalej       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Czytelnik                                | - Dostęp do odczytu, w tym tajemnice                                                                   | - Dostęp do **odczytu, z wyłączeniem wpisów tajnych** |           |   |   |
| Operator klastra HDInsight<br>(Nowa rola) | Nie dotyczy                                                                                              | - Dostęp do odczytu / zapisu, w tym tajemnice         |   |   |
| Współautor                           | - Dostęp do odczytu / zapisu, w tym tajemnice<br>- Tworzenie i zarządzanie wszystkimi typami zasobów platformy Azure.     | Bez zmian |
| Właściciel                                 | - Dostęp do odczytu / zapisu, w tym tajemnice<br>- Pełny dostęp do wszystkich zasobów<br>- Delegowanie dostępu do innych | Bez zmian |

Aby uzyskać informacje na temat dodawania przypisania roli operatora klastra HDInsight do użytkownika w celu przyznania mu dostępu do odczytu/zapisu do wpisów tajnych klastra, zobacz [poniższą sekcję Dodawanie przypisania roli operatora klastra HDInsight do użytkownika](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Czy te zmiany na nie dotyczą mnie?

Dotyczy to następujących jednostek i scenariuszy:

- [API](#api): Użytkownicy korzystający z `/configurations` lub `/configurations/{configurationName}` punktów końcowych.
- [Narzędzia usługi Azure HDInsight tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) w wersji 1.1.1 lub niższej.
- [Zestaw narzędzi platformy Azure dla technologii IntelliJ](#azure-toolkit-for-intellij) w wersji 3.20.0 lub niższej.
- [Narzędzia Usługi Azure Data Lake i Stream Analytics Tools dla programu Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) poniżej wersji 2.3.9000.1.
- [Zestaw narzędzi platformy Azure dla](#azure-toolkit-for-eclipse) programu Eclipse w wersji 3.15.0 lub niższej.
- [Zestaw SDK środowiska .NET](#sdk-for-net)
    - [wersje 1.x lub 2.x](#versions-1x-and-2x) `GetClusterConfigurations`: `GetConnectivitySettings` `ConfigureHttpSettings`Użytkownicy `DisableHttp` używający , , lub `EnableHttp` metod z ConfigurationsOperationsExtensions klasy.
    - [wersje 3.x i nowsze](#versions-3x-and-up) `EnableHttp`: `DisableHttp` Użytkownicy `ConfigurationsOperationsExtensions` korzystający `Get`z , `Update`, lub metod z klasy.
- [SDK dla Języka Python:](#sdk-for-python)Użytkownicy używający `get` lub `update` metody z `ConfigurationsOperations` klasy.
- [SDK dla Języka Java:](#sdk-for-java)Użytkownicy korzystający z `update` lub `get` metody z `ConfigurationsInner` klasy.
- [SDK for Go](#sdk-for-go): `Get` `Update` Użytkownicy `ConfigurationsClient` korzystający z lub metody z struktury.
- [Program Az.HDInsight PowerShell](#azhdinsight-powershell) poniżej wersji 2.0.0.
Zobacz poniższe sekcje (lub użyj powyższych łączy), aby zobaczyć kroki migracji dla twojego scenariusza.

### <a name="api"></a>interfejs API

Następujące interfejsy API zostaną zmienione lub przestarzałe:

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (usunięte poufne informacje)
    - Poprzednio używany do uzyskiwania poszczególnych typów konfiguracji (w tym wpisów tajnych).
    - Począwszy od 3 września 2019 r. to wywołanie interfejsu API zwróci teraz poszczególne typy konfiguracji z pominiętymi wpisami tajnymi. Aby uzyskać wszystkie konfiguracje, w tym wpisy tajne, należy użyć nowego wywołania POST /configurations. Aby uzyskać tylko ustawienia bramy, użyj nowego wywołania POST /getGatewaySettings.
- [**GET /konfiguracje**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (przestarzałe)
    - Poprzednio używane do uzyskania wszystkich konfiguracji (w tym wpisów tajnych)
    - Począwszy od 3 września 2019 r. to wywołanie interfejsu API zostanie przestarzałe i nie będzie już obsługiwane. Aby uzyskać wszystkie konfiguracje w przyszłości, użyj nowego wywołania POST /configurations. Aby uzyskać konfiguracje z pominiętymi parametrami poufnymi, należy użyć wywołania GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (przestarzałe)
    - Poprzednio używane do aktualizowania poświadczeń bramy.
    - Począwszy od 3 września 2019 r. to wywołanie interfejsu API zostanie przestarzałe i nie będzie już obsługiwane. Zamiast tego użyj nowego POST /updateGatewaySettings.

Dodano następujące zastępywane interfejsy API:</span>

- [**POST /konfiguracje**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Ten interfejs API służy do uzyskiwania wszystkich konfiguracji, w tym wpisów tajnych.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Użyj tego interfejsu API, aby uzyskać ustawienia bramy.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Użyj tego interfejsu API, aby zaktualizować ustawienia bramy (nazwa użytkownika i/lub hasło).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Narzędzia usługi Azure HDInsight dla kodu programu Visual Studio

Jeśli używasz wersji 1.1.1 lub poniżej, zaktualizuj do [najnowszej wersji narzędzia Usługi Azure HDInsight Tools for Visual Studio Code,](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) aby uniknąć przerw.

### <a name="azure-toolkit-for-intellij"></a>Zestaw Azure Toolkit for IntelliJ

Jeśli używasz wersji 3.20.0 lub poniżej, zaktualizuj do [najnowszej wersji wtyczki Azure Toolkit for IntelliJ,](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) aby uniknąć przerw.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Narzędzia usługi Azure Data Lake i Stream Analytics tools dla programu Visual Studio

Zaktualizuj do wersji 2.3.9000.1 lub nowszej [usługi Azure Data Lake i Narzędzia do analizy strumienia dla programu Visual Studio,](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) aby uniknąć przerw.  Aby uzyskać pomoc dotyczącą aktualizacji, zobacz naszą [dokumentację, Aktualizacja narzędzi Data Lake Tools dla programu Visual Studio.](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)

### <a name="azure-toolkit-for-eclipse"></a>Zestaw narzędzi platformy Azure dla środowiska Eclipse

Jeśli używasz wersji 3.15.0 lub poniżej, zaktualizuj do [najnowszej wersji zestawu narzędzi azure dla programu Eclipse,](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) aby uniknąć przerw.

### <a name="sdk-for-net"></a>Zestaw SDK środowiska .NET

#### <a name="versions-1x-and-2x"></a>Wersje 1.x i 2.x

Aktualizacja do [wersji 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) hdinsight SDK dla .NET. Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany:

- `ClusterOperationsExtensions.GetClusterConfigurations`nie będzie **już zwracać poufnych parametrów,** takich jak klucze magazynu (lokacja rdzenia) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w `ClusterOperationsExtensions.ListConfigurations` tym parametry poufne, należy użyć w przyszłości.  Należy zauważyć, że użytkownicy z rolą "Reader" nie będą mogli korzystać z tej metody. Pozwala to na szczegółowe kontroli nad którym użytkownicy mogą uzyskać dostęp do poufnych informacji dla klastra.
    - Aby pobrać tylko poświadczenia bramy `ClusterOperationsExtensions.GetGatewaySettings`HTTP, użyj programu .

- `ClusterOperationsExtensions.GetConnectivitySettings`jest przestarzały i został `ClusterOperationsExtensions.GetGatewaySettings`zastąpiony przez .

- `ClusterOperationsExtensions.ConfigureHttpSettings`jest przestarzały i został `ClusterOperationsExtensions.UpdateGatewaySettings`zastąpiony przez .

- `ConfigurationsOperationsExtensions.EnableHttp`i `DisableHttp` są teraz przestarzałe. Protokół HTTP jest teraz zawsze włączony, więc te metody nie są już potrzebne.

#### <a name="versions-3x-and-up"></a>Wersje 3.x i nowsze

Aktualizacja do [wersji 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) lub nowszej pakietu HDInsight SDK dla platformy .NET. Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)nie będzie **już zwracać poufnych parametrów,** takich jak klucze magazynu (lokacja rdzenia) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) tym parametry poufne, należy użyć w przyszłości.Należy zauważyć, że użytkownicy z rolą "Reader" nie będą mogli korzystać z tej metody. Pozwala to na szczegółowe kontroli nad którym użytkownicy mogą uzyskać dostęp do poufnych informacji dla klastra. 
    - Aby pobrać tylko poświadczenia bramy [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)HTTP, użyj programu . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)jest przestarzały i został [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)zastąpiony przez . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)i [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) są teraz przestarzałe. Protokół HTTP jest teraz zawsze włączony, więc te metody nie są już potrzebne.

### <a name="sdk-for-python"></a>Zestaw SDK dla środowiska Python

Aktualizacja do [wersji 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) lub nowszej pakietu HDInsight SDK dla języka Python. Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)nie będzie **już zwracać poufnych parametrów,** takich jak klucze magazynu (lokacja rdzenia) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) tym parametry poufne, należy użyć w przyszłości.Należy zauważyć, że użytkownicy z rolą "Reader" nie będą mogli korzystać z tej metody. Pozwala to na szczegółowe kontroli nad którym użytkownicy mogą uzyskać dostęp do poufnych informacji dla klastra. 
    - Aby pobrać tylko poświadczenia bramy [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)HTTP, użyj programu .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)jest przestarzały i został [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)zastąpiony przez .

### <a name="sdk-for-java"></a>SDK dla Java

Aktualizacja [do wersji 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) lub nowszej pakietu HDInsight SDK dla języka Java. Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany:

- `ConfigurationsInner.get`nie będzie **już zwracać poufnych parametrów,** takich jak klucze magazynu (lokacja rdzenia) lub poświadczenia HTTP (brama).
- `ConfigurationsInner.update`jest teraz przestarzała.

### <a name="sdk-for-go"></a>SDK na dopuszka do pracy

Aktualizacja do [wersji 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) lub nowszej pakietu HDInsight SDK for Go. Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)nie będzie **już zwracać poufnych parametrów,** takich jak klucze magazynu (lokacja rdzenia) lub poświadczenia HTTP (brama).
    - Aby pobrać wszystkie konfiguracje, w [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) tym parametry poufne, należy użyć w przyszłości.Należy zauważyć, że użytkownicy z rolą "Reader" nie będą mogli korzystać z tej metody. Pozwala to na szczegółowe kontroli nad którym użytkownicy mogą uzyskać dostęp do poufnych informacji dla klastra. 
    - Aby pobrać tylko poświadczenia bramy [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)HTTP, użyj programu .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)jest przestarzały i został [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)zastąpiony przez .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Aktualizacja do [programu Az PowerShell w wersji 2.0.0](https://www.powershellgallery.com/packages/Az) lub nowszej, aby uniknąć przerw.  Minimalne modyfikacje kodu mogą być wymagane, jeśli używasz metody, której dotyczą te zmiany.
- `Grant-AzHDInsightHttpServicesAccess`jest przestarzałe i zostało zastąpione przez nowe `Set-AzHDInsightGatewayCredential` polecenie cmdlet.
- `Get-AzHDInsightJobOutput`został zaktualizowany w celu obsługi szczegółowego dostępu opartego na rolach do klucza magazynu.
    - Nie ma to wpływu na użytkowników mających rolę Operator, Współautor lub Właściciel w klastrze usługi HDInsight.
    - Użytkownicy z tylko reader roli `DefaultStorageAccountKey` będzie musiał określić parametr jawnie.
- `Revoke-AzHDInsightHttpServicesAccess`jest teraz przestarzała. Protokół HTTP jest teraz zawsze włączony, więc to polecenie cmdlet nie jest już potrzebne.
 Zobacz [az. Przewodnik migracji HDInsight, aby](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) uzyskać więcej informacji.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Dodawanie przypisania roli operatora klastra HDInsight do użytkownika

Użytkownik z rolą [Właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) może przypisać rolę [operatora klastra HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) do użytkowników, którzy mają mieć dostęp do odczytu/zapisu do poufnych wartości konfiguracji klastra HDInsight (takich jak poświadczenia bramy klastra i klucze kont magazynu).

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Najprostszym sposobem dodania tego przypisania `az role assignment create` roli jest użycie polecenia w interfejsie wiersza polecenia platformy Azure.

> [!NOTE]
> To polecenie musi być uruchamiane przez użytkownika z rolą Owner, ponieważ tylko oni mogą udzielić tych uprawnień. Jest `--assignee` to nazwa jednostki usługi lub adres e-mail użytkownika, do którego chcesz przypisać rolę operatora klastra HDInsight. Jeśli zostanie wyświetlony błąd niewystarczających uprawnień, zapoznaj się z często zadawanymi pytaniami poniżej.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Rola przyznawania na poziomie zasobu (klastra)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Rola przyznawania na poziomie grupy zasobów

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Rola przyznawania dotacji na poziomie subskrypcji

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Alternatywnie można użyć witryny Azure portal, aby dodać przypisanie roli operatora klastra HDInsight do użytkownika. Zobacz dokumentację [Zarządzanie dostępem do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal — dodawanie przypisania roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>Często zadawane pytania

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Dlaczego widzę odpowiedź 403 (Zabronione) po zaktualizowaniu żądań interfejsu API i/lub narzędzia?

Konfiguracje klastra są teraz za szczegółową kontrolę `Microsoft.HDInsight/clusters/configurations/*` dostępu opartą na rolach i wymagają uprawnień dostępu do nich. Aby uzyskać to uprawnienie, przypisz rolę Operator klastra HDInsight, Współautor lub Właściciel do jednostki użytkownika lub jednostki usługi próbującej uzyskać dostęp do konfiguracji.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Dlaczego podczas uruchamiania polecenia interfejsu wiersza polecenia Azure CLI jest widoczne "Niewystarczające uprawnienia do wykonania operacji", aby przypisać rolę operatora klastra HDInsight innemu użytkownikowi lub podmiotowi usługi?

Oprócz roli Owner, użytkownik lub jednostka usługi wykonująca polecenie musi mieć wystarczające uprawnienia usługi Azure AD, aby wyszukać identyfikatory obiektów cesjonariusza. Ten komunikat wskazuje niewystarczające uprawnienia usługi Azure AD. Spróbuj zastąpić `-–assignee` `–assignee-object-id` argument i podać identyfikator obiektu cesjonariusza jako parametr zamiast nazwy (lub identyfikatora głównego w przypadku tożsamości zarządzanej). Zobacz sekcję parametry opcjonalne [przypisania roli az tworzenie dokumentacji,](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) aby uzyskać więcej informacji.

Jeśli to nadal nie działa, skontaktuj się z administratorem usługi Azure AD, aby uzyskać odpowiednie uprawnienia.

### <a name="what-will-happen-if-i-take-no-action"></a>Co się stanie, jeśli nie podejmę żadnych działań?

Począwszy od 3 września 2019 r. `GET /configurations` i `POST /configurations/gateway` wywołania nie będą już zwracać żadnych informacji i `GET /configurations/{configurationName}` wywołanie nie będzie już zwracać poufnych parametrów, takich jak klucze konta magazynu lub hasło klastra. To samo dotyczy odpowiednich metod zestawu SDK i poleceń cmdlet programu PowerShell.

Jeśli używasz starszej wersji jednego z narzędzi dla visual studio, VSCode, IntelliJ lub Eclipse wymienionych powyżej, nie będą już działać, dopóki nie zaktualizujesz.

Aby uzyskać bardziej szczegółowe informacje, zobacz odpowiednią sekcję tego dokumentu dla twojego scenariusza.
