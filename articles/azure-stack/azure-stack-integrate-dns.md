---
title: Integracja Azure datacenter stosu - DNS
description: Dowiedz się, jak zintegrować usługę Azure DNS stosu z centrum danych DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 5bdac2f3e6082f9449800fe2d4b303e2d59ade46
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733863"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integracja Azure datacenter stosu - DNS
Aby można było uzyskać dostępu do punktów końcowych stosu Azure (`portal`, `adminportal`, `management`, `adminmanagement`itp.)  ze stosu poza Azure musisz Integrowanie usług DNS stosu Azure z serwerów DNS, które hostuje stref DNS, który ma być używany w stosie Azure.

## <a name="azure-stack-dns-namespace"></a>Azure nazw DNS stosu
Jesteś wymagane jest podanie niektóre istotne informacje związane z usługą DNS podczas wdrażania usługi Azure stosu.


|Pole  |Opis  |Przykład|
|---------|---------|---------|
|Region|Lokalizację geograficzną wdrożenia stosu Azure.|`east`|
|Nazwy domen zewnętrznych|Nazwa strefy, który ma być używany dla danego wdrożenia stosu Azure.|`cloud.fabrikam.com`|
|Internal Domain Name|Nazwa strefy wewnętrzny używany dla infrastruktury usług Azure stosu.  Jest zintegrowana usługa katalogowa i prywatne (nieosiągalny z poza wdrożenia stosu Azure).|`azurestack.local`|
|DNS Forwarder|Serwery DNS, które są używane do przesyłania zapytań DNS, strefy DNS i rekordów, które znajdują się poza Azure stosu, w intranecie firmy lub publicznego Internetu.|`10.57.175.34`<br>`8.8.8.8`|
|Prefiks nazwy (opcjonalnie)|Nazewnictwa prefiks nazwy użytkownika stosu Azure infrastruktury roli wystąpienia w maszyny mają mieć.  Jeśli nie zostanie podana, wartość domyślna to `azs`.|`azs`|

W pełni kwalifikowaną nazwę (FQDN) wdrożenia stosu Azure i punkty końcowe jest kombinacją parametr regionu i parametr zewnętrzną nazwę domeny. Używając wartości z przykładów w poprzedniej tabeli, nazwy FQDN dla tego wdrożenia stosu Azure będzie następującej nazwy:

`east.cloud.fabrikam.com`

W efekcie przykłady niektórych punktów końcowych dla tego wdrożenia będzie wyglądać następujące adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Aby użyć tej przestrzeni nazw DNS na przykład wdrożenia stosu Azure, wymagane są następujące warunki:

- Strefa `fabrikam.com` został zarejestrowany z Rejestratora domen, wewnętrznego serwera DNS firmy lub obu w zależności od wymagań rozpoznawania nazw.
- Domena podrzędna `cloud.fabrikam.com` istnieje w ramach strefy `fabrikam.com`.
- Serwery DNS, które zawierają stref `fabrikam.com` i `cloud.fabrikam.com` można nawiązać połączenia z wdrożenia stosu Azure.

Aby można było do rozpoznawania nazw DNS dla punktów końcowych stosu Azure i wystąpień ze stosu Azure poza, konieczny do integracji serwerów DNS obsługujących zewnętrznych strefę DNS stosu Azure z serwerów DNS obsługujących strefę nadrzędną, który ma być używany.


## <a name="resolution-and-delegation"></a>Rozpoznawanie i delegowanie

Istnieją dwa typy serwerów DNS:

- Autorytatywny serwer DNS hostuje strefy DNS. Odpowiada na zapytania DNS dotyczące rekordów tylko w tych strefach.
- Rekursywny serwer DNS nie obsługuje stref DNS. Odpowiada na zapytania DNS, wywołując autorytatywne serwery DNS w celu zebrania danych, których potrzebuje.

Stos Azure obejmuje zarówno autorytatywne i serwery DNS cyklicznego. Serwery cykliczne są używane do rozpoznawania nazw wszystkich elementów z wyjątkiem wewnętrznej strefy prywatnych i zewnętrznych publicznego strefy DNS dla tego wdrożenia stosu Azure. 

![Architektura usługi Azure DNS stosu](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Rozpoznawanie nazw DNS zewnętrznych ze stosu Azure

Do rozpoznawania nazw DNS dla punktów końcowych poza stosu Azure (na przykład: www.bing.com), musisz podać serwerów DNS, które stosu Azure służy do przekazywania żądań DNS, dla których nie jest autorytatywny stosu Azure. W przypadku wdrożenia serwerów DNS, które stosu Azure przekazuje żądania są wymagane w arkuszu wdrożenia (w tym polu usługi przesyłania dalej DNS). Podaj co najmniej dwóch serwerów, w tym polu odporności na uszkodzenia. Bez tych wartości stos Azure wdrożenie zakończy się niepowodzeniem.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurowanie warunkowego przesyłania dalej DNS

> [!IMPORTANT]
> Dotyczy to tylko wdrożenia usług AD FS.

Aby włączyć rozpoznawanie nazw z istniejącą infrastrukturą DNS, należy skonfigurować przekierowanie warunkowe.

Aby dodać warunkowego przesyłania dalej, należy użyć uprzywilejowanych punktu końcowego.

Do wykonania tej procedury należy użyć komputera w sieci centrum danych, który może komunikować się z punktem końcowym uprzywilejowanych w stosie Azure.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator) i łączyć się z adresem IP uprzywilejowanych punktu końcowego. Użyj poświadczeń do uwierzytelniania CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po nawiązaniu połączenia z punktem końcowym uprzywilejowane, uruchom następujące polecenie programu PowerShell. Zastąp przykładowe wartości wyposażone nazwę domeny i adresy IP serwerów DNS, który ma być używany.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Rozpoznawanie nazw DNS stosu Azure ze stosu Azure poza
Autorytatywne serwery są te, które są przechowywane informacje dotyczące zewnętrznego strefy DNS i wszystkich stref utworzonych przez użytkownika. Integracja z tymi serwerami, aby umożliwić delegowanie strefy lub warunkowego przesyłania dalej do rozpoznawania nazw DNS stosu Azure z zewnętrznego stosu Azure.

## <a name="get-dns-server-external-endpoint-information"></a>Uzyskiwanie informacji o serwerze DNS zewnętrznego punktu końcowego

Aby zintegrować wdrożenia stosu Azure z infrastrukturą DNS, potrzebne są następujące informacje:

- Serwer DNS nazwy FQDN
- Adresy IP serwera DNS

Nazwy FQDN dla serwerów DNS stosu Azure mają następujący format:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Serwery przy użyciu przykładowe wartości nazwy FQDN dla serwera DNS są:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Te informacje jest tworzona na końcu wszystkie wdrożenia stosu Azure w pliku o nazwie `AzureStackStampDeploymentInfo.json`. Ten plik znajduje się w `C:\CloudDeployment\logs` folderu wdrożenia maszyny wirtualnej. Jeśli nie masz pewności, jakie wartości były używane do wdrożenia usługi Azure stosu, w tym miejscu można pobrać wartości.

Jeśli wdrożenie maszyny wirtualnej nie jest już dostępny, lub jest niedostępna, łącząc się z punktem końcowym uprzywilejowanych i uruchamiając mogą uzyskać wartości `Get-AzureStackInfo` polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji na temat uprzywilejowanych punktu końcowego zobacz (link do artykułu w tym miejscu Wstaw).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Konfigurowanie warunkowego przesyłania dalej do stosu Azure

Najprostsza i najbardziej bezpieczny sposób integracji stosu Azure z infrastrukturą DNS jest przesyłanie warunkowe strefy od serwera, który hostuje strefy nadrzędnej. Takie podejście jest zalecane, jeśli masz bezpośrednią kontrolę nad serwerów DNS obsługujących strefę nadrzędną dla przestrzeni nazw DNS z zewnętrznego stosu Azure.

Jeśli nie masz doświadczenia w obsłudze jak to zrobić warunkowego przesyłania dalej z serwerem DNS, zobacz następujący artykuł w witrynie TechNet: [przypisać warunkowego przesyłania dalej dla nazwy domeny](https://technet.microsoft.com/library/cc794735), lub z dokumentacją specyficzną dla rozwiązania DNS.

W scenariuszach, w którym określone zewnętrznych strefy DNS stosu Azure, aby wyglądały jak domeny podrzędnej z nazwą domeny firmy nie można użyć warunkowego przesyłania dalej. Należy skonfigurować delegowanie usługi DNS.

Przykład:

- Nazwa domeny firmowej usłudze DNS: `contoso.com`
- Nazwa domeny DNS zewnętrznego stosu Azure: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegowanie zewnętrznych strefę DNS do stosu Azure

Nazwy DNS jest rozpoznawana z poza wdrożenia stosu Azure należy skonfigurować delegowanie DNS.

Każdy rejestrator ma swoje własne narzędzia do zarządzania systemem DNS służące do zmiany rekordów serwerów nazw dla domeny. Na stronie zarządzania DNS rejestratora Edytuj rekordy NS i Zastąp rekordy NS dla strefy widocznych w stosie Azure.

Większość rejestratorów DNS wymaga podania co najmniej dwa serwery DNS, aby ukończyć delegowanie.

## <a name="next-steps"></a>Kolejne kroki

[Integracja z zaporą](azure-stack-firewall.md)
