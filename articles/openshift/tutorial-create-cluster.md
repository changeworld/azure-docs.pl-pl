---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster systemu Microsoft Azure Red Hat OpenShift, za pomocą wiersza polecenia platformy Azure
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 9fd37a8343858f44719fe4422b3b9994db42f8af
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672460"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Samouczek: Tworzenie klastra usługi Azure Red Hat OpenShift

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak utworzyć klaster systemu Microsoft Azure Red Hat OpenShift, za pomocą wiersza polecenia platformy Azure, przeskalujesz ją, a następnie usuń go, aby wyczyścić zasoby.

W części pierwszej serii dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift
> * [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Usuwanie klastra usługi Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Ten samouczek wymaga 2.0.65 wersję interfejsu wiersza polecenia platformy Azure.
>    
> Zanim użyjesz usługi Azure Red Hat OpenShift, musisz kupić co najmniej 4 węzły aplikacji Azure Red Hat OpenShift zarezerwowane, zgodnie z opisem w [skonfigurować swoje Środowisko deweloperskie platformy Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Przed rozpoczęciem tego samouczka:

Upewnij się, że masz [Konfigurowanie środowiska projektowego](howto-setup-environment.md), która obejmuje:
- Instalowanie najnowszego interfejsu wiersza polecenia (wersję 2.0.65 lub nowszy)
- Tworzenie dzierżawy, jeśli nie masz jeszcze jeden
- Tworzenie obiektu aplikacji na platformie Azure, jeśli nie masz jeszcze jeden
- Utworzenie grupy zabezpieczeń
- Tworzenie użytkownika usługi Active Directory, aby zarejestrować się w klastrze.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Logowanie do platformy Azure

Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, otwórz skorupach i uruchom polecenie powłoki Bash `az login` zalogować się do platformy Azure.

```bash
az login
```

 Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` z subskrypcją, w której chcesz użyć.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Tworzenie klastra usługi Azure Red Hat OpenShift

W oknie polecenia powłoki Bash ustaw następujące zmienne:

> [!IMPORTANT]
> Wybierz nazwę możesz klastra, który jest unikatowy, a wszystkie tworzenia małe lub klastra zakończy się niepowodzeniem.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Wybierz lokalizację, aby utworzyć klaster. Aby uzyskać listę regionów świadczenia usługi azure obsługuje OpenShift na platformie Azure, zobacz [regionach obsługiwanych](supported-resources.md#azure-regions). Na przykład: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Ustaw `APPID` wartości zapisane w kroku nr 5 procedury [tworzenia rejestracji aplikacji w usłudze Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Wartość "GROUPID" wartość został zapisany w kroku 10 [Utwórz grupę zabezpieczeń usługi Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Ustaw `SECRET` wartości zapisane w kroku 8 [Utwórz klucz tajny klienta](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Ustaw `TENANT` wartość Identyfikatora dzierżawy został zapisany w kroku 7 [utworzyć nową dzierżawę](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Utwórz grupę zasobów klastra. Z tego samego powłoki Bash, używany do definiowania zmiennych powyżej, uruchom następujące polecenie:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcjonalnie: Łączenie sieci wirtualnej klastra z istniejącej sieci wirtualnej

Jeśli nie potrzebujesz połączyć sieć wirtualną (VNET) klastra utworzone do istniejącej sieci Wirtualnej za pomocą komunikacji równorzędnej, Pomiń ten krok.

Jeśli komunikacja równorzędna sieci poza domyślną subskrypcję, następnie w tej subskrypcji, należy również zarejestrować dostawcę Microsoft.ContainerService. Aby to zrobić, uruchom poniższe polecenie w danej subskrypcji. W przeciwnym razie jeśli są komunikacji równorzędnej sieci Wirtualnej znajduje się w tej samej subskrypcji, możesz pominąć krok rejestrowania. 

`az provider register -n Microsoft.ContainerService --wait`

Najpierw Pobierz identyfikator istniejącej sieci Wirtualnej. Identyfikator będzie mieć postać: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Jeśli nie znasz nazwy sieciowej lub istniejącej sieci Wirtualnej należy do grupy zasobów, przejdź do strony [bloku sieci wirtualnych](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) i kliknij przycisk w sieci wirtualnej. Strona sieci wirtualnej pojawi się i zostanie wyświetlona lista nazwa sieci i grupy zasobów, do której należy.

Zdefiniuj zmienną VNET_ID w powłoce BASH przy użyciu następującego polecenia interfejsu wiersza polecenia:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Na przykład: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Tworzenie klastra

Teraz możesz przystąpić do tworzenia klastra. Następujące spowoduje utworzenie klastra w określonej usłudze Azure AD dzierżawy, należy określić obiekt aplikacji usługi Azure AD i hasło do użycia jako podmiot zabezpieczeń oraz grupy zabezpieczeń, która zawiera elementy członkowskie, które mają dostęp administratora do klastra.

Jeśli jesteś **nie** komunikacji równorzędnej klaster do sieci wirtualnej, użyj następującego polecenia:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Jeśli użytkownik **są** komunikacji równorzędnej klaster do sieci wirtualnej, użyj następującego polecenia, który dodaje `--vnet-peer` flagi:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Jeśli wystąpi błąd nazwy hosta nie jest dostępna, może to być, ponieważ nazwa klastra nie jest unikatowa. Spróbuj usuwanie oryginalnej rejestracji aplikacji i ponawianie czynności wymagane przez inną nazwę klastra w [tworzenie nowej rejestracji aplikacji] (howto-aad-app-configuration.md#create-a-new-app-registration), pomijając kroku tworzenia nowych użytkowników i grup zabezpieczeń.

Po kilku minutach `az openshift create` zostanie ukończone.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Uzyskiwanie znaku w adresie URL klastra

Pobierz adres URL do logowania do klastra, uruchamiając następujące polecenie:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Wyszukaj `publicHostName` w danych wyjściowych, na przykład: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

Znak w adresie URL klastra będzie `https://` następuje `publicHostName` wartość.  Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Użyjesz tego identyfikatora URI w następnym kroku jako część identyfikatora URI przekierowania rejestracji aplikacji.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Krok 3: Aktualizacja Twojego identyfikatora URI przekierowania rejestracji aplikacji

Teraz, gdy znak w adresie URL klastra Ustaw przekierowania rejestracji aplikacji interfejsu użytkownika:

1. Otwórz [bloku rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Kliknij obiekt rejestracji aplikacji.
3. Kliknij pozycję **Dodaj identyfikator URI przekierowania**.
4. Upewnij się, że **typu** jest **Web** i ustaw **identyfikator URI PRZEKIEROWANIA** przy użyciu następującego wzorca: `https://<public host name>/oauth2callback/Azure%20AD`. Na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kliknij polecenie **Zapisz**.

## <a name="step-4-sign-in-to-the-openshift-console"></a>Krok 4: Zaloguj się do konsoli platformy OpenShift

Teraz możesz zalogować się do konsoli OpenShift nowego klastra. [Konsoli sieci Web platformy OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) pozwala na wizualizowanie, przeglądania i zarządzania zawartością swoje projekty platformy OpenShift.

Potrzebna będzie wystąpienie nowej przeglądarki, które nie pamięci podręcznej tożsamości, zwykle używane do logowania w witrynie Azure Portal.

1. Otwórz *incognito* okna (przeglądarka Chrome) lub *InPrivate* okna (Microsoft Edge).
2. Przejdź do logowania jednokrotnego adres URL uzyskany powyżej, na przykład: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Zaloguj się przy użyciu nazwy użytkownika, utworzony w kroku 3 procedury [Tworzenie nowego użytkownika usługi Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

A **żądane uprawnienia** pojawi się okno dialogowe. Kliknij przycisk **zgody w imieniu swojej organizacji** a następnie kliknij przycisk **Akceptuj**.

Teraz użytkownik jest zalogowany do konsoli usługi klastra.

![Zrzut ekranu konsoli klastra platformy OpenShift](./media/aro-console.png)

 Dowiedz się więcej o [przy użyciu konsoli OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) do tworzenia i wbudowane obrazy [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentacji.

## <a name="step-5-install-the-openshift-cli"></a>Krok 5. Zainstaluj interfejs wiersza polecenia platformy OpenShift

[Interfejsu wiersza polecenia platformy OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (lub *narzędzia oC.* ) zapewnia polecenia do zarządzania aplikacjami i niższego poziomu narzędzi do interakcji z różnych składników klastra platformy OpenShift.

W konsoli platformy OpenShift, kliknij znak zapytania w prawym górnym rogu według nazwy logowania i wybierz **narzędzi wiersza polecenia**.  Postępuj zgodnie z **najnowszej wersji** link, aby pobrać i zainstalować obsługiwanych oC. interfejsu wiersza polecenia dla systemów Linux, MacOS lub Windows.

> [!NOTE]
> Jeśli nie widzisz ikonę znaku zapytania w prawym górnym rogu, wybierz opcję *katalogu usług* lub *aplikacji konsoli* w górnym lewym menu rozwijanego.
>
> Alternatywnie możesz [Pobierz oC. interfejsu wiersza polecenia](https://www.okd.io/download.html) bezpośrednio.

**Narzędzi wiersza polecenia** strona zawiera polecenia formularza `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Kliknij przycisk *Kopiuj do Schowka* przycisk, aby skopiować tego polecenia.  W oknie terminala [Ustaw ścieżki](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) obejmujący lokalnej instalacji narzędzi oC. Następnie zaloguj się do klastra za pomocą polecenia interfejsu wiersza polecenia oC. skopiowany.

Jeśli nie można pobrać wartość tokenu przy użyciu powyższych kroków, Uzyskaj wartość tokenu z: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Kolejne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie klastra usługi Azure Red Hat OpenShift

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Skalowanie klastra usługi Azure Red Hat OpenShift](tutorial-scale-cluster.md)
