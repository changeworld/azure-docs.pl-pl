---
title: Red Hat Enterprise Linux obrazów na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat Red Hat Enterprise Linux obrazów w Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: c11ce31913baa8c638e94bdf92ef622cd8899e03
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764301"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux obrazów na platformie Azure
W tym artykule opisano dostępne obrazy Red Hat Enterprise Linux (RHEL) w witrynie Azure Marketplace oraz zasady dotyczące ich nazewnictwa i przechowywania.

Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

>[!Important]
> Obrazy RHEL dostępne obecnie w portalu Azure Marketplace obsługują modele licencjonowania "Przenieś własne subskrypcje" (BYOS) lub "płatność zgodnie z rzeczywistym użyciem". [Korzyści z używania hybrydowej platformy Azure](../windows/hybrid-use-benefit-licensing.md) i przełączanie dynamiczne między BYOS i PAYG nie są obsługiwane. Przełączenie trybu licencjonowania wymaga ponownego wdrożenia maszyny wirtualnej z odpowiedniego obrazu.

>[!Note]
> Wszelkie problemy związane z obrazami RHEL w galerii portalu Azure Marketplace zapoznaj się z pomocą techniczną firmy Microsoft.

## <a name="images-available-in-the-ui"></a>Obrazy dostępne w interfejsie użytkownika
Podczas wyszukiwania "Red Hat" w portalu Marketplace lub podczas tworzenia zasobu w Azure Portal interfejsie użytkownika zobaczysz podzestaw dostępnych obrazów RHEL oraz powiązane produkty Red Hat. Pełny zestaw dostępnych obrazów maszyn wirtualnych można zawsze uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure/programu PowerShell/interfejsu API.

Aby wyświetlić pełny zestaw dostępnych obrazów Red Hat na platformie Azure, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Konwencja nazewnictwa
Obrazy maszyn wirtualnych na platformie Azure są zorganizowane według wydawcy, oferty, jednostki SKU i wersji. Kombinacja wydawcy: Oferta: SKU: wersja to obraz URN i unikatowa identyfikacja obrazu do użycia.

Na przykład `RedHat:RHEL:7-RAW:7.6.2018103108` odwołuje się do obrazu RHEL 7,6 RAW, utworzonego w dniu 31 października 2018.

Poniżej przedstawiono przykład sposobu tworzenia maszyny wirtualnej z systemem RHEL 7,6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Krótka moniker
Interfejs API REST platformy Azure umożliwia korzystanie z monikera "Najnowsza" w wersji zamiast określonej wersji. Użycie opcji "Najnowsza" spowoduje udostępnienie najnowszego dostępnego obrazu dla danego wydawcy, oferty i jednostki SKU.

Na przykład `RedHat:RHEL:7-RAW:latest` odwołuje się do najnowszego obrazu z niesformatowaną partycją RHEL 7.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Ogólnie porównanie wersji do określenia najnowszych jest zgodne z regułami [metody CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Bieżąca Konwencja nazewnictwa
Wszystkie aktualnie opublikowane obrazy RHEL używają modelu płatność zgodnie z rzeczywistym użyciem i są połączone z [usługą Red Hat Update Infrastructure (RHUI) na platformie Azure](https://aka.ms/rhui-update). Przyjęto nową konwencję nazewnictwa dla obrazów rodziny RHEL 7, w których schemat partycjonowania dysku (RAW, LVM) jest określony w jednostce SKU zamiast wersji. Wersja obrazu RHEL będzie zawierać 7-RAW lub 7-LVM. W tej chwili nie zmieniono nazewnictwa rodziny RHEL 6.

W tej konwencji nazewnictwa będą dostępne 2 typy jednostek SKU obrazów RHEL 7: Jednostki SKU, które wyświetlają wersję pomocniczą i jednostki SKU, które nie są. Jeśli chcesz użyć jednostki SKU 7-RAW lub 7-LVM, możesz określić wersję pomocniczą RHEL, która ma zostać wdrożona w wersji. W przypadku wybrania "najnowszej wersji" zostanie zainicjowana Najnowsza wersja pomocnicza programu RHEL.

>[!NOTE]
> W RHEL dla zestawu obrazów oprogramowania SAP wersja RHEL jest stała. W związku z tym ich Konwencja nazewnictwa obejmuje konkretną wersję w jednostce SKU.

>[!NOTE]
> Zestaw obrazów RHEL 6 nie został przeniesiony do nowej konwencji nazewnictwa.

## <a name="extended-update-support-eus"></a>Rozszerzona obsługa aktualizacji (EUS)
Od kwietnia 2019 obrazy RHEL są dostępne, które są domyślnie dołączone do repozytoriów pomocy technicznej aktualizacji (EUS). Więcej szczegółów na temat RHEL EUS są dostępne w [dokumentacji Red Hat](https://access.redhat.com/articles/rhel-eus).

Instrukcje dotyczące przełączenia maszyny wirtualnej do EUS i więcej szczegółów na temat EUS z punktu końcowego pomocy technicznej są dostępne [tutaj](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS nie są obsługiwane w dodatkach RHEL. Oznacza to, że w przypadku instalowania pakietu, który jest zazwyczaj dostępny w kanale RHEL Extras, nie będzie można wykonać tego działania w EUS. Cykl życia produktu Red Hat jest szczegółowo opisany w [tym miejscu](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Klienci, którzy chcą korzystać z obrazów EUS:
Klienci, którzy chcą korzystać z obrazów dołączonych do repozytoriów EUS, powinni używać obrazu RHEL zawierającego numer pomocniczej RHEL w jednostce SKU. Te obrazy staną się niesformatowane na partycje (tj. nie LVM).

Na przykład mogą zostać wyświetlone następujące 2 RHEL 7,4 obrazów:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
W tym przypadku `RedHat:RHEL:7.4:7.4.2019041718` zostanie on domyślnie dołączony do repozytoriów EUS i `RedHat:RHEL:7-RAW:7.4.2018010506` zostanie domyślnie dołączony do repozytoriów innych niż EUS.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Dla klientów, którzy nie chcą korzystać z obrazów EUS:
Jeśli nie chcesz używać obrazu, który jest domyślnie połączony z EUS, wdróż go przy użyciu obrazu, który nie zawiera pomocniczego numeru wersji w jednostce SKU.

#### <a name="rhel-images-with-eus"></a>Obrazy RHEL z EUS
Poniższa tabela ma zastosowanie do obrazów RHEL zawierających wersję pomocniczą w jednostce SKU.

>[!NOTE]
> W czasie pisania tylko RHEL 7,4 i nowsze wersje pomocnicze mają EUS pomoc techniczną. EUS nie jest już obsługiwana dla RHEL < = 7,3.
>
> Więcej informacji na temat dostępności RHEL EUS można znaleźć [tutaj](https://access.redhat.com/support/policy/updates/errata).

Wersja pomocnicza |Przykład obrazu EUS              |Stan EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Obrazy opublikowane w kwietniu 2019 i nowszych będą domyślnie EUS|
RHEL W WERSJI 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Obrazy opublikowane czerwiec 2019 i nowsze będą domyślnie EUS |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrazy opublikowane z maja 2019 i nowsze będą domyślnie EUS  |
RHEL 8,0      |ND                            | Brak EUS dostępnych w witrynie Red Hat                               |


## <a name="list-of-rhel-images-available"></a>Lista dostępnych obrazów RHEL
Następujące oferty są obecnie dostępne do użytku ogólnego:

Oferta| SKU | Partycjonowanie | Aprowizowanie | Uwagi
:----|:----|:-------------|:-------------|:-----
RHEL          | 7 — NIEPRZETWORZONY    | RAW    | Agent systemu Linux | Rodzina obrazów RHEL 7. x. <br> Nie dołączono do domyślnie repozytoriów EUS.
|             | 7 — LVM    | LVM    | Agent systemu Linux | Rodzina obrazów RHEL 7. x. <br> Nie dołączono do domyślnie repozytoriów EUS.
|             | 7-RAW-CI | NIEPRZETWORZONY ELEMENT CI | Cloud-init  | Rodzina obrazów RHEL 7. x. <br> Nie dołączono do domyślnie repozytoriów EUS.
|             | 6.7      | RAW    | Agent systemu Linux |
|             | 6.8      | RAW    | Agent systemu Linux |
|             | 6.9      | RAW    | Agent systemu Linux |
|             | 6.10     | RAW    | Agent systemu Linux |
|             | 7.2      | RAW    | Agent systemu Linux |
|             | 7.3      | RAW    | Agent systemu Linux |
|             | 7.4      | RAW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od kwietnia 2019.
|             | 7.5      | RAW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od czerwca 2019.
|             | 7,6      | RAW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od maja 2019.
|             | 7,7      | LVM    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
RHEL-SAP      | 7.4      | LVM    | Agent systemu Linux | RHEL 7,4 for SAP HANA i aplikacje biznesowe
|             | 7.5      | LVM    | Agent systemu Linux | RHEL 7,5 for SAP HANA i aplikacje biznesowe
RHEL-SAP-HANA | 6.7      | RAW    | Agent systemu Linux | RHEL 6,7 dla SAP HANA
|             | 7.2      | LVM    | Agent systemu Linux | RHEL 7,2 dla SAP HANA
|             | 7.3      | LVM    | Agent systemu Linux | RHEL 7,3 dla SAP HANA
RHEL — SAP-APPS | 6.8      | RAW    | Agent systemu Linux | RHEL 6,8 dla oprogramowania SAP Business Applications
|             | 7.3      | LVM    | Agent systemu Linux | RHEL 7,3 dla oprogramowania SAP Business Applications
RHEL – HA       | 7.4      | LVM    | Agent systemu Linux | RHEL 7,4 z dodatkiem HA
|             | 7.5      | LVM    | Agent systemu Linux | RHEL 7,5 z dodatkiem HA
|             | 7,6      | LVM    | Agent systemu Linux | RHEL 7,6 z dodatkiem HA
RHEL — SAP-HA   | 7.4      | LVM    | Agent systemu Linux | RHEL 7,4 dla oprogramowania SAP z dodatkiem HA
|             | 7.5      | LVM    | Agent systemu Linux | RHEL 7,5 dla oprogramowania SAP z dodatkiem HA
|             | 7,6      | LVM    | Agent systemu Linux | RHEL 7,6 dla oprogramowania SAP z dodatkiem HA

### <a name="old-naming-convention"></a>Stara Konwencja nazewnictwa
Rodzina obrazów RHEL 7 i rodzina RHEL 6 obrazów używa określonych wersji w swoich jednostkach SKU do momentu wprowadzenia zmiany konwencji nazewnictwa opisanej powyżej.

Liczby jednostek SKU znajdziesz na liście pełny obraz. Firmy Microsoft i Red Hat będą tworzyć nowe, liczbowe jednostki SKU, gdy zostanie wydana nowa wersja pomocnicza.

### <a name="other-available-offers-and-skus"></a>Inne dostępne oferty i jednostki SKU
Pełna lista dostępnych ofert i jednostek SKU może zawierać dodatkowe obrazy poza wymienionymi w powyższej tabeli, na przykład `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Oferty te mogą służyć do świadczenia pomocy technicznej dotyczącej określonych rozwiązań w witrynie Marketplace lub mogą być publikowane na potrzeby wersji zapoznawczych i testowych. Mogą zostać zmienione lub usunięte w dowolnym momencie bez ostrzeżenia. Nie należy ich używać, chyba że ich obecność została publicznie udokumentowana przez firmę Microsoft lub Red Hat.

## <a name="publishing-policy"></a>Zasady publikowania
Obrazy aktualizacji firmy Microsoft i Red Hat jako nowe wersje pomocnicze są udostępniane, zgodnie z wymaganiami dotyczącymi konkretnych CVEs lub okazjonalnych zmian konfiguracji/aktualizacji. Staramy się dostarczyć zaktualizowane obrazy najszybciej, jak to możliwe — w ciągu trzech dni roboczych po wydaniu lub udostępnieniu poprawki CVE.

Aktualizujemy tylko bieżącą wersję pomocniczą w danej rodzinie obrazu. W przypadku wersji nowszej wersji pomocniczej firma Microsoft zaprzestanie aktualizowania starszej wersji pomocniczej. Na przykład w przypadku wersji RHEL 7,6 RHEL 7,5 obrazy nie będą już aktualizowane.

>[!NOTE]
> Aktywne maszyny wirtualne platformy Azure, które są udostępniane z obrazów z opcją płatność zgodnie z rzeczywistym użyciem, są połączone z usługą Azure RHUI i mogą otrzymywać aktualizacje i poprawki natychmiast po ich wydaniu przez Red Hat i zreplikowane na platformie Azure RHUI (zazwyczaj w ciągu mniej niż 24 godzin od oficjalnego wydania przez Red Hat) . Te maszyny wirtualne nie wymagają nowego opublikowanego obrazu do pobrania aktualizacji, a klienci mają pełną kontrolę nad tym, kiedy należy inicjować aktualizację.

## <a name="image-retention-policy"></a>Zasady przechowywania obrazów
Nasze bieżące zasady mają na celu zachowanie wszystkich poprzednio opublikowanych obrazów. Firma Microsoft zastrzega sobie prawo do usuwania obrazów, które są znane, aby powodowały problemy jakiegokolwiek rodzaju. Na przykład obrazy z nieprawidłowymi konfiguracjami ze względu na kolejne aktualizacje platformy lub składników mogą zostać usunięte. Obrazy, które mogą zostać usunięte, będą zgodne z bieżącymi zasadami witryny Marketplace w celu dostarczenia powiadomień do 30 dni przed usunięciem obrazu.

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat infrastruktury aktualizacji Red Hat platformy Azure można znaleźć [tutaj](https://aka.ms/rhui-update).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
