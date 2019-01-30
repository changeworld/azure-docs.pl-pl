---
title: Wymagania wstępne wdrożenia (ASDK) usługi Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Przejrzyj wymagania dotyczące środowiska i sprzętu dla usługi Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: f874be6081a1ea01ecf616c9b97db878554d441c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242420"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Zagadnienia dotyczące planowania wdrożenie usługi Azure Stack
Przed wdrożeniem usługi Azure Stack Development Kit (ASDK), upewnij się, że komputer hosta zestaw deweloperski spełnia wymagania opisane w tym artykule.


## <a name="hardware"></a>Sprzęt
| Składnik | Minimalne | Zalecane |
| --- | --- | --- |
| Stacje dysków: System operacyjny |1 dysk systemu operacyjnego z co najmniej 200 GB miejsca dostępnego dla partycji systemowej (SSD lub HDD) |1 dysk systemu operacyjnego z co najmniej 200 GB miejsca dostępnego dla partycji systemowej (SSD lub HDD) |
| Stacje dysków: Ogólne ustawienia projektowania zestawu danych<sup>*</sup>  |4 dyski. Każdy dysk udostępnia co najmniej 240 GB pojemności (SSD lub HDD). Używane są wszystkie dostępne dyski. |4 dyski. Każdy dysk udostępnia co najmniej 400 GB pojemności (SSD lub HDD). Używane są wszystkie dostępne dyski. |
| Obliczenia: Procesor CPU |Dual-Socket: 16 rdzeni fizycznych (łącznie) |Dual-Socket: 20 rdzeni fizycznych (łącznie) |
| Obliczenia: Memory (Pamięć) |192 GB PAMIĘCI RAM |256 GB PAMIĘCI RAM |
| Obliczenia: BIOS |Włączona funkcja Hyper-V (z obsługą usługi SLAT) |Włączona funkcja Hyper-V (z obsługą usługi SLAT) |
| Network: NIC |Windows Server 2012 R2 certyfikacji. Żadne specjalne funkcje wymagane |Windows Server 2012 R2 certyfikacji. Żadne specjalne funkcje wymagane |
| Certyfikacja logo sprzętu |[Certyfikowane dla systemu Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certyfikowane dla systemu Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Potrzebujesz więcej niż pojemność to zalecane, jeśli planowane jest dodanie wielu [elementów portalu marketplace](asdk-marketplace-item.md) z platformy Azure.

**Konfiguracja stacji dysków danych:** Wszystkie dyski danych muszą być tego samego typu (wszystkie SAS wszystkie SATA i NVMe wszystkie) i pojemności. Jeśli używane są dyski SAS, stacje dysków muszą być dołączone za pomocą pojedynczej ścieżki (nie jest zapewniana obsługa funkcji MPIO, obsługa wielościeżkowa).

**Opcje konfiguracji karty HBA**

* (Preferowane) Prosta karta HBA
* Karta HBA z technologią RAID — karta musi być skonfigurowana w trybie przekazywania
* Karta HBA z technologią RAID — dyski należy skonfigurować jako pojedynczy dysk, RAID-0

**Kombinacje typu nośnika i magistrali obsługiwane**

* HDD SATA
* HDD SAS
* HDD RAID
* RAID SSD (Jeśli typ nośnika jest nieokreślony/nieznany<sup>*</sup>)
* SSD SATA + HDD SATA
* SSD SAS + HDD SAS
* NVMe

<sup>*</sup> Kontrolery RAID bez możliwości przekazywania nie rozpoznają typu nośnika. Takie kontrolery oznaczyć HDD i SSD jako nieokreślone. W takim przypadku dysków SSD jest używany jako Magazyn trwały zamiast buforowania urządzeń. W związku z tym można wdrożyć zestaw development kit na tych dyskach SSD.

**Przykładowe karty HBA**: LSI 9207-8i, LSI-9300-8i lub LSI-9265-8i w trybie przekazywania

Dostępne są przykładowe konfiguracje OEM.

## <a name="operating-system"></a>System operacyjny
|  | **Wymagania** |
| --- | --- |
| **Wersja systemu operacyjnego** |System Windows Server 2016 lub nowszego. Wersja systemu operacyjnego nie jest krytyczne przed rozpoczęciem wdrażania, ponieważ komputer-host będzie rozruch wirtualnego dysku twardego, który znajduje się w instalacji programu Azure Stack. Obraz już zintegrowany system operacyjny i wszystkie wymagane poprawki. Nie używaj żadnych kluczy można aktywować żadnych wystąpień systemu Windows Server, używany w zestawie. |

> [!TIP]
> Po zainstalowaniu systemu operacyjnego, można użyć [narzędzia do sprawdzania wdrożenia usługi Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) aby upewnić się, że sprzęt spełnia wszystkie wymagania.

## <a name="account-requirements"></a>Wymagania dotyczące konta
Zazwyczaj można wdrażać deweloperski z łącznością internetową, gdzie możesz połączyć w systemie Microsoft Azure. W takim przypadku należy skonfigurować konto usługi Azure Active Directory (Azure AD), aby wdrożyć zestaw development kit.

Jeśli środowisko nie jest połączony z Internetem lub nie chcesz używać usługi Azure AD, można wdrożyć usługę Azure Stack przy użyciu usługi Active Directory Federation Services (AD FS). Zestaw development kit zawiera swój własny wystąpienia usług AD FS i Active Directory Domain Services. W przypadku wdrożenia przy użyciu tej opcji, nie masz konta, które wcześniej.

>[!NOTE]
W przypadku wdrożenia przy użyciu opcji usług AD FS, należy ponownie wdrożyć usługi Azure Stack, aby przełączyć się do usługi Azure AD.

### <a name="azure-active-directory-accounts"></a>Konta usługi Azure Active Directory
Aby wdrożyć usługę Azure Stack przy użyciu konta usługi Azure AD, należy przygotować konta usługi Azure AD, przed uruchomieniem wdrożenia skryptu programu PowerShell. To konto staje się administratorem globalnym dla dzierżawy usługi Azure AD. Jest on używany do aprowizacji i delegować uprawnienia do aplikacji i nazwy główne usług dla wszystkich usług Azure Stack, współpracujące z usługą Azure Active Directory i interfejsu API programu Graph. Służy również jako właściciel domyślną subskrypcję dostawcy (które można później zmienić). Możesz zalogować się do portalu administratora systemu usługi Azure Stack przy użyciu tego konta.

1. Utwórz konto usługi Azure AD, który jest administratorem katalogu dla co najmniej jednej usługi Azure AD. Jeśli masz już takie konto, możesz go użyć. W przeciwnym razie możesz je utworzyć za darmo na [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/pricing/free/) (w Chinach, odwiedź stronę <https://go.microsoft.com/fwlink/?LinkID=717821> zamiast). Jeśli zamierzasz później [rejestrowania usługi Azure Stack na platformie Azure](asdk-register.md), musi także mieć subskrypcję, w tym nowo utworzone konto.
   
    Zapisz te poświadczenia do użycia jako administratora usługi. To konto można skonfigurować i zarządzać nimi zasoby chmury, konta użytkowników, plany dzierżawy, limity przydziału i cennika. W portalu umożliwia ono tworzenie chmur witryn sieci Web, chmur prywatnych maszyn wirtualnych, tworzenie planów i zarządzanie subskrypcjami użytkowników.
1. Utwórz co najmniej jedno testowe konto użytkownika w usłudze Azure AD, dzięki czemu możesz zalogować się w zestawie jako dzierżawca.
   
   | **Konta usługi Azure Active Directory** | **Obsługiwane?** |
   | --- | --- |
   | Konto służbowe z ważną subskrypcją publicznej platformy Azure |Yes |
   | Konto Microsoft z ważną subskrypcją publicznej platformy Azure |Yes |
   | Konto służbowe z ważną subskrypcją platformy Azure (Chiny) |Yes |
   | Konto służbowe z prawidłową US Government subskrypcją platformy Azure |Yes |

Po wdrożeniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo ponownie przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.

## <a name="network"></a>Sieć
### <a name="switch"></a>Przełącznik
Jeden dostępny port na przełączniku dla maszyny development kit.  

Na komputerze deweloperskim w zestaw obsługuje łączenie się z portem dostępu przełącznika lub portem magistrali. Nie są wymagane żadne specjalne funkcje na przełączniku. Jeśli jest używany port magistrali lub konieczne jest skonfigurowanie identyfikatora sieci VLAN, należy podać identyfikator sieci VLAN jako parametr wdrożenia.

### <a name="subnet"></a>Podsieć
Nie podłączaj komputera zestawu deweloperskiego do następujących podsieci:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Te podsieci są zarezerwowane dla sieci wewnętrznych w środowisku deweloperskim zestaw.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Obsługiwany jest tylko protokół IPv4. Nie można tworzyć sieci obsługujących protokół IPv6.

### <a name="dhcp"></a>DHCP
Upewnij się, że serwer DHCP jest dostępny w sieci, z którą łączy się karta sieciowa. Jeśli usługa DHCP nie jest dostępna, należy przygotować dodatkowe sieci korzystające ze statycznego adresu IPv4 oprócz tej używanej przez hosta. Należy podać ten adres IP i bramę jako parametr wdrożenia.

### <a name="internet-access"></a>Dostęp do Internetu
Usługa Azure Stack wymaga dostępu do Internetu, bezpośrednio lub za pośrednictwem przezroczystym serwerem proxy. Usługa Azure Stack nie obsługuje konfiguracji serwera proxy sieci web, aby umożliwić dostęp do Internetu. Adres IP hosta i nowy adres IP przypisany do AzS-BGPNAT01 (za pomocą protokołu DHCP lub statycznego adresu IP) musi umożliwiać dostępu do Internetu. Porty 80 i 443 są używane w domenach graph.windows.net i login.microsoftonline.com.


## <a name="next-steps"></a>Kolejne kroki
[Pobierz pakiet wdrożeniowy ASDK](asdk-download.md)
