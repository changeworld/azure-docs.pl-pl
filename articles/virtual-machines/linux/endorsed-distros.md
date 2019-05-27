---
title: Dystrybucje systemu Linux zalecanych dla na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o systemie Linux w dystrybucjach zatwierdzone na platformie Azure, takich jak wskazówki dla Ubuntu, CentOS, Oracle i SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: szark
ms.openlocfilehash: a1be0b6870882d3c7b0281dec7933e87c50e49de
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834565"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Zatwierdzone dystrybucje systemu Linux na platformie Azure
Etap to udostępnienie obrazów systemu Linux w witrynie Azure Marketplace. Współpracujemy z wieloma społecznościami systemu Linux, aby dodać jeszcze więcej pozycji do listy dystrybucyjnej zatwierdzone. W międzyczasie dystrybucji, które nie są dostępne w portalu Marketplace, zawsze Przenoszenie własnych Linux postępując zgodnie ze wskazówkami w temacie [tworzenie i przekazywanie wirtualnego dysku twardego zawierającego system operacyjny Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje
W poniższej tabeli wymieniono dystrybucje systemu Linux i wersji, które są obsługiwane na platformie Azure. Zapoznaj się [pomocy technicznej dla obrazów systemu Linux na platformie Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) Aby uzyskać szczegółowe informacje dotyczące pomocy technicznej dla systemu Linux i technologii typu open source na platformie Azure.

Sterowniki usługi LIS (Linux Integration) dla funkcji Hyper-V i platformą Azure są modułów jądra, których Microsoft przyczynia się bezpośrednio do nadrzędnego jądra systemu Linux.  Niektórych sterowników LIS są wbudowane w jądra dystrybucji domyślnie. Starsze dystrybucje, które są oparte na systemie Red Hat Enterprise (RHEL) / CentOS są dostępne do pobrania osobno na [Linux integracji usług w wersji 4.2 dla funkcji Hyper-V i Azure](https://www.microsoft.com/download/details.aspx?id=55106). Zobacz [wymagania jądra systemu Linux](create-upload-generic.md#linux-kernel-requirements) Aby uzyskać więcej informacji na temat sterowników LIS.

Agent systemu Linux platformy Azure wstępnie zainstalowanym systemem obrazów portalu Azure Marketplace i są zazwyczaj dostępne z repozytorium pakietów dystrybucji. Kod źródłowy znajduje się na [GitHub](https://github.com/azure/walinuxagent).


| Dystrybucja | Wersja | Sterowniki | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3: [Pobierz LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: Jądra |Pakiet: W [repozytorium](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Jądra |Kod źródłowy: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+ |Jądra |Pakiet: W repozytorium, w obszarze "waagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Jądra |Pakiet: W repozytorium, w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+, 8.0+ |Jądra |Pakiet: W repozytorium, w obszarze "WALinuxAgent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1+<br>15|Jądra |Pakiet:<p> 11 WE [chmury: narzędzia](https://build.opensuse.org/project/show/Cloud:Tools) repozytorium<br>Aby uzyskać 12 zawartych w Module "Chmura publiczna" w obszarze "python-azure-agent"<br/>Kod źródłowy: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |Jądra |Pakiet: W [chmury: narzędzia](https://build.opensuse.org/project/show/Cloud:Tools) repozytorium w obszarze "python-azure-agent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |Jądra |Pakiet: W repozytorium, w obszarze "walinuxagent" <br/>Kod źródłowy: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  informacji o rozszerzoną obsługę Ubuntu 12.04 14.04 można znaleźć tutaj: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cykl aktualizacji obrazu
Platforma Azure wymaga, że wydawców zalecanych dystrybucjach systemu Linux regularnie aktualizować obrazów w portalu Azure Marketplace przy użyciu najnowszych poprawek i poprawki zabezpieczeń, w erze kwartalnych lub szybciej. Zaktualizowanych obrazów w portalu Azure Marketplace są dostępne automatycznie dla klientów jako nowe wersje SKU obrazu. Więcej informacji na temat wyszukiwania obrazów systemu Linux: [Znajdowanie obrazów maszyny Wirtualnej systemu Linux w witrynie Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Dodatkowe linki
 - [Cykl życia obraz chmury publicznej SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Dopasowane Azure jądra

Azure ściśle współpracuje z różne dystrybucje systemu Linux zalecanych optymalizacji obrazów, opublikowanych w portalu Azure Marketplace. Jednym z aspektów ta współpraca to rozwoju "dostosowane" jądra systemu Linux, które są zoptymalizowane pod kątem platformy Azure i dostarczane jako w pełni obsługiwane składniki dystrybucji systemu Linux. Jądra dopasowane do platformy Azure obejmują nowe funkcje i ulepszenia wydajności i na szybsze tempa (zazwyczaj co kwartał) w porównaniu do domyślnych lub jądra ogólnego, które są dostępne z dystrybucji.

W większości przypadków można znaleźć te jądra wstępnie zainstalowanych na domyślne obrazy w witrynie Azure Marketplace, a klienci więc platformy Azure zostanie natychmiast możesz korzystać z tych zoptymalizowane jądra. Więcej informacji na temat tych jądra dostrojone Azure znajdują się w następujących łączy:

 - CentOS dostrojone Azure jądra - dostępne za pośrednictwem wirtualizacji CentOS SIG - [uzyskać więcej informacji](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian chmury — dostępne jądra z Debian 10 i obrazu systemu Debian 9 "backports" na platformie Azure — [żądania dalszych informacji](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES dostrojone Azure jądra - [uzyskać więcej informacji](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu dostrojone Azure jądra - [uzyskać więcej informacji](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partnerzy

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Z systemu CoreOS witryny sieci Web:

*CoreOS jest przeznaczona dla zabezpieczeń, zgodności i niezawodności. Zamiast instalowania pakietów za pomocą narzędzia yum lub apt, CoreOS używa kontenerów systemu Linux w celu zarządzania usługami na wyższym poziomie abstrakcji. Kod pojedynczą usługę i wszystkie zależności są spakowane w kontenerze, który może działać na jednej lub wielu maszynach CoreOS.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ to niezależne konsultacji i usług firmy, która specjalizuje się w projektowania i implementacji profesjonalnych rozwiązań przy użyciu bezpłatnego oprogramowania. Jako wiodących specjalistów typu open source Credativ ma międzynarodowy uznanie z wielu działów IT, korzystających z pomocy technicznej. Wspólnie z firmą Microsoft Credativ obecnie przygotowuje odpowiednie obrazy systemu Debian dla Debian 8 (Jessie) oraz Debian przed 7 (Wheezy). Oba obrazy są specjalnie zaprojektowane do uruchamiania na platformie Azure i można łatwo zarządzać za pomocą platformy. Credativ będzie również obsługiwać długotrwałe utrzymanie i aktualizowanie obrazów systemu Debian dla platformy Azure za pośrednictwem jej centrów Otwórz źródło pomocy technicznej.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Firmy Oracle strategia polega na oferują szeroką gamę rozwiązań dla chmur prywatnych i publicznych. Ta strategia zapewnia wybór i elastyczność, w jaki sposób wdrożenia oprogramowania Oracle w chmurach programu Oracle i innych chmur. Partnerstwo firmy Oracle z firmą Microsoft umożliwia klientom wdrażanie oprogramowania Oracle w chmurach publicznych i prywatnych firmy Microsoft przy zapewnieniu certyfikacji i wsparcia firmy Oracle.  Zobowiązanie firmy Oracle oraz zwrot z inwestycji w rozwiązania chmur publicznych i prywatnych firmy Oracle ulega zmianie.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Na świecie wiodący dostawca rozwiązań typu open source, Red Hat ułatwia ponad 90% firm z rankingu Fortune 500 rozwiązywania problemów biznesowych i IT i strategii biznesowej i przygotowania przyszłość technologii. Red Hat robi to, zapewniając bezpiecznych rozwiązań za pośrednictwem modelu biznesowego otwarte i niedrogie stała, przewidywalna subskrypcji.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server na platformie Azure jest sprawdzonej platformie, która zapewnia doskonałą niezawodność i bezpieczeństwo chmury obliczeniowej. Wszechstronna platforma systemu Linux firmy SUSE bezproblemowo integruje się z usług Azure cloud services w celu dostarczenia środowiska chmury łatwe w zarządzaniu. Z więcej niż 9,200 certyfikowanych aplikacji od ponad 1800 niezależnych dostawców oprogramowania dla systemu SUSE Linux Enterprise Server SUSE gwarantuje, że obciążeń obsługiwanych w centrum danych można bez obaw wdrożyć na platformie Azure.

### <a name="canonical"></a>Kanoniczne
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical inżynierii i nadzór społeczności open dysku sukcesu firmy Ubuntu w klienta, serwera i chmury obliczeniowej, w tym lokacjach chmury osobistej usług dla konsumentów. Wizja firmy Canonical ujednolicone, bezpłatne platformy w systemie Ubuntu z telefonu w chmurze, zapewnia rodziny spójnych interfejsów telefon, tablet, takich jak Telewizor i pulpitu. Tę wizję sprawia, że Ubuntu pierwszy wybór różnych mogą odnieść instytucje od dostawców chmury publicznej twórcy elektronicznych i ulubionych między poszczególne technologists.

Z deweloperami i inżynierów centrach na całym świecie firmy Canonical jednoznacznie znajduje się do współpracy z twórcami sprzętu, dostawców zawartości i deweloperów oprogramowania wprowadzanie Ubuntu rozwiązań na rynek, na którym komputerami, serwerami i urządzeń przenośnych.
