---
title: Obrazy systemu Red Hat Enterprise Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obrazów Red Hat Enterprise Linux na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: fb3c0e46324a22bdd95bf7d93c28e69c195927e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542445"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Obrazy systemu Red Hat Enterprise Linux na platformie Azure
W tym artykule opisano dostępne obrazy Red Hat Enterprise Linux (RHEL) w witrynie Azure Marketplace oraz zasady dotyczące nazewnictwa i przechowywania.

Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.

>[!Important]
> Obrazy systemu RHEL, obecnie dostępna w witrynie Azure marketplace obsługuje Bring-Your-właścicielem-subskrypcji (BYOS) lub płatność za rzeczywiste użycie (PAYG) Modele licencjonowania. [Korzyści z używania hybrydowej platformy Azure](../windows/hybrid-use-benefit-licensing.md) i dynamicznego przełączania się między BYOS i PAYG nie jest obsługiwana. Przełączanie trybu licencjonowania wymaga ponownego wdrożenia maszyny Wirtualnej z odpowiadających im obrazów.

>[!Note]
> Na dowolnym problemem dotyczącym programu obrazów systemu RHEL w galerii witryny Azure marketplace Zgłoś bilet pomocy technicznej firmy Microsoft.

## <a name="images-available-in-the-ui"></a>Obrazów dostępnych w interfejsie użytkownika
Podczas wyszukiwania "Red Hat" w witrynie Marketplace lub tworzenie zasobu w witrynie Azure portal interfejsu użytkownika, zobaczysz podzbioru dostępnych obrazów systemu RHEL i powiązanych produktów firmy Red Hat. Można zawsze uzyskać pełen zestaw dostępnych obrazów maszyn wirtualnych przy użyciu interfejsu wiersza polecenia/programu PowerShell/interfejsu API usługi Azure.

Aby wyświetlić pełny zestaw dostępnych obrazów Red Hat na platformie Azure, uruchom następujące polecenie

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Konwencje nazewnictwa
Obrazy maszyn wirtualnych na platformie Azure są zorganizowane według wydawcy, oferty, jednostki SKU i wersji. Kombinacja wydawcy: oferta: SKU:Version jest styl obrazowy URN i unikatowo identyfikuje obraz, który ma być używany.

Na przykład `RedHat:RHEL:7-RAW:7.6.2018103108` odwołuje się do RHEL 7.6 pierwotne partycjonowane obrazu utworzonego 31 października 2018 r.

Poniżej przedstawiono przykład sposobu tworzenia maszyny Wirtualnej 7.6 systemu RHEL.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Monikera "najnowsza".
Interfejs API REST platformy Azure umożliwia użycie krótkiej nazwy "latest" dla wersji zamiast określonych wersji. Za pomocą "latest" przydzieli najnowsza wersja obrazu dla danego wydawcy, oferty i jednostki SKU.

Na przykład `RedHat:RHEL:7-RAW:latest` odwołuje się do najnowszego RHEL 7 rodziny pierwotne partycjonowane obrazu dostępne.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Ogólnie rzecz biorąc, porównanie wersji do określenia najnowszej następuje [CompareTo — metoda](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Bieżący konwencji nazewnictwa
Wszystkie obecnie opublikowanych obrazów systemu RHEL korzystania z modelu płatność za rzeczywiste użycie i są połączone z [Red Hat Update infrastruktury (RHUI) na platformie Azure](https://aka.ms/rhui-update). Ze względu na ograniczenie usługi RHUI do projektu zostało zaadoptowane nowej konwencji nazewnictwa dla rodziny obrazów systemu RHEL 7. W tej chwili nie został zmodyfikowany RHEL 6 rodziny nazewnictwa.

Ograniczenie to z faktu, że w przypadku nie selektywną `yum update` jest wykonywany dla maszyny Wirtualnej podłączone do usługi RHUI, wersja oprogramowania RHEL zostanie zaktualizowany do najnowszej wersji z bieżącego rodziny. Aby uzyskać więcej informacji, zobacz [ten link](https://aka.ms/rhui-update). Może to spowodować błąd podczas aprowizowanego obrazów systemu RHEL 7.2 staje się RHEL 7.6 po aktualizacji. Nadal można udostępnić z obrazu starsze, zgodnie z przedstawionymi w powyższych przykładach jawne określenie wymaganej wersji. Jeśli nie określono wymaganej wersji podczas aprowizowania nowych obrazów systemu RHEL 7, najnowszego obrazu będzie gotowa.

>[!NOTE]
> W RHEL for SAP zestawu obrazów wersja oprogramowania RHEL pozostaje stały. W efekcie ich Konwencja nazewnictwa obejmuje określoną wersję w ramach jednostki SKU.

>[!NOTE]
> Zestaw obrazów systemu RHEL 6 nie zostało przeniesione do nowej konwencji nazewnictwa.

Jednostki SKU są aktualnie dostępne do użytku ogólnego są następujące oferty:

Oferta| SKU | Partycjonowanie | Inicjowanie obsługi | Uwagi
:----|:----|:-------------|:-------------|:-----
RHEL | NIEPRZETWORZONE 7 | RAW | Agent systemu Linux | Rodzina RHEL 7 obrazów
| | 7-LVM | LVM | Agent systemu Linux | Rodzina RHEL 7 obrazów
| | 7-RAW-CI | NIEPRZETWORZONE CIĄGŁEJ INTEGRACJI | Cloud-init | Rodzina RHEL 7 obrazów
| | 6.7 | RAW | Agent systemu Linux | Obrazy systemu RHEL 6.7 starą konwencję nazewnictwa
| | 6.8 | RAW | Agent systemu Linux | Takie same jak powyżej dla RHEL 6.8
| | 6.9 | RAW | Agent systemu Linux | Takie same jak powyżej dla RHEL 6,9
| | 6.10 | RAW | Agent systemu Linux | Takie same jak powyżej dla RHEL 6.10
| | 7.2 | RAW | Agent systemu Linux | Takie same jak powyżej dla systemu RHEL 7.2
| | 7.3 | RAW | Agent systemu Linux | Takie same jak powyżej dla RHEL 7.3
| | 7.4 | RAW | Agent systemu Linux | Takie same jak powyżej dla systemu RHEL w wersji 7.4
| | 7.5 | RAW | Agent systemu Linux | Takie same jak powyżej dla RHEL w wersji 7.5
RHEL-SAP | 7.4 | LVM | Agent systemu Linux | 7.4 systemu RHEL for SAP HANA i aplikacje biznesowe
| | 7.5 | LVM | Agent systemu Linux | RHEL w wersji 7.5 dla oprogramowania SAP HANA i aplikacje biznesowe
RHEL-SAP-HANA | 6.7 | RAW | Agent systemu Linux | RHEL 6.7 platformy SAP Hana
| | 7.2 | LVM | Agent systemu Linux | 7.2 RHEL for SAP HANA
| | 7.3 | LVM | Agent systemu Linux | 7.3 RHEL for SAP HANA
RHEL-SAP-APPS | 6.8 | RAW | Agent systemu Linux | 6.8 systemu RHEL for SAP Business Applications
| | 7.3 | LVM | Agent systemu Linux | 7.3 systemu RHEL for SAP Business Applications

### <a name="old-naming-convention"></a>Stara Konwencja nazewnictwa
Rodzina RHEL 7, obrazów i obrazów systemów z rodziny RHEL 6 określonych wersji są używane w ich jednostki SKU, do czasu zmiany konwencji nazewnictwa wyjaśniono powyżej.

Liczbowe jednostki SKU znajdzie się na liście pełnego obrazu. Microsoft i Red Hat spowoduje utworzenie nowych jednostek SKU numeryczne, gdy nowa wersja pomocnicza powróci do.

### <a name="other-available-offers-and-skus"></a>Innych dostępnych ofert oraz jednostek SKU
Pełną listę dostępnych ofert oraz jednostek SKU może zawierać dodatkowe obrazy poza wymienione w powyższej tabeli, na przykład `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Te oferty mogą być używane do obsługi rozwiązaniami z witryny marketplace określone lub mogą być publikowane podglądy i testowania. Może zostać zmienione lub usunięte w dowolnym momencie bez ostrzeżenia. Nie należy ich używać, chyba że ich obecność zostały publicznie opisane przez firmę Microsoft lub Red Hat.

## <a name="publishing-policy"></a>Publikowanie zasad
Firma Microsoft i Red Hat aktualizacji obrazów, który został wydawane są nowe wersje pomocnicze, zgodnie z potrzebami w celu rozwiązania określonych CVEs lub konfiguracji okazjonalne zmian/aktualizacji. Staramy się aby jak najszybciej — w ciągu trzech dni roboczych od wersji lub dostępne rozwiązanie CVE zaktualizowanych obrazów.

Aktualizujemy tylko bieżącej wersji pomocniczej w rodzinie danego obrazu. W wersji nowszej wersji pomocniczej Trwa zatrzymywanie aktualizowanie starszych wersji pomocniczej. Na przykład wraz z wydaniem systemu RHEL 7.6 obrazów systemu RHEL w wersji 7.5 już przechodzenia do zaktualizowania.

>[!NOTE]
> Aktywnych maszyn wirtualnych platformy Azure z systemem RHEL płatność za rzeczywiste użycie obrazów są podłączone do usługi RHUI platformy Azure i może odbierać aktualizacje i poprawki, zaraz po ich wydanymi przez firmy Red Hat i replikowane do usługi RHUI platformy Azure (zwykle w mniej niż 24 godziny po oficjalnym wydaniem przez firmy Red Hat) . Te maszyny wirtualne nie wymagają nowych obrazów opublikowanych w celu uzyskania aktualizacji i klienci mają pełną kontrolę nad tym, kiedy można zainicjować aktualizację.

## <a name="image-retention-policy"></a>Zasady przechowywania obrazu
Nasze bieżące zasady jest przechowywanie wszystkich poprzednio opublikowanych obrazów. Firma Microsoft zastrzega sobie prawo do usunięcia obrazów, które są znane spowodować problemy wszelkiego rodzaju. Na przykład obrazy z konfiguracjami niepoprawna z powodu kolejnych platformy lub aktualizacje składników mogą zostać usunięte. Obrazy, które mogą zostać usunięte podlegają zasadom bieżącej witryny Marketplace do udostępniania powiadomień o się do 30 dni przed usunięcie obrazu.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o usłudze Azure Red Hat Update Infrastructure [tutaj](https://aka.ms/rhui-update).
* Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.
