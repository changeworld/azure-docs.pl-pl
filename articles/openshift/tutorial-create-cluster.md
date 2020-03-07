---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift
description: Dowiedz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 0e6aecccc19572ee980feb4d816fae1f2b0101b7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381481"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Samouczek: Tworzenie klastra usługi Azure Red Hat OpenShift

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak utworzyć Microsoft Azure klaster Red Hat OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure, skalować go, a następnie usunąć, aby wyczyścić zasoby.

W pierwszej części serii przedstawiono następujące informacje:

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

Upewnij się, że [skonfigurowano środowisko programistyczne](howto-setup-environment.md), w tym:
- Instalowanie najnowszego interfejsu wiersza polecenia (w wersji 2.0.65 lub nowszej)
- Tworzenie dzierżawy, jeśli jeszcze jej nie masz
- Tworzenie obiektu aplikacji platformy Azure, jeśli jeszcze go nie masz
- Tworzenie grupy zabezpieczeń
- Tworzenie Active Directory użytkownika w celu zalogowania się do klastra.

## <a name="step-1-sign-in-to-azure"></a>Krok 1. Logowanie do platformy Azure

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, Otwórz powłokę poleceń bash i uruchom `az login`, aby zalogować się do platformy Azure.

```bash
az login
```

 Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` subskrypcją, której chcesz użyć.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2. Tworzenie klastra usługi Azure Red Hat OpenShift

W oknie polecenia bash ustaw następujące zmienne:

> [!IMPORTANT]
> Wybierz nazwę klastra, która jest unikatowa, a wszystkie małe litery lub utworzenie klastra zakończy się niepowodzeniem.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Wybierz lokalizację, w której ma zostać utworzony klaster. Aby zapoznać się z listą regionów świadczenia usługi Azure, które obsługują OpenShift na platformie Azure, zobacz [Obsługiwane regiony](supported-resources.md#azure-regions). Na przykład: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ustaw `APPID` wartość zapisaną w kroku 5 [Tworzenie rejestracji aplikacji usługi Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).

```bash
APPID=<app ID value>
```

Dla opcji "GROUPID" Ustaw wartość zapisaną w kroku 10 [tworzenia grupy zabezpieczeń usługi Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ustaw `SECRET` wartość zapisaną w kroku 8 [tworzenia klucza tajnego klienta](howto-aad-app-configuration.md#create-a-client-secret).

```bash
SECRET=<secret value>
```

Ustaw `TENANT` wartość identyfikatora dzierżawy zapisanej w kroku 7 [Tworzenie nowej dzierżawy](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Utwórz grupę zasobów dla klastra. Uruchom następujące polecenie z tej samej powłoki bash, która została użyta do zdefiniowania powyższych zmiennych:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcjonalne: łączenie sieci wirtualnej klastra z istniejącą siecią wirtualną

Jeśli nie musisz łączyć sieci wirtualnej (VNET) tworzonego klastra z istniejącą siecią wirtualną za pośrednictwem komunikacji równorzędnej, Pomiń ten krok.

W przypadku komunikacji równorzędnej z siecią poza domyślną subskrypcją w ramach tej subskrypcji należy również zarejestrować dostawcę Microsoft. ContainerService. Aby to zrobić, uruchom poniższe polecenie w tej subskrypcji. W przeciwnym razie, jeśli sieć wirtualna jest zlokalizowana w tej samej subskrypcji, możesz pominąć krok rejestrowania.

`az provider register -n Microsoft.ContainerService --wait`

Najpierw Pobierz identyfikator istniejącej sieci wirtualnej. Identyfikator będzie miał postać: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Jeśli nie znasz nazwy sieci lub grupy zasobów, do której należy istniejąca sieć wirtualna, przejdź do [bloku sieci wirtualne](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) , a następnie kliknij sieć wirtualną. Zostanie wyświetlona strona Sieć wirtualna z nazwą sieci i grupą zasobów, do której należy.

Zdefiniuj zmienną VNET_ID przy użyciu następującego polecenia CLI w BASH Shell:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Na przykład: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Opcjonalnie: łączenie klastra z usługą Azure Monitoring

Najpierw Pobierz identyfikator **istniejącego** obszaru roboczego usługi log Analytics. Identyfikator będzie miał postać:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Jeśli nie znasz nazwy obszaru roboczego analizy dzienników lub grupy zasobów, do której należy istniejący obszar roboczy usługi log Analytics, przejdź do [obszaru roboczego Analiza dzienników](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) i kliknij obszar roboczy analizy dzienników. Zostanie wyświetlona strona obszaru roboczego Analiza dzienników, na której będzie wyświetlana nazwa obszaru roboczego i grupy zasobów, do której należy.

_Aby utworzyć obszar roboczy usługi log Analytics, zobacz temat [Tworzenie obszaru roboczego usługi log Analytics](../azure-monitor/learn/quick-create-workspace-cli.md)_

Zdefiniuj zmienną WORKSPACE_ID przy użyciu następującego polecenia CLI w BASH Shell:

```bash
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>Tworzenie klastra

Teraz można przystąpić do tworzenia klastra. Poniższe polecenie spowoduje utworzenie klastra w określonej dzierżawie usługi Azure AD, określenie obiektu aplikacji usługi Azure AD i wpisu tajnego, który będzie używany jako podmiot zabezpieczeń, oraz grupy zabezpieczeń zawierającej członków z dostępem administratora do klastra.

> [!IMPORTANT]
> Upewnij się, że zostały prawidłowo dodane odpowiednie uprawnienia do aplikacji usługi Azure AD zgodnie z opisem w [tym miejscu](howto-aad-app-configuration.md#add-api-permissions) przed utworzeniem klastra

Jeśli klaster **nie** jest używany jako Komunikacja równorzędna z siecią wirtualną lub **nie** chcesz monitorować platformy Azure, użyj następującego polecenia:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Jeśli klaster **jest** używany jako Komunikacja równorzędna z siecią wirtualną, użyj następującego polecenia, które dodaje flagę `--vnet-peer`:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Jeśli **chcesz** monitorować platformę Azure przy użyciu klastra, użyj następującego polecenia, które dodaje flagę `--workspace-id`:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Jeśli wystąpi błąd, że nazwa hosta jest niedostępna, może to być spowodowane faktem, że nazwa klastra nie jest unikatowa. Spróbuj usunąć pierwotną rejestrację aplikacji i wykonać kroki z inną nazwą klastra w temacie [Tworzenie nowej aplikacji Rejestracja](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), pomijając krok tworzenia nowego użytkownika i grupy zabezpieczeń.




Po kilku minutach `az openshift create` zostanie ukończona.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Pobieranie adresu URL logowania dla klastra

Pobierz adres URL logowania do klastra, uruchamiając następujące polecenie:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Poszukaj `publicHostName` w danych wyjściowych, na przykład: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Adres URL logowania dla klastra będzie `https://` po którym następuje wartość `publicHostName`.  Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Ten identyfikator URI zostanie użyty w następnym kroku jako część identyfikatora URI przekierowania rejestracji aplikacji.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3. aktualizowanie identyfikatora URI przekierowania rejestracji aplikacji

Teraz, gdy masz adres URL logowania dla klastra, ustaw interfejs użytkownika przekierowania rejestracji aplikacji:

1. Otwórz [blok rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Kliknij obiekt rejestracji aplikacji.
3. Kliknij pozycję **Dodaj identyfikator URI przekierowania**.
4. Upewnij się, że **Typ** to **Web** i ustaw **Identyfikator URI przekierowania** przy użyciu następującego wzorca: `https://<public host name>/oauth2callback/Azure%20AD`. Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kliknij pozycję **Zapisz**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4. Logowanie do konsoli OpenShift

Teraz możesz zalogować się do konsoli usługi OpenShift dla nowego klastra. [Konsola sieci Web OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) umożliwia wizualizację i przeglądanie zawartości projektów OpenShift oraz zarządzanie nią.

Musisz mieć nowe wystąpienie przeglądarki, które nie buforuje tożsamości, której zwykle używasz do logowania się do Azure Portal.

1. Otwórz okno *incognito* (Chrome) lub okno *InPrivate* (Microsoft Edge).
2. Przejdź do adresu URL logowania uzyskanego powyżej, na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Zaloguj się przy użyciu nazwy użytkownika utworzonej w kroku 3 [tworzenia nowego użytkownika Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Zostanie wyświetlone okno dialogowe z **żądanymi uprawnieniami** . Kliknij pozycję **wyrażanie zgody w imieniu organizacji** , a następnie kliknij przycisk **Akceptuj**.

Użytkownik jest zalogowany do konsoli klastra.

![Zrzut ekranu przedstawiający konsolę klastra OpenShift](./media/aro-console.png)

 Dowiedz się więcej o [korzystaniu z konsoli OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) do tworzenia i kompilowania obrazów w dokumentacji [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) .

## <a name="step-5-install-the-openshift-cli"></a>Krok 5. Instalowanie interfejsu wiersza polecenia OpenShift

[Interfejs wiersza polecenia OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (lub " *OC Tools*") dostarcza poleceń służących do zarządzania aplikacjami i narzędzi niższego poziomu na potrzeby współdziałania z różnymi składnikami klastra OpenShift.

W konsoli OpenShift kliknij znak zapytania w prawym górnym rogu, podając nazwę logowania, a następnie wybierz pozycję **narzędzia wiersza polecenia**.  Użyj linku **najnowszej wersji** , aby pobrać i zainstalować obsługiwany interfejs wiersza polecenia w systemie OC dla systemu Linux, MacOS lub Windows.

> [!NOTE]
> Jeśli ikona znaku zapytania nie jest widoczna w prawym górnym rogu, wybierz pozycję *Katalog usług* lub *konsola aplikacji* z górnego lewego menu rozwijanego.
>
> Alternatywnie można [pobrać interfejs wiersza polecenia OC](https://www.okd.io/download.html) bezpośrednio.

Strona **narzędzi wiersza polecenia** zawiera polecenie `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Kliknij przycisk *Kopiuj do schowka* , aby skopiować to polecenie.  W oknie terminalu [Ustaw ścieżkę](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) do uwzględnienia lokalnej instalacji narzędzi oC. Następnie zaloguj się do klastra przy użyciu skopiowanego polecenia CLI w OC.

Jeśli nie można uzyskać wartości tokenu przy użyciu powyższych kroków, Pobierz wartość tokenu z: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
