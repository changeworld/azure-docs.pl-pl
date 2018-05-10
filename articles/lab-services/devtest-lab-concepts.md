---
title: Pojęcia dotyczące DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się więcej podstawowych pojęć dotyczących DevTest Labs i jak może być łatwe do tworzenia, zarządzania i monitorowania maszyn wirtualnych platformy Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ce0c51a3b97c57095a336128919c5e27f860905b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="devtest-labs-concepts"></a>DevTest Labs — pojęcia
## <a name="overview"></a>Przegląd
Poniższa lista zawiera podstawowe pojęcia DevTest Labs i definicje:

## <a name="labs"></a>Laboratoria
Laboratorium jest infrastrukturę, która obejmuje grupy zasobów, takich jak maszyn wirtualnych (VM), które umożliwiają lepsze zarządzanie tych zasobów, określając ograniczenia i limity przydziału.

## <a name="virtual-machine"></a>Maszyna wirtualna
Maszyny Wirtualnej platformy Azure jest jednym z kilku typów [na żądanie, skalowalnych zasobów obliczeniowych](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) udostępniającej Azure. Maszyny wirtualne platformy Azure zapewniają elastyczność wirtualizacji bez konieczności kupowania i utrzymywania sprzętem fizycznym, który uruchamia go, mimo że można nadal należy obsługiwać maszynę Wirtualną za wykonywanie określonych zadań, takich jak konfigurowanie, poprawki i instalacji oprogramowania, które działa na go.

[Omówienie maszyn wirtualnych systemu Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) zapewnia informacje o kwestiach należy rozważyć przed Utwórz Maszynę wirtualną, jak utworzyć i jak zarządzać.

## <a name="claimable-vm"></a>Claimable maszyny Wirtualnej
Claimable maszyny Wirtualnej platformy Azure jest maszyny wirtualnej, która jest dostępna do użycia przez każdy laboratorium użytkownik z uprawnieniami. Administrator laboratorium można przygotować maszyn wirtualnych z określonych obrazów podstawowej i artefakty i zapisywać je do współużytkowanej puli. Użytkownik laboratorium następnie mogą rezerwować pracy maszyny Wirtualnej z puli, jeśli wymagany jest jeden z tej konkretnej konfiguracji.

Maszynę Wirtualną, która jest claimable początkowo nie jest przypisana do żadnych określonego użytkownika, ale będą widoczne w obszarze "Claimable maszyny wirtualnej" na liście każdego użytkownika. Po maszyny Wirtualnej jest określona przez użytkownika, jest przenoszony do ich obszarze "Moje maszyny wirtualne" i nie jest już claimable przez innego użytkownika.

## <a name="environment"></a>Środowisko
W usłudze DevTest Labs środowisku odwołuje się do kolekcji zasobów platformy Azure w laboratorium. [Ten wpis w blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) omówiono sposób tworzenia środowisk wielu maszyn wirtualnych z szablonów usługi Azure Resource Manager.

## <a name="base-images"></a>Obrazy podstawowe
Podstawowy obrazy są obrazy maszyny Wirtualnej ze wszystkimi narzędzia i ustawienia wstępnie zainstalowane i skonfigurowane, aby szybko utworzyć Maszynę wirtualną. Maszyny Wirtualnej można udostępnić pobrania istniejących podstawowej i dodając artefaktu, aby zainstalować agenta testowego. Następnie można zapisać zainicjowana VM jako podstawa tak, aby podstawowym może być używany bez konieczności ponownej instalacji agenta testowego do każdego inicjowania obsługi maszyny wirtualnej.

## <a name="artifacts"></a>Artefakty
Artefakty służą do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi maszyny Wirtualnej. Artefaktami mogą być:

* Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, program Fiddler i program Visual Studio.
* Akcje, które chcesz uruchomić na maszynie Wirtualnej — np. klonowanie repozytorium.
* Aplikacje, które chcesz przetestować.

Artefakty [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pliki JSON, które zawierają instrukcje, aby wykonać wdrożenie, a następnie zastosować konfigurację.

## <a name="artifact-repositories"></a>Repozytoria artefaktów
Repozytoria artefaktu są repozytoria git, gdzie artefakty są sprawdzane w. Repozytoria artefaktu można dodać do wielu laboratoriów w Twojej organizacji, udostępniania i włączanie ponownego użycia.

## <a name="formulas"></a>Formuły
Formuły, oprócz obrazy podstawowe mechanizm umożliwiający szybkiej obsługi maszyny Wirtualnej. Formuły w usłudze DevTest Labs znajduje się lista domyślnych wartości właściwości używany do tworzenia maszyny Wirtualnej laboratorium.
Mogą być tworzone z formułami, maszyn wirtualnych przy użyciu tych samych właściwości — takie jak podstawowy obraz, rozmiar maszyny Wirtualnej, sieci wirtualnej i artefakty — bez konieczności określenie tych właściwości za każdym razem. Podczas tworzenia maszyny Wirtualnej z formuły, wartości domyślne mogą pełnić- lub zmodyfikowany.

## <a name="policies"></a>Zasady
Pomoc dotycząca zasad kontrolowania kosztów w laboratorium. Na przykład można utworzyć zasad, aby automatycznie zamknąć maszyn wirtualnych na podstawie zdefiniowanego harmonogramu.

## <a name="caps"></a>Cap
Włączony klawisz Caps jest mechanizm, aby zminimalizować odpady w laboratorium. Na przykład można ustawić zakończenia, aby ograniczyć liczbę maszyn wirtualnych, które mogą być tworzone dla poszczególnych użytkowników lub w laboratorium.

## <a name="security-levels"></a>Poziomy zabezpieczeń
Dostęp zabezpieczeń jest określany przez based kontroli dostępu (RBAC). Aby zrozumieć, jak działa dostęp, warto poznać różnice między uprawnienia roli i zakresu, zgodnie z definicją RBAC.

* Uprawnienie - uprawnienie jest zdefiniowane dostępu do określonej akcji (np. dostęp do odczytu do wszystkich maszyn wirtualnych).
* Rola: Rola to zestaw uprawnień, które mogą być grupowane i przypisana do użytkownika. Na przykład *właściciel subskrypcji* rola ma dostęp do wszystkich zasobów w ramach subskrypcji.
* Zakres - zakres jest poziom w hierarchii zasobów platformy Azure, takich jak grupy zasobów, pojedynczy laboratorium lub całej subskrypcji.

W zakresie DevTest Labs, istnieją dwa typy ról do definiowania uprawnień użytkownika: laboratorium właściciela i użytkownika laboratorium.

* Właściciela laboratorium — właściciela laboratorium ma dostęp do wszystkich zasobów w ramach laboratorium. W związku z tym właściciela laboratorium można modyfikować zasady, zapisywania i odczytywania żadnej maszyny wirtualnej, zmień sieci wirtualnej, a itd.
* Użytkownik laboratorium —, użytkownik laboratorium mogą wyświetlać wszystkie zasoby laboratorium, takich jak maszyny wirtualne, zasady i sieci wirtualne, ale nie można zmodyfikować zasady lub żadnych maszyn wirtualnych utworzonych przez innych użytkowników.

Aby zobaczyć, jak utworzyć niestandardowe role w usłudze DevTest Labs, zapoznaj się z artykułem [udzielić użytkownikowi uprawnień do laboratorium określonych zasad](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, jeśli użytkownik ma uprawnienia w zakresie niektórych, automatycznie otrzymują tych uprawnień w zakresie niższego poziomu, co razem. Na przykład jeśli użytkownik jest przypisany do roli właściciela subskrypcji, następnie mają dostęp do wszystkich zasobów w ramach subskrypcji, które obejmują wszystkie maszyny wirtualne, wszystkie sieci wirtualne i laboratoriów wszystkie. W związku z tym właściciela subskrypcji automatycznie przejmuje rolę właściciela laboratorium. Jednak przeciwieństwem nie jest prawdziwe. Właściciela laboratorium ma dostęp do laboratorium jest zakresem niższym niż poziom subskrypcji. W związku z tym właściciela laboratorium nie będzie mógł wyświetlić maszyn wirtualnych lub sieciami wirtualnymi lub wszystkie zasoby, które znajdują się poza laboratorium.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Wszystkie omówione w tym artykule można skonfigurować przy użyciu szablonów usługi Azure Resource Manager, które umożliwiają definiowanie infrastruktury/konfiguracji rozwiązania Azure i wielokrotnie wdrażać w spójnym stanie.

[Struktura i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) opisano strukturę szablonu usługi Azure Resource Manager i właściwości, które są dostępne w różnych części szablonu.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie laboratorium w usłudze DevTest Labs](devtest-lab-create-lab.md)
