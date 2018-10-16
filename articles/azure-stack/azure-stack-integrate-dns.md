---
title: Usługa Azure Stack Integracja z centrum danych — DNS
description: Dowiedz się, jak zintegrować usługę Azure Stack DNS z centrum danych DNS
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: bf412809f9d10296ad600e64abb6d870dbb88d3e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339682"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Usługa Azure Stack Integracja z centrum danych — DNS
Aby móc uzyskiwać dostęp punktów końcowych usługi Azure Stack (**portal**, **adminportal**, **zarządzania**, **adminmanagement**itp.)  z zewnętrznej usługi Azure Stack konieczna Integracja usługi Azure Stack DNS przy użyciu serwerów DNS, które hostuje stref DNS, którego chcesz użyć w usłudze Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Azure — przestrzeń nazw DNS stosu
Jest wymagane podanie pewnych informacji ważnych związane z usługą DNS podczas wdrażania usługi Azure Stack.


|Pole  |Opis  |Przykład|
|---------|---------|---------|
|Region|Geograficzna lokalizacja wdrożenia usługi Azure Stack.|`east`|
|Nazwa domeny zewnętrznej|Nazwa strefy, w której chcesz użyć dla wdrożenia usługi Azure Stack.|`cloud.fabrikam.com`|
|Nazwa domeny wewnętrznej|Nazwa wewnętrznego strefy, który jest używany dla usług infrastruktury w usłudze Azure Stack.  Jest zintegrowany z usługą katalogową i prywatne (nie jest dostępny z zewnątrz wdrożenia usługi Azure Stack).|`azurestack.local`|
|Usługa przesyłania dalej DNS|Serwery DNS, które są używane do przesyłania zapytań DNS, strefy i rekordy DNS, które znajdują się poza usługi Azure Stack w intranecie firmy lub publicznego Internetu.|`10.57.175.34`<br>`8.8.8.8`|
|Prefiks nazwy (opcjonalnie)|Prefiks nazw, które mają nazwy maszyny wystąpienia roli infrastruktury Azure Stack mieć.  Jeśli nie zostanie podana, wartość domyślna to `azs`.|`azs`|

W pełni kwalifikowana nazwa domeny (FQDN) punktów końcowych czy wdrożenia usługi Azure Stack jest kombinacją Region i parametru zewnętrznej nazwy domeny. Używając wartości z przykładów w poprzedniej tabeli, nazwy FQDN dla tego wdrożenia usługi Azure Stack będą następującą nazwę:

`east.cloud.fabrikam.com`

W efekcie przykłady niektórych punktów końcowych dla tego wdrożenia będzie wyglądać następujące adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Aby użyć tej przestrzeni nazw DNS na przykład podczas wdrażania usługi Azure Stack, wymagane są następujące warunki:

- Strefa `fabrikam.com` został zarejestrowany przy użyciu rejestratora domen, wewnętrznego serwera DNS firmy lub obu, w zależności od Twojego wymagania dotyczące rozpoznawania nazw.
- Domeny podrzędnej `cloud.fabrikam.com` istnieje w ramach strefy `fabrikam.com`.
- Serwery DNS, obsługujące strefy `fabrikam.com` i `cloud.fabrikam.com` jest osiągalna z wdrożenia usługi Azure Stack.

Aby można było do rozpoznawania nazw DNS dla punktów końcowych usługi Azure Stack i instancji z zewnętrznej usługi Azure Stack, konieczna integracja serwerów DNS, które hostują zewnętrznych strefy DNS dla usługi Azure Stack przy użyciu serwerów DNS obsługujących strefę nadrzędną, do którego chcesz użyć.


## <a name="resolution-and-delegation"></a>Rozpoznawanie i delegowanie

Istnieją dwa typy serwerów DNS:

- Autorytatywny serwer DNS hostuje strefy DNS. Odpowiada na zapytania DNS dotyczące rekordów tylko w tych strefach.
- Rekursywny serwer DNS nie hostuje stref DNS. Odpowiada na zapytania DNS, wywołując autorytatywne serwery DNS w celu zebrania danych, których potrzebuje.

Usługa Azure Stack obejmuje zarówno autorytatywne i cykliczne serwery DNS. Cykliczne serwery są używane do rozpoznawania nazw wszystkich elementów z wyjątkiem wewnętrznych prywatnej strefy i zewnętrznych publicznej strefie DNS dla tego wdrożenia usługi Azure Stack. 

![Architektura usługi Azure Stack DNS](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Rozpoznawanie nazw DNS zewnętrznych z usługi Azure Stack

Rozpoznawanie nazw DNS dla punktów końcowych spoza usługi Azure Stack (na przykład: www.bing.com), musisz podać serwerów DNS, które usługi Azure Stack można użyć do przekazywania żądań DNS, dla których nie jest autorytatywny usługi Azure Stack. W przypadku wdrożenia serwerów DNS, usługi Azure Stack przekazuje żądania, które są wymagane w arkusz wdrażania (w polu usługi przesyłania dalej DNS). Podaj co najmniej dwa serwery w tym polu odporności na uszkodzenia. Bez tych wartości wdrożenie usługi Azure Stack zakończy się niepowodzeniem.

### <a name="configure-conditional-dns-forwarding"></a>Konfigurowanie warunkowego przesyłania dalej DNS

> [!IMPORTANT]
> Dotyczy to tylko wdrożenie usług AD FS.

Aby włączyć rozpoznawanie nazw z istniejącą infrastrukturą DNS, należy skonfigurować warunkowego przesyłania dalej.

Aby dodać warunkowego przesyłania dalej, należy użyć uprzywilejowanych punktu końcowego.

Do wykonania tej procedury należy użyć komputera w sieci centrum danych, który może komunikować się z punktem końcowym uprzywilejowanych w usłudze Azure Stack.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator) i nawiązać połączenie z adresu IP uprzywilejowanych punktu końcowego. Użyj poświadczeń do uwierzytelniania CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po podłączeniu do endpoint uprzywilejowanego, uruchom następujące polecenie programu PowerShell. Zastąp przykładowe wartości, podane nazwę domeny oraz adresy IP serwerów DNS chcesz użyć.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Rozpoznawanie nazw System DNS Azure Stack z zewnętrznej usługi Azure Stack
Autorytatywne serwery są te, które pełnią zewnętrznych informacji o strefie DNS i wszystkich stref utworzonych przez użytkownika. Integracja z tych serwerów, aby umożliwić delegowanie strefy lub warunkowego przesyłania dalej do rozpoznawania nazw usługi system DNS Azure Stack z zewnętrznej usługi Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Uzyskaj informacje zewnętrzny punkt końcowy serwera DNS

Aby zintegrować wdrożenia usługi Azure Stack z infrastrukturą DNS, potrzebne są następujące informacje:

- Serwer DNS, nazwy FQDN
- Adresy IP serwera DNS

Nazwy FQDN dla serwerów usługi Azure Stack DNS mają następujący format:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Serwery przy użyciu przykładowych wartości nazw FQDN DNS to:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Te informacje również jest tworzony na końcu wszystkich wdrożeń usługi Azure Stack w pliku o nazwie `AzureStackStampDeploymentInfo.json`. Ten plik znajduje się w `C:\CloudDeployment\logs` folderu wdrożenia maszyny wirtualnej. Jeśli nie masz pewności, jakie wartości zostały użyte do wdrożenia usługi Azure Stack, w tym miejscu można pobrać wartości.

Jeśli wdrożenie maszyny wirtualnej nie jest już dostępny lub jest niedostępny, można uzyskać wartości nawiązywania połączenia z uprzywilejowanym punktu końcowego, a następnie uruchamiając `Get-AzureStackInfo` polecenia cmdlet programu PowerShell. Aby uzyskać więcej informacji, zobacz [uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Konfigurowanie warunkowego przesyłania dalej do usługi Azure Stack

Najprostszy i najbezpieczniejszy sposób integracji usługi Azure Stack z infrastruktury DNS jest przesyłanie warunkowe strefy z serwera, który obsługuje strefę nadrzędną. Takie podejście jest zalecane, jeśli masz bezpośrednią kontrolę nad serwerów DNS obsługujących strefę nadrzędną dla usługi Azure Stack zewnętrzny obszar nazw DNS.

Jeśli nie znasz dobrze jak to zrobić warunkowego przesyłania dalej z serwerem DNS, zobacz następujący artykuł w witrynie TechNet: [przypisać warunkowego przesyłania dalej dla nazwy domeny](https://technet.microsoft.com/library/cc794735), lub z dokumentacją specyficzną dla rozwiązania DNS.

W scenariuszach, gdzie określone zewnętrznych strefy DNS stosu Azure wyglądać domeny podrzędnej z nazwą domeny firmowej warunkowego przesyłania dalej nie można użyć. Należy skonfigurować delegowanie usługi DNS.

Przykład:

- Nazwa domeny firmowego serwera DNS: `contoso.com`
- Nazwa domeny zewnętrznego serwera DNS w usłudze Azure Stack: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegowanie strefy DNS zewnętrznego do usługi Azure Stack

Dla nazw DNS jako możliwej do rozpoznania z spoza wdrożenia usługi Azure Stack należy skonfigurować delegowanie DNS.

Każdy rejestrator ma swoje własne narzędzia do zarządzania systemem DNS służące do zmiany rekordów serwerów nazw dla domeny. W stronie zarządzania systemem DNS rejestratora Edytuj rekordy NS i Zastąp rekordy NS dla strefy w usłudze Azure Stack.

Większość rejestratorów DNS wymaga podania co najmniej dwa serwery DNS, aby ukończyć delegowanie.

## <a name="next-steps"></a>Kolejne kroki

[Integracja z zaporą](azure-stack-firewall.md)
