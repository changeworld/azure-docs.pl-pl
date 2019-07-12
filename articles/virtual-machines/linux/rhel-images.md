---
title: Obrazy systemu Red Hat Enterprise Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obrazów Red Hat Enterprise Linux na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708380"
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
Wszystkie obecnie opublikowanych obrazów systemu RHEL korzystania z modelu płatność za rzeczywiste użycie i są połączone z [Red Hat Update infrastruktury (RHUI) na platformie Azure](https://aka.ms/rhui-update). Nowa Konwencja nazewnictwa zostało zaadoptowane obrazów rodziny RHEL 7, w których schemat jest partycjonowanie dysku (pierwotne LVM) jest określony w ramach jednostki SKU zamiast wersji. Wersja obrazu systemu RHEL będzie zawierać albo pierwotne 7 lub 7 LVM. W tej chwili nie został zmodyfikowany RHEL 6 rodziny nazewnictwa.

Będzie 2 typy obrazu systemu RHEL 7 jednostek SKU w tę konwencję nazewnictwa: Jednostki SKU, w których przedstawiono wersja pomocnicza i jednostek SKU, które nie. Chcąc użycia 7-RAW lub 7 LVM jednostki SKU, można określić wersję pomocniczą systemu RHEL, które mają zostać wdrożone w wersji. Jeśli wybierzesz opcję "najnowszej" wersji, będą aprowizowane z najnowszą wersją pomocniczą systemu RHEL.

>[!NOTE]
> W RHEL for SAP zestawu obrazów wersja oprogramowania RHEL pozostaje stały. W efekcie ich Konwencja nazewnictwa obejmuje określoną wersję w ramach jednostki SKU.

>[!NOTE]
> Zestaw obrazów systemu RHEL 6 nie zostało przeniesione do nowej konwencji nazewnictwa.

## <a name="extended-update-support-eus"></a>Aktualizacja rozszerzonej pomocy technicznej (EUS)
Jak 2019 kwietnia obrazów systemu RHEL są dostępne dołączonych do repozytoriów obsługę rozszerzonych aktualizacji (EUS) domyślnie. Szczegółowe informacje na temat EUS systemu RHEL są dostępne w [dokumentacji firmy Red Hat](https://access.redhat.com/articles/rhel-eus).

Dostępne są instrukcje jak przełączyć maszyny Wirtualnej EUS i więcej szczegółów na temat daty zakończenia eksploatacji pomocy technicznej EUS [tutaj](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS nie jest obsługiwana na RHEL dodatki. Oznacza to, że jeśli instalujesz pakiet, który znajduje się zwykle z kanału dodatki RHEL, nie będzie w tym celu znajduje się na EUS. Red Hat dodatki cykl życia została szczegółowo opisana [tutaj](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Dla klientów, które mają być używane EUS obrazów:
Klienci, których chcesz używać obrazów, które są dołączone do repozytoriów EUS powinni używać obrazu systemu RHEL, który zawiera numer wersji pomocniczej RHEL w ramach jednostki SKU. Te obrazy będą pierwotnych na partycje (czyli nie LVM).

Na przykład może zostać wyświetlony następujący 2 RHEL 7.4 dostępne obrazy:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
W tym przypadku `RedHat:RHEL:7.4:7.4.2019041718` jest dołączana do repozytoriów EUS domyślnie i `RedHat:RHEL:7-RAW:7.4.2018010506` zostanie dołączona do innego niż EUS repozytoriów domyślnie.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Dla klientów, których nie chcesz, aby używać obrazów EUS:
Jeśli nie chcesz używać obrazu, który jest podłączony do EUS domyślnie, można wdrożyć przy użyciu obrazu, który nie zawiera podrzędny numer wersji w ramach jednostki SKU.

#### <a name="rhel-images-with-eus"></a>Obrazy systemu RHEL EUS
Poniższa tabela będą naliczane dla obrazów systemu RHEL, które zawierają wersję pomocniczą w ramach jednostki SKU.

>[!NOTE]
> W czasie pisania tylko RHEL 7.4 i nowszej wersje pomocnicze mają EUS pomocy technicznej. EUS nie jest już obsługiwana systemu RHEL < = 7.3.

Wersja pomocnicza |Przykład EUS obrazu              |Stan EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Obrazów opublikowanych 2019 kwietnia i później będzie EUS domyślnie|
RHEL W WERSJI 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Obrazów opublikowanych 2019 czerwca i później będzie EUS domyślnie |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrazów opublikowanych maja 2019 i później będzie EUS domyślnie  |
RHEL 8.0      |ND                            | Obecnie nie EUS obrazów dostępnych                 |


## <a name="list-of-rhel-images-available"></a>Lista dostępnych obrazów systemu RHEL
Jednostki SKU są aktualnie dostępne do użytku ogólnego są następujące oferty:

Oferta| SKU | Partycjonowanie | Inicjowanie obsługi | Uwagi
:----|:----|:-------------|:-------------|:-----
RHEL          | NIEPRZETWORZONE 7    | RAW    | Agent systemu Linux | Rodzina RHEL 7 obrazów. <br> Nie dołączony do repozytoriów EUS domyślnie.
|             | 7-LVM    | LVM    | Agent systemu Linux | Rodzina RHEL 7 obrazów. <br> Nie dołączony do repozytoriów EUS domyślnie.
|             | 7-RAW-CI | NIEPRZETWORZONE CIĄGŁEJ INTEGRACJI | Cloud-init  | Rodzina RHEL 7 obrazów. <br> Nie dołączony do repozytoriów EUS domyślnie.
|             | 6.7      | RAW    | Agent systemu Linux | Obrazy systemu RHEL 6.7 starą konwencję nazewnictwa
|             | 6.8      | RAW    | Agent systemu Linux | Takie same jak powyżej dla RHEL 6.8
|             | 6.9      | RAW    | Agent systemu Linux | Takie same jak powyżej dla RHEL 6,9
|             | 6.10     | RAW    | Agent systemu Linux | Takie same jak powyżej dla RHEL 6.10
|             | 7.2      | RAW    | Agent systemu Linux | Takie same jak powyżej dla systemu RHEL 7.2
|             | 7.3      | RAW    | Agent systemu Linux | Takie same jak powyżej dla RHEL 7.3
|             | 7.4      | RAW    | Agent systemu Linux | Wartość taka sama jak powyżej dla RHEL 7.4. <br> Dołączone do repozytoriów EUS domyślnie począwszy od kwietnia 2019 r
|             | 7.5      | RAW    | Agent systemu Linux | Wartość taka sama jak powyżej dla RHEL w wersji 7.5. <br> Dołączone do repozytoriów EUS domyślnie począwszy od czerwca 2019 r
|             | 7.6      | RAW    | Agent systemu Linux | Wartość taka sama jak powyżej dla 7.6 systemu RHEL. <br> Dołączone do repozytoriów EUS domyślnie począwszy od maja 2019 r
RHEL-SAP      | 7.4      | LVM    | Agent systemu Linux | 7\.4 systemu RHEL for SAP HANA i aplikacje biznesowe
|             | 7.5      | LVM    | Agent systemu Linux | RHEL w wersji 7.5 dla oprogramowania SAP HANA i aplikacje biznesowe
RHEL-SAP-HANA | 6.7      | RAW    | Agent systemu Linux | RHEL 6.7 platformy SAP Hana
|             | 7.2      | LVM    | Agent systemu Linux | 7\.2 RHEL for SAP HANA
|             | 7.3      | LVM    | Agent systemu Linux | 7\.3 RHEL for SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Agent systemu Linux | 6\.8 systemu RHEL for SAP Business Applications
|             | 7.3      | LVM    | Agent systemu Linux | 7\.3 systemu RHEL for SAP Business Applications

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

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze Azure Red Hat Update Infrastructure [tutaj](https://aka.ms/rhui-update).
* Można znaleźć informacji na temat zasad wsparcia firmy Red Hat dla wszystkich wersji systemu RHEL na [Red Hat Enterprise Linux cyklu](https://access.redhat.com/support/policy/updates/errata) strony.
