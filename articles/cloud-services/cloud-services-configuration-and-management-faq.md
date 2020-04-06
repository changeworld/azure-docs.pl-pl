---
title: Często zadawane pytania dotyczące konfiguracji i zarządzania
titleSuffix: Azure Cloud Services
description: W tym artykule wymieniono często zadawane pytania dotyczące konfiguracji i zarządzania usługami w chmurze platformy Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 5821c72ae1be4759cf5aa76ff1f5af43337749c0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668578"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z konfiguracją i zarządzaniem usługami w chmurze Azure: często zadawane pytania (często zadawane pytania)

Ten artykuł zawiera często zadawane pytania dotyczące problemów z konfiguracją i zarządzaniem [usługami w chmurze platformy Microsoft Azure.](https://azure.microsoft.com/services/cloud-services) Informacje o rozmiarach można również zapoznać się ze [stroną Rozmiar maszyny Wirtualnej usług w chmurze.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certyfikaty**

- [Dlaczego łańcuch certyfikatów mojego certyfikatu TLS/SSL usługi w chmurze jest niekompletny?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Jaki jest cel certyfikatu szyfrowania narzędzi systemu Windows Azure dla rozszerzeń?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak wygenerować żądanie podpisania certyfikatu (CSR) bez "RDP-ing" w wystąpieniu?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mój certyfikat zarządzania usługą w chmurze wygasa. Jak go odnowić?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak zautomatyzować instalację głównego certyfikatu TLS/SSL(.pfx) i certyfikatu pośredniego(.p7b)?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Jaki jest cel certyfikatu "Microsoft Azure Service Management for MachineKey"?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorowanie i rejestrowanie**

- [Jakie są nadchodzące funkcje usługi w chmurze w witrynie Azure portal, które mogą pomóc w zarządzaniu i monitorowaniu aplikacji?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Dlaczego usługi IIS przestają zapisywać w katalogu dziennika?](#why-does-iis-stop-writing-to-the-log-directory)
- [Jak włączyć rejestrowanie WAD dla usług w chmurze?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfiguracja sieci**

- [Jak ustawić limit czasu bezczynny dla modułu równoważenia obciążenia platformy Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak skojarzyć statyczny adres IP z usługą w chmurze?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Jakie są funkcje i możliwości, które zapewnia podstawowe usługi IPS/IDS platformy Azure i DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak włączyć http/2 na maszynie wirtualnej usług w chmurze?](#how-to-enable-http2-on-cloud-services-vm)

**Uprawnienia**

- [Czy wewnętrzny pulpit firmy Microsoft może być zdalny od pulpitu do wystąpienia usługi w chmurze bez uprawnień?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Nie mogę zdalnie odde biurka do maszyny Wirtualnej usługi w chmurze przy użyciu pliku RDP. Pojawia się następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalowanie**

- [Nie mogę skalować poza wystąpienia X](#i-cannot-scale-beyond-x-instances)
- [Jak skonfigurować automatyczne skalowanie na podstawie metryk pamięci?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Ogólny**

- [Jak dodać "nosniff" do mojej witryny?](#how-do-i-add-nosniff-to-my-website)
- [Jak dostosować usługi IIS dla roli sieci Web?](#how-do-i-customize-iis-for-a-web-role)
- [Jaki jest limit przydziału dla mojej usługi w chmurze?](#what-is-the-quota-limit-for-my-cloud-service)
- [Dlaczego dysk na mojej maszynie wirtualnej usługi w chmurze ma bardzo mało wolnego miejsca na dysku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak mogę dodać rozszerzenie ochrony przed złośliwym oprogramowaniem dla moich usług w chmurze w sposób zautomatyzowany?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak włączyć wskazanie nazwy serwera (SNI) dla usług w chmurze?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak dodać tagi do usługi Azure Cloud Service?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Witryna Azure portal nie wyświetla wersji SDK mojej usługi w chmurze. Jak mogę to uzyskać?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chcę zamknąć usługę w chmurze na kilka miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adresu IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certyfikaty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Dlaczego łańcuch certyfikatów mojego certyfikatu TLS/SSL usługi w chmurze jest niekompletny?
    
Zaleca się, aby klienci instalowali pełny łańcuch certyfikatów (certyfikat liściowy, certyfikaty pośrednie i certyfikat główny) zamiast tylko certyfikatu liścia. Po zainstalowaniu tylko certyfikatu liścia, można polegać na systemie Windows do tworzenia łańcucha certyfikatów przez przejście CTL. Jeśli sporadyczne problemy z siecią lub usługą DNS występują na platformie Azure lub w witrynie Windows Update, gdy system Windows próbuje zweryfikować certyfikat, certyfikat może zostać uznany za nieprawidłowy. Instalując pełny łańcuch certyfikatów, można uniknąć tego problemu. Blog w [jak zainstalować łańcuchowy certyfikat SSL](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) pokazuje, jak to zrobić.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Jaki jest cel certyfikatu szyfrowania narzędzi systemu Windows Azure dla rozszerzeń?

Certyfikaty te są tworzone automatycznie za każdym razem, gdy rozszerzenie jest dodawane do usługi w chmurze. Najczęściej jest to rozszerzenie WAD lub rozszerzenie RDP, ale mogą to być inne, takie jak rozszerzenie Ochrony przeciw złośliwemu oprogramowania lub modułu zbierającego dzienniki. Certyfikaty te są używane tylko do szyfrowania i odszyfrowywania prywatnej konfiguracji rozszerzenia. Data wygaśnięcia nigdy nie jest sprawdzana, więc nie ma znaczenia, czy certyfikat wygasł. 

Można zignorować te certyfikaty. Jeśli chcesz wyczyścić certyfikaty, możesz spróbować usunąć je wszystkie. Platforma Azure zda błąd, jeśli spróbujesz usunąć używany certyfikat.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak wygenerować żądanie podpisania certyfikatu (CSR) bez "RDP-ing" w wystąpieniu?

Zapoznaj się z następującymi wskazówkami:

[Uzyskiwanie certyfikatu do użytku z witrynami sieci Web systemu Windows Azure (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

CSR to tylko plik tekstowy. Nie musi być tworzony z komputera, na którym ostatecznie zostanie użyty certyfikat.Mimo że ten dokument jest napisany dla usługi app service, tworzenie csr jest ogólny i dotyczy również usług w chmurze.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mój certyfikat zarządzania usługą w chmurze wygasa. Jak go odnowić?

Aby odnowić certyfikaty zarządzania, można użyć następujących poleceń programu PowerShell:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Plik Get-AzurePublishSettingsFile** utworzy nowy certyfikat zarządzania w**certyfikatach zarządzania** **subskrypcjami** > w portalu Azure. Nazwa nowego certyfikatu wygląda jak "YourSubscriptionNam]-[CurrentDate]-credentials".

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak zautomatyzować instalację głównego certyfikatu TLS/SSL(.pfx) i certyfikatu pośredniego(.p7b)?

To zadanie można zautomatyzować przy użyciu skryptu startowego (batch/cmd/PowerShell) i zarejestrować ten skrypt startowy w pliku definicji usługi. Dodaj skrypt startowy i certyfikat (plik p7b) w folderze projektu tego samego katalogu skryptu startowego.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Jaki jest cel certyfikatu "Microsoft Azure Service Management for MachineKey"?

Ten certyfikat służy do szyfrowania kluczy maszynowych w rolach sieci Web platformy Azure. Aby dowiedzieć się więcej, zapoznaj się z [tym poradnikiem](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Zadania uruchamiania usługi Common Cloud Service](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jakie są nadchodzące funkcje usługi w chmurze w witrynie Azure portal, które mogą pomóc w zarządzaniu i monitorowaniu aplikacji?

Możliwość wygenerowania nowego certyfikatu dla protokołu RDP (Remote Desktop Protocol) już wkrótce. Alternatywnie można uruchomić ten skrypt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Możliwość wyboru blob lub lokalne dla csdef i cscfg przesłać lokalizację już wkrótce. Za pomocą [New-AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), można ustawić każdą wartość lokalizacji.

Możliwość monitorowania metryk na poziomie wystąpienia. Dodatkowe możliwości monitorowania są dostępne w obszarze Jak monitorować usługi w [chmurze.](cloud-services-how-to-monitor.md)

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Dlaczego usługi IIS przestają zapisywać w katalogu dziennika?
Wyczerpano przydział magazynu lokalnego do zapisania w katalogu dziennika.Aby to poprawić, możesz wykonać jedną z trzech czynności:
* Włącz diagnostykę dla iIS i okresowo przenoszone diagnostyki do magazynu obiektów blob.
* Ręcznie usuń pliki dziennika z katalogu rejestrowania.
* Zwiększ limit przydziałów dla zasobów lokalnych.

Więcej informacji można znaleźć w następujących dokumentach:
* [Przechowywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](/azure/storage/common/storage-introduction)
* [Dzienniki usług IIS przestają pisać w usłudze w chmurze](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Jak włączyć rejestrowanie WAD dla usług w chmurze?
Rejestrowanie diagnostyki platformy Windows Azure (WAD) można włączyć za pomocą następujących opcji:
1. [Włącz z programu Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Włącz za pomocą kodu .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Włącz za pomocą programu PowerShell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Aby uzyskać bieżące ustawienia WAD usługi w chmurze, można użyć [Get-AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd lub można wyświetlić za pośrednictwem portalu z "Usługi w chmurze --> rozszerzenia" bloku.


## <a name="network-configuration"></a>Konfiguracja sieci

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak ustawić limit czasu bezczynny dla modułu równoważenia obciążenia platformy Azure?
Limit czasu w pliku definicji usługi (csdef) można określić w ten sposób:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Zobacz [nowy: Konfigurowalny limit czasu bezczynności dla modułu Równoważenia obciążenia platformy Azure,](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) aby uzyskać więcej informacji.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak skojarzyć statyczny adres IP z usługą w chmurze?
Aby skonfigurować statyczny adres IP, należy utworzyć zastrzeżony adres IP. Ten zarezerwowany adres IP może być skojarzony z nową usługą w chmurze lub z istniejącym wdrożeniem. Szczegółowe informacje można znaleźć w następujących dokumentach:
* [Jak utworzyć zastrzeżony adres IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Zarezerwuj adres IP istniejącej usługi w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Kojarzenie zastrzeżonego adresu IP z nową usługą w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Kojarzenie zastrzeżonego adresu IP z uruchomionym wdrożeniem](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Kojarzenie zastrzeżonego adresu IP z usługą w chmurze przy użyciu pliku konfiguracji usługi](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Jakie są funkcje i możliwości, które zapewnia podstawowe usługi IPS/IDS platformy Azure i DDOS?
Platforma Azure ma usługi IPS/IDS na serwerach fizycznych centrum danych w celu ochrony przed zagrożeniami. Ponadto klienci mogą wdrażać rozwiązania zabezpieczeń innych firm, takie jak zapory aplikacji sieci web, zapory sieciowe, ochrona przed złośliwym oprogramowaniem, wykrywanie włamań, systemy zapobiegania (IDS/IPS) i inne. Aby uzyskać więcej informacji, zobacz [Ochrona danych i zasobów oraz zgodność z globalnymi standardami bezpieczeństwa](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Firma Microsoft stale monitoruje serwery, sieci i aplikacje w celu wykrywania zagrożeń. Wielozadaniowe podejście platformy Azure do zarządzania zagrożeniami wykorzystuje wykrywanie włamań, zapobieganie atakom rozproszonych typu "odmowa usługi", testowanie penetracji, analizę behawioralną, wykrywanie anomalii i uczenie maszynowe, aby stale wzmacniać swoją obronę i zmniejszać ryzyko. Ochrona przed złośliwym oprogramowaniem firmy Microsoft dla platformy Azure chroni usługi w chmurze Azure i maszyny wirtualne. Ponadto można wdrożyć rozwiązania zabezpieczeń innych firm, takie jak ściany przeciwpożarowe aplikacji sieci web, zapory sieciowe, ochrona przed złośliwym oprogramowaniem, systemy wykrywania włamań i zapobiegania włamaniom (IDS/IPS) i inne.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak włączyć http/2 na maszynie wirtualnej usług w chmurze?

Systemy Windows 10 i Windows Server 2016 są wyposażone w obsługę protokołu HTTP/2 zarówno po stronie klienta, jak i serwera. Jeśli klient (przeglądarka) łączy się z serwerem usług IIS za pośrednictwem protokołu TLS negocjuje http/2 za pośrednictwem rozszerzeń TLS, nie trzeba wprowadzać żadnych zmian po stronie serwera. Dzieje się tak, ponieważ za pośrednictwem protokołu TLS nagłówek h2-14 określający użycie protokołu HTTP/2 jest wysyłany domyślnie. Jeśli z drugiej strony klient wysyła nagłówek uaktualnienia w celu uaktualnienia do protokołu HTTP/2, należy wprowadzić poniższą zmianę po stronie serwera, aby upewnić się, że uaktualnienie działa i skończysz z połączeniem HTTP/2. 

1. Uruchom plik regedit.exe.
2. Przejdź do klucza rejestru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Utwórz nową wartość DWORD o nazwie **DuoEnabled**.
4. Ustaw jego wartość na 1.
5. Uruchom ponownie serwer.
6. Przejdź do **domyślnej witryny sieci Web** i w obszarze **Powiązania**utwórz nowe powiązanie TLS z utworzonym właśnie certyfikatem z podpisem własnym. 

Aby uzyskać więcej informacji, zobacz:

- [HTTP/2 na usługi IIS](https://blogs.iis.net/davidso/http2)
- [Klip wideo: HTTP/2 w systemie Windows 10: przeglądarka, aplikacje i serwer www](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Te kroki mogą być zautomatyzowane za pomocą zadania uruchamiania, dzięki czemu za każdym razem, gdy zostanie utworzone nowe wystąpienie PaaS, może wykonać powyższe zmiany w rejestrze systemowym. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować i uruchomić zadania uruchamiania usługi w chmurze](cloud-services-startup-tasks.md).

 
Po wykonaniu tej czynności można sprawdzić, czy protokół HTTP/2 został włączony, czy nie, korzystając z jednej z następujących metod:

- Włącz wersję protokołu w dziennikach IIS i zajrzyj do dzienników IIS. Pokaże HTTP/2 w dziennikach. 
- Włącz narzędzie F12 Developer Tool w programie Internet Explorer lub Microsoft Edge i przełącz się na kartę Sieć, aby zweryfikować protokół. 

Aby uzyskać więcej informacji, zobacz [HTTP/2 w serwisach IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Uprawnienia

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak zaimplementować dostęp oparty na rolach dla usług w chmurze?
Usługi w chmurze nie obsługują modelu kontroli dostępu opartego na rolach (RBAC), ponieważ nie jest to usługa oparta na usłudze Azure Resource Manager.

Zobacz [Opis różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Pulpit zdalny

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Czy wewnętrzny pulpit firmy Microsoft może być zdalny od pulpitu do wystąpienia usługi w chmurze bez uprawnień?
Firma Microsoft stosuje rygorystyczny proces, który nie zezwala inżynierom wewnętrznym na zdalne korzystanie z pulpitu w usłudze w chmurze bez pisemnej zgody (wiadomości e-mail lub innej pisemnej komunikacji) od właściciela lub osoba, która została przez niego zaprojektowane.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Nie mogę zdalnie odde biurka do maszyny Wirtualnej usługi w chmurze przy użyciu pliku RDP. Pojawia się następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)

Ten błąd może wystąpić, jeśli używasz pliku RDP z komputera, który jest przyłączony do usługi Azure Active Directory. Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Kliknij prawym przyciskiem myszy pobrany plik RDP, a następnie wybierz polecenie **Edytuj**.
2. Dodaj "&#92;" jako prefiks przed nazwą użytkownika. Na przykład użyj **.\username** zamiast **nazwy użytkownika**.

## <a name="scaling"></a>Skalowanie

### <a name="i-cannot-scale-beyond-x-instances"></a>Nie mogę skalować poza wystąpienia X
Twoja subskrypcja platformy Azure ma limit liczby rdzeni, których można użyć. Skalowanie nie będzie działać, jeśli użyto wszystkich dostępnych rdzeni. Na przykład jeśli masz limit 100 rdzeni, oznacza to, że może mieć 100 wystąpień maszyny wirtualnej formatU A1 dla usługi w chmurze lub 50 wystąpień maszyny wirtualnej formatu A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak skonfigurować automatyczne skalowanie na podstawie metryk pamięci?

Automatyczne skalowanie na podstawie metryk pamięci dla usług w chmurze nie jest obecnie obsługiwane. 

Aby obejść ten problem, można użyć usługi Application Insights. Automatyczne skalowanie obsługuje usługę Application Insights jako źródło metryk i może skalować liczbę wystąpień roli na podstawie metryki gościa, takiej jak "Pamięć".  Musisz skonfigurować usługę Application Insights w pliku pakietu projektu usługi w chmurze (*.cspkg) i włączyć rozszerzenie diagnostyki platformy Azure w usłudze, aby zaimplementować ten wyczyn.

Aby uzyskać więcej informacji na temat korzystania z metryki niestandardowej za pośrednictwem usługi Application Insights w celu skonfigurowania automatycznego skalowania w usługach w chmurze, zobacz Wprowadzenie do [automatycznego skalowania według metryki niestandardowej](../azure-monitor/platform/autoscale-custom-metric.md) na platformie Azure

Aby uzyskać więcej informacji na temat integrowania diagnostyki platformy Azure z usługami Application Insights dla usług w chmurze, zobacz [Wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub sieci szkieletowej usług do aplikacji Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Aby uzyskać więcej informacji na temat włączania usługi Application Insights for Cloud Services, zobacz [Usługa Application Insights dla usług w chmurze Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Aby uzyskać więcej informacji na temat włączania rejestrowania diagnostyki platformy Azure dla usług w chmurze, zobacz [Konfigurowanie diagnostyki usług w chmurze Azure i maszyn wirtualnych](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Ogólny

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak dodać "nosniff" do mojej witryny?
Aby uniemożliwić klientom wąchanie typów MIME, dodaj ustawienie w pliku *web.config.*

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Można również dodać to jako ustawienie w iis. Użyj następującego polecenia z [typowymi zadaniami uruchamiania.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak dostosować usługi IIS dla roli sieci Web?
Użyj skryptu startowego usługi IIS z artykułu [typowe zadania uruchamiania.](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Jaki jest limit przydziału dla mojej usługi w chmurze?
Zobacz [Limity specyficzne dla usługi](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Dlaczego dysk na mojej maszynie wirtualnej usługi w chmurze ma bardzo mało wolnego miejsca na dysku?
Jest to oczekiwane zachowanie i nie powinno powodować żadnych problemów z aplikacją. Rejestrowanie jest włączone dla dysku %approot% na maszynach wirtualnych Usługi Azure PaaS, co zasadniczo zużywa dwukrotnie więcej miejsca, jaką zwykle zajmują pliki. Jednak istnieje kilka rzeczy, aby zdawać sobie sprawę, że zasadniczo przekształcić to w non-problem.

Rozmiar dysku %approot% jest \<obliczany jako rozmiar .cspkg + maksymalny rozmiar arkusza + margines wolnego miejsca> lub 1,5 GB, w zależności od tego, która wartość jest większa. Rozmiar maszyny Wirtualnej nie ma wpływu na to obliczenie. (Rozmiar maszyny Wirtualnej wpływa tylko na rozmiar tymczasowego dysku C:). 

Nie jest obsługiwane do zapisu na dysku %approot%. Jeśli piszesz do maszyny Wirtualnej platformy Azure, należy to zrobić w tymczasowym zasobu LocalStorage (lub innej opcji, takich jak magazyn obiektów blob, pliki platformy Azure itp.). Tak więc ilość wolnego miejsca w folderze %approot% nie ma znaczenia. Jeśli nie masz pewności, czy aplikacja zapisuje się na dysku %approot%, zawsze możesz pozwolić, aby usługa działała przez kilka dni, a następnie porównać rozmiary "przed" i "po". 

Platforma Azure nie będzie nic zapisywać na dysku %approot%. Po utworzeniu dysku VHD z pliku cspkg i zamontowaniu w maszynie Wirtualnej platformy Azure jedyną rzeczą, która może zapisywać się na tym dysku, jest aplikacja. 

Ustawienia dziennika nie można skonfigurować, więc nie można ich wyłączyć.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak mogę dodać rozszerzenie ochrony przed złośliwym oprogramowaniem dla moich usług w chmurze w sposób zautomatyzowany?

Rozszerzenie ochrony przed złośliwym oprogramowaniem można włączyć za pomocą skryptu programu PowerShell w zadaniu uruchamiania. Wykonaj kroki opisane w tych artykułach, aby go zaimplementować: 
 
- [Tworzenie zadania uruchamiania programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Aby uzyskać więcej informacji na temat scenariuszy wdrażania ochrony przed złośliwym oprogramowaniem i sposobu włączania go z portalu, zobacz [Scenariusze wdrażania ochrony przed złośliwym oprogramowaniem](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak włączyć wskazanie nazwy serwera (SNI) dla usług w chmurze?

Funkcję SNI można włączyć w usługach w chmurze przy użyciu jednej z następujących metod:

**Metoda 1: Użyj programu PowerShell**

Powiązanie SNI można skonfigurować przy użyciu polecenia cmdlet Programu PowerShell **New-WebBinding** w zadaniu uruchamiania dla wystąpienia roli usługi w chmurze, jak poniżej:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Jak opisano [w tym miejscu,](https://technet.microsoft.com/library/ee790567.aspx)$sslFlags może być jedną z następujących wartości:

|Wartość|Znaczenie|
------|------
|0|Nie SNI|
|1|Włączone SNI|
|2|Powiązanie niewolniczej na SNI, które korzysta z Centralnego Magazynu certyfikatów|
|3|Powiązanie SNI, które korzysta z magazynu certyfikatów centralnych|
 
**Metoda 2: Użyj kodu**

Powiązanie SNI można również skonfigurować za pomocą kodu w starcie roli, zgodnie z opisem w tym wpisie w [blogu:](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/)

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                    binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Przy użyciu dowolnego z powyższych metod odpowiednich certyfikatów (*.pfx) dla określonych nazwy hostów muszą być najpierw zainstalowane w wystąpieniach roli przy użyciu zadania uruchamiania lub za pomocą kodu, aby powiązanie SNI było skuteczne.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak dodać tagi do usługi Azure Cloud Service? 

Usługa w chmurze jest zasobem klasycznym. Tylko zasoby utworzone za pomocą tagów pomocy technicznej usługi Azure Resource Manager. Nie można zastosować tagów do zasobów klasycznych, takich jak usługa w chmurze. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Witryna Azure portal nie wyświetla wersji SDK mojej usługi w chmurze. Jak mogę to uzyskać?

Pracujemy nad wprowadzeniem tej funkcji w witrynie Azure portal. W międzyczasie można użyć następujących poleceń programu PowerShell, aby uzyskać wersję SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chcę zamknąć usługę w chmurze na kilka miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adresu IP?

Już wdrożona usługa w chmurze pobiera naliczone naliczane płatności za używane dane obliczeniowe i magazyn. Więc nawet po zamknięciu maszyny Wirtualnej platformy Azure, nadal będzie naliczane na faktury dla magazynu. 

Oto, co możesz zrobić, aby zmniejszyć rozliczenia bez utraty adresu IP usługi:

1. [Zarezerwuj adres IP](../virtual-network/virtual-networks-reserved-public-ip.md) przed usunięciem wdrożeń.  Na ten adres IP zostanie naliczona tylko faktura. Aby uzyskać więcej informacji na temat rozliczeń adresów IP, zobacz [ceny adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Usuń wdrożenia. Nie usuwaj xxx.cloudapp.net, aby można było jej używać w przyszłości.
3. Jeśli chcesz ponownie wdrożyć usługę w chmurze przy użyciu tego samego rezerwowego adresu IP, który został zarezerwowany w ramach subskrypcji, zobacz [Zarezerwowane adresy IP dla usług w chmurze i maszyn wirtualnych](https://azure.microsoft.com/blog/reserved-ip-addresses/).

