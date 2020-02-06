---
title: Konsorcjum sieci szkieletowej w ramach usługi Azure Kubernetes Service (AKS)
description: Jak wdrożyć i skonfigurować sieć szkieletową z systemem webledger w usłudze Azure Kubernetes Service
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 5aed420295fd17cf4e7b26c86e8b84c4687e6545
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029915"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Konsorcjum sieci szkieletowej w ramach usługi Azure Kubernetes Service (AKS)

Można użyć szablonu sieci szkieletowej (HLF) w szablonie usługi Azure Kubernetes Service (AKS) w celu wdrożenia i skonfigurowania sieci szkieletowej dla konsorcjum Fabric na platformie Azure.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę na temat sieci szkieletowej i różne składniki tworzące bloki konstrukcyjne sieci szkieletowej łańcucha bloków.
- Dowiedz się, jak wdrożyć i skonfigurować konsorcjum sieci szkieletowej w usłudze Azure Kubernetes na potrzeby scenariuszy produkcyjnych.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcjum sieci szkieletowej

Aby utworzyć sieć szkieletową z systemem Azure, należy wdrożyć usługę porządkowania i organizację z węzłami równorzędnymi. Różne podstawowe składniki, które są tworzone w ramach wdrożenia szablonu są następujące:

- **Węzły programu orderer**: węzeł, który jest odpowiedzialny za porządkowanie transakcji w księdze. Wraz z innymi węzłami uporządkowane węzły tworzą usługę porządkowania sieci szkieletowej.

- **Węzły równorzędne**: węzeł, który głównie obsługuje księgi i inteligentne kontrakty, te podstawowe elementy sieci.

- **Urząd certyfikacji sieci szkieletowej**: Urząd certyfikacji sieci szkieletowej jest urzędem certyfikacji dla sieci szkieletowej. Urząd certyfikacji sieci szkieletowej umożliwia zainicjowanie i uruchomienie procesu serwera, który jest hostem urzędu certyfikacji. Umożliwia zarządzanie tożsamościami i certyfikatami. Każdy klaster AKS wdrożony jako część szablonu będzie miał domyślnie ten urząd certyfikacji sieci szkieletowej.

- **CouchDB lub LevelDB**: ogólnoświatowa baza danych stanu dla węzłów równorzędnych może być przechowywana w LevelDB lub CouchDB. LevelDB to domyślna baza danych stanu osadzona w węźle równorzędnym, która przechowuje dane chaincode jako proste pary klucz-wartość i obsługuje tylko klucze, zakres kluczy i kwerendy klucza złożonego. CouchDB to opcjonalna alternatywna baza danych stanu, która obsługuje zaawansowane zapytania, gdy wartości danych chaincode są modelowane jako kod JSON.

Szablon w ramach wdrożenia umożliwia rozmieszczenie różnych zasobów platformy Azure w ramach subskrypcji. Do różnych wdrożonych zasobów platformy Azure należą:

- **Klaster AKS**: klaster usługi Azure Kubernetes skonfigurowany zgodnie z parametrami wejściowymi dostarczonymi przez klienta. Klaster AKS ma różne zasobniki skonfigurowane do uruchamiania składników sieci szkieletowej. Tworzone są różne zasobniki:

  - **Narzędzia sieci szkieletowej**: Narzędzie sieci szkieletowej jest odpowiedzialne za Konfigurowanie składników sieci szkieletowej.
  - **Zamówienie/elementy równorzędne**: węzły sieci HLF.
  - **Serwer proxy**: NGNIX serwer proxy, za pomocą którego aplikacje klienckie mogą być interfejsem z klastrem AKS.
  - **Urząd certyfikacji sieci szkieletowej**: na tym komputerze, na którym działa urząd certyfikacji sieci szkieletowej.
- **PostgreSQL**: wystąpienie PostgreSQL zostało wdrożone w celu obsługi tożsamości urzędu certyfikacji sieci szkieletowej.

- **Magazyn kluczy Azure**: wystąpienie magazynu kluczy zostało wdrożone w celu zapisania poświadczeń urzędu certyfikacji sieci szkieletowej oraz certyfikatów głównych dostarczonych przez klienta, które są używane w przypadku ponowienia wdrożenia szablonu, aby obsłużyć Mechanics szablonu.
- **Dysk zarządzany przez platformę Azure**: dysk zarządzany na platformie Azure jest przeznaczony dla magazynu trwałego dla bazy danych stanu w świecie i węzła równorzędnego.
- **Publiczny adres IP**: publiczny punkt końcowy IP klastra AKS wdrożony do współdzielenia z klastrem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Konfiguracja sieci szkieletowej webłańcucha bloków Network

Aby rozpocząć, musisz mieć subskrypcję platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i kont magazynu w warstwie Standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Skonfiguruj sieć szkieletową webłańcucha bloków Network, wykonując następujące czynności:

- [Wdrażanie programu orderer/organizacja równorzędna](#deploy-the-ordererpeer-organization)
- [Kompiluj konsorcjum](#build-the-consortium)
- [Uruchamianie natywnych operacji HLF](#run-native-hlf-operations)

## <a name="deploy-the-ordererpeer-organization"></a>Wdrażanie programu orderer/organizacja równorzędna

Aby rozpocząć wdrażanie składników sieciowych HLF, przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **Utwórz zasób > łańcucha bloków** > Wyszukiwanie **w sieci szkieletowej w usłudze Azure Kubernetes**.

1. Wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie szablonu. Zostanie wyświetlona **usługa tworzenia sieci szkieletowej w usłudze Azure Kubernetes** .

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Wprowadź szczegóły projektu na stronie **podstawy** .

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Wprowadź następujące wartości:
    - **Subskrypcja**: wybierz nazwę subskrypcji, w której chcesz wdrożyć składniki sieci HLF.
    - **Grupa zasobów**: Utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów, a grupa zasobów będzie przechowywać wszystkie zasoby wdrożone w ramach szablonu.
    - **Region**: Wybierz region platformy Azure, w którym chcesz wdrożyć klaster usługi Azure Kubernetes dla składników HLF. Szablon jest dostępny we wszystkich regionach, w których dostępny jest AKS, upewnij się, że wybrano region, w którym subskrypcja nie powoduje limitu przydziału maszyny wirtualnej.
    - **Prefiks zasobu**: prefiks do nazewnictwa wdrożonych zasobów. Długość prefiksu zasobu musi być krótsza niż sześć znaków, a kombinacja znaków musi zawierać cyfry i litery.
4. Wybierz kartę **Ustawienia sieci szkieletowej** , aby zdefiniować składniki sieci HLF, które zostaną wdrożone.

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Wprowadź następujące wartości:
    - **Nazwa organizacji**: Nazwa organizacji sieci szkieletowej, która jest wymagana dla różnych operacji na płaszczyźnie danych.
    - **Składnik sieci szkieletowej**: wybierz pozycję porządkowanie usługi lub węzłów równorzędnych na podstawie składnika sieci łańcucha bloków, który chcesz skonfigurować.
    - **Liczba węzłów** — następujące dwa typy węzłów:
        - Porządkowanie usługi — wybierz liczbę węzłów, w przypadku których podano odporność na uszkodzenia sieci. Tylko 3, 5 i 7 są obsługiwaną liczbą węzłów programu orderer.
        - Węzły równorzędne — można wybrać 1-10 węzłów na podstawie wymagań.
    - **Baza danych stanu węzła równorzędnego**: Wybierz między LevelDB i CoucbDB. To pole jest wyświetlane, gdy użytkownik wybierze węzeł równorzędny w liście rozwijanej składnik sieci szkieletowej.
    - **Nazwa użytkownika sieci szkieletowej**: Wprowadź nazwę użytkownika używaną do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Hasło urzędu sieci szkieletowej**: wprowadź hasło do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Potwierdź hasło**: Potwierdź hasło urzędu sieci szkieletowej.
    - **Certyfikaty**: Jeśli chcesz użyć własnych certyfikatów głównych w celu zainicjowania urzędu certyfikacji sieci szkieletowej, wybierz opcję Przekaż certyfikat główny dla sieci szkieletowej, a w przeciwnym razie domyślnie urząd certyfikacji sieci szkieletowej tworzy certyfikaty z podpisem własnym.
    - **Certyfikat główny**: Przekaż certyfikat główny (klucz publiczny), z którym musi zostać zainicjowany urząd certyfikacji sieci szkieletowej. Certyfikaty formatu PEM są obsługiwane, certyfikaty powinny być prawidłowe w strefie czasowej UTC.
    - **Klucz prywatny certyfikatu głównego**: Przekaż klucz prywatny certyfikatu głównego. Jeśli masz certyfikat. pem, który ma połączony klucz publiczny i prywatny, przekaż go również w tym miejscu.


6. Wybierz kartę **Ustawienia klastra AKS** , aby zdefiniować konfigurację klastra usługi Azure Kubernetes, która jest podstawową infrastrukturą, w której zostaną skonfigurowane składniki sieci szkieletowej.

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Wprowadź następujące wartości:
    - **Nazwa klastra Kubernetes**: Nazwa utworzonego klastra AKS. To pole jest wstępnie wypełniane na podstawie podanego prefiksu zasobu, w razie potrzeby można je zmienić.
    - **Wersja Kubernetes**: wersja Kubernetes, która zostanie wdrożona w klastrze. W oparciu o region wybrany na karcie **podstawowe** dostępne wersje mogą ulec zmianie.
    - **Prefiks DNS**: prefiks nazwy systemu nazw domen (DNS) dla klastra AKS. Usługa DNS umożliwia łączenie się z interfejsem API Kubernetes podczas zarządzania kontenerami po utworzeniu klastra.
    - **Rozmiar węzła**: rozmiar węzła Kubernetes można wybrać z listy jednostek składowania maszyn wirtualnych dostępnych na platformie Azure. W celu uzyskania optymalnej wydajności zalecamy użycie standardowego DS3 v2.
    - **Liczba węzłów**: liczba węzłów Kubernetes, które mają zostać wdrożone w klastrze. Zaleca się pozostawienie tej liczby węzłów co najmniej równej liczbie węzłów HLF określonych w ustawieniach sieci szkieletowej.
    - **Identyfikator klienta nazwy głównej usługi**: Wprowadź identyfikator klienta istniejącej jednostki usługi lub Utwórz nową, która jest wymagana do uwierzytelniania AKS. Zobacz, kroki [tworzenia nazwy głównej usługi](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Klucz tajny klienta jednostki usługi**: Wprowadź klucz tajny klienta w jednostce usługi podanej w identyfikatorze klienta nazwy głównej usługi.
    - **Potwierdź klucz tajny klienta**: Potwierdź klucz tajny klienta podany w kluczu tajnym klienta jednostki usługi.
    - **Włącz monitorowanie kontenera**: Wybierz, aby włączyć monitorowanie AKS, które umożliwia wypychanie dzienników AKS do określonego obszaru roboczego log Analytics.
    - **Obszar roboczy log Analytics**: obszar roboczy usługi log Analytics zostanie wypełniony domyślnym obszarem roboczym, który jest tworzony w przypadku włączenia monitorowania.

8. Po wprowadzeniu wszystkich powyższych szczegółów wybierz pozycję **Przegląd i Utwórz** kartę. Przegląd i tworzenie wyzwalają weryfikację podanych wartości.
9. Po zakończeniu walidacji można wybrać pozycję **Utwórz**.
Wdrożenie zazwyczaj trwa 10-12 minut, może się różnić w zależności od rozmiaru i liczby określonych węzłów AKS.
10. Po pomyślnym wdrożeniu otrzymujesz powiadomienie za pomocą powiadomień platformy Azure w prawym górnym rogu.
11. Wybierz pozycję **Przejdź do grupy zasobów** , aby sprawdzić wszystkie zasoby utworzone w ramach wdrożenia szablonu. Wszystkie nazwy zasobów rozpoczną się od prefiksu podanego w ustawieniach **podstawy** .

## <a name="build-the-consortium"></a>Kompiluj konsorcjum

Aby skompilować łańcucha bloków konsorcjum do wdrożenia usługi porządkowania i węzłów równorzędnych, należy wykonać poniższe kroki w kolejności. **Utwórz skrypt sieciowy** (byn.sh), który pomaga w konfigurowaniu konsorcjum, tworzeniu kanału i instalowaniu chaincode.

> [!NOTE]
> Udostępniony skrypt sieci (byn) jest ściśle używany na potrzeby scenariuszy demonstracyjnych/DevTest. W celu skonfigurowania klasy produkcyjnej zalecamy użycie natywnych interfejsów API HLF.

Wszystkie polecenia służące do uruchamiania skryptu byn można wykonać za pomocą interfejsu wiersza polecenia (CLI) platformy Azure bash. Możesz zalogować się do wersji sieci Web usługi Azure Shell za pomocą ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Opcja w prawym górnym rogu Azure Portal. W wierszu polecenia wpisz bash i ENTER, aby przełączyć się do interfejsu CLI bash.

Aby uzyskać więcej informacji, zobacz temat [Azure Shell](https://docs.microsoft.com/azure/cloud-shell/overview) .

![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Pobierz plik byn.sh i Fabric-admin. YAML.

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/byn.sh -o byn.sh; chmod 777 byn.sh
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/consortiumScripts/fabric-admin.yaml -o fabric-admin.yaml
```
**Ustaw poniższe zmienne środowiskowe w powłoce bash interfejsu wiersza polecenia platformy Azure**:

Ustawianie informacji o kanale i informacji o organizacji w programie orderer

```bash
SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
ORDERER_AKS_SUBSCRIPTION=<ordererAKSClusterSubscriptionID>
ORDERER_AKS_RESOURCE_GROUP=<ordererAKSClusterResourceGroup>
ORDERER_AKS_NAME=<ordererAKSClusterName>
ORDERER_DNS_ZONE=$(az aks show --resource-group $ORDERER_AKS_RESOURCE_GROUP --name $ORDERER_AKS_NAME --subscription $ORDERER_AKS_SUBSCRIPTION -o json | jq .addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName | tr -d '"')
ORDERER_END_POINT="orderer1.$ORDERER_DNS_ZONE:443"
CHANNEL_NAME=<channelName>
```
Ustawianie informacji o organizacji równorzędnej

```bash
PEER_AKS_RESOURCE_GROUP=<peerAKSClusterResourceGroup>
PEER_AKS_NAME=<peerAKSClusterName>
PEER_AKS_SUBSCRIPTION=<peerAKSClusterSubscriptionID>
#Peer organization name is case-sensitive. Specify exactly the same name, which was provided while creating the Peer AKS Cluster.
PEER_ORG_NAME=<peerOrganizationName>
```

Utwórz jeden udział plików platformy Azure, aby współużytkować różne certyfikaty publiczne między organizacjami równorzędnymi i firmowymi.

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
**Polecenia zarządzania kanałem**

Przejdź do organizacji orderer AKS klaster i wydaj polecenie, aby utworzyć nowy kanał

```bash
SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
./byn.sh createChannel "$CHANNEL_NAME"
```

**Polecenia zarządzania konsorcjum**

Wykonaj poniższe polecenia w danej kolejności, aby dodać organizację równorzędną w kanale i konsorcjum.

1. Przejdź do węzła weborganization AKS Cluster i przekaż jego element członkowski (MSP) do usługi Azure File Storage.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh uploadOrgMSP "$AZURE_FILE_CONNECTION_STRING"
    ```

2. Przejdź do pozycji orderer Organization AKS klaster i Dodaj organizację równorzędną w kanale i konsorcjum.

    ```bash
    SWITCH_TO_AKS_CLUSTER $ORDERER_AKS_RESOURCE_GROUP $ORDERER_AKS_NAME $ORDERER_AKS_SUBSCRIPTION
    #add peer in consortium
    ./byn.sh addPeerInConsortium "$PEER_ORG_NAME" "$AZURE_FILE_CONNECTION_STRING"
    #add peer in channel
    ./byn.sh addPeerInChannel "$PEER_ORG_NAME" "$CHANNEL_NAME" "$AZURE_FILE_CONNECTION_STRING"
    ```

3. Wróć do obszaru organizacja równorzędna i wydaj polecenie, aby przyłączyć węzły równorzędne w kanale.

    ```bash
    SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
    ./byn.sh joinNodesInChannel "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
    ```

Podobnie aby dodać więcej organizacji równorzędnych w kanale, zaktualizuj zmienne środowiskowe AKS równorzędne zgodnie z wymaganą organizacją równorzędną i wykonaj kroki od 1 do 3.

**Polecenia zarządzania Chaincode**

Wykonaj poniższe polecenie, aby wykonać operację powiązaną z chaincode. Te polecenia wykonują wszystkie operacje na chaincode demonstracyjnym. Ten chaincode demonstracyjny ma dwie zmienne "a" i "b". Podczas tworzenia wystąpienia elementu chaincode, "a" jest inicjowany z 1000, a element "b" jest inicjowany z 2000. W każdym wywołaniu chaincode 10 jednostek są przesyłane z "a" do "b". Operacja zapytania na chaincode pokazuje stan globalny zmiennej "a".

Wykonaj następujące polecenia wykonywane w klastrze AKS organizacji równorzędnej.

```bash
# switch to peer organization AKS cluster. Skip this command if already connected to the required Peer AKS Cluster
SWITCH_TO_AKS_CLUSTER $PEER_AKS_RESOURCE_GROUP $PEER_AKS_NAME $PEER_AKS_SUBSCRIPTION
```
**Polecenia Chaincode operacji**

```bash
PEER_NODE_NAME="peer<peer#>"
./byn.sh installDemoChaincode "$PEER_NODE_NAME"
./byn.sh instantiateDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh invokeDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME" "$ORDERER_END_POINT" "$AZURE_FILE_CONNECTION_STRING"
./byn.sh queryDemoChaincode "$PEER_NODE_NAME" "$CHANNEL_NAME"
```

## <a name="run-native-hlf-operations"></a>Uruchamianie natywnych operacji HLF

Aby pomóc klientom w rozpoczęciu wykonywania poleceń natywnych dla księgi w sieci HLF na AKS. Dostępna jest przykładowa aplikacja korzystająca z zestawu Fabric NodeJS SDK do wykonywania operacji HLF. Polecenia są dostarczane w celu utworzenia nowej tożsamości użytkownika i zainstalowania własnych chaincode.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonaj poniższe polecenia, aby skonfigurować początkową aplikację:

- Pobierz pliki aplikacji
- Generuj profil połączenia i profil administratora
- Importuj tożsamość użytkownika administracyjnego

Po zakończeniu początkowej konfiguracji można użyć zestawu SDK, aby osiągnąć poniższe operacje:

- Generowanie tożsamości użytkownika
- Operacje Chaincode

Powyższe polecenia można wykonać z Azure Cloud Shell.

### <a name="download-application-files"></a>Pobierz pliki aplikacji

Pierwszym instalatorem uruchamiania aplikacji jest pobranie wszystkich plików aplikacji w folderze.

**Utwórz folder aplikacji i przejdź do folderu**:

```bash
mkdir app
cd app
```
Wykonaj poniższe polecenie, aby pobrać wszystkie wymagane pliki i pakiety:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/application/setup.sh | bash
```
To polecenie pobiera czas ładowania wszystkich pakietów. Po pomyślnym wykonaniu polecenia zobaczysz folder `node_modules` w bieżącym katalogu. Wszystkie wymagane pakiety są ładowane w folderze `node_modules`.

### <a name="generate-connection-profile-and-admin-profile"></a>Generuj profil połączenia i profil administratora

Utwórz katalog `profile` wewnątrz folderu `app`

```bash
cd app
mkdir ./profile
```
Ustaw te zmienne środowiskowe w usłudze Azure Cloud Shell

```bash
# Organization name whose connection profile is to be generated
ORGNAME=<orgname>
# Organization AKS cluster resource group
AKS_RESOURCE_GROUP=<resourceGroup>
```

Wykonaj poniższe polecenie, aby wygenerować profil połączenia i profil administratora organizacji

```bash
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/gateway/g"| xargs curl > ./profile/$ORGNAME-ccp.json
./getConnector.sh $AKS_RESOURCE_GROUP | sed -e "s/{action}/admin/g"| xargs curl > ./profile/$ORGNAME-admin.json
```

Spowoduje to utworzenie profilu połączenia i `profile` administratora organizacji w folderze profilu o nazwie `<orgname>-ccp.json` i `<orgname>-admin.json` odpowiednio.

Podobnie Wygeneruj profil połączenia i profil administratora dla każdej organizacji programu orderer i równorzędnej.


### <a name="import-admin-user-identity"></a>Importuj tożsamość użytkownika administracyjnego

Ostatnim krokiem jest zaimportowanie tożsamości użytkownika administratora w portfelu.

```bash
npm run importAdmin -- -o <orgName>

```
Powyższe polecenie wykonuje importAdmin. js, aby zaimportować tożsamość użytkownika administracyjnego do portfela. Skrypt odczytuje tożsamość administratora z profilu administratora `<orgname>-admin.json` i importuje ją w portfelu na potrzeby wykonywania operacji HLF.

Skrypty wykorzystują portfel systemu plików do przechowywania tożsamości. Tworzy portfel zgodnie z ścieżką określoną w polu "portfel" w profilu połączenia. Domyślnie pole "portfel" jest inicjowane z użyciem `<orgname>`, co oznacza, że folder o nazwie `<orgname>` jest tworzony w bieżącym katalogu do przechowywania tożsamości. Jeśli chcesz utworzyć portfel w innej ścieżce, zmodyfikuj pole "portfel" w profilu połączenia przed uruchomieniem funkcji Zarejestruj użytkownika administratora i innych operacji HLF.

Podobnie należy zaimportować tożsamość użytkownika administracyjnego dla każdej organizacji.

Skorzystaj z pomocy polecenia, aby uzyskać więcej informacji na temat argumentów przekazaną w poleceniu.

```bash
npm run importAdmin -- -h

```

### <a name="user-identity-generation"></a>Generowanie tożsamości użytkownika

Wykonaj poniższe polecenia w danej kolejności, aby wygenerować nowe tożsamości użytkowników dla organizacji HLF.

> [!NOTE]
> Przed rozpoczęciem procedury generowania tożsamości użytkowników upewnij się, że początkowa konfiguracja aplikacji jest gotowa.

Ustawianie poniżej zmiennych środowiskowych w usłudze Azure Cloud Shell

```bash
# Organization name for which user identity is to be generated
ORGNAME=<orgname>
# Name of new user identity. Identity will be registered with the Fabric-CA using this name.
USER_IDENTITY=<username>

```

Rejestrowanie i rejestrowanie nowego użytkownika

Aby zarejestrować i zarejestrować nowego użytkownika, wykonaj poniższe polecenie, które wykonuje kontrolka registeruser. js. Zapisuje wygenerowaną tożsamość użytkownika w portfelu.

```bash
npm run registerUser -- -o $ORGNAME -u $USER_IDENTITY

```

> [!NOTE]
> Tożsamość użytkownika administratora służy do wystawiania polecenia Register dla nowego użytkownika. W związku z tym przed wykonaniem tego polecenia wymagane jest posiadanie tożsamości administratora w portfelu. W przeciwnym razie to polecenie zakończy się niepowodzeniem.

Skorzystaj z pomocy polecenia, aby uzyskać więcej szczegółów na temat argumentów przekazaną w poleceniu

```bash
npm run registerUser -- -h

```

### <a name="chaincode-operations"></a>Operacje Chaincode


> [!NOTE]
> Przed rozpoczęciem pracy z dowolną operacją chaincode upewnij się, że jest wykonywana początkowa konfiguracja aplikacji.

Ustaw poniżej chaincode określone zmienne środowiskowe w usłudze Azure Cloud Shell:

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

Następujące operacje chaincode można wykonać:

- Zainstaluj chaincode
- Tworzenie wystąpienia chaincode
- Wywołaj chaincode
- Chaincode zapytania

### <a name="install-chaincode"></a>Zainstaluj chaincode

Wykonaj poniższe polecenie, aby zainstalować chaincode w organizacji równorzędnej.

```bash
npm run installCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION

```
Zostanie on zainstalowany na wszystkich węzłach równorzędnych zestawu organizacji w `ORGNAME` zmiennej środowiskowej. Jeśli w kanale znajdują się co najmniej dwie organizacje równorzędne i chcesz zainstalować chaincode na wszystkich z nich, wykonaj polecenia oddzielnie dla każdej organizacji równorzędnej.

Wykonaj następujące czynności:

- Ustaw `ORGNAME` na `<peerOrg1Name>` i wydaj `installCC` polecenie.
- Ustaw `ORGNAME` na `<peerOrg2Name>` i wydaj `installCC` polecenie.

  Wykonaj ją dla każdej organizacji równorzędnej.

Zapoznaj się z pomocą polecenia, aby uzyskać więcej szczegółów na temat argumentów przekazaną w poleceniu.

```bash
npm run installCC -- -h

```

### <a name="instantiate-chaincode"></a>Tworzenie wystąpienia chaincode

Wykonaj poniższe polecenie, aby utworzyć wystąpienie chaincode w elemencie równorzędnym.

```bash
npm run instantiateCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL -f <instantiateFunc> -a <instantiateFuncArgs>

```
Przekaż nazwę funkcji tworzenia wystąpień i listę oddzielonych przecinkami argumentów odpowiednio w `<instantiateFunc>` i `<instantiateFuncArgs>`. Na przykład w [fabrcar chaincode](https://github.com/hyperledger/fabric-samples/blob/release/chaincode/fabcar/fabcar.go), aby utworzyć wystąpienie chaincode ustawione `<instantiateFunc>`, aby `"Init"` i `<instantiateFuncArgs>` do pustego `""`ciągu.

> [!NOTE]
> Wykonaj polecenie dla raz z dowolnej organizacji równorzędnej w kanale.
> Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym chaincode jest tworzona na wszystkich węzłach równorzędnych na wszystkich organizacjach równorzędnych w kanale.

Skorzystaj z pomocy polecenia, aby uzyskać więcej szczegółów na temat argumentów przekazaną w poleceniu

```bash
npm run instantiateCC -- -h

```

### <a name="invoke-chaincode"></a>Wywołaj chaincode

Wykonaj poniższe polecenie, aby wywołać funkcję chaincode:

```bash
npm run invokeCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <invokeFunc> -a <invokeFuncArgs>

```
Przekaż nazwę funkcji Invoke i rozdzieloną przecinkami listę argumentów odpowiednio w `<invokeFunction>` i `<invokeFuncArgs>`. Kontynuując przykład fabcar chaincode, aby wywołać funkcję initLedger, `<invokeFunction>` do `"initLedger"` i `<invokeFuncArgs>` do `""`.

> [!NOTE]
> Wykonaj polecenie dla raz z dowolnej organizacji równorzędnej w kanale.
> Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym stan świata zostanie zaktualizowany we wszystkich węzłach równorzędnych w kanale.

Skorzystaj z pomocy polecenia, aby uzyskać więcej szczegółów na temat argumentów przekazaną w poleceniu

```bash
npm run invokeCC -- -h

```

### <a name="query-chaincode"></a>Chaincode zapytania

Wykonaj poniższe polecenie, aby wysłać zapytanie do chaincode:

```bash
npm run queryCC -- -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL -f <queryFunction> -a <queryFuncArgs>

```

Należy przekazać nazwę funkcji zapytania i listę oddzielonych przecinkami argumentów odpowiednio w `<queryFunction>` i `<queryFuncArgs>`. Ponownie, pobierając `fabcar` chaincode jako odwołanie, aby wysłać zapytanie do wszystkich samochodów w stanie światowym, `<queryFunction>` do `"queryAllCars"` i `<queryArgs>` do `""`.

Skorzystaj z pomocy polecenia, aby uzyskać więcej szczegółów na temat argumentów przekazaną w poleceniu

```bash
npm run queryCC -- -h

```
