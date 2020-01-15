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
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 211ac68fd10cd745faf68a5efae7392345008d7b
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941447"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Przegląd obrazów Red Hat Enterprise Linux
W tym artykule opisano dostępne obrazy Red Hat Enterprise Linux (RHEL) w witrynie Azure Marketplace oraz zasady dotyczące ich nazewnictwa i przechowywania.

Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) . Aby uzyskać szczegółowe informacje o cenach, odwiedź [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Obrazy RHEL dostępne obecnie w portalu Azure Marketplace obsługują modele licencjonowania "Przenieś własne subskrypcje" (BYOS) lub "płatność zgodnie z rzeczywistym użyciem". [Korzyści z używania hybrydowej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) i przełączanie dynamiczne między BYOS i PAYG nie są obsługiwane. Przełączenie trybu licencjonowania wymaga ponownego wdrożenia maszyny wirtualnej z odpowiedniego obrazu.

>[!NOTE]
> Wszelkie problemy związane z obrazami RHEL w portalu Azure Marketplace można znaleźć na stronie pomocy technicznej firmy Microsoft.

## <a name="images-available-in-azure"></a>Obrazy dostępne na platformie Azure
Podczas wyszukiwania "Red Hat" w portalu Marketplace lub podczas tworzenia zasobu w Azure Portal interfejsie użytkownika zobaczysz tylko podzestaw wszystkich dostępnych obrazów RHEL. Pełny zestaw dostępnych obrazów maszyn wirtualnych można zawsze uzyskać przy użyciu interfejsu wiersza polecenia platformy Azure/programu PowerShell/interfejsu API.

Aby wyświetlić pełny zestaw dostępnych obrazów Red Hat na platformie Azure, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konwencja nazewnictwa
Obrazy maszyn wirtualnych na platformie Azure są zorganizowane według wydawcy, oferty, jednostki SKU i wersji. Kombinacja wydawcy: Oferta: SKU: wersja to obraz URN i unikatowa identyfikacja obrazu do użycia.

Na przykład `RedHat:RHEL:7-LVM:7.6.2018103108` odnosi się do obrazu RHEL 7,6 LVM, utworzonego w dniu 31 października 2018.

Poniżej przedstawiono przykład sposobu tworzenia maszyny wirtualnej z systemem RHEL 7,6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Krótka moniker
Interfejs API REST platformy Azure umożliwia korzystanie z monikera "Najnowsza" w wersji zamiast określonej wersji. Użycie opcji "Najnowsza" spowoduje udostępnienie najnowszego dostępnego obrazu dla danego wydawcy, oferty i jednostki SKU.

Na przykład `RedHat:RHEL:7-LVM:latest` odnosi się do najnowszego obrazu LVM z rodziną RHEL 7.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Ogólnie porównanie wersji do określenia najnowszych jest zgodne z regułami [metody CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="rhel-6-image-types"></a>RHEL 6 — typy obrazów
W przypadku obrazów RHEL 6. x typy obrazów są następujące:

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (np. 6,9) | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 6.9.2018010506) | Wszystkie obrazy standardowego RHEL 6. x są zgodne z tą konwencją
|RedHat | RHEL — BYOS | RHEL — raw69 | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 6.9.20181023) | Ten obraz jest obrazem RHEL 6,9 BYOS.
|RedHat | RHEL | RHEL — SAP-APPS | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 6.8.2017053118) | Jest to obraz RHEL 6,8 for SAP Applications. Jest on uprawniony do uzyskiwania dostępu do repozytoriów aplikacji SAP, a także podstawowych repozytoriów RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 6.7.2017053121) | Jest to RHEL 6,7 dla obrazu SAP HANA. Jest on uprawniony do uzyskiwania dostępu SAP HANA repozytoriów, a także podstawowych repozytoriów RHEL.

### <a name="rhel-7-image-types"></a>RHEL 7 — typy obrazów
W przypadku obrazów RHEL 7. x istnieje kilka różnych typów obrazów. W poniższej tabeli przedstawiono różne zestawy oferowanych przez nas obrazów. Pełną listę można wyświetlić za pomocą polecenia AZ CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> O ile nie wskazano inaczej, wszystkie obrazy są LVM na partycje i będą łączyć się z regularnymi repozytoriami RHEL (tj. nie EUS, a nie E4S). Przechodząc do przodu, przejdziemy do publikowania tylko LVM obrazów z podziałem na partycje, ale są otwarte do przesyłania opinii na temat tej decyzji. Szczegółowe informacje na temat rozszerzonej obsługi aktualizacji i usług aktualizacji dla oprogramowania SAP są dostępne na [stronie cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (np. 7,6) | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019102813) | Obrazy opublikowane przed 2019 kwietnia zostaną dołączone do standardowych repozytoriów RHEL. Obrazy opublikowane po kwietnia 2019 zostaną dołączone do repozytoriów pomocy technicznej aktualizacji programu Red Hat (EUS) w celu umożliwienia blokowania wersji określonej wersji pomocniczej. Klienci chcący regularne repozytoria powinni używać obrazów zawierających 7-LVM lub 7-RAW w wartości SKU (szczegóły poniżej). Obrazy RHEL 7,7 i nowsze będą LVM z podziałem na partycje. Wszystkie inne obrazy z tej kategorii są podzielone na partycje.
|RedHat | RHEL | 7 — NIEPRZETWORZONY | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019102813) | Obrazy te są podzielone na partycje (tj. żadne woluminy logiczne nie zostały dodane).
|RedHat | RHEL | 7-RAW-CI | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019072418) | Obrazy te są podzielone na partycje (tj. żadne woluminy logiczne nie zostały dodane) i będą używać usługi Cloud-init do aprowizacji.
|RedHat | RHEL | 7 — LVM | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019062414) | Te obrazy są LVM na partycje.
|RedHat | RHEL — BYOS | RHEL — {LVM, RAW} | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.7.20190819) | Te obrazy to obrazy w programie RHEL 7 BYOS. Nie są one dołączone do żadnych repozytoriów i nie będą naliczane opłaty za usługę RHEL Premium. Jeśli interesują Cię obrazy RHEL BYOS, [Zażądaj dostępu](https://aka.ms/rhel-byos). Wartości jednostki SKU kończą się wersją pomocniczą i oznaczają, czy obraz jest nieprzetworzony lub LVM. Na przykład wartość SKU RHEL-lvm77 wskazuje obraz RHEL 7,7 z partycją LVM.
|RedHat | RHEL | RHEL-SAP | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019071300) | Obrazy te są RHEL dla obrazów SAP. Są one uprawnione do uzyskiwania dostępu do repozytoriów SAP HANA i aplikacji oraz repozytoriów E4S RHEL. Rozliczenia obejmują RHEL Premium i SAP Premium na podstawie podstawowej opłaty za obliczenia.
|RedHat | RHEL | RHEL — SAP-HA | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019062320) | Te obrazy są RHEL dla oprogramowania SAP z obrazami wysokiej dostępności i aktualizacji usług. Są one uprawnione do uzyskiwania dostępu do repozytoriów SAP HANA i aplikacji oraz repozytoriów o wysokiej dostępności, a także repozytoriów E4S RHEL. Rozliczanie obejmuje RHEL Premium, SAP Premium i HA Premium na podstawie podstawowej opłaty za obliczenia.
|RedHat | RHEL | RHEL – HA | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.6.2019062019) | Są to obrazy RHEL, które są również uprawnione do uzyskiwania dostępu do dodatku o wysokiej dostępności. Opłaty są naliczane nieco z góry na RHEL i podstawowa opłata za obliczanie ze względu na dodatkową wersję Premium.
|RedHat | RHEL | RHEL — SAP-APPS | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.3.2017053118) | Te obrazy są nieaktualne, ponieważ aplikacje SAP i repozytoria SAP HANA zostały połączone z repozytoriami SAP. Są to RHEL dla obrazów aplikacji SAP. Są one uprawnione do uzyskiwania dostępu do repozytoriów aplikacji SAP, a także podstawowych repozytoriów RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 7.3.2018051421) | Te obrazy są nieaktualne, ponieważ aplikacje SAP i repozytoria SAP HANA zostały połączone z repozytoriami SAP. Są to RHEL for SAP HANA obrazy. Są one uprawnione do uzyskiwania dostępu SAP HANA repozytoriów, a także podstawowych repozytoriów RHEL.

### <a name="rhel-8-image-types"></a>RHEL 8 — typy obrazów
Szczegóły dotyczące typów obrazów RHEL 8 są poniżej.

|Publisher | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 8.0.20191023) | Te obrazy to RHEL 8,0 LVM — obrazy partycjonowane połączone z standardowymi repozytoriami Red Hat.
|RedHat | RHEL | 8 — Gen2 | Połączone wartości wersji pomocniczej RHEL i Data publikacji (np. 8.0.20191024) | Są to obrazy z funkcją Hyper-V Generation 2 RHEL 8,0 LVM-obrazy połączone z standardowymi repozytoriami Red Hat. Więcej informacji o maszynach wirtualnych 2. generacji na platformie Azure jest dostępnych [tutaj](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="extended-update-support-eus"></a>Rozszerzona obsługa aktualizacji (EUS)
Od kwietnia 2019 obrazy RHEL są dostępne, które są domyślnie dołączone do repozytoriów pomocy technicznej aktualizacji (EUS). Więcej szczegółów na temat RHEL EUS są dostępne w [dokumentacji Red Hat](https://access.redhat.com/articles/rhel-eus).

Możliwe jest przełączenie do repozytoriów EUS i jest ono obsługiwane. Instrukcje dotyczące przełączenia maszyny wirtualnej do EUS i więcej szczegółów na temat EUS z punktu końcowego pomocy technicznej są dostępne [tutaj](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS nie są obsługiwane w dodatkach RHEL. Oznacza to, że w przypadku instalowania pakietu, który jest zazwyczaj dostępny w kanale RHEL Extras, nie będzie można wykonać tego działania w EUS. Cykl życia produktu Red Hat jest szczegółowo opisany w [tym miejscu](https://access.redhat.com/support/policy/updates/extras/).

### <a name="differentiating-between-regular-and-eus-images"></a>Rozróżnianie obrazów zwykłych i EUS.
Klienci, którzy chcą korzystać z obrazów dołączonych do repozytoriów EUS, powinni używać obrazu RHEL zawierającego numer pomocniczej RHEL w jednostce SKU.

Na przykład mogą zostać wyświetlone następujące dwa dostępne obrazy z RHEL 7,4:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
W takim przypadku `RedHat:RHEL:7.6:7.6.2019102813` będzie dołączany do repozytoriów EUS domyślnie (wartość SKU 7,4), a `RedHat:RHEL:7-LVM:7.6.2019062414` zostaną dołączone do repozytoriów innych niż EUS domyślnie (wartość SKU wynosząca 7-LVM).

Jeśli chcesz używać zwykłych repozytoriów (innych niż EUS), wdróż go przy użyciu obrazu, który nie zawiera pomocniczego numeru wersji w jednostce SKU.

#### <a name="rhel-images-with-eus"></a>Obrazy RHEL z EUS
Poniższa tabela ma zastosowanie do obrazów RHEL, które są połączone z repozytoriami EUS.

>[!NOTE]
> W czasie pisania tylko RHEL 7,4 i nowsze wersje pomocnicze mają EUS pomoc techniczną. EUS nie jest już obsługiwana dla RHEL < = 7,3.
>
> Więcej informacji na temat dostępności RHEL EUS można znaleźć [tutaj](https://access.redhat.com/support/policy/updates/errata).

Wersja pomocnicza |Przykład obrazu EUS              |Stan EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Obrazy opublikowane w kwietniu 2019 i nowszych będą domyślnie EUS|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Obrazy opublikowane czerwiec 2019 i nowsze będą domyślnie EUS |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrazy opublikowane z maja 2019 i nowsze będą domyślnie EUS  |
RHEL 8,0      |ND                            | Brak EUS dostępnych w witrynie Red Hat                               |





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
* Zapoznaj się z pełną listą [obrazów RHEL na platformie Azure](./redhat-imagelist.md).
* Więcej informacji na temat infrastruktury aktualizacji Red Hat platformy Azure można znaleźć [tutaj](https://aka.ms/rhui-update).
* Dowiedz się więcej o [ofercie BYOS RHEL](./byos.md).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
