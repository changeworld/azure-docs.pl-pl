---
title: Konfiguracja i zarządzanie nim problemy dotyczące często zadawane pytania dotyczące systemu Microsoft Azure Cloud Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawane pytania dotyczące konfiguracji i zarządzania dla usług Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: 85296b4549d7c9499b8d0b815ddf1cd2e85e2b1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337429"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfiguracja i problemów z zarządzaniem usług Azure Cloud Services: Często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące konfiguracji i zarządzania problemy dotyczące [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Można także zapoznać się [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) Aby uzyskać informacje o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certyfikaty**

- [Łańcuch certyfikatów certyfikatu SSL usługi w chmurze jest niekompletna](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Co to jest celem "Windows Azure Tools szyfrowania dla rozszerzenia certyfikatów"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak mogę wygenerować żądania podpisania certyfikatu (CSR) bez "RDP-ing" w do wystąpienia?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Moja chmura usługi zarządzania certyfikat wygaśnie. Jak go odnowić?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak zautomatyzować instalację głównych certificate(.pfx) protokołu SSL i pośredniego certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)
- [Co to jest celem "Microsoft Azure Service Management dla" certyfikatu MachineKey?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Monitorowanie i rejestrowanie**

- [Jakie są możliwości usługi w chmurze nadchodzących w witrynie Azure portal, co może pomóc zarządzać i monitorować aplikacje?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Dlaczego IIS zatrzymanie zapisywania katalogu dziennika?](#why-does-iis-stop-writing-to-the-log-directory)
- [Jak włączyć rejestrowanie WAD dla usług w chmurze?](#how-do-i-enable-wad-logging-for-cloud-services)

**Konfiguracja sieci**

- [Jak ustawić limit czasu bezczynności dla modułu równoważenia obciążenia platformy Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak skojarzyć statycznego adresu IP do usługi w chmurze?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Co to są funkcje i możliwości udostępnianych przez Azure podstawowe adresy IP/Identyfikatory i przed atakami DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak włączyć protokołu HTTP/2 na maszynie Wirtualnej Services chmurze?](#how-to-enable-http2-on-cloud-services-vm)

**Uprawnienia**

- [Czy pulpit zdalny wewnętrznych inżynierów firmy Microsoft do wystąpień usługi w chmurze bez zgody?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Nie mogę Pulpit zdalny dla maszyny Wirtualnej usługi w chmurze przy użyciu pliku RDP. Czy mogę uzyskać następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalowanie**

- [Nie mogę skalować poza X wystąpień](#i-cannot-scale-beyond-x-instances)
- [Jak skonfigurować automatyczne skalowanie na podstawie metryk pamięci?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Ogólny**

- [Jak dodać "nosniff" do witryny sieci Web?](#how-do-i-add-nosniff-to-my-website)
- [Jak dostosować usług IIS dla roli sieci web?](#how-do-i-customize-iis-for-a-web-role)
- [Co to jest limit przydziału dla usługi w chmurze?](#what-is-the-quota-limit-for-my-cloud-service)
- [Dlaczego dysk na maszynie Wirtualnej usługi w chmurze pokazać bardzo mało wolnego miejsca na dysku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak mogę dodać rozszerzenia ochrony przed złośliwym kodem dla usług w chmurze w zautomatyzowany sposób?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak włączyć oznaczaniem nazwy serwera (SNI) dla usług w chmurze?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak dodać tagi do usługi w chmurze Azure](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Wersja zestawu SDK usługi w chmurze nie są wyświetlane w portalu Azure. Jak uzyskać który?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chcę zamknąć usługę w chmurze w ciągu kilku miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adres IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certyfikaty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Łańcuch certyfikatów certyfikatu SSL usługi w chmurze jest niekompletna
    
Firma Microsoft zaleca klientom zainstalowanie pełnego łańcucha certyfikatów (certyfikatu liścia, certyfikaty pośrednie i certyfikatu głównego) zamiast po prostu certyfikatu liścia. Po zainstalowaniu tylko certyfikatu liścia, możesz polegać na Windows do tworzenia łańcucha certyfikatów przez zalet listy CTL. Jeśli sieciowych okresowymi lub problemy z usługą DNS na platformie Azure lub Windows Update podczas Windows do weryfikacji certyfikatu, certyfikat może być uznawane za nieprawidłowe. Instalując łańcucha certyfikatów pełną, można uniknąć tego problemu. Blog znajduje się na [jak zainstalować łańcuchowy certyfikat SSL](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) pokazuje, jak to zrobić.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Co to jest celem "Windows Azure Tools szyfrowania dla rozszerzenia certyfikatów"?

Te certyfikaty są tworzone automatycznie, gdy rozszerzenie zostanie dodany do usługi w chmurze. Najczęściej jest to rozszerzenie WAD lub rozszerzenia RDP, ale może to być inne, takie jak rozszerzenia ochrony przed złośliwym oprogramowaniem lub moduł zbierający dzienniki. Te certyfikaty są używane tylko do szyfrowania i odszyfrowywania konfiguracji prywatnej rozszerzenia. Data wygaśnięcia nigdy nie jest zaznaczone, więc nie ma znaczenia, jeśli certyfikat wygasł. 

Można zignorować te certyfikaty. Jeśli chcesz wyczyścić certyfikatów, można próbować usunąć je wszystkie. Azure będzie sygnalizować błąd, jeśli próbujesz usunąć certyfikat, który jest używany.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak mogę wygenerować żądania podpisania certyfikatu (CSR) bez "RDP-ing" w do wystąpienia?

Zobacz następujące wytyczne:

[Uzyskiwanie certyfikatu do użycia z Windows Azure Web Sites (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Plik CSR jest po prostu plikiem tekstowym. Nie ma ma zostać utworzony na komputerze, w której ostatecznie będzie można użyć certyfikatu. Chociaż ten dokument jest przeznaczony dla usługi App Service, tworzenia żądania CSR jest ogólny i ma zastosowanie również w przypadku usług Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Moja chmura usługi zarządzania certyfikat wygaśnie. Jak go odnowić?

Aby odnowić certyfikaty zarządzania, można użyć następujących poleceń programu PowerShell:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get AzurePublishSettingsFile** spowoduje utworzenie nowego certyfikatu zarządzania w **subskrypcji** > **certyfikaty zarządzania** w witrynie Azure portal. Nazwa nowego certyfikatu wygląda "YourSubscriptionNam]-[CurrentDate] - poświadczenia".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak zautomatyzować instalację głównych certificate(.pfx) protokołu SSL i pośredniego certificate(.p7b)?

Można zautomatyzować to zadanie przy użyciu skryptu uruchamiania (/ cmd/programu PowerShell usługi batch) i zarejestrować tego skryptu uruchamiania w pliku definicji usługi. Dodaj skrypt uruchamiania i certyfikatu .p7b pliku w folderze projektu w tym samym katalogu skryptu uruchamiania.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Co to jest celem "Microsoft Azure Service Management dla" certyfikatu MachineKey?

Ten certyfikat jest używany do szyfrowania kluczy maszyn dla ról internetowych platformy Azure. Aby dowiedzieć się więcej, zapoznaj się z [w tym biuletynie](https://docs.microsoft.com/security-updates/securityadvisories/2018/4092731).

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Popularne zadania uruchamiania usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jakie są możliwości usługi w chmurze nadchodzących w witrynie Azure portal, co może pomóc zarządzać i monitorować aplikacje?

Możliwość generowania nowego certyfikatu dla protokołu RDP (Remote Desktop) będzie dostępna wkrótce. Alternatywnie możesz uruchomić ten skrypt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Możliwość wybrania lokalnych lub obiektu blob dla Twoich csdef i cscfg przekazywania lokalizacji będzie dostępna wkrótce. Za pomocą [New AzureDeployment](/powershell/module/servicemanagement/azure/new-azuredeployment?view=azuresmps-4.0.0), można ustawić wartość w każdej lokalizacji.

Możliwość monitorowania metryk na poziomie wystąpienia. Dodatkowe funkcje monitorowania są dostępne w [jak monitorowanie usług Cloud Services](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Dlaczego IIS zatrzymanie zapisywania katalogu dziennika?
Wyczerpano limit przydziału magazynu lokalnego do zapisywania katalogu dziennika. Aby rozwiązać ten problem, wykonaj jedną z trzech czynności:
* Włącz diagnostykę dla usług IIS i diagnostyki okresowo przeniesione do magazynu obiektów blob.
* Ręcznie usuń pliki dziennika z katalog rejestrowania.
* Zwiększ limit przydziału dla zasobów lokalnych.

Więcej informacji na ten temat można znaleźć w następujących dokumentach:
* [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [Dzienniki usług IIS zatrzymywanie, zapisywanie w usłudze w chmurze](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Jak włączyć rejestrowanie WAD dla usług w chmurze?
Można włączyć rejestrowanie diagnostyki Azure Windows (WAD) za pośrednictwem następujących opcji:
1. [Korzystanie z programu Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Włączyć za pomocą kodu platformy .NET](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics)
3. [Włącz za pomocą programu Powershell](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell)

Aby uzyskać bieżące ustawienia WAD usługi w chmurze, możesz skorzystać z [Get AzureServiceDiagnosticsExtensions](https://docs.microsoft.com/azure/cloud-services/cloud-services-diagnostics-powershell#get-current-diagnostics-extension-configuration) ps cmd lub można je wyświetlać za pośrednictwem portalu, w bloku "Usługi w chmurze--> rozszerzenia".


## <a name="network-configuration"></a>Konfiguracja sieci

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak ustawić limit czasu bezczynności dla modułu równoważenia obciążenia platformy Azure?
Limit czasu można określić w pliku definicji (csdef) usługi następująco:

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
Zobacz [nowe: Można skonfigurować limit czasu bezczynności dla modułu równoważenia obciążenia Azure](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) Aby uzyskać więcej informacji.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak skojarzyć statycznego adresu IP do usługi w chmurze?
Aby skonfigurować statyczny adres IP, należy utworzyć zastrzeżonego adresu IP. Ten zastrzeżony adres IP może być skojarzone z nową usługą w chmurze lub do istniejącego wdrożenia. Zobacz następujące dokumenty, aby uzyskać szczegółowe informacje:
* [Jak utworzyć zastrzeżonego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Zastrzec adres IP istniejącej usługi w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Skojarzenie zastrzeżonego adresu IP z nową usługą w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Skojarzenie zastrzeżonego adresu IP do uruchomionego wdrożenia](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Skojarzenie zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji usługi](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Co to są funkcje i możliwości udostępnianych przez Azure podstawowe adresy IP/Identyfikatory i przed atakami DDOS?
Platforma Azure ma adresy IP/Identyfikatory w centrum danych serwerów fizycznych do obrony przed zagrożeniami. Ponadto klienci mogą wdrażać rozwiązań zabezpieczeń innych firm, takich jak zapory aplikacji sieci web, zapory sieciowe, ochrony przed złośliwym oprogramowaniem, Wykrywanie nieautoryzowanego dostępu, zapobiegania systemów (IDS/IPS) i inne. Aby uzyskać więcej informacji, zobacz [Chroń swoje dane i zasoby i zgodność ze standardami zabezpieczeń globalnych](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Firma Microsoft stale monitoruje serwery, sieci i aplikacji w celu wykrywania zagrożeń. Wykrywanie nieautoryzowanego dostępu platformy Azure multipronged zarządzanie zagrożeniami podejście używa, rozproszonej typu "odmowa usługi" (DDoS) zapobieganie atakom, penetracji badań behawioralnych analizy, wykrywanie anomalii i uczenia maszynowego, aby stale wzmocnienie jego obrony i zmniejszenia ryzyka. Microsoft Antimalware dla platformy Azure chroni maszyny wirtualne i usługi Azure Cloud Services. Masz możliwość Ponadto wdrażanie rozwiązań zabezpieczeń innych firm, takich jak okno zapory aplikacji sieci web, zapory sieciowe, ochrony przed złośliwym oprogramowaniem i wykrywania i zapobiegania im oraz systemy włamań (IDS/IPS).

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak włączyć protokołu HTTP/2 na maszynie Wirtualnej Services chmurze?

Windows 10 i Windows Server 2016 pochodzą z obsługą protokołu HTTP/2 po stronie klienta i serwera. Jeśli Twój klient (przeglądarka) nawiązuje połączenie z serwerem usług IIS za pośrednictwem protokołu TLS, negocjuje protokołu HTTP/2 za pośrednictwem rozszerzenia protokołu TLS, a następnie nie trzeba wprowadzać zmian po stronie serwera. Jest to spowodowane za pośrednictwem protokołu TLS, nagłówka h2-14, określając korzystanie z protokołu HTTP/2 są domyślnie wysyłane. Jeśli z drugiej strony klienta wysyła uaktualnienia nagłówka, aby uaktualnić do protokołu HTTP/2, należy wprowadzić zmianę poniżej po stronie serwera, aby upewnić się, że uaktualnienie działa i znajdą się za pośrednictwem połączenia HTTP/2. 

1. Uruchom regedit.exe.
2. Przejdź do klucza rejestru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Utwórz nową wartość DWORD o nazwie **DuoEnabled**.
4. Ustaw jego wartość na 1.
5. Uruchom ponownie serwer.
6. Przejdź do swojej **domyślna witryna sieci Web** i w obszarze **powiązania**, Utwórz nowe powiązanie protokołu TLS z właśnie utworzony certyfikat z podpisem własnym. 

Aby uzyskać więcej informacji, zobacz:

- [Protokołu HTTP/2 w programie IIS](https://blogs.iis.net/davidso/http2)
- [Wideo: Protokołu HTTP/2 w systemie Windows 10: Przeglądarka, aplikacje i serwera sieci Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Te kroki można zautomatyzować za pomocą zadań uruchamiania, tak, aby zawsze wtedy, gdy tworzone jest nowe wystąpienie PaaS, można to zrobić zmiany powyżej w rejestrze systemu. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania i uruchamiania zadania uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

 
Po zostało to zrobione, można sprawdzić, czy włączono protokołu HTTP/2 nie przy użyciu jednej z następujących metod:

- Włącz wersja protokołu w dzienniku dzienniki usług IIS i możliwość przejrzenia dzienników usług IIS. Protokołu HTTP/2 będzie wyświetlany w dziennikach. 
- Włącz narzędzie programistyczne F12 w przeglądarce Internet Explorer/Microsoft Edge i przejdź do karty sieci, aby sprawdzić protokołu. 

Aby uzyskać więcej informacji, zobacz [protokołu HTTP/2 w programie IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Uprawnienia

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Sposób implementacji opartej na rolach dostęp do usług w chmurze
Usługi cloud Services nie obsługuje modelu kontroli dostępu opartej na rolach, ponieważ nie jest to usługa oparta na usłudze Azure Resource Manager.

Zobacz [zrozumienie różnych ról na platformie Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Pulpit zdalny

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Czy pulpit zdalny wewnętrznych inżynierów firmy Microsoft do wystąpień usługi w chmurze bez zgody?
Microsoft stosuje rygorystyczne procesu, który nie pozwoli na wewnętrznych inżynierów do usług pulpitu zdalnego w usłudze w chmurze bez pisemnej zgody (adres e-mail lub inne rodzaje komunikacji pisemnej) z jej właściciela lub ich osobę przez wyznaczoną.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Nie mogę Pulpit zdalny dla maszyny Wirtualnej usługi w chmurze przy użyciu pliku RDP. Czy mogę uzyskać następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)

Ten błąd może wystąpić, jeśli używasz pliku RDP na komputerze, który jest przyłączony do usługi Azure Active Directory. Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Kliknij prawym przyciskiem myszy pobrany plik RDP, a następnie wybierz pozycję **Edytuj**.
2. Dodaj "&#92;" jako prefiksu przed nazwą użytkownika. Na przykład użyć **. \username** zamiast **username**.

## <a name="scaling"></a>Skalowanie

### <a name="i-cannot-scale-beyond-x-instances"></a>Nie mogę skalować poza X wystąpień
Twojej subskrypcji platformy Azure ma limit liczby rdzeni, których można użyć. Skalowanie nie będzie działać, jeśli używano dostępnych rdzeni. Na przykład jeśli masz limitem rdzeni 100, oznacza to, może mieć 100 wystąpień maszyn wirtualnych A1 wielkości dla usługi w chmurze lub 50 A2 o rozmiarach wystąpień maszyny wirtualnej.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak skonfigurować automatyczne skalowanie na podstawie metryk pamięci?

Automatyczne skalowanie na podstawie pamięci metryk dla usług w chmurze nie jest obecnie obsługiwane. 

Aby obejść ten problem, można użyć usługi Application Insights. Automatycznego skalowania obsługuje usługi Application Insights jako źródło metryki i możesz skalować liczbę wystąpień roli w oparciu metryki gościa, takich jak "Pamięci".  Należy skonfigurować usługę Application Insights w pliku pakietu projektu usługi w chmurze (*.cspkg) i włączanie rozszerzenia diagnostyki Azure na usługę, aby zaimplementować to feat.

Aby uzyskać więcej informacji na temat Korzystanie z metryk niestandardowych za pomocą usługi Application Insights, aby skonfigurować automatyczne skalowanie w usługach Cloud Services, zobacz [wprowadzenie automatyczne skalowanie według metryki niestandardowe na platformie Azure](../azure-monitor/platform/autoscale-custom-metric.md)

Aby uzyskać więcej informacji na temat sposobu integrowania usługi Azure Diagnostics za pomocą usługi Application Insights dla usług Cloud Services, zobacz [wysyłania usługi w chmurze, maszyny wirtualnej lub usługi Service Fabric danych diagnostycznych do usługi Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)

Aby uzyskać więcej informacji na temat Włącz usługę Application Insights dla usług Cloud Services, zobacz [usługi Application Insights dla usług Azure Cloud Services](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Aby uzyskać więcej informacji o tym, jak włączyć rejestrowanie diagnostyki Azure dla usług Cloud Services, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Ogólny

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak dodać "nosniff" do witryny sieci Web?
Aby uniemożliwić klientom wykrywanie typów MIME, należy dodać ustawienie w swojej *web.config* pliku.

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

Można również dodać to ustawienie w usługach IIS. Użyj następującego polecenia z [popularne zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artykułu.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak dostosować usług IIS dla roli sieci web?
Użyj skryptu uruchamiania usług IIS z [popularne zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artykułu.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Co to jest limit przydziału dla usługi w chmurze?
Zobacz [ogranicza specyficzne dla usługi](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Dlaczego dysk na maszynie Wirtualnej usługi w chmurze pokazać bardzo mało wolnego miejsca na dysku?
Jest to oczekiwane zachowanie i nie powinna spowodować, że wszelkie problemy z aplikacją. Rejestrowanie jest włączone dla dysku % approot % na maszynach wirtualnych PaaS platformy Azure, która zasadniczo zużywa double ilość miejsca na pliki, ale zazwyczaj zajmują. Jednak istnieje kilka sposobów, aby wiedzieć, który zasadniczo przekształcając to bez problemu.

Rozmiar dysku % approot % jest obliczany jako < rozmiar cspkg + rozmiar maksymalny dziennika > + margines wolnego miejsca lub 1,5 GB pamięci, która kwota jest większa. Rozmiar maszyny Wirtualnej nie ma wpływu na to obliczenie. (Rozmiar maszyny Wirtualnej tylko wpływa na rozmiar dysku tymczasowego C:). 

Go nie jest obsługiwany do zapisu na dysku % approot %. Jeśli piszesz maszyną wirtualną platformy Azure, należy to zrobić w zasobie LocalStorage tymczasowego (lub innych opcji, takich jak usługi Blob storage, Azure Files, itp.). Zatem ilość wolnego miejsca w folderze % approot % nie ma sensu. Jeśli nie masz pewności, jeśli aplikacja zapisuje na dysku % approot %, zawsze można pozostawić usługi, uruchom przez kilka dni, a następnie porównaj "przed" i "after" rozmiary. 

Azure nie zapisze żadnych dysku % approot %. Wirtualny dysk twardy jest tworzone na podstawie Twojej cspkg i zainstalowany na maszynie Wirtualnej platformy Azure, jedynym elementem, który może zapisać do tego dysku po aplikacji. 

Ustawienia dziennika są niemożliwą do skonfigurowania, więc nie można wyłączyć go.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak mogę dodać rozszerzenia ochrony przed złośliwym kodem dla usług w chmurze w zautomatyzowany sposób?

Można włączyć rozszerzenia ochrony przed złośliwym oprogramowaniem, za pomocą skryptu programu PowerShell w zadaniu uruchamiania. Wykonaj kroki opisane w tych artykułach, do jej wdrożenia: 
 
- [Utwórz zadanie uruchamiania programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Aby uzyskać więcej informacji o scenariuszach wdrażania ochrony przed złośliwym oprogramowaniem i jak go włączyć z poziomu portalu, zobacz [scenariusze wdrażania ochrony przed złośliwym oprogramowaniem](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak włączyć oznaczaniem nazwy serwera (SNI) dla usług w chmurze?

Należy włączyć SNI w usługach w chmurze przy użyciu jednej z następujących metod:

**Metoda 1. Za pomocą programu PowerShell**

Powiązanie SNI można skonfigurować przy użyciu polecenia cmdlet programu PowerShell **New WebBinding** w zadaniu uruchamiania dla wystąpienia roli usługi w chmurze, tak jak pokazano poniżej:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Zgodnie z opisem [tutaj](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags może być jedną z wartości zgodnie z poniższymi:

|Wartość|Znaczenie|
------|------
|0|No SNI|
|1|Rozszerzenie SNI włączone |
|2 |SNI bez powiązania, który używa Store certyfikatu centralnego|
|3|Powiązanie SNI, która używa certyfikatu centralnego przechowywania |
 
**Metoda 2. Użyj kodu**

Powiązanie SNI można również skonfigurować za pomocą kodu w uruchamiania roli zgodnie z opisem w tym [wpis w blogu](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Przy użyciu dowolnej z metod powyżej, odpowiednich certyfikatów (*.pfx) dla określonej nazwy hostów trzeba najpierw zainstalować na wystąpieniach roli za pomocą zadania uruchamiania lub za pośrednictwem kodu, aby powiązanie SNI zaczęła obowiązywać.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak dodać tagi do usługi w chmurze Azure 

Usługa w chmurze jest zasobem model klasyczny. Tylko zasobów utworzonych za pomocą tagów pomocy technicznej usługi Azure Resource Manager. Tagi nie dotyczą klasyczne zasoby, takie jak usługi w chmurze. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Wersja zestawu SDK usługi w chmurze nie są wyświetlane w portalu Azure. Jak uzyskać który?

Pracujemy nad udostępnieniem tej funkcji w witrynie Azure portal. W tym samym czasie służy następujących poleceń programu PowerShell można pobrać wersji zestawu SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chcę zamknąć usługę w chmurze w ciągu kilku miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adres IP?

Zasobów obliczeniowych i magazynu używa jest naliczana dla już wdrożonej usługi w chmurze. Dlatego nawet wtedy, gdy maszyna wirtualna platformy Azure zostanie zamknięta, będzie naliczana do przechowywania. 

Oto, co można zrobić, aby zmniejszyć rozliczeniami bez utraty adres IP dla usługi:

1. [Zastrzec adres IP](../virtual-network/virtual-networks-reserved-public-ip.md) przed usunięciem wdrożeń.  Możesz tylko jest naliczana dla tego adresu IP. Aby uzyskać więcej informacji o rozliczeniach adresu IP, zobacz [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Usunięcie jego wdrożeń. Nie należy usuwać xxx.cloudapp.net, tak, aby używać go do przyszłości.
3. Jeśli chcesz ponownie wdrożyć usługę w chmurze przy użyciu tego samego adresu IP rezerwy zarezerwowanego w ramach subskrypcji, zobacz [zastrzeżone adresy IP dla maszyn wirtualnych i usług w chmurze](https://azure.microsoft.com/blog/reserved-ip-addresses/).

