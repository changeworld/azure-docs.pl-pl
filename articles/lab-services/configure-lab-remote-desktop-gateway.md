---
title: Konfigurowanie laboratorium, aby użyć bramy usług pulpitu zdalnego w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować laboratorium Azure DevTest Labs przy użyciu bramy usług pulpitu zdalnego w celu zapewnienia bezpiecznego dostępu do laboratorium, maszyny wirtualne bez konieczności udostępniania portów protokołu RDP.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079004"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurowanie środowiska laboratoryjnego w usłudze Azure DevTest Labs, aby użyć bramy usług pulpitu zdalnego
W usłudze Azure DevTest Labs można skonfigurować bramy usług pulpitu zdalnego dla swojego laboratorium zapewnić bezpieczny dostęp do maszyn wirtualnych laboratorium (VM) bez konieczności udostępniania portów protokołu RDP. Laboratorium udostępnia centralne miejsce dla użytkowników do wyświetlania i nawiązać połączenie z wszystkich maszyn wirtualnych, które mają dostęp do laboratorium. **Connect** znajdujący się na **maszyny wirtualnej** strony tworzy plik RDP specyficzny dla komputera, który można otworzyć, aby połączyć się z komputerem. Dodatkowo można dostosować i bezpieczne połączenie RDP, łącząc swoje laboratorium w bramie usług pulpitu zdalnego. 

To podejście jest bardziej bezpieczne, ponieważ użytkownik laboratorium uwierzytelnia się bezpośrednio na maszynie bramy, lub można użyć poświadczeń firmowych na komputerze przyłączonym do domeny bramy, aby nawiązać połączenie ich maszyn. Laboratorium obsługuje również przy użyciu tokenu uwierzytelniania na maszynie bramy, która umożliwia użytkownikom nawiązywanie połączenia z ich maszyn wirtualnych laboratorium bez portu RDP, połączenie z Internetem. W tym artykule przedstawiono przykład, w jaki sposób skonfigurować konfigurację laboratorium, które używa uwierzytelniania tokenu, połączyć się z maszyn w laboratorium.

## <a name="architecture-of-the-solution"></a>Architektura rozwiązania

![Architektura rozwiązania](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. [Zawartość pliku RDP Pobierz](/rest/api/dtl/virtualmachines/getrdpfilecontents) akcji jest wywoływana, gdy wybierzesz **Connect** button.1. 
1. Akcja zawartość pliku RDP Pobierz wywołuje `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` do wysłania żądania tokenu uwierzytelniania.
    1. `{gateway-hostname}` jest nazwą hosta bramy, określone w **ustawienia Lab** strony dla laboratorium w witrynie Azure portal. 
    1. `{lab-machine-name}` jest nazwą komputera, do którego próbujesz się połączyć.
    1. `{port-number}` jest to port, na którym połączenie musi zapewnić. Port ten jest zazwyczaj 3389. Jeśli laboratorium korzysta z maszyny Wirtualnej [udostępnione IP](devtest-lab-shared-ip.md) funkcji w usłudze DevTest Labs, port będzie różna.
1. Brama usług pulpitu zdalnego odracza wywołanie funkcji z `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` funkcji platformy Azure w celu wygenerowania tokenu uwierzytelniania. Usługa DevTest Labs automatycznie zawiera klucz funkcji w nagłówku żądania. Klucz funkcji jest do zapisania w usłudze key vault w laboratorium. Nazwa tego wpisu tajnego mają być wyświetlane jako **klucz tajny tokenu bramy** na **ustawienia Lab** strony dla laboratorium.
1. Oczekiwano funkcji platformy Azure do zwracania tokenu dla tokenu uwierzytelniania certyfikatów na maszynie bramy.  
1. Plik RDP, Pobierz zawartość akcji, a następnie zwraca pełne pliku RDP, w tym informacje o uwierzytelnianiu.
1. Możesz otworzyć plik RDP, za pomocą preferowanego programu połączenie RDP. Należy pamiętać, że nie wszystkie programy połączenia RDP obsługuje uwierzytelnianie przy użyciu tokenów. Token uwierzytelniania mają datę wygaśnięcia, ustawiane przez aplikację funkcji. Nawiązywanie połączenia maszynę Wirtualną laboratorium, przed wygaśnięciem tokenu.
1. Gdy na maszynie bramy usług pulpitu zdalnego uwierzytelnia token w pliku RDP, połączenie jest przekazywany do komputera laboratorium.

### <a name="solution-requirements"></a>Wymagania rozwiązania
Aby pracować z funkcją uwierzytelniania tokenu usługi DevTest Labs, istnieje kilka wymagań konfiguracji dla maszyny bramy, usług nazw domen (DNS) i funkcji.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>Wymagania dotyczące maszyn bramy usług pulpitu zdalnego
- Certyfikat SSL należy zainstalować na maszynie bramy do obsługi ruchu HTTPS. Certyfikat musi odpowiadać w pełni kwalifikowana nazwa domeny (FQDN) modułu równoważenia obciążenia kolektywu serwerów bramy lub nazwę FQDN samą maszynę, jeśli istnieje tylko jedna maszyna. Certyfikaty SSL z symbolami wieloznacznymi nie działają.  
- Certyfikat podpisywania zainstalowanym maszyny bramy. Tworzenie certyfikatu podpisywania przy użyciu [SigningCertificate.ps1 Utwórz](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) skryptu.
- Zainstaluj [uwierzytelniania łączonego](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) moduł, który obsługuje uwierzytelnianie przy użyciu tokenów dla bramy usług pulpitu zdalnego. Przykładem takich modułów jest `RDGatewayFedAuth.msi` dostarczany z [obrazów programu System Center Virtual Machine Manager (VMM)](/system-center/vmm/install-console?view=sc-vmm-1807). Aby uzyskać więcej informacji na temat programu System Center, zobacz [dokumentacja programu System Center](https://docs.microsoft.com/system-center/) i [cennik](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Serwer bramy może obsługiwać żądań kierowanych do `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Nazwa hosta bramy jest nazwę FQDN modułu równoważenia obciążenia kolektywu serwerów bramy lub nazwę FQDN samą maszynę, jeśli istnieje tylko jedna maszyna. `{lab-machine-name}` Nazywa się komputer laboratorium, na którym próbujesz nawiązać połączenie, oraz `{port-number}` jest port, na którym będzie nawiązywane połączenie.  Domyślnie ten port jest 3389.  Jednak jeśli maszyna wirtualna używa [udostępnione IP](devtest-lab-shared-ip.md) funkcji w usłudze DevTest Labs, port będzie różna.
- [Routing żądań aplikacji w](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) modułu dla programu Internet Information Server (IIS) może służyć do przekierowania `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` żądania funkcji platformy azure, która obsługuje żądania, które można pobrać tokenu uwierzytelniania.


## <a name="requirements-for-azure-function"></a>Wymagania dotyczące funkcji platformy Azure
Żądanie obsługi funkcji platformy Azure z formatem `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` i zwraca token uwierzytelniania oparte na tym samym podpisywania certyfikatu zainstalowanego na komputerach bramy. `{function-app-uri}` Jest identyfikator uri umożliwiający dostęp do funkcji. Klucz funkcji jest automatycznie przekazane w nagłówku żądania. Dla funkcji przykładowe, zobacz [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Wymagania dotyczące sieci

- DNS dla nazwy FQDN skojarzone z certyfikatowi SSL zainstalowanemu na maszynach bramy musi kierować ruch do maszyny bramy lub moduł równoważenia obciążenia kolektywu serwerów maszyny bramy.
- Jeśli na maszynie laboratorium korzysta z prywatnych adresów IP, musi być ścieżkę sieciową z maszyny bramy maszyny laboratorium, przez udostępnianie tej samej sieci wirtualnej lub za pomocą wirtualnych sieciach równorzędnych.

## <a name="configure-the-lab-to-use-token-authentication"></a>Konfigurowanie laboratorium, aby użyć uwierzytelniania tokenu 
W tej sekcji przedstawiono sposób konfigurowania laboratorium na maszynie bramy usług pulpitu zdalnego, który obsługuje uwierzytelnianie przy użyciu tokenów. Ta sekcja nie obejmuje sposób konfigurowania kolektywu serwerów bramy usług pulpitu zdalnego, sam. Aby uzyskać te informacje, zobacz [przykładu można utworzyć bramy usług pulpitu zdalnego](#sample-to-create-a-remote-desktop-gateway) sekcji na końcu tego artykułu. 

Przed uaktualnieniem lab ustawienia należy przechowywać klucz wymaganej do wykonania pomyślnie funkcja zwraca token uwierzytelniania w usłudze key vault w laboratorium. Wartość klucza funkcji można uzyskać **Zarządzaj** strony dla tej funkcji w witrynie Azure portal. Aby uzyskać więcej informacji na temat sposobu zapisywania wpisu tajnego w magazynie kluczy, zobacz [dodać wpis tajny do usługi Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Zapisz nazwę wpisu tajnego do późniejszego użycia.

Aby znaleźć identyfikator magazynu kluczy w laboratorium, uruchom następujące polecenie z wiersza polecenia platformy Azure: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurowanie laboratorium, aby użyć uwierzytelniania tokenu za pomocą tych kroków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **wszystkich usług**, a następnie wybierz pozycję **DevTest Labs** z listy.
1. Wybierz z listy labs, Twoje **laboratorium**.
1. Na stronie laboratorium wybierz **konfiguracji i zasad**.
1. W menu po lewej stronie w **ustawienia** zaznacz **ustawienia Lab**.
1. W **pulpitu zdalnego** sekcji, wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP na maszynie bramy usług pulpitu zdalnego lub farmy dla **hostname bramy** pola. Ta wartość musi odpowiadać nazwa FQDN certyfikat SSL używany na maszynach bramy.

    ![Opcje dotyczące usług pulpitu zdalnego w ustawieniach laboratorium](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. W **pulpitu zdalnego** sekcji dla **tokenu bramy** klucz tajny, wprowadź nazwę klucza tajnego, utworzony wcześniej. Ta wartość nie jest sam klucz funkcji, ale nazwa wpisu tajnego w magazynie kluczy laboratorium, który zawiera klucz funkcji.

    ![Klucz tajny tokenu bramy w ustawieniach laboratorium](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Zapisz** zmiany.

    > [!NOTE] 
    > Klikając **Zapisz**, wyrażasz zgodę na [postanowienia licencyjne bramy usług pulpitu zdalnego firmy](https://www.microsoft.com/licensing/product-licensing/products). Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [usług pulpitu zdalnego — Zapraszamy](https://aka.ms/rds) i [wdrożyć środowisko pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


W przypadku konfigurowania laboratorium przez automatyzację preferowaną, zobacz [DevTestLabGateway.ps1 zestaw](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) uzyskać przykładowy skrypt programu PowerShell ustawić **hostname bramy** i **klucz tajny tokenu bramy**ustawienia. [Repozytorium Azure DevTest Labs w witrynie GitHub](https://github.com/Azure/azure-devtestlab) także szablonu usługi Azure Resource Manager, który tworzy lub aktualizuje laboratorium obejmującym **hostname bramy** i **klucz tajny tokenu bramy**ustawienia.

## <a name="configure-network-security-group"></a>Skonfiguruj grupy zabezpieczeń sieci
Aby dodatkowo zabezpieczyć laboratorium, można dodać sieciowej grupy zabezpieczeń (NSG) do sieci wirtualnej, używanych przez maszyny wirtualne laboratorium. Aby uzyskać instrukcje dotyczące sposobu konfigurowania sieciowej grupy zabezpieczeń, zobacz [tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci](../virtual-network/manage-network-security-group.md).

Oto przykład sieciowej grupy zabezpieczeń, która pozwala tylko na ruch, który najpierw przechodzi przez bramę do osiągnięcia maszyn w laboratorium. Źródło w tej regule jest adres IP komputera jednej bramy lub adres IP modułu równoważenia obciążenia przed maszyny bramy.

![Sieciowa grupa zabezpieczeń — zasady](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Przykładu można utworzyć bramy usług pulpitu zdalnego

> [!NOTE] 
> Korzystając z przykładowych szablonów, zgadzasz się [postanowienia licencyjne bramy usług pulpitu zdalnego firmy](https://www.microsoft.com/licensing/product-licensing/products). Aby uzyskać więcej informacji na temat bramy zdalnej, zobacz [usług pulpitu zdalnego — Zapraszamy](https://aka.ms/rds) i [wdrożyć środowisko pulpitu zdalnego](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

[Repozytorium Azure DevTest Labs w witrynie GitHub](https://github.com/Azure/azure-devtestlab) zawiera kilka przykładów ułatwiające Instalatora zasobów wymaganych do uwierzytelniania tokenu i bramy usług pulpitu zdalnego za pomocą usługi DevTest Labs. Te przykłady obejmują szablony usługi Azure Resource Manager dla maszyny bramy, ustawienia lab i aplikacji funkcji.

Wykonaj następujące kroki, aby skonfigurować przykładowe rozwiązanie kolektywu serwerów bramy usług pulpitu zdalnego.

1. Utwórz certyfikat podpisywania.  Uruchom [tworzenie SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Zapisz odcisku palca, hasło i kodowanie Base64 utworzonego certyfikatu.
2. Uzyskaj certyfikat protokołu SSL. Nazwa FQDN skojarzony z certyfikatem SSL musi być dla domeny, które możesz kontrolować. Zapisz odcisku palca, hasło i kodowanie Base64 dla tego certyfikatu. Aby uzyskać odcisk palca przy użyciu programu PowerShell, użyj następujących poleceń.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    Aby uzyskać kodowanie Base64 przy użyciu programu PowerShell, użyj następującego polecenia.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Pobieranie plików z [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    Ten szablon wymaga dostępu do kilku szablonów usługi Resource Manager i powiązanych zasobów, w tym samym podstawowy identyfikator URI. Skopiuj wszystkie pliki z [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) i RDGatewayFedAuth.msi na kontener obiektów blob na koncie magazynu.  
4. Deploy **azuredeploy.json** from [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). Szablon przyjmuje następujące parametry:
    - adminUsername — wymagane.  Nazwa użytkownika administratora dla maszyny bramy.
    - adminPassword — wymagane. Hasło dla konta administratora dla maszyny bramy.
    - instanceCount — liczba maszyn w celu utworzenia bramy.  
    - alwaysOn — wskazuje, czy zachować utworzoną aplikację usługi Azure Functions w stanie dostępu do ciepłych, czy nie. Utrzymywanie aplikacji usługi Azure Functions pozwoli uniknąć opóźnień, gdy użytkownicy najpierw spróbują połączyć się z ich maszynę Wirtualną laboratorium, ale niesie ze sobą skutki kosztów.  
    - tokenLifetime — czas, który będzie prawidłowy token utworzony. Format to: mm: ss.
    - sslCertificate — Base64 kodowanie certyfikatu SSL na maszynie bramy.
    - Parametr sslCertificatePassword — hasło certyfikatu SSL na maszynie bramy.
    - sslCertificateThumbprint — odcisk palca certyfikatu do identyfikacji w lokalnym magazynie certyfikatów certyfikatu SSL.
    - signCertificate — Base64 kodowanie podpisywania certyfikatu na maszynie bramy.
    - signCertificatePassword — hasło podpisywania certyfikatu na maszynie bramy.
    - signCertificateThumbprint — odcisk palca certyfikatu do identyfikacji w lokalnym magazynie certyfikatów podpisywania certyfikatu.
    - _artifactsLocation — identyfikator URI lokalizacji, gdzie można znaleźć wszystkie zasoby pomocnicze. Ta wartość musi być w pełni kwalifikowaną UIR, nie ścieżkę względną.
    - _artifactsLocationSasToken — token sygnatury dostępu współdzielonego (SAS) umożliwiają dostęp do zasobów pomocniczych, jeśli lokalizacja znajduje się konto magazynu platformy Azure.

    Szablon można wdrożyć za pomocą wiersza polecenia platformy Azure przy użyciu następującego polecenia:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Poniżej przedstawiono opisy parametrów:

    - {— Konta — punkt końcowy magazynu} można uzyskać, uruchamiając `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  {Storage-acct-name} jest nazwą konta magazynu, który zawiera pliki, które można przekazać.  
    - {Nazwa kontenera} jest nazwą kontenera w elemencie {storage-acct-name}, w którym przechowywane są pliki, które można przekazać.  
    - {Token sygnatury dostępu współdzielonego} można uzyskać, uruchamiając `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - {Storage-acct-name} jest nazwą konta magazynu, który zawiera pliki, które można przekazać.  
        - {Nazwa kontenera} jest nazwą kontenera w elemencie {storage-acct-name}, w którym przechowywane są pliki, które można przekazać.  
        - {Utc wygaśnięcia — data} to dzień, w formacie UTC, jaką wygaśnięcia tokenu sygnatury dostępu Współdzielonego i tokenu sygnatury dostępu Współdzielonego już nie można uzyskać dostępu do konta magazynu.

    Zapisz wartości gatewayFQDN i gatewayIP z danych wyjściowych z wdrożenia szablonu. Należy także Zapisz wartość klucza funkcji dla nowo utworzonej funkcji można znaleźć w [ustawień aplikacji funkcji](../azure-functions/functions-how-to-use-azure-function-app-settings.md) kartę.
5. Konfigurowanie systemu DNS, dzięki czemu tego certyfikatu nazwa FQDN protokołu SSL Określa, że adres IP gatewayIP z poprzedniego kroku.

    Po utworzeniu kolektywu serwerów bramy usług pulpitu zdalnego i odpowiednie aktualizacje rekordów DNS zostały wprowadzone, jest gotowy do użycia przez laboratorium w usłudze DevTest Labs. **Hostname bramy** i **klucz tajny tokenu bramy** ustawienia muszą zostać skonfigurowane do użycia z maszyny bramy została wdrożona. 

    > [!NOTE]
    > Jeśli na maszynie laboratorium korzysta z prywatnych adresów IP, musi być ścieżkę sieciową z maszyny bramy na komputerze laboratoryjnym, albo przy użyciu wirtualnej sieci równorzędnej lub udostępnianie tej samej sieci wirtualnej.

    Po skonfigurowaniu bramy i laboratorium plik połączenia tworzony po kliknięciu przez użytkownika laboratorium **Connect** automatycznie będą zawierać informacje wymagane do łączenia z przy użyciu tokenu uwierzytelniania.     

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący artykuł, aby dowiedzieć się więcej na temat usług pulpitu zdalnego: [Dokumentacja usługi usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


