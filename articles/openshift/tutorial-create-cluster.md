---
title: Samouczek — Tworzenie klastra usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster systemu Microsoft Azure Red Hat OpenShift, za pomocą wiersza polecenia platformy Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/08/2019
ms.openlocfilehash: baada8a5238725456ca4a2ec7e8257c229066115
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466178"
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

Przed rozpoczęciem tego samouczka:

Upewnij się, że masz [Konfigurowanie środowiska projektowego](howto-setup-environment.md), która obejmuje:
- Instalowanie najnowszego interfejsu wiersza polecenia (wersję 2.0.64 lub nowszy)
- Tworzenie dzierżawy
- Tworzenie obiektu aplikacji na platformie Azure
- Tworzenie użytkownika usługi Active Directory użyte do zalogowania się do aplikacji działających w klastrze.

## <a name="step-1-sign-in-to-azure"></a>Krok 1: Logowanie do platformy Azure

Jeśli korzystasz z wiersza polecenia platformy Azure lokalnie, otwórz skorupach i uruchom polecenie powłoki Bash `az login` zalogować się do platformy Azure.

```bash
az login
```

 Jeśli masz dostęp do wielu subskrypcji, uruchom `az account set -s {subscription ID}` zastępowanie `{subscription ID}` z subskrypcją, w której chcesz użyć.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Krok 2: Tworzenie klastra usługi Azure Red Hat OpenShift

W oknie polecenia powłoki Bash ustaw następujące zmienne:

> [!IMPORTANT]
> Nazwa klastra musi zawierać tylko małe litery, lub tworzenia klastra zakończy się niepowodzeniem.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Użyj tej samej nazwy dla klastra, która została wybrana w kroku 6 procedury [tworzenia nowej rejestracji aplikacji](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Wybierz lokalizację, aby utworzyć klaster. Aby uzyskać listę regionów świadczenia usługi azure obsługuje OpenShift na platformie Azure, zobacz [regionach obsługiwanych](supported-resources.md#azure-regions). Na przykład: `LOCATION=eastus`.

Ustaw `FQDN` w pełni kwalifikowaną nazwę klastra. Ta nazwa składa się z nazwą klastra, lokalizacji, a `.cloudapp.azure.com` dołączany na końcu. Jest to taka sama jak adres URL logowania utworzonego w kroku 6 procedury [tworzenia nowej rejestracji aplikacji](howto-aad-app-configuration.md#create-a-new-app-registration). Na przykład:  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Ustaw `APPID` wartości zapisane w kroku 9 [tworzenia nowej rejestracji aplikacji](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Ustaw `SECRET` wartości zapisane w kroku 6 [Utwórz klucz tajny klienta](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Ustaw `TENANT` wartość Identyfikatora dzierżawy został zapisany w kroku 7 [utworzyć nową dzierżawę](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Utwórz grupę zasobów klastra. Uruchom następujące polecenie z poziomu powłoki Bash, używany do definiowania zmiennych powyżej:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcjonalnie: Łączenie sieci wirtualnej klastra z istniejącej sieci wirtualnej

Jeśli nie potrzebujesz połączyć sieć wirtualną (VNET) klastra utworzone do istniejącej sieci Wirtualnej za pomocą komunikacji równorzędnej, Pomiń ten krok.

Najpierw Pobierz identyfikator istniejącej sieci Wirtualnej. Identyfikator będzie mieć postać: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Jeśli nie znasz nazwy sieciowej lub istniejącej sieci Wirtualnej należy do grupy zasobów, przejdź do strony [bloku sieci wirtualnych](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) i kliknij przycisk w sieci wirtualnej. Strona sieci wirtualnej pojawi się i zostanie wyświetlona lista nazwa sieci i grupy zasobów, do której należy.

Zdefiniuj zmienną VNET_ID w powłoce BASH przy użyciu następującego polecenia interfejsu wiersza polecenia:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Na przykład: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Tworzenie klastra

Teraz możesz przystąpić do tworzenia klastra.

 Nie w przypadku łączenia sieci wirtualnej klastra do istniejącej sieci wirtualnej, można pominąć końcowe `--vnet-peer-id $VNET_ID` parametr w następującym przykładzie.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Po kilku minutach `az openshift create` zostanie ukończone pomyślnie i zwracać odpowiedź w formacie JSON zawierającego szczegółowe informacje o sieci klastra.

> [!NOTE]
> Jeśli wystąpi błąd nazwy hosta nie jest dostępna, może to być, ponieważ nazwa klastra nie jest unikatowa. Spróbuj usuwanie oryginalnej rejestracji aplikacji i ponawianie kroki opisane w [tworzenie nowej rejestracji aplikacji] (howto-aad-app-configuration.md#create-a-new-app-registration) (z pominięciem ostatnim krokiem tworzenia nowego użytkownika, ponieważ już taką utworzoną) z inną nazwę klastra.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Krok 3: Zaloguj się do konsoli platformy OpenShift

Teraz możesz zalogować się do konsoli OpenShift nowego klastra. [Konsoli sieci Web platformy OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) pozwala na wizualizowanie, przeglądania i zarządzania zawartością swoje projekty platformy OpenShift.

Firma Microsoft będzie Zaloguj się jako [nowego użytkownika usługi Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) utworzone na potrzeby testowania. Aby to zrobić, należy wystąpienie nowej przeglądarki, które nie pamięci podręcznej tożsamości, zwykle używane do logowania w witrynie Azure Portal.

1. Otwórz *incognito* okna (przeglądarka Chrome) lub *InPrivate* okna (Microsoft Edge).
2. Przejdź do URL logowania, który został utworzony w kroku 6 procedury [tworzenia nowej rejestracji aplikacji](howto-aad-app-configuration.md#create-a-new-app-registration). Na przykład: https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> Konsola OpenShift używa certyfikatu z podpisem własnym.
> Po wyświetleniu monitu w przeglądarce, należy pominąć to ostrzeżenie i zaakceptuj "niezaufany" certyfikat.

Zaloguj się przy użyciu użytkownika i hasła, który został utworzony w [Tworzenie nowego użytkownika usługi Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user) podczas **żądane uprawnienia** zostanie wyświetlone okno dialogowe, wybierz **zgody w imieniu swojej organizacji**  i następnie **zaakceptować**.

Teraz użytkownik jest zalogowany do konsoli usługi klastra.

![Zrzut ekranu konsoli klastra platformy OpenShift](./media/aro-console.png)

 Dowiedz się więcej o [przy użyciu konsoli OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) do tworzenia i wbudowane obrazy [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentacji.

## <a name="step-4-install-the-openshift-cli"></a>Krok 4: Zainstaluj interfejs wiersza polecenia platformy OpenShift

[Interfejsu wiersza polecenia platformy OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (lub *narzędzia oC.*) zapewnia polecenia do zarządzania aplikacjami i niższego poziomu narzędzi do interakcji z różnych składników klastra platformy OpenShift.

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