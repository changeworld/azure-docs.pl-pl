---
title: Konsorcjum sieci szkieletowej Hyperledger w usłudze Azure Kubernetes Service (AKS)
description: Jak wdrożyć i skonfigurować sieć konsorcjów sieci Hyperledger Fabric w usłudze Azure Kubernetes
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 2312c002e5c2e0b813f8acbdc3e3bff597f204d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476444"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Konsorcjum sieci szkieletowej Hyperledger w usłudze Azure Kubernetes Service (AKS)

Za pomocą szablonu usługi Azure Kubernetes Service (AKS) można użyć szablonu sieci szkieletowej Hyperledger (HLF) do wdrażania i konfigurowania sieci konsorcjum sieci Hyperledger fabric na platformie Azure.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj wiedzę na temat pracy hyperledger fabric i różnych komponentów, które tworzą budulec sieci blockchain Hyperledger Fabric.
- Dowiedz się, jak wdrożyć i skonfigurować konsorcjum sieci szkieletowej Hyperledger w usłudze Azure Kubernetes dla scenariuszy produkcyjnych.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcjum Hyperledger Fabric Consortium

Aby utworzyć sieć sieci szkieletowej Hyperledger na platformie Azure, należy wdrożyć usługę zamawiania i organizację z węzłami równorzędnym. Różne podstawowe składniki, które są tworzone w ramach wdrażania szablonu są:

- **Węzły zlecania**: Węzeł odpowiedzialny za zamawianie transakcji w księdze. Wraz z innymi węzłami uporządkowane węzły tworzą usługę zamawiania sieci Sieci szkieletowej Hyperledger.

- **Węzły równorzędne:** węzeł, który przede wszystkim hostuje księgi i kontrakty inteligentne, te podstawowe elementy sieci.

- **Urząd certyfikacji sieci**szkieletowej: Urząd certyfikacji jest urzędem certyfikacji (CA) dla sieci szkieletowej hiperledgera. Urząd certyfikacji sieci szkieletowej umożliwia zainicjowanie i uruchomienie procesu serwera, na którym znajduje się urząd certyfikacji. Umożliwia zarządzanie tożsamościami i certyfikatami. Każdy klaster AKS wdrożony jako część szablonu będzie miał domyślnie zasobnik urzędu certyfikacji sieci szkieletowej.

- **CouchDB lub LevelDB:** Baza danych stanu świata dla węzłów równorzędnych może być przechowywana w LevelDB lub CouchDB. LevelDB jest domyślną bazą danych stanu osadzoną w węźle równorzędnym i przechowuje dane chaincode jako proste pary klucza-wartość i obsługuje tylko klucze, zakres kluczy i zapytania klucza złożonego. CouchDB to opcjonalna alternatywna baza danych stanu, która obsługuje zaawansowane zapytania, gdy wartości danych kodu łańcucha są modelowane jako JSON.

Szablon wdrażania uruchamia różne zasoby platformy Azure w ramach subskrypcji. Różne wdrożone zasoby platformy Azure to:

- **Klaster AKS:** klaster Azure Kubernetes, który jest skonfigurowany zgodnie z parametrami wejściowymi dostarczonymi przez klienta. Klaster AKS ma różne zasobników skonfigurowane do uruchamiania składników sieci Hyperledger Fabric. Utworzone różne zasobniki to:

  - **Narzędzia sieci szkieletowej:** Narzędzie sieci szkieletowej jest odpowiedzialne za konfigurowanie komponentów Hyperledger Fabric.
  - **Zasobniki porządkowe/równorzędne:** węzły sieci HLF.
  - **Proxy**: Zasobnik serwera proxy NGNIX, za pośrednictwem którego aplikacje klienckie mogą współpracować z klastrem AKS.
  - **Urząd certyfikacji sieci**szkieletowej: zasobnik, który uruchamia urząd certyfikacji sieci szkieletowej.
- **PostgreSQL**: Wystąpienie PostgreSQL jest wdrażane w celu utrzymania tożsamości urzędu certyfikacji sieci szkieletowej.

- **Magazyn kluczy azure:** wystąpienie magazynu kluczy jest wdrażany w celu zapisania poświadczeń urzędu certyfikacji sieci szkieletowej i certyfikatów głównych dostarczonych przez klienta, który jest używany w przypadku ponownego ponawiania wdrażania szablonu, jest to do obsługi mechaniki szablonu.
- **Dysk zarządzany platformy Azure:** Dysk zarządzany platformy Azure jest przeznaczony do magazynu trwałego dla bazy danych stanu świata księgi i węzłów równorzędnych.
- **Publiczny adres IP:** publiczny punkt końcowy ip klastra AKS wdrożony do współdziałania z klastrem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Konfiguracja sieci Hyperledger Fabric Blockchain

Aby rozpocząć, potrzebujesz subskrypcji platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i standardowych kont magazynu. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Konfiguruj sieć Hyperledger Fabric Blockchain, wykonując następujące czynności:

- [Wdrażanie organizacji zamawiającej/równorzędnej](#deploy-the-ordererpeer-organization)
- [Zbuduj konsorcjum](#build-the-consortium)
- [Uruchamianie natywnych operacji HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Wdrażanie organizacji zamawiającej/równorzędnej

Aby rozpocząć wdrażanie składników sieci HLF, przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **pozycję Utwórz zasób > blockchain** > wyszukaj sieć **szkieletową Hyperledger w usłudze Azure Kubernetes.**

1. Wybierz **utwórz,** aby rozpocząć wdrażanie szablonu. Zostanie **wyświetlona usługa Tworzenie sieci szkieletowej Hyperledger w usłudze Azure Kubernetes.**

    ![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Wprowadź szczegóły projektu na stronie **Podstawowe** informacje.

    ![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Wprowadź następujące wartości:
    - **Subskrypcja**: Wybierz nazwę subskrypcji, w której chcesz wdrożyć składniki sieci HLF.
    - **Grupa zasobów:** Utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów, grupa zasobów będzie przechowywać wszystkie zasoby wdrożone jako część szablonu.
    - **Region:** Wybierz region platformy Azure, w którym chcesz wdrożyć klaster Azure Kubernetes dla składników HLF. Szablon jest dostępny we wszystkich regionach, w których usługa AKS jest dostępna Upewnij się wybrać region, w którym subskrypcja nie osiąga limitu przydziału maszyny wirtualnej.The template is available in all regions where AKS is available Ensure to choose a region where your subscription is not hitting the Virtual Machine (VM) quota limit.
    - **Prefiks zasobu:** Prefiks do nazewnictwa zasobów, które są wdrażane. Prefiks zasobu musi mieć mniej niż sześć znaków, a kombinacja znaków musi zawierać zarówno cyfry, jak i litery.
4. Wybierz kartę **Ustawienia sieci szkieletowej,** aby zdefiniować składniki sieci HLF, które zostaną wdrożone.

    ![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Wprowadź następujące wartości:
    - **Nazwa organizacji**: Nazwa organizacji Fabric, która jest wymagana dla różnych operacji na płaszczyźnie danych. Nazwa organizacji musi być unikatowa dla na wdrożenie. 
    - **Składnik sieci szkieletowej:** wybierz węzły usługi zamawiania lub równorzędne na podstawie składnika sieci Blockchain, który chcesz skonfigurować.
    - **Liczba węzłów** — oto dwa typy węzłów:
        - Usługa zamawiania — wybierz liczbę węzłów, które mają zapewnić odporność na uszkodzenia sieci. Tylko 3,5 i 7 są obsługiwane liczby węzłów zamawiającego.
        - Węzły równorzędne — można wybrać 1-10 węzłów na podstawie wymagań.
    - **Baza danych stanu świata węzłów równorzędnych:** Wybierz między LevelDB i CoucbDB. To pole jest wyświetlane, gdy użytkownik wybierze węzeł równorzędny w rozwijaniu składnika sieci szkieletowej.
    - **Nazwa użytkownika sieci**szkieletowej : Wprowadź nazwę użytkownika używaną do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Hasło urzędu certyfikacji sieci szkieletowej:** Wprowadź hasło do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Potwierdź hasło:** Potwierdź hasło urzędu certyfikacji sieci szkieletowej.
    - **Certyfikaty:** Jeśli chcesz użyć własnych certyfikatów głównych do zainicjowania urzędu certyfikacji sieci szkieletowej, wybierz opcję Przekaż certyfikat główny dla urzędu certyfikacji sieci szkieletowej, w przeciwnym razie domyślnie urząd certyfikacji sieci szkieletowej utworzy certyfikaty z podpisem własnym.
    - **Certyfikat główny:** Przekaż certyfikat główny (klucz publiczny), za pomocą którego należy zainicjować urząd certyfikacji sieci szkieletowej. Obsługiwane są certyfikaty w formacie pem, certyfikaty powinny być ważne w strefie czasowej UTC.
    - **Klucz prywatny certyfikatu głównego**: Przekaż klucz prywatny certyfikatu głównego. Jeśli masz certyfikat .pem, który ma klucz publiczny i prywatny w połączeniu, prześlij go również tutaj.


6. Wybierz kartę **Ustawienia klastra AKS,** aby zdefiniować konfigurację klastra usługi Azure Kubernetes, która jest podstawową infrastrukturą, na której będą konfigurowane składniki sieci szkieletowej.

    ![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Wprowadź następujące wartości:
    - **Nazwa klastra kubernetes**: Nazwa utworzonego klastra AKS. To pole jest wstępnie zaludnione na podstawie dostarczonego prefiksu zasobu, można zmienić w razie potrzeby.
    - **Wersja kubernetes:** wersja kubernetes, które zostaną wdrożone w klastrze. Na podstawie regionu wybranego na karcie **Podstawy** dostępne wersje mogą ulec zmianie.
    - **Prefiks DNS : Prefiks**nazwy dns (Domain Name System) dla klastra AKS. Użyjesz dns, aby połączyć się z interfejsem API kubernetes podczas zarządzania kontenerami po utworzeniu klastra.
    - **Rozmiar węzła:** Rozmiar węzła Kubernetes, można wybrać z listy jednostki przechowywania zapasów maszyn wirtualnych (SKU) dostępne na platformie Azure. Aby uzyskać optymalną wydajność, zalecamy standard DS3 v2.
    - **Liczba węzłów:** liczba węzłów Kubernetes do wdrożenia w klastrze. Zaleca się zachowanie tej liczby węzłów co najmniej równą lub większą niż liczba węzłów HLF określona w ustawieniach sieci szkieletowej.
    - **Identyfikator klienta jednostki usługi:** Wprowadź identyfikator klienta istniejącego podmiotu usługi lub utwórz nowy, który jest wymagany dla uwierzytelniania AKS. Zobacz, kroki, aby [utworzyć jednostkę usługi](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Klucz tajny klienta jednostki usługi:** Wprowadź klucz tajny klienta jednostki usługi pod warunkiem, że identyfikator klienta jednostki usługi.
    - **Potwierdź klucz tajny klienta:** Potwierdź klucz tajny klienta podany w kluczu tajnym klienta głównego usługi.
    - **Włącz monitorowanie kontenerów:** Wybierz, aby włączyć monitorowanie usługi AKS, co umożliwia dzienniki AKS wypychanie do określonego obszaru roboczego usługi Log Analytics.
    - **Obszar roboczy usługi Log Analytics:** Obszar roboczy analizy dzienników zostanie wypełniony domyślnym obszarem roboczym utworzonym, jeśli monitorowanie jest włączone.

8. Po podaniu wszystkich powyższych szczegółów wybierz pozycję **Przejrzyj i utwórz** kartę. Przegląd i tworzenie wyzwala sprawdzanie poprawności dla podanych wartości.
9. Po przejściu sprawdzania poprawności można wybrać **polecenie utwór create**.
Wdrożenie trwa zwykle 10-12 minut, może się różnić w zależności od rozmiaru i liczby węzłów usługi AKS określonych.
10. Po pomyślnym wdrożeniu zostaniesz powiadomiony za pośrednictwem powiadomień platformy Azure w prawym górnym rogu.
11. Wybierz **pozycję Przejdź do grupy zasobów,** aby sprawdzić wszystkie zasoby utworzone w ramach wdrażania szablonu. Wszystkie nazwy zasobów będą rozpoczynać się od prefiksu podanego w ustawieniu **Podstawy.**

## <a name="build-the-consortium"></a>Zbuduj konsorcjum

Aby utworzyć konsorcjum łańcucha bloków po wdrożeniu usługi zamawiania i węzłów równorzędnych, należy wykonać poniższe kroki w kolejności. Tworzenie skryptu **sieciowego** (byn.sh), który pomaga w konfigurowaniu konsorcjum, tworzeniu kanału i instalowaniu kodu łańcucha.

> [!NOTE]
> Tworzenie sieci (byn) skrypt pod warunkiem jest ściśle do użycia w scenariuszach demo/devtest. W przypadku konfiguracji klasy produkcyjnej zaleca się używanie natywnych interfejsów API HLF.

Wszystkie polecenia do uruchomienia skryptu byn mogą być wykonywane za pośrednictwem interfejsu wiersza polecenia programu Azure Bash (CLI). Możesz zalogować się do wersji internetowej powłoki platformy Azure za pomocą ![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) w prawym górnym rogu witryny Azure portal. W wierszu polecenia wpisz bash i wprowadź, aby przełączyć się na bash CLI.

Zobacz [powłoki platformy Azure,](https://docs.microsoft.com/azure/cloud-shell/overview) aby uzyskać więcej informacji.

![Szablon usługi Azure Kubernetes w sieci szkieletowej hyperledger](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Pobierz plik byn.sh i fabric-admin.yaml.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Ustaw poniżej zmienne środowiskowe w powłoce Bash interfejsu wiersza polecenia platformy Azure:**

Ustawianie informacji o kanale i informacji o organizacji zamawiającego

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Ustawianie informacji o organizacji elementów równorzędnych

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Utwórz jeden udział w usłudze Azure File, aby udostępniać różne certyfikaty publiczne między organizacjami równorzędnym i zamawiającymi.

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>

az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING="https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN"
```
**Polecenia zarządzania kanałami**

Przejdź do organizacji zamawiania AKS cluster i polecenia wydawania, aby utworzyć nowy kanał

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Polecenia zarządzania konsorcjum**

Wykonaj poniżej polecenia w danej kolejności, aby dodać organizację równorzędną w kanale i konsorcjum.

1. Przejdź do klastra AKS organizacji równorzędnej i przekaż jego usługę członkowskiej usługi (MSP) w usłudze Azure File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Przejdź do klastra AKS organizacji zamawiającej i dodaj organizację równorzędną w kanale i konsorcjum.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Wróć do organizacji równorzędnej i wydaj polecenie, aby dołączyć do węzłów elementów równorzędnych w kanale.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Podobnie, aby dodać więcej organizacji równorzędnych w kanale, zaktualizuj zmienne środowiskowe równorzędnego AKS zgodnie z wymaganą organizacją równorzędną i wykonaj kroki od 1 do 3.

**Polecenia zarządzania kodami łańcuchowymi**

Wykonaj poniższe polecenie, aby wykonać operację związaną z kodem łańcucha. Polecenia te wykonują wszystkie operacje na demo chaincode. Ten kod łańcucha demo ma dwie zmienne "a" i "b". Podczas tworzenia wystąpienia kodu łańcucha "a" jest inicjowane z 1000 i "b" jest inicjowany z 2000. Przy każdym wywołaniu kodu łańcucha 10 jednostek jest przenoszonych z "a" do "b". Operacja kwerendy na chaincode pokazuje stan świata zmiennej "a".

Wykonaj następujące polecenia wykonywane w klastrze AKS organizacji równorzędnej.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Polecenia operacji chaincode**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Uruchamianie natywnych operacji HLF

Aby ułatwić klientom rozpoczęcie wykonywania natywnych poleceń Hyperledger w sieci HLF w uzywmieniu AKS. Przykładowa aplikacja jest pod warunkiem, że używa szkieletu NodeJS SDK do wykonywania operacji HLF. Polecenia są dostarczane do tworzenia nowej tożsamości użytkownika i zainstalować własny kod łańcucha.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Postępuj zgodnie z poniższymi poleceniami dla początkowej konfiguracji aplikacji:

- Pobieranie plików aplikacji
- Generowanie profilu połączenia i profilu administratora
- Importowanie tożsamości użytkownika administratora

Po zakończeniu wstępnej konfiguracji można użyć sdk, aby osiągnąć następujące operacje:

- Generowanie tożsamości użytkownika
- Operacje chaincode

Powyższe polecenia mogą być wykonywane z usługi Azure Cloud Shell.

### <a name="download-application-files"></a>Pobieranie plików aplikacji

Pierwszą konfiguracją uruchomionej aplikacji jest pobranie wszystkich plików aplikacji w folderze.

**Utwórz folder aplikacji i wprowadź go do folderu:**

```bash
mkdir app
cd app
```
Wykonaj poniższe polecenie, aby pobrać wszystkie wymagane pliki i pakiety:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
To polecenie wymaga czasu, aby załadować wszystkie pakiety. Po pomyślnym wykonaniu polecenia, `node_modules` można zobaczyć folder w bieżącym katalogu. Wszystkie wymagane pakiety są `node_modules` ładowane do folderu.

### <a name="generate-connection-profile-and-admin-profile"></a>Generowanie profilu połączenia i profilu administratora

Tworzenie `profile` katalogu wewnątrz `app` folderu

```bash
cd app
mkdir ./profile
```
Ustawianie tych zmiennych środowiskowych w powłoce chmury platformy Azure

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Wykonaj polecenie poniżej, aby wygenerować profil połączenia i profil administratora organizacji

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Utworzy profil połączenia `profile` i administratora organizacji w `<orgname>-ccp.json` folderze profilu z nazwą i `<orgname>-admin.json` odpowiednio.

Podobnie wygeneruj profil połączenia i profil administratora dla każdego zleceniowerera i organizacji równorzędnej.


### <a name="import-admin-user-identity"></a>Importowanie tożsamości użytkownika administratora

Ostatnim krokiem jest zaimportować tożsamość administratora organizacji w portfelu.

```bash
npm run importAdmin -- -o <orgName>

```
Powyższe polecenie wykonuje importAdmin.js, aby zaimportować tożsamość użytkownika administratora do portfela. Skrypt odczytuje tożsamość administratora z profilu `<orgname>-admin.json` administratora i importuje ją w portfelu do wykonywania operacji HLF.

Skrypty używają portfela systemu plików do przechowywania tożsamości. Tworzy portfel zgodnie ze ścieżką określoną w polu ".portfel" w profilu połączenia. Domyślnie pole ".wallet" jest `<orgname>`inicjowane za `<orgname>` pomocą programu , co oznacza, że folder o nazwie jest tworzony w bieżącym katalogu w celu przechowywania tożsamości. Jeśli chcesz utworzyć portfel na innej ścieżce, zmodyfikuj pole ".wallet" w profilu połączenia przed uruchomieniem zarejestrowanego użytkownika administratora i innych operacji HLF.

Podobnie importowanie tożsamości administratora dla każdej organizacji.

Aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu, zapoznaj się z pomocą polecenia.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generowanie tożsamości użytkownika

Wykonaj poniższe polecenia w danej kolejności, aby wygenerować nowe tożsamości użytkowników dla organizacji HLF.

> [!NOTE]
> Przed rozpoczęciem kroków generowania tożsamości użytkownika upewnij się, że początkowa konfiguracja aplikacji jest wykonywana.

Ustaw poniżej zmienne środowiskowe w połocie azure shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Zarejestruj i zarejestruj nowego użytkownika

Aby zarejestrować i zarejestrować nowego użytkownika, wykonaj poniższe polecenie, które wykonuje registerUser.js. Zapisuje wygenerowaną tożsamość użytkownika w portfelu.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Tożsamość użytkownika administratora jest używana do wydawania polecenia rejestracji dla nowego użytkownika. W związku z tym jest obowiązkowe, aby mieć tożsamość użytkownika administratora w portfelu przed wykonaniem tego polecenia. W przeciwnym razie to polecenie zakończy się niepowodzeniem.

Aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu, zapoznaj się z pomocą polecenia

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operacje chaincode


> [!NOTE]
> Przed rozpoczęciem operacji kodu łańcucha upewnij się, że początkowa konfiguracja aplikacji jest wykonywana.

Ustaw poniżej zmienne środowiskowe specyficzne dla kodu łańcucha w połocie Azure Cloud:

```bash
# peer organization name where chaincode is to be installed
ORGNAME=<orgName>
USER_IDENTITY="admin.$ORGNAME"
CC_NAME=<chaincodeName>
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'
# Default value is 'golang'
CC_LANG=<chaincodeLanguage>
# CC_PATH contains the path where your chaincode is place. In case of go chaincode, this path is relative to 'GOPATH'.
# For example, if your chaincode is present at path '/opt/gopath/src/chaincode/chaincode.go'.
# Then, set GOPATH to '/opt/gopath' and CC_PATH to 'chaincode'
CC_PATH=<chaincodePath>
# 'GOPATH' environment variable. This needs to be set in case of go chaincode only.
export GOPATH=<goPath>
# Channel on which chaincode is to be instantiated/invoked/queried
CHANNEL=<channelName>

````

Poniższe operacje chaincode mogą być przeprowadzane:

- Instalowanie kodu łańcucha
- Tworzenie wystąpienia kodu łańcucha
- Wywoływanie kodu łańcuchowego
- Kod łańcucha kwerendy

### <a name="install-chaincode"></a>Instalowanie kodu łańcucha

Wykonaj polecenie poniżej, aby zainstalować kod łańcucha w organizacji równorzędnej.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Zainstaluje kod łańcucha na wszystkich węzłach elementów `ORGNAME` równorzędnych organizacji ustawionych w zmiennej środowiskowej. Jeśli w kanale znajdują się dwie lub więcej organizacji równorzędnych i chcesz zainstalować kod łańcucha na wszystkich z nich, należy wykonać polecenia oddzielnie dla każdej organizacji równorzędnej.

Wykonaj następujące czynności:

- Ustaw `ORGNAME` `<peerOrg1Name>` polecenie `installCC` i wydaj polecenie.
- Ustaw `ORGNAME` `<peerOrg2Name>` polecenie `installCC` i wydaj polecenie.

  Wykonaj go dla każdej organizacji równorzędnej.

Zapoznaj się z pomocą polecenia, aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Tworzenie wystąpienia kodu łańcucha

Wykonaj polecenie poniżej, aby utworzyć wystąpienie kodu łańcucha w elementów równorzędnych.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Przekaż nazwę funkcji wystąpienia i przecinek oddzielone `<instantiateFunc>` `<instantiateFuncArgs>` listę argumentów w i odpowiednio. Na przykład w [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), aby utworzyć `<instantiateFunc>` `"Init"` wystąpienie kodu łańcucha ustawionego na i `<instantiateFuncArgs>` pusty ciąg `""`.

> [!NOTE]
> Wykonaj polecenie raz z jednej organizacji równorzędnej w kanale.
> Po pomyślnym przesłaniu transakcji do zamawiającego, zleceniowerownik rozdziela tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym chaincode jest tworzone na wszystkich węzłów elementów równorzędnych we wszystkich organizacjach równorzędnych w kanale.

Aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu, zapoznaj się z pomocą polecenia

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Wywoływanie kodu łańcuchowego

Wykonaj poniższe polecenie, aby wywołać funkcję chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Przekaż wywołać nazwę funkcji i przecinek `<invokeFunction>` oddzielone listę argumentów w i `<invokeFuncArgs>` odpowiednio. Kontynuując przykład kodu łańcucha fabcar, aby wywołać funkcję `<invokeFunction>` `"initLedger"` initLedger ustawioną na i `<invokeFuncArgs>` do `""`.

> [!NOTE]
> Wykonaj polecenie raz z jednej organizacji równorzędnej w kanale.
> Po pomyślnym przesłaniu transakcji do zamawiającego, zleceniowerownik rozdziela tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym stan świata jest aktualizowany na wszystkich węzłach elementów równorzędnych wszystkich organizacji równorzędnych w kanale.

Aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu, zapoznaj się z pomocą polecenia

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Kod łańcucha kwerendy

Wykonaj polecenie poniżej, aby wykonać kwerendę chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Nazwa funkcji kwerendy przekazu i przecinek oddzielone listę argumentów w `<queryFunction>` i `<queryFuncArgs>` odpowiednio. Ponownie, `fabcar` biorąc chaincode jako odniesienie, do zapytania wszystkich `<queryFunction>` `"queryAllCars"` samochodów `<queryArgs>` `""`w stanie świata ustawić i do .

Aby uzyskać więcej informacji na temat argumentów przekazanych w poleceniu, zapoznaj się z pomocą polecenia

```bash
npm run queryCC -- -h

```
