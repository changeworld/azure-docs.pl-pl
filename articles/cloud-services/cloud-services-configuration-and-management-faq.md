---
title: Konfigurowanie i zarządzanie problemy dotyczące Microsoft Azure Cloud Services często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących konfiguracji i zarządzania dla usługi w chmurze Microsoft Azure.
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
ms.date: 12/11/2017
ms.author: genli
ms.openlocfilehash: cc43d1a0e07eac78a47e6f183c2fd066a489f4f4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Konfigurowanie i zarządzanie problemy dotyczące usług Azure Cloud Services: często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące problemów dotyczących konfiguracji i zarządzania dla [usługi w chmurze Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Można również znaleźć [strony rozmiar maszyny Wirtualnej usługi w chmurze](cloud-services-sizes-specs.md) dla informacji o rozmiarze.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Certyfikaty**

- [Łańcuch certyfikatów certyfikatu SSL usługi w chmurze jest niekompletna](#why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete)
- [Co to jest celem "Systemu Windows Azure Tools szyfrowania certyfikatu dla rozszerzenia"?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Jak mogę wygenerować certyfikat żądania Podpisania bez "Używać protokołu RDP" w do wystąpienia?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Wygasa certyfikatu zarządzania usługi chmury. Jak odnowić go?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Jak zautomatyzować instalację główny certyfikat SSL (.pfx) i pośredniego certificate(.p7b)?](#how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b)

**Monitorowanie i rejestrowanie**

- [Jakie są nadchodzących możliwości usługi w chmurze w portalu Azure, które ułatwiają zarządzanie i monitorowanie aplikacji?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Dlaczego IIS zaprzestanie zapisywania katalogu dziennika?](#why-does-iis-stop-writing-to-the-log-directory)

**Konfiguracja sieci**

- [Jak ustawić limit czasu bezczynności dla usługi równoważenia obciążenia Azure?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Jak skojarzyć statycznego adresu IP do usługi w chmurze?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Co to są funkcje i możliwości dostępnych adresów IP/identyfikatorów Azure podstawowe i przed atakami DDOS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Jak włączyć HTTP/2 na maszynie Wirtualnej usługi w chmurze?](#how-to-enable-http2-on-cloud-services-vm)

**Uprawnienia**

- [Można pulpitu zdalnego firmy Microsoft inżynierowie wewnętrzny do wystąpień usługi w chmurze bez uprawnienia?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Nie mogę pulpitu zdalnego do maszyny Wirtualnej usługi chmury, za pomocą pliku RDP. Pobierz I następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalowanie**

- [Nie można skalować poza X wystąpień](#i-cannot-scale-beyond-x-instances)
- [Jak można skonfigurować oparty na pamięci metryki automatycznego skalowania](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Ogólny**

- [Jak dodać "nosniff" do witryny sieci Web?](#how-do-i-add-nosniff-to-my-website)
- [Jak dostosować usług IIS dla roli sieci web?](#how-do-i-customize-iis-for-a-web-role)
- [Co to jest limit przydziału dla usługi w chmurze?](#what-is-the-quota-limit-for-my-cloud-service)
- [Dlaczego dysk na maszynie Wirtualnej usługi w chmurze Wyświetla bardzo mało wolnego miejsca na dysku?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Jak dodać rozszerzenie ochrony przed złośliwym oprogramowaniem dla usługi w chmurze w zautomatyzowany sposób?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Jak włączyć oznaczenia nazwy serwera (SNI) dla usług w chmurze?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Jak dodać tagi do usługi w chmurze Azure](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Wersja zestawu SDK usługi w chmurze nie są wyświetlane w portalu Azure. Jak uzyskać który?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Chcę wyłączyć usługę w chmurze dla kilku miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adres IP?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Certyfikaty

### <a name="why-is-the-certificate-chain-of-my-cloud-service-ssl-certificate-incomplete"></a>Łańcuch certyfikatów certyfikatu SSL usługi w chmurze jest niekompletna
    
Firma Microsoft zaleca klientom zainstalowanie pełny łańcuch certyfikatów wysyłany (certyfikatu liścia, certyfikaty pośrednich i certyfikatu głównego) zamiast tylko certyfikatu liścia. Po zainstalowaniu właśnie certyfikatu liścia użytkownik korzysta z systemu Windows, można utworzyć łańcucha certyfikatu przez krótki listy CTL. Jeśli sieci sporadyczne problemy lub usługą DNS wystąpić w Azure lub usługi Windows Update, gdy system Windows podejmuje próbę weryfikacji certyfikatu, certyfikat może być uznawane za nieprawidłowe. Instalując łańcucha certyfikatów pełne, można uniknąć tego problemu. Blog na [jak zainstalować certyfikat SSL łańcuchowa](https://blogs.msdn.microsoft.com/azuredevsupport/2010/02/24/how-to-install-a-chained-ssl-certificate/) pokazano, jak to zrobić.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Co to jest celem "Systemu Windows Azure Tools szyfrowania certyfikatu dla rozszerzenia"?

Te certyfikaty są tworzone automatycznie, gdy rozszerzenie zostanie dodane do usługi w chmurze. Najczęściej jest to rozszerzenie WAD lub z rozszerzeniem RDP, ale może to być inne, takie jak rozszerzenie ochrony przed złośliwym oprogramowaniem lub moduł zbierający dzienniki. Te certyfikaty są używane tylko do szyfrowania i odszyfrowywania konfiguracji prywatnej rozszerzenia. Data wygaśnięcia nigdy nie jest zaznaczone, przez co nie ma znaczenia, jeśli certyfikat wygasł. 

Te certyfikaty, można zignorować. Jeśli chcesz wyczyścić certyfikatów, możesz spróbować usuwając je wszystkie. Azure zgłosi błąd przy próbie można usunąć certyfikatu, który jest używany.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Jak mogę wygenerować certyfikat żądania Podpisania bez "Używać protokołu RDP" w do wystąpienia?

Zobacz następujące wytyczne:

[Uzyskiwanie certyfikatu do użytku z systemem Windows Azure witryn sieci Web (WAWS)](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Renderowanie po stronie klienta jest tylko plik tekstowy. Nie ma ma zostać utworzony z maszyny, w którym będzie ostatecznie używany certyfikat. Chociaż ten dokument jest przeznaczony dla usług aplikacji, tworzenie CSR jest rodzajowy i ma zastosowanie również do usługi w chmurze.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Wygasa certyfikatu zarządzania usługi chmury. Jak odnowić go?

Aby odnowić certyfikaty zarządzania służy następujących poleceń programu PowerShell:

    Add-AzureAccount
    Select-AzureSubscription -Current -SubscriptionName <your subscription name>
    Get-AzurePublishSettingsFile

**Get-AzurePublishSettingsFile** utworzy nowy certyfikat zarządzania w **subskrypcji** > **certyfikaty zarządzania** w portalu Azure. Nazwa nowego świadectwa wygląda "YourSubscriptionNam]-[CurrentDate] - poświadczenia".

### <a name="how-to-automate-the-installation-of-main-ssl-certificatepfx-and-intermediate-certificatep7b"></a>Jak zautomatyzować instalację główny certyfikat SSL (.pfx) i pośredniego certificate(.p7b)?

Można zautomatyzować to zadanie za pomocą skryptu uruchomieniowego (partii cmd/PowerShell) i zarejestrować tego skryptu do uruchomienia w pliku definicji usługi. Dodaj zarówno uruchomienia skryptu, jak i certyfikat (plik p7b) w folderze projektu w tym samym katalogu skryptu uruchomieniowego.

Aby uzyskać więcej informacji zobacz następujące artykuły:
- [Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)
- [Typowe zadania uruchamiania usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks-common)

## <a name="monitoring-and-logging"></a>Monitorowanie i rejestrowanie

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Jakie są nadchodzących możliwości usługi w chmurze w portalu Azure, które ułatwiają zarządzanie i monitorowanie aplikacji?

Możliwość generuje nowy certyfikat dla protokołu RDP (Remote Desktop) będzie dostępna wkrótce. Alternatywnie możesz uruchomić ten skrypt:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Wybieranie obiektów blob lub lokalnej dla Twoich csdef i cscfg przekazać lokalizacji będzie dostępna wkrótce. Przy użyciu [AzureDeployment nowy](/powershell/module/azure/new-azuredeployment?view=azuresmps-4.0.0), można ustawić wartości w każdej lokalizacji.

Możliwość monitorowania metryki na poziomie wystąpienia. Dostępne są dodatkowe funkcje monitorowania [jak usługi w chmurze Monitor](cloud-services-how-to-monitor.md).

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Dlaczego IIS zaprzestanie zapisywania katalogu dziennika?
Wykorzystał zapisu do katalogu dziennika limit przydziału magazynu lokalnego. Aby rozwiązać ten problem, wykonaj jedno z trzech zdarzeń:
* Włącz diagnostykę dla usług IIS i diagnostyki okresowo przeniesione do magazynu obiektów blob.
* Ręcznie usuń pliki dziennika z katalogu rejestrowania.
* Zwiększ limit przydziału dla zasobów lokalnych.

Aby uzyskać więcej informacji można znaleźć w następujących dokumentach:
* [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
* [Dzienniki programu IIS zatrzymywanie, zapisywanie w usłudze w chmurze](https://blogs.msdn.microsoft.com/cie/2013/12/21/iis-logs-stops-writing-in-cloud-service/)

## <a name="network-configuration"></a>Konfiguracja sieci

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Jak ustawić limit czasu bezczynności dla usługi równoważenia obciążenia Azure?
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
Zobacz [nowy: można skonfigurować limit czasu bezczynności dla usługi równoważenia obciążenia Azure](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/) Aby uzyskać więcej informacji.

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Jak skojarzyć statycznego adresu IP do usługi w chmurze?
Aby skonfigurować statyczny adres IP, należy utworzyć zastrzeżonego adresu IP. Ten zastrzeżony adres IP może być skojarzony, do nowej usługi w chmurze lub do istniejącego wdrożenia. Zobacz szczegółowe informacje w następujących dokumentach:
* [Tworzenie zastrzeżonego adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md#manage-reserved-vips)
* [Rezerwacja adresu IP istniejącej usługi w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
* [Skojarzyć zastrzeżonego adresu IP do nowej usługi w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-new-cloud-service)
* [Skojarzyć zastrzeżonego adresu IP do uruchomionego wdrożenia](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-running-deployment)
* [Skojarzyć zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji usługi](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Co to są funkcje i możliwości dostępnych adresów IP/identyfikatorów Azure podstawowe i przed atakami DDOS?
Platforma Azure ma adresów IP/identyfikatorów w centrum danych serwerów fizycznych do ochrony przed zagrożeniami. Ponadto klienci mogą wdrożyć rozwiązania innych firm zabezpieczeń, takie jak zapory aplikacji sieci web, zapory sieciowe ochrony przed złośliwym kodem, wykrywania nieautoryzowanego dostępu, systemów zapobiegania (Identyfikatory/IP) i więcej. Aby uzyskać więcej informacji, zobacz [ochrony danych i zasobów i być zgodne ze standardami zabezpieczeń globalnych](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft stale monitoruje serwery, sieci i aplikacje w celu wykrycia zagrożeń. Używa wykrywania nieautoryzowanego dostępu platformy Azure multipronged zarządzanie zagrożeniami podejście, dystrybuowane typu "odmowa usługi" (DDoS) zapobieganie atakom, penetracji testowania, behawioralnej analytics wykrywania anomalii i stale zwiększenia poziomu jej obrony i zmniejszyć ryzyko uczenia maszynowego. Antimalware Microsoft Azure chroni usług Azure Cloud Services i maszyn wirtualnych. Istnieje możliwość wdrażania rozwiązań innych firm zabezpieczeń dodatkowo, takich jak okno zapory aplikacji sieci web, zapory sieciowe, ochrony przed złośliwym oprogramowaniem i nieautoryzowanego dostępu i zapobiegania systemów (Identyfikatory/adresów IP).

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Jak włączyć HTTP/2 na maszynie Wirtualnej usługi w chmurze?

Windows 10 i Windows Server 2016 pochodzić z obsługą protokołu HTTP/2 po stronie klienta i serwera. Jeśli nawiązuje połączenie z klienta (przeglądarki) na serwerze IIS za pośrednictwem protokołu TLS który negocjuje HTTP/2 za pośrednictwem rozszerzenia protokołu TLS, a następnie jest konieczne wprowadzenie zmian po stronie serwera. To dlatego za pośrednictwem protokołu TLS, domyślnie jest wysyłane nagłówka h2 14 określający użycie protokołu HTTP/2. Jeśli klient wysyła z drugiej strony uaktualnienia nagłówka do uaktualnienia do wersji HTTP/2, następnie należy zmienić poniżej po stronie serwera, aby upewnić się, że działa uaktualniania, a na końcu połączenie HTTP/2. 

1. Uruchom regedit.exe.
2. Przejdź do klucza rejestru: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Utwórz nową wartość DWORD o nazwie **DuoEnabled**.
4. Ustaw dla niego wartość 1.
5. Uruchom ponownie serwer.
6. Przejdź do Twojej **domyślna witryna sieci Web** i w obszarze **powiązania**, Utwórz nowe powiązanie, TLS z właśnie utworzony certyfikat z podpisem własnym. 

Aby uzyskać więcej informacji, zobacz:

- [HTTP/2 w usługach IIS](https://blogs.iis.net/davidso/http2)
- [Wideo: HTTP/2 w systemie Windows 10: przeglądarki, aplikacji i serwer sieci Web](https://channel9.msdn.com/Events/Build/2015/3-88)
         

Te kroki można można zautomatyzować za pomocą zadania uruchamiania, dzięki czemu zawsze, gdy nowe wystąpienie PaaS pobiera utworzony, można wykonać zmiany powyżej w rejestrze systemu. Aby uzyskać więcej informacji, zobacz [sposób konfigurowania i uruchamiania zadań uruchamiania dla usługi w chmurze](cloud-services-startup-tasks.md).

 
Po przeprowadzeniu, można sprawdzić, czy włączono HTTP/2 nie przy użyciu jednej z następujących metod:

- Włącz wersja protokołu w dziennikach usług IIS i poszukaj w dziennikach usług IIS. Wyświetli HTTP/2 w dziennikach. 
- Włącz narzędzia dla deweloperów F12 w Internet Explorer/Microsoft Edge i przejść do karty sieciowej można zweryfikować protokołu. 

Aby uzyskać więcej informacji, zobacz [HTTP/2 w usługach IIS](https://blogs.iis.net/davidso/http2).

## <a name="permissions"></a>Uprawnienia

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Jak wdrożyć dostępu opartej na rolach dla usługi w chmurze?
Usługi w chmurze nie obsługuje modelu kontroli dostępu opartej na rolach (RBAC), ponieważ nie jest usługa Azure Resource Manager na podstawie.

Zobacz [Azure RBAC, a administratorzy subskrypcji klasycznego](../role-based-access-control/overview.md#azure-rbac-vs-classic-subscription-administrators).

## <a name="remote-desktop"></a>Pulpit zdalny

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Można pulpitu zdalnego firmy Microsoft inżynierowie wewnętrzny do wystąpień usługi w chmurze bez uprawnienia?
Microsoft z jej właściciela lub ich osobę przez wyznaczoną wynika strict procesu, który nie zezwala na wewnętrzny inżynierów do usług pulpitu zdalnego do usługi w chmurze bez pisemnej zgody (adres e-mail lub innych pisemnej).

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Nie mogę pulpitu zdalnego do maszyny Wirtualnej usługi chmury, za pomocą pliku RDP. Pobierz I następujący błąd: Wystąpił błąd uwierzytelniania (kod: 0x80004005)

Ten błąd może wystąpić, jeżeli używany jest plik RDP na komputerze, który jest przyłączony do usługi Azure Active Directory. Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy pobrany plik RDP, a następnie wybierz **Edytuj**.
2. Dodaj "&#92;" jako prefiksu przed nazwy użytkownika. Na przykład użyć **. \username** zamiast **username**.

## <a name="scaling"></a>Skalowanie

### <a name="i-cannot-scale-beyond-x-instances"></a>Nie można skalować poza X wystąpień
Subskrypcji platformy Azure ma limit liczby rdzeni, których można użyć. Skalowanie nie będzie działać, jeśli zostały użyte wszystkie dostępne rdzenie. Na przykład jeśli istnieje limit liczby rdzeni (100), oznacza to, może mieć 100 wystąpień maszyna wirtualna A1 o rozmiarze dla usługi w chmurze lub wystąpieniu maszyny wirtualnej o rozmiarze 50 A2.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Jak można skonfigurować oparty na pamięci metryki automatycznego skalowania

Oparciu metryki pamięci dla usług w chmurze automatycznego skalowania nie jest obecnie obsługiwane. 

Aby obejść ten problem, można użyć usługi Application Insights. Automatyczne skalowanie obsługuje usługi Application Insights jako źródło metryki i można go skalować liczbę wystąpień roli w oparciu metryki gościa, takich jak "Pamięci".  Należy skonfigurować usługi Application Insights w pliku pakietu (*.cspkg) projektu usługi w chmurze i włączyć rozszerzenie Azure Diagnostics na usługę, aby zaimplementować to feat.

Aby uzyskać więcej informacji na temat sposobu korzystania niestandardowa Metryka za pomocą usługi Application Insights, aby skonfigurować automatyczne skalowanie na usługi w chmurze, zobacz [Rozpoczynanie pracy z Skalowanie automatyczne według metryki niestandardowe na platformie Azure](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)

Aby uzyskać więcej informacji na temat integracji Azure Diagnostics z usługi Application Insights dla usługi w chmurze, zobacz [wysyłania usługi w chmurze maszyny wirtualnej i sieci szkieletowej usług danych diagnostycznych do usługi Application Insights](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)

Aby uzyskać więcej informacji o włączenie usługi Application Insights dla usługi w chmurze, zobacz [usługi Application Insights dla usługi w chmurze Azure](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices)

Aby uzyskać więcej informacji o sposobie włączania rejestrowania diagnostyki Azure dla usługi w chmurze, zobacz [Ustaw diagnostyki dla usług Azure Cloud Services i maszyny wirtualne](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)

## <a name="generic"></a>Ogólny

### <a name="how-do-i-add-nosniff-to-my-website"></a>Jak dodać "nosniff" do witryny sieci Web?
Aby uniemożliwić klientom wykrywanie typów MIME, Dodaj ustawienie w Twojej *web.config* pliku.

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

Można również dodać to ustawienie w usługach IIS. Użyj następującego polecenia z [typowe zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artykułu.

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Jak dostosować usług IIS dla roli sieci web?
Użyj skryptu uruchamiania usług IIS z [typowe zadania uruchamiania](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe) artykułu.

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Co to jest limit przydziału dla usługi w chmurze?
Zobacz [ogranicza specyficzne dla usługi](../azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Dlaczego dysk na maszynie Wirtualnej usługi w chmurze Wyświetla bardzo mało wolnego miejsca na dysku?
Jest to oczekiwane zachowanie, a nie powinny powodować jakikolwiek problem do aplikacji. Rejestrowanie jest włączone dla dysku % approot % w maszynach wirtualnych PaaS platformy Azure, który zużywa zasadniczo double ilość miejsca na pliki zwykle zajmują. Istnieją jednak kilka kwestii, które należy pamiętać, że zasadniczo włączenie tej funkcji do innych niż problem.

Rozmiar dysku % approot % jest obliczany jako wartość < rozmiar cspkg + rozmiar maksymalny dziennika > + margines wolnego miejsca lub 1,5 GB w zależności od jest większy. Rozmiar maszyny Wirtualnej nie ma żadnych wpływu na tego obliczenia. (Rozmiar maszyny Wirtualnej tylko wpływa na rozmiar tymczasowej dysk C:). 

Go nie jest obsługiwana na zapis na dysku % approot %. Podczas pisania do maszyny Wirtualnej Azure, należy to zrobić w tymczasowej zasobów LocalStorage (lub innych opcji, takie jak magazyn obiektów Blob, plików Azure itp.). Dlatego ilość wolnego miejsca w folderze % approot % nie ma sensu. Jeśli nie masz pewności, czy aplikacja jest zapisywania na dysku % approot %, zawsze można pozostawić usługi Uruchom kilka dni, a następnie porównaj "przed" i "after" rozmiary. 

Azure nie zapisze żadnych na dysku % approot %. Wirtualny dysk twardy jest utworzone na podstawie Twojej cspkg i instalowany w Maszynie wirtualnej Azure, jedynym elementem, który może zapisać do tego dysku po aplikacji. 

Ustawienia dziennika są niemożliwą do skonfigurowania, więc nie można wyłączyć go.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Jak dodać rozszerzenie ochrony przed złośliwym oprogramowaniem dla usługi w chmurze w zautomatyzowany sposób?

Można włączyć rozszerzenia ochrony przed złośliwym kodem przy użyciu skryptu programu PowerShell w ramach zadania uruchamiania. Wykonaj kroki opisane w następujących artykułach do zaimplementowania go: 
 
- [Utwórz zadanie uruchamiania programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](https://docs.microsoft.com/powershell/module/Azure/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Aby uzyskać więcej informacji na temat scenariuszy wdrażania ochrony przed złośliwym oprogramowaniem i jak włączyć go w portalu, zobacz [scenariuszy wdrażania ochrony przed złośliwym kodem](../security/azure-security-antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Jak włączyć oznaczenia nazwy serwera (SNI) dla usług w chmurze?

Można włączyć SNI usług w chmurze przy użyciu jednej z następujących metod:

**Metoda 1: Przy użyciu programu PowerShell**

Powiązanie SNI można skonfigurować przy użyciu polecenia cmdlet programu PowerShell **WebBinding nowy** zadania uruchamiania dla wystąpienia roli usługi w chmurze, jak pokazano poniżej:
    
    New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags 
    
Zgodnie z opisem [tutaj](https://technet.microsoft.com/library/ee790567.aspx), $sslFlags może być jedna z wartości w następujący sposób:

|Wartość|Znaczenie|
------|------
|0|Nie SNI|
|1|Funkcja SNI włączone |
|2 |Z systemem innym niż SNI powiązania, który używa centralnego magazynu certyfikatów|
|3|Przechowywanie SNI powiązania, który używa certyfikatu z centralnej |
 
**Metoda 2: Użyj kodu**

Powiązanie SNI można również skonfigurować za pomocą kodu przy uruchamianiu roli zgodnie z opisem w tym [wpis w blogu](https://blogs.msdn.microsoft.com/jianwu/2014/12/17/expose-ssl-service-to-multi-domains-from-the-same-cloud-service/):

    
    //<code snip> 
                    var serverManager = new ServerManager(); 
                    var site = serverManager.Sites[0]; 
                    var binding = site.Bindings.Add(“:443:www.test1.com”, newCert.GetCertHash(), “My”); 
                    binding.SetAttributeValue(“sslFlags”, 1); //enables the SNI 
                    serverManager.CommitChanges(); 
    //</code snip> 
    
Przy użyciu jednej z metod powyżej, odpowiednich certyfikatów (*.pfx) dla określonej nazwy hostów muszą być najpierw zainstalowane dla wystąpień roli za pomocą zadania uruchamiania lub za pośrednictwem kodu w kolejności dla powiązania SNI były skuteczne.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Jak dodać tagi do usługi w chmurze Azure 

Usługi w chmurze jest zasobem klasycznego. Tylko zasoby utworzone za pomocą tagów pomocy technicznej usługi Azure Resource Manager. Nie można zastosować tagi Classic zasoby, takie jak usługi w chmurze. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Wersja zestawu SDK usługi w chmurze nie są wyświetlane w portalu Azure. Jak uzyskać który?

Pracujemy nad wprowadzeniem tej funkcji w portalu Azure. W tym samym czasie służy następujących poleceń programu PowerShell do pobrania wersji zestawu SDK:

    Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Chcę wyłączyć usługę w chmurze dla kilku miesięcy. Jak zmniejszyć koszt rozliczeń usługi w chmurze bez utraty adres IP?

Już raz wdrożonej usługi w chmurze pobiera rozliczane dla zasobów obliczeniowych i magazynu używa. Tak nawet w przypadku zamykania maszyny Wirtualnej platformy Azure można będzie nadal uzyskać użyta dla magazynu. 

Oto, co możesz zrobić, aby zmniejszyć rozliczeniowego bez utraty adres IP dla usługi:

1. [Zastrzec adresu IP](../virtual-network/virtual-networks-reserved-public-ip.md) przed usunięcie jego wdrożeń.  Tylko będą naliczane dla tego adresu IP. Aby uzyskać więcej informacji dotyczących rozliczeń adresu IP, zobacz [adresy IP cennik](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Usunięcie jego wdrożeń. Nie należy usuwać xxx.cloudapp.net, tak aby można go używać przyszłość.
3. Jeśli chcesz ponownie wdrożyć usługę w chmurze przy użyciu tego samego adresu IP rezerwy zarezerwowane w ramach subskrypcji, zobacz [zarezerwowane adresy IP dla usługi w chmurze i maszyn wirtualnych](https://azure.microsoft.com/blog/reserved-ip-addresses/).

