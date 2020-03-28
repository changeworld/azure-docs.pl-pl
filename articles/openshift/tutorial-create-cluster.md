---
title: Samouczek — tworzenie klastra OpenShift red hat azure
description: Dowiedz się, jak utworzyć klaster OpenShift Red Hat platformy Microsoft Azure przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455302"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Samouczek: Tworzenie klastra OpenShift red hat azure

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak utworzyć klaster OpenShift Red Hat platformy Microsoft Azure przy użyciu interfejsu wiersza polecenia platformy Azure, skalować go, a następnie usunąć, aby oczyścić zasoby.

W pierwszej części serii dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift
> * [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Ten samouczek wymaga wersji 2.0.65 interfejsu wiersza polecenia platformy Azure.

Przed rozpoczęciem tego samouczka:

Upewnij się, że [skonfigurowałeś środowisko programistyczne,](howto-setup-environment.md)które obejmuje:
- Instalowanie najnowszego interfejsu wiersza polecenia (wersja 2.0.65 lub wyższa)
- Tworzenie dzierżawy, jeśli jeszcze jej nie masz
- Tworzenie obiektu aplikacji platformy Azure, jeśli jeszcze go nie masz
- Tworzenie grupy zabezpieczeń
- Tworzenie użytkownika usługi Active Directory w celu zalogowania się do klastra.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Zaloguj się na platformie Azure

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, otwórz powłokę polecenia Bash i uruchom, `az login` aby zalogować się na platformie Azure.

```azurecli
az login
```

 Jeśli masz dostęp do wielu `az account set -s {subscription ID}` subskrypcji, uruchom zastąpienie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Tworzenie klastra OpenShift red hat azure

W oknie polecenia Bash ustaw następujące zmienne:

> [!IMPORTANT]
> Wybierz nazwę dla klastra, który jest unikatowy i wszystkie małe litery lub tworzenie klastra zakończy się niepowodzeniem.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Wybierz lokalizację, aby utworzyć klaster. Aby uzyskać listę regionów platformy Azure obsługujących funkcję OpenShift na platformie Azure, zobacz [Obsługiwane regiony](supported-resources.md#azure-regions). Na przykład: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ustaw `APPID` wartość zapisaną w kroku 5 [tworzenie rejestracji aplikacji usługi Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Ustaw "GROUPID" na wartość zapisaną w kroku 10 [tworzenia grupy zabezpieczeń usługi Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ustaw `SECRET` wartość zapisaną w kroku 8 [utwórz klucz tajny klienta](howto-aad-app-configuration.md#create-a-client-secret).

```bash
SECRET=<secret value>
```

Ustaw `TENANT` wartość identyfikatora dzierżawy zapisaną w kroku 7 [tworzenie nowej dzierżawy](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Utwórz grupę zasobów dla klastra. Uruchom następujące polecenie z tej samej powłoki Bash, która została użyta do zdefiniowania zmiennych powyżej:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcjonalnie: łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną

Jeśli nie trzeba łączyć sieci wirtualnej (VNET) klastra, który tworzysz do istniejącej sieci wirtualnej za pośrednictwem komunikacji równorzędnej, pomiń ten krok.

Jeśli komunikacja równorzędna z siecią poza subskrypcją domyślną, a następnie w tej subskrypcji, należy również zarejestrować dostawcę Microsoft.ContainerService. Aby to zrobić, uruchom poniższe polecenie w tej subskrypcji. W przeciwnym razie jeśli sieci wirtualnej, które są komunikacji równorzędnej znajduje się w tej samej subskrypcji, można pominąć krok rejestracji.

`az provider register -n Microsoft.ContainerService --wait`

Najpierw pobierz identyfikator istniejącej sieci wirtualnej. Identyfikator będzie miał postać: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Jeśli nie znasz nazwy sieci lub grupy zasobów, do której należy istniejąca sieć wirtualna, przejdź do [bloku Sieci wirtualne](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) i kliknij sieć wirtualną. Zostanie wyświetlona strona Sieć wirtualna, na której zostanie wyświetlona nazwa sieci i grupy zasobów, do której należy.

Zdefiniuj zmienną VNET_ID za pomocą następującego polecenia CLI w powłoce BASH:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Na przykład: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Opcjonalnie: łączenie klastra z monitorowaniem platformy Azure

Najpierw uzyskaj identyfikator **istniejącego** obszaru roboczego analizy dzienników. Identyfikator będzie miał postać:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Jeśli nie znasz nazwy obszaru roboczego analizy dzienników lub grupy zasobów, do której należy istniejący obszar roboczy analizy dzienników, przejdź do [obszaru roboczego usługi Log-Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) i kliknij obszary robocze analizy dzienników. Zostanie wyświetlona strona obszaru roboczego analizy dzienników, na której zostanie wyświetlona nazwa obszaru roboczego i grupy zasobów, do której należy.

_Aby utworzyć obszar roboczy analizy dzienników, zobacz [Tworzenie obszaru roboczego analizy dzienników](../azure-monitor/learn/quick-create-workspace-cli.md)_

Zdefiniuj zmienną WORKSPACE_ID za pomocą następującego polecenia CLI w powłoce BASH:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Tworzenie klastra

Teraz możesz utworzyć klaster. Poniżej utworzy klaster w określonej dzierżawie usługi Azure AD, określ obiekt aplikacji usługi Azure AD i klucz tajny do użycia jako podmiot zabezpieczeń oraz grupę zabezpieczeń zawierającą członków, którzy mają dostęp administratora do klastra.

> [!IMPORTANT]
> Upewnij się, że poprawnie dodano odpowiednie uprawnienia dla aplikacji usługi Azure AD, jak [opisano tutaj](howto-aad-app-configuration.md#add-api-permissions) przed utworzeniem klastra

Jeśli **nie** są równorzędne klastra do sieci wirtualnej lub **nie** chcesz monitorowania platformy Azure, użyj następującego polecenia:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Jeśli klaster **jest** równorzędny z siecią wirtualną, użyj następującego polecenia, które dodaje flagę: `--vnet-peer`

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Jeśli **chcesz, aby** usługa Azure Monitoring z `--workspace-id` klastrem używała następującego polecenia, które dodaje flagę:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Jeśli pojawi się błąd, że nazwa hosta nie jest dostępna, może to być spowodowane nazwa klastra nie jest unikatowa. Spróbuj usunąć oryginalną rejestrację aplikacji i ponowić kroki przy innej nazwie klastra w [temacie Tworzenie nowej rejestracji aplikacji,](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)pomijając krok tworzenia nowego użytkownika i grupy zabezpieczeń.




Po kilku minutach, `az openshift create` zakończy.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Pobierz adres URL logowania dla klastra

Pobierz adres URL, aby zalogować się do klastra, uruchamiając następujące polecenie:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Poszukaj w `publicHostName` danych wyjściowych, na przykład:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Po adresie URL logowania `https://` dla klastra `publicHostName` następuje wartość.  Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Użyjesz tego identyfikatora URI w następnym kroku jako część identyfikatora URI przekierowania rejestracji aplikacji.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3: Aktualizowanie identyfikatora URI przekierowania rejestracji aplikacji

Teraz, gdy masz adres URL logowania dla klastra, ustaw interfejs użytkownika przekierowania rejestracji aplikacji:

1. Otwórz [blok Rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Kliknij obiekt rejestracji aplikacji.
3. Kliknij **przycisk Dodaj identyfikator URI przekierowania**.
4. Upewnij się, że **type** jest **web** i ustawić **identyfikator URI przekierowania** przy użyciu następującego wzorca: `https://<public host name>/oauth2callback/Azure%20AD`. Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kliknij **przycisk Zapisz**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4: Zaloguj się do konsoli OpenShift

Teraz możesz zalogować się do konsoli OpenShift dla nowego klastra. [Konsola internetowa OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) umożliwia wizualizację, przeglądanie i zarządzanie zawartością projektów OpenShift.

Musisz świeże wystąpienie przeglądarki, które nie buforowane tożsamości zwykle używasz do logowania się do witryny Azure portal.

1. Otwórz okno *incognito* (Chrome) lub *InPrivate* (Microsoft Edge).
2. Przejdź do uzyskanego powyżej adresu URL logowania:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Zaloguj się przy użyciu nazwy użytkownika utworzonej w kroku 3 [utwórz nowego użytkownika usługi Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Zostanie wyświetlone okno dialogowe **żądane uprawnienia.** Kliknij **pozycję Zgoda w imieniu organizacji,** a następnie kliknij pozycję **Zaakceptuj**.

Teraz użytkownik jest zalogowany do konsoli klastra.

![Zrzut ekranu przedstawiający konsolę klastrową OpenShift](./media/aro-console.png)

 Dowiedz się więcej o tworzeniu i tworzeniu obrazów w dokumentacji Red Hat OpenShift [za pomocą konsoli OpenShift.](https://docs.openshift.com/aro/getting_started/developers_console.html) [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)

## <a name="step-5-install-the-openshift-cli"></a>Krok 5: Zainstaluj przycisk OpenShift CLI

[OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (lub *NARZĘDZIA OC)* zapewniają polecenia do zarządzania aplikacjami i narzędzi niższego poziomu do interakcji z różnymi składnikami klastra OpenShift.

W konsoli OpenShift kliknij znak zapytania w prawym górnym rogu przy nazwie logowania i wybierz polecenie **Narzędzia wiersza polecenia**.  Kliknij link **Najnowsze wydanie,** aby pobrać i zainstalować obsługiwany interfejs wiersza polecenia oc dla systemów Linux, MacOS lub Windows.

> [!NOTE]
> Jeśli ikona znaku zapytania nie jest widoczna w prawym górnym rogu, wybierz *katalog usług* lub *konsolę aplikacji* z lewego górnego menu rozwijanym.
>
> Alternatywnie, można [pobrać oc CLI](https://www.okd.io/download.html) bezpośrednio.

Strona **Narzędzia wiersza polecenia** zawiera `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`polecenie formularza .  Kliknij przycisk *Kopiuj do schowka,* aby skopiować to polecenie.  W oknie terminala [ustaw ścieżkę,](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) aby uwzględnić lokalną instalację narzędzi oc. Następnie zaloguj się do klastra za pomocą skopiowanego polecenia oc CLI.

Jeśli nie można uzyskać wartości tokenu przy użyciu powyższych `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`kroków, pobierz wartość tokenu z: .

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
