---
title: Obrazy red hat enterprise z systemem Linux na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obrazach systemu Red Hat Enterprise linux na platformie Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239875"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Przegląd obrazów Red Hat Enterprise Linux

W tym artykule opisano dostępne obrazy Red Hat Enterprise Linux (RHEL) w portalu Azure Marketplace i zasady dotyczące ich nazewnictwa i przechowywania.

Aby uzyskać informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata). Aby uzyskać szczegółowe informacje o cenach, zobacz [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Obrazy RHEL obecnie dostępne w witrynie Azure Marketplace obsługują modele licencjonowania bring-your-own-subscription (BYOS) lub pay-as-you-go. [Korzyści z użycia hybrydowego platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) i dynamiczne przełączanie między systemami BYOS a licencjonowaniem płatności zgodnie z rzeczywistym użyciem nie są obsługiwane. Aby przełączyć tryb licencjonowania, należy ponownie wdrożyć maszynę wirtualną z odpowiedniego obrazu.

>[!NOTE]
> W przypadku jakichkolwiek problemów związanych z obrazami RHEL w portalu Azure Marketplace, złóż bilet pomocy technicznej w firmie Microsoft.

## <a name="view-images-available-in-azure"></a>Wyświetlanie obrazów dostępnych na platformie Azure

Podczas wyszukiwania "Red Hat" w portalu Azure Marketplace lub podczas tworzenia zasobu w interfejsie użytkownika witryny azure portal, zobaczysz tylko podzbiór wszystkich dostępnych obrazów RHEL. Zawsze można uzyskać pełny zestaw dostępnych obrazów maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell i interfejsu API.

Aby wyświetlić pełny zestaw dostępnych obrazów red hat na platformie Azure, uruchom następujące polecenie:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Konwencja nazewnictwa

Obrazy maszyn wirtualnych na platformie Azure są zorganizowane według wydawcy, oferty, jednostki SKU i wersji. Kombinacja Publisher:Offer:SKU:Version jest obraz urn i jednoznacznie identyfikuje obraz, który ma być używany.

Na przykład `RedHat:RHEL:8-LVM:8.1.20200318` odnosi się do RHEL 8.1 LVM partycjonowane obrazu utworzonego w dniu 18 marca 2020.

Przykład tworzenia maszyny Wirtualnej RHEL 8.1 jest pokazany tutaj.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>"Najnowszy" pseudonim

Interfejs API rest platformy Azure umożliwia użycie moniker "najnowsze" dla wersji zamiast określonej wersji. Korzystając z "najnowszych" przepisów, najnowszy dostępny obraz dla danego wydawcy, oferty i jednostki SKU.

Na przykład `RedHat:RHEL:8-LVM:latest` odnosi się do najnowszego obrazu rhel 8 rodziny LVM partycjonowane dostępne.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Ogólnie rzecz biorąc porównanie wersji w celu określenia najnowszych następuje zgodnie z regułami [CompareTo metody](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
To porównanie wersji obrazu odbywa się przez porównanie wartości jako [Version](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) obiektu, a nie jako ciąg.

## <a name="rhel-6-image-types"></a>RHEL 6 typów obrazów

W przypadku obrazów RHEL 6.x typy obrazów są wyświetlane w poniższej tabeli.

|Wydawca | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (na przykład 6.9) | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 6.9.2018010506) | Wszystkie standardowe obrazy RHEL 6.x są zgodne z tą konwencją.
|RedHat | rhel-byos | rhel-raw69 | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 6.9.20181023) | Ten obraz jest obrazem RHEL 6.9 BYOS.
|RedHat | RHEL | APLIKACJE RHEL-SAP | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 6.8.2017053118) | Ten obraz jest obrazem RHEL 6.8 for SAP Applications. Jest uprawniony do dostępu do repozytoriów aplikacji SAP i bazowych repozytoriów RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 6.7.2017053121) | Ten obraz jest obrazem RHEL 6.7 dla SAP HANA. Jest uprawniony do dostępu do repozytoriów SAP HANA i bazowych repozytoriów RHEL.

## <a name="rhel-7-image-types"></a>RHEL 7 typów obrazów

W przypadku obrazów RHEL 7.x istnieje kilka różnych typów obrazów. W poniższej tabeli przedstawiono różne zestawy obrazów, które oferujemy. Aby wyświetlić pełną listę, użyj `az vm image list --publisher redhat --all`polecenia interfejsu wiersza polecenia platformy Azure .

>[!NOTE]
> O ile nie wskazano inaczej, wszystkie obrazy są LVM podzielonych na partycje i połączyć się ze zwykłymi repozytoria RHEL. Oznacza to, że repozytoria nie są rozszerzoną obsługą aktualizacji (EUS) i nie są usługami aktualizacji dla SAP (E4S). Idąc dalej, przechodzimy do publikowania tylko obrazów podzielonych na partycje LVM, ale są otwarci na opinie na temat tej decyzji. Aby uzyskać więcej informacji na temat rozszerzonej obsługi aktualizacji i usług aktualizacji dla sap, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).

|Wydawca | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | Wersja pomocnicza (na przykład 7.6) | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019102813) | Zdjęcia opublikowane przed kwietniem 2019 r. są dołączone do standardowych repozytoriów RHEL. Zdjęcia opublikowane po kwietniu 2019 r. są dołączone do repozytoriów EUS Red Hat, aby umożliwić blokowanie wersji określonej wersji pomocniczej. Klienci, którzy chcą regularnych repozytoriów należy użyć obrazów, które zawierają 7-LVM lub 7-RAW w wartości jednostki SKU (szczegóły postępuj). Obrazy RHEL 7.7 i nowsze są podzielone na partycje LVM. Wszystkie inne obrazy w tej kategorii są nieprzetworzone na partycje.
|RedHat | RHEL | 7-RAW | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019102813) | Te obrazy są nieprzetworzone na partycje (na przykład nie dodano żadnych woluminów logicznych).
|RedHat | RHEL | 7-RAW-CI | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019072418) | Te obrazy są nieprzetworzone partycjonowane (na przykład nie dodano woluminów logicznych) i używają init w chmurze do inicjowania obsługi administracyjnej.
|RedHat | RHEL | 7-LVM | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019062414) | Te obrazy są LVM podzielonych na partycje.
|RedHat | rhel-byos | rhel-{lvm,surowy} | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.7.20190819) | Te obrazy są obrazy RHEL 7 BYOS. Nie są one dołączone do żadnych repozytoriów i nie pobierają opłaty za premię RHEL. Jeśli jesteś zainteresowany obrazami RHEL BYOS, [poproś o dostęp](https://aka.ms/rhel-byos). Wartości jednostki SKU kończą się wersją pomocniczą i oznaczają, czy obraz jest nieprzetworzony, czy LVM podzielony na partycje. Na przykład wartość jednostki SKU rhel-lvm77 wskazuje obraz RHEL 7.7 podzielony na partycje LVM.
|RedHat | RHEL | RHEL-SAP | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019071300) | Te obrazy są RHEL dla obrazów SAP. Mają oni prawo dostępu do repozytoriów SAP HANA i aplikacji, a także repozytoriów RHEL E4S. Rozliczenia obejmują premię RHEL i premię SAP oprócz podstawowej opłaty obliczeniowej.
|RedHat | RHEL | RHEL-SAP-HA | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019062320) | Te obrazy są RHEL dla SAP z wysokiej dostępności i aktualizacji usług obrazów. Mają oni prawo dostępu do repozytoriów SAP HANA i aplikacji oraz repozytoriów wysokiej dostępności, a także repozytoriów RHEL E4S. Rozliczenia obejmują premię RHEL, premię SAP i premię za wysoką dostępność oprócz podstawowej opłaty obliczeniowej.
|RedHat | RHEL | RHEL-HA | Wartości łączone w wersji pomocniczej RHEL i data publikacji (na przykład 7.6.2019062019) | Te obrazy są obrazy RHEL, które są również uprawnione do dostępu do dodatku Wysokiej dostępności. Pobierają nieco więcej na szczycie RHEL i podstawowej opłaty obliczeniowej ze względu na premię dodatkową o wysokiej dostępności.
|RedHat | RHEL | APLIKACJE RHEL-SAP | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.3.2017053118) | Te obrazy są nieaktualne, ponieważ repozytoria APLIKACJI SAP i SAP HANA zostały połączone w repozytoria SAP. Te obrazy są RHEL dla obrazów aplikacji SAP. Mają prawo dostępu do repozytoriów aplikacji SAP i bazowych repozytoriów RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 7.3.2018051421) | Te obrazy są nieaktualne, ponieważ repozytoria APLIKACJI SAP i SAP HANA zostały połączone w repozytoria SAP. Te obrazy są RHEL dla obrazów SAP HANA. Mają prawo dostępu do repozytoriów SAP HANA i bazowych repozytoriów RHEL.

## <a name="rhel-8-image-types"></a>RHEL 8 typów obrazów

Szczegóły dotyczące typów obrazów RHEL 8 znajdują się poniżej.

|Wydawca | Oferta | Wartość jednostki SKU | Wersja | Szczegóły
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 8.0.20191023) | Te obrazy są RHEL 8 LVM partycjonowane obrazy podłączone do standardowych repozytoriów Red Hat.
|RedHat | RHEL | 8-gen2 | Wartości łączone wersji pomocniczej RHEL i data publikacji (na przykład 8.0.20191024) | Te obrazy są Hyper-V Generacji 2 RHEL 8 LVM partycjonowane obrazy podłączone do standardowych repozytoriów Red Hat. Aby uzyskać więcej informacji na temat maszyn wirtualnych generacji 2 na platformie Azure, zobacz [Pomoc techniczna dla maszyn wirtualnych generacji 2 na platformie Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

## <a name="rhel-longer-support-add-ons"></a>RHEL dłuższe dodatki do obsługi

### <a name="extended-update-support"></a>Rozszerzona obsługa aktualizacji

Od kwietnia 2019 r. dostępne są obrazy RHEL, które są domyślnie dołączone do repozytoriów EUS. Więcej informacji na temat RHEL EUS można znaleźć w [dokumentacji Red Hat.](https://access.redhat.com/articles/rhel-eus)

Przejście do repozytoriów EUS jest możliwe i jest obsługiwane. Aby uzyskać instrukcje dotyczące przełączania maszyny wirtualnej na EUS i więcej informacji na temat dat zakończenia eksploatacji pomocy technicznej EUS, zobacz [RHEL EUS i maszyny wirtualne RHEL blokujące wersje.](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)

>[!NOTE]
> EUS nie jest obsługiwany w dodatkach RHEL. Oznacza to, że jeśli zainstalujesz pakiet, który jest zwykle dostępny na kanale RHEL Extras, nie będziesz mógł tego zrobić podczas eus. Aby uzyskać więcej informacji na temat cyklu życia produktu Red Hat Extras, zobacz [Cykl życia dodatków Red Hat Enterprise Linux Extras](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Rozróżnienie między obrazami zwykłymi i EUS

Klienci, którzy chcą używać obrazów dołączonych do repozytoriów EUS, powinni użyć obrazu RHEL zawierającego pomocniczy numer wersji RHEL w jednostce SKU.

Na przykład mogą zostać wyświetlene dwa następujące obrazy RHEL 7.4.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

W takim `RedHat:RHEL:7.6:7.6.2019102813` przypadku jest domyślnie dołączony do repozytoriów EUS. Wartość jednostki SKU wynosi 7,4. I `RedHat:RHEL:7-LVM:7.6.2019062414` jest domyślnie dołączony do repozytoriów spoza EUS. Wartość jednostki SKU to 7-LVM.

Aby używać zwykłych repozytoriów (innych niż EUS), należy użyć obrazu, który nie zawiera pomocniczego numeru wersji w jednostce SKU.

#### <a name="rhel-images-with-eus"></a>Obrazy RHEL z EUS

Informacje w poniższej tabeli dotyczą obrazów RHEL połączonych z repozytoriami EUS.

>[!NOTE]
> W momencie pisania tego tekstu tylko wersje RHEL 7.4 i nowsze wersje pomocnicze mają wsparcie EUS. EUS nie jest już wspierany dla RHEL <=7,3.
>
> Aby uzyskać więcej informacji na temat dostępności RHEL EUS, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).

Wersja pomocnicza |Przykład obrazu EUS              |Status EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Obrazy opublikowane w kwietniu 2019 r. i później są domyślnie EUS.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Obrazy opublikowane w czerwcu 2019 r. i później są domyślnie EUS. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Obrazy opublikowane w maju 2019 r. i później są domyślnie EUS. |
RHEL 8.0      |Nie dotyczy                            | Z red hat nie jest dostępny żaden EUS.                               |

### <a name="update-services-for-sap"></a>Usługi aktualizacji dla sap

Najnowsze obrazy RHEL dla SAP będą połączone z subskrypcjami Update Services for SAP Solutions (E4S). Aby uzyskać więcej informacji na temat usługi E4S, zobacz [dokumentację](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)Red Hat .

#### <a name="rhel-images-with-e4s"></a>Obrazy RHEL z E4S

Obrazy z następujących ofert utworzonych po grudniu 2019 r. są połączone z repozytoriami E4S:

* RHEL-SAP (RHEL dla SAP)
* RHEL-SAP-HA (RHEL dla SAP z usługami o wysokiej dostępności i aktualizacji)

## <a name="other-available-offers-and-skus"></a>Inne dostępne oferty i jednostki SKU

Pełna lista dostępnych ofert i jednostek SKU może zawierać dodatkowe obrazy poza tym, co jest wymienione w poprzedniej tabeli. Może to być na przykład `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Te oferty mogą służyć do zapewnienia obsługi określonych rozwiązań w portalu marketplace. Mogą one zostać opublikowane w celu wyświetlania podglądu i testowania. Mogą one zostać zmienione lub usunięte w dowolnym momencie bez ostrzeżenia. Nie używaj ich, chyba że ich obecność jest publicznie udokumentowana przez firmę Microsoft lub Red Hat.

## <a name="publishing-policy"></a>Zasady publikowania

Microsoft i Red Hat aktualizują obrazy w miarę wydania nowych wersji pomocniczych, zgodnie z wymaganiami dotyczącymi określonych typowych luk w zabezpieczeniach i ekspozycji (CVE) lub sporadycznych zmian konfiguracji lub aktualizacji. Dokładamy wszelkich starań, aby udostępnić zaktualizowane obrazy tak szybko, jak to możliwe w ciągu trzech dni roboczych od wydania lub dostępności poprawki CVE.

Aktualizujemy tylko bieżącą wersję pomocniczą w danej rodzinie obrazów. Wraz z wydaniem nowszej wersji pomocniczej przestajemy aktualizować starszą wersję pomocniczą. Na przykład wraz z wydaniem RHEL 7.6 obrazy RHEL 7.5 nie są już aktualizowane.

>[!NOTE]
> Aktywne maszyny wirtualne platformy Azure aprowizowane z obrazów płatności zgodnie z rzeczywistym użyciem platformy Azure są połączone z platformą Azure RHUI i mogą odbierać aktualizacje i poprawki, gdy tylko zostaną wydane przez Red Hat i zreplikowane na platformie Azure RHUI. Czas jest zwykle mniej niż 24 godziny po oficjalnym wydaniu przez Red Hat. Te maszyny wirtualne nie wymagają nowego opublikowanego obrazu do uzyskiwania aktualizacji. Klienci mają pełną kontrolę nad tym, kiedy zainicjować aktualizację.

## <a name="image-retention-policy"></a>Zasady przechowywania obrazów

Bieżące zasady są do przechowywania wszystkich wcześniej opublikowanych obrazów. Zastrzegamy sobie prawo do usuwania obrazów, o których wiadomo, że powodują jakiekolwiek problemy. Na przykład obrazy z niepoprawną konfiguracją z powodu kolejnych aktualizacji platformy lub składnika mogą zostać usunięte. Obrazy, które mogą zostać usunięte, są zgodne z bieżącymi zasadami portalu Azure Marketplace, aby dostarczać powiadomienia do 30 dni przed usunięciem obrazu.

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę obrazów RHEL na platformie Azure, zobacz [Obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure.](./redhat-imagelist.md)
* Aby dowiedzieć się więcej o infrastrukturze aktualizacji usługi Azure Red Hat Update, zobacz [Red Hat Update Infrastructure for on-demand RHEL VMs in Azure](https://aka.ms/rhui-update).
* Aby dowiedzieć się więcej o ofercie RHEL BYOS, zobacz [Red Hat Enterprise Linux bring-your-own-subscription Gold Images in Azure](./byos.md).
* Aby uzyskać informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).
