---
title: DevTest Labs — pojęcia | Dokumentacja firmy Microsoft
description: Podstawowe informacje na temat usługi DevTest Labs i jak ją mogą znacznie ułatwiają tworzenie, zarządzanie i monitorowanie maszyn wirtualnych platformy Azure
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
ms.openlocfilehash: 08cae51da20b6093b284618de92c61aab4bf5b55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65508379"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs — pojęcia
## <a name="overview"></a>Omówienie
Poniższa lista zawiera kluczowe DevTest Labs — pojęcia i definicje:

## <a name="labs"></a>Laboratoria
Laboratorium jest infrastrukturę, która obejmuje grupę zasobów, takich jak maszyny wirtualne (VM), które umożliwia lepsze zarządzanie tymi zasobami przez określenie ograniczeń i przydziałów.

## <a name="virtual-machine"></a>Maszyna wirtualna
Maszynę wirtualną platformy Azure jest jednym z kilku typów [na żądanie, skalowalnych zasobów obliczeniowych](/azure/architecture/guide/technology-choices/compute-decision-tree) pozwalającą na platformie Azure. Maszyny wirtualne platformy Azure zapewniają elastyczność wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu potrzebnego do działa, mimo że nadal trzeba utrzymywać w maszynie Wirtualnej, wykonując pewnych zadań, takich jak konfigurowania, poprawiania i instalowanie oprogramowania, które jest uruchamiane na go.

[Omówienie maszyn wirtualnych Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) zapewnia możesz dowiedzieć się, jakie należy rozważyć przed Utwórz Maszynę wirtualną, jak utworzyć i jak można zarządzać.

## <a name="claimable-vm"></a>Przejęcia maszyn wirtualnych
Przejęcia maszyn wirtualnych platformy Azure jest maszyny wirtualnej, która jest dostępna do użycia przez dowolnego użytkownika laboratorium przy użyciu uprawnień. Administratorem laboratorium można przygotować maszyn wirtualnych przy użyciu określonych obrazy podstawowe i artefaktów i zapisywać je do współużytkowanej puli. Użytkownik laboratorium następnie może przyjąć działającą maszynę Wirtualną z puli, kiedy ich potrzebują przy użyciu tej określonej konfiguracji.

Maszynę Wirtualną, która jest przejęcia początkowo nie jest przypisany do żadnym określonym użytkownikiem, ale pojawią się na liście każdego użytkownika w obszarze "Przejęcia maszyny wirtualne". Po maszyny Wirtualnej jest zgłoszone przez użytkownika, zostanie przeniesiony do ich obszaru "Mój maszyny wirtualne" i nie jest już przejęcia przez innych użytkowników.

## <a name="environment"></a>Środowisko
W usłudze DevTest Labs środowiska odnosi się do kolekcji zasobów platformy Azure w laboratorium. [Ten wpis w blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) w tym artykule omówiono sposób tworzenia środowisk z wieloma Maszynami wirtualnymi za pomocą szablonów usługi Azure Resource Manager.

## <a name="base-images"></a>Obrazy podstawowe
Podstawowe obrazy są obrazy maszyny Wirtualnej z wszystkie narzędzia i ustawienia wstępnie zainstalowane i skonfigurowane, aby szybko utworzyć Maszynę wirtualną. Pobrania istniejących base i dodając artefakt, aby zainstalować agenta testowego można aprowizować maszynę Wirtualną. Tak, aby base mogą być używane bez konieczności ponownej instalacji agenta testowego w każdym aprowizacji maszyny Wirtualnej, jako podstawa można zapisać aprowizowanej maszyny Wirtualnej.

## <a name="artifacts"></a>Artefakty
Artefakty są używane do wdrażania i konfigurowania aplikacji po zaaprowizowaniu maszyny Wirtualnej. Artefaktami mogą być:

* Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — np. agenci, narzędzie Fiddler i Visual Studio.
* Akcje, które chcesz uruchomić na maszynie Wirtualnej — takimi jak klonowanie repozytorium.
* Aplikacje, które chcesz przetestować.

Artefakty są [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pliki w formacie JSON, które zawierają instrukcje, aby wykonać wdrożenie, a następnie zastosować konfigurację.

## <a name="artifact-repositories"></a>Repozytoria artefaktu
Repozytoria artefaktów są repozytoria git, w których artefakty są ewidencjonowane. Repozytoria artefaktów można dodać do wielu laboratoriów w Twojej organizacji, umożliwiając ponowne używanie i udostępnianie.

## <a name="formulas"></a>Formuły
Formuły, oprócz podstawowe obrazy zawierają mechanizm szybka aprowizacja maszyn wirtualnych. Formuły w usłudze DevTest Labs znajduje się lista domyślnych wartości właściwości, używany do tworzenia maszyn wirtualnych laboratorium.
Mogą być tworzone za pomocą formuł, maszyn wirtualnych przy użyciu tego samego zestawu właściwości — takich jak obraz podstawowy, rozmiar maszyny Wirtualnej, sieci wirtualnej i artefakty — bez konieczności określania tych właściwości za każdym razem. Podczas tworzenia maszyny Wirtualnej z formuły, wartości domyślne może służyć jako — lub zmodyfikowany.

## <a name="policies"></a>Zasady
Zasady pomóc w kontrolowaniu kosztów w środowisku laboratoryjnym. Na przykład można utworzyć zasad w celu automatycznego wyłączania maszyn wirtualnych na podstawie zdefiniowanego harmonogramu.

## <a name="caps"></a>Wielkości graniczne
Limity jest mechanizm umożliwiający minimalizowanie strat w środowisku laboratoryjnym. Na przykład można ustawić limit, aby ograniczyć liczbę maszyn wirtualnych, które mogą być tworzone dla każdego użytkownika lub w laboratorium.

## <a name="security-levels"></a>Poziomy zabezpieczeń
Zabezpieczenia dostępu jest określany przez based kontroli dostępu (RBAC). Aby zrozumieć, jak działa dostęp, warto poznać różnice między uprawnienia, roli i zakresu, zgodnie z definicją RBAC.

* Uprawnienia — uprawnienia są zdefiniowane dostępu do określonej akcji (np.-dostęp do odczytu do wszystkich maszyn wirtualnych).
* Role - rola to zestaw uprawnień, które mogą być grupowane i przypisane do użytkownika. Na przykład *właściciela subskrypcji* rola ma dostęp do wszystkich zasobów w ramach subskrypcji.
* Zakres - zakres jest poziom w hierarchii zasobu platformy Azure, takich jak grupy zasobów, jednym laboratorium lub całej subskrypcji.

W zakresie usługi DevTest Labs, istnieją dwa typy ról, aby zdefiniować uprawnienia użytkownika: właściciel laboratorium i użytkowników laboratorium.

* Właściciel laboratorium - właściciel laboratorium ma dostępu do żadnych zasobów w środowisku laboratoryjnym. W związku z tym właściciel laboratorium można modyfikować zasady, odczytu i zapisu wszelkie maszyny wirtualne, zmień sieć wirtualną i tak dalej.
* Użytkownik laboratorium —, użytkownik laboratorium można wyświetlić wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasad i sieciami wirtualnymi, ale nie można zmodyfikować zasady lub wszystkie maszyny wirtualne utworzone przez innych użytkowników.

Aby zobaczyć, jak utworzyć niestandardowe role w usłudze DevTest Labs, zapoznaj się z artykułem [udzielić użytkownikowi uprawnień do zasad określonych laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, jeśli użytkownik ma uprawnienia w określonym zakresie, automatycznie otrzymują te uprawnienia w każdym zakresie niższego poziomu wchodzących w skład. Na przykład jeśli użytkownik jest przypisany do roli właściciela subskrypcji, następnie mają dostęp do wszystkich zasobów w ramach subskrypcji, obejmujące wszystkie maszyny wirtualne, wszystkie sieci wirtualne i wszystkie labs. W związku z tym właściciel subskrypcji automatycznie dziedziczy roli właściciel laboratorium. Odwrotny jest true. Właściciel laboratorium ma dostęp do laboratorium, która jest zakresem niższym niż poziom subskrypcji. W związku z tym właściciel laboratorium nie będzie można zobaczyć maszyn wirtualnych lub sieciami wirtualnymi lub wszystkie zasoby, które znajdują się poza laboratorium.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Wszystkie omówione w tym artykule można skonfigurować przy użyciu szablonów usługi Azure Resource Manager, które umożliwiają definiowanie infrastruktury/Konfiguracja rozwiązania platformy Azure i wdrażać je w spójnym stanie.

[Omówienie struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) opisujący strukturę szablonu usługi Azure Resource Manager i właściwości, które są dostępne w różnych części szablonu.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
[Tworzenie laboratorium w usłudze DevTest Labs](devtest-lab-create-lab.md)
