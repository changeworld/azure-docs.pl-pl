---
title: Koncepcje Laboratoriów DevTest | Dokumenty firmy Microsoft
description: Poznaj podstawowe pojęcia devtest labs i jak można łatwo tworzyć, zarządzać i monitorować maszyny wirtualne platformy Azure
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
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428914"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs — pojęcia
## <a name="overview"></a>Omówienie
Poniższa lista zawiera kluczowe pojęcia i definicje devtest labs:

## <a name="labs"></a>Laboratoria
Laboratorium to infrastruktura, która obejmuje grupę zasobów, takich jak maszyny wirtualne (maszyny wirtualne), która umożliwia lepsze zarządzanie tymi zasobami przez określenie limitów i przydziałów.

## <a name="virtual-machine"></a>Maszyna wirtualna
Maszyna wirtualna platformy Azure jest jednym z kilku typów [skalowalnych zasobów obliczeniowych na żądanie,](/azure/architecture/guide/technology-choices/compute-decision-tree) które oferuje platforma Azure. Maszyny wirtualne platformy Azure zapewniają elastyczność wirtualizacji bez konieczności kupowania i utrzymywania fizycznego sprzętu, który ją uruchamia, chociaż nadal trzeba utrzymywać maszynę wirtualną, wykonując pewne zadania, takie jak konfigurowanie, łatanie i instalowanie oprogramowania, które działa na go.

[Omówienie maszyn wirtualnych systemu Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) zawiera informacje o tym, co należy wziąć pod uwagę przed utworzeniem maszyny wirtualnej, jak ją utworzyć i jak nią zarządzać.

## <a name="claimable-vm"></a>Maszyna wirtualna z roszczeniem
Maszyna wirtualna z roszczeniem platformy Azure to maszyna wirtualna, która jest dostępna do użycia przez dowolnego użytkownika laboratorium z uprawnieniami. Administrator laboratorium może przygotować maszyny wirtualne z określonymi obrazami podstawowymi i artefaktami i zapisać je w puli udostępnionej. Użytkownik laboratorium może następnie zgłosić działającą maszynę wirtualną z puli, gdy potrzebują jej przy tej określonej konfiguracji.

Maszyna wirtualna, która jest godną oświadczenia, nie jest początkowo przypisana do określonego użytkownika, ale pojawi się na liście każdego użytkownika w obszarze "Maszyny wirtualne podlegające żądaniu". Po odebraniu maszyny Wirtualnej przez użytkownika, jest przenoszony do obszaru "Moje maszyny wirtualne" i nie jest już zgłaszany przez innego użytkownika.

## <a name="environment"></a>Środowisko
W laboratoriach DevTest, środowisko odwołuje się do kolekcji zasobów platformy Azure w laboratorium. [W tym wpisie w blogu](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) omówiono sposób tworzenia środowisk wielu maszyn wirtualnych z szablonów usługi Azure Resource Manager.

## <a name="base-images"></a>Obrazy podstawowe
Obrazy podstawowe to obrazy maszyn wirtualnych ze wszystkimi narzędziami i ustawieniami preinstalowanymi i skonfigurowanym do szybkiego tworzenia maszyny Wirtualnej. Można aprowizować maszynę wirtualną, wybierając istniejącą bazę i dodając artefakt, aby zainstalować agenta testowego. Następnie można zapisać aprowizowanych maszyn wirtualnych jako podstawy, dzięki czemu baza może być używana bez konieczności ponownej instalacji agenta testowego dla każdej inicjowania obsługi administracyjnej maszyny Wirtualnej.

## <a name="artifacts"></a>Artefakty
Artefakty są używane do wdrażania i konfigurowania aplikacji po zainicjowaniu obsługi administracyjnej maszyny Wirtualnej. Artefakty mogą być:

* Narzędzia, które chcesz zainstalować na maszynie Wirtualnej — takie jak agenci, Fiddler i Visual Studio.
* Akcje, które chcesz uruchomić na maszynie Wirtualnej — takie jak klonowanie repozytorium.
* Aplikacje, które chcesz przetestować.

Artefakty to pliki JSON [usługi Azure Resource Manager,](../azure-resource-manager/management/overview.md) które zawierają instrukcje dotyczące wdrażania i stosowania konfiguracji.

## <a name="artifact-repositories"></a>Repozytoria artefaktów
Repozytoria artefaktów to repozytoria git, w których zaewidencjonowane są artefakty. Repozytoria artefaktów można dodać do wielu laboratoriów w organizacji, umożliwiając ponowne użycie i udostępnianie.

## <a name="formulas"></a>Formuły
Formuły, oprócz obrazów podstawowych, zapewniają mechanizm szybkiego inicjowania obsługi administracyjnej maszyn wirtualnych. Formuła w DevTest Labs to lista wartości właściwości domyślnych używanych do tworzenia maszyny Wirtualnej w laboratorium.
Za pomocą formuł maszyny wirtualne z tym samym zestawem właściwości — takich jak obraz podstawowy, rozmiar maszyny wirtualnej, sieć wirtualna i artefakty — można tworzyć bez konieczności określania tych właściwości za każdym razem. Podczas tworzenia maszyny Wirtualnej z formuły wartości domyślne mogą być używane jako — jest lub modyfikowane.

## <a name="policies"></a>Zasady
Zasady pomagają w kontrolowaniu kosztów w laboratorium. Na przykład można utworzyć zasadę, aby automatycznie zamykać maszyny wirtualne na podstawie zdefiniowanego harmonogramu.

## <a name="caps"></a>Czapki
Czapki to mechanizm minimalizująjący ilość odpadów w laboratorium. Na przykład można ustawić limit, aby ograniczyć liczbę maszyn wirtualnych, które mogą być tworzone na użytkownika lub w laboratorium.

## <a name="security-levels"></a>Poziomy zabezpieczeń
Dostęp do zabezpieczeń jest określany przez kontrolę dostępu opartą na rolach platformy Azure (RBAC). Aby zrozumieć, jak działa dostęp, pomaga zrozumieć różnice między uprawnieniem, rolę i zakres zdefiniowany przez RBAC.

* Uprawnienie — uprawnienie jest zdefiniowanym dostępem do określonej akcji (np. dostęp do odczytu do wszystkich maszyn wirtualnych).
* Rola — rola to zestaw uprawnień, które można zgrupować i przypisać do użytkownika. Na przykład rola *właściciela subskrypcji* ma dostęp do wszystkich zasobów w ramach subskrypcji.
* Zakres — zakres jest poziomem w hierarchii zasobu platformy Azure, takiego jak grupa zasobów, pojedyncze laboratorium lub cała subskrypcja.

W zakresie DevTest Labs istnieją dwa typy ról do definiowania uprawnień użytkownika: właściciel laboratorium i użytkownik laboratorium.

* Właściciel laboratorium — właściciel laboratorium ma dostęp do wszelkich zasobów w laboratorium. W związku z tym właściciel laboratorium można modyfikować zasady, odczytywać i zapisywać maszyny wirtualne, zmienić sieć wirtualną i tak dalej.
* Użytkownik laboratorium — użytkownik laboratorium może wyświetlać wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasady i sieci wirtualne, ale nie może modyfikować zasad ani żadnych maszyn wirtualnych utworzonych przez innych użytkowników.

Aby zobaczyć, jak utworzyć role niestandardowe w laboratoriach DevTest, zapoznaj się z tym [artykułem, Udziel uprawnień użytkowników określonym zasadom laboratoryjnym.](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)

Ponieważ zakresy są hierarchiczne, gdy użytkownik ma uprawnienia w określonym zakresie, są one automatycznie przyznawane te uprawnienia w każdym zakresie niższego poziomu objęte. Na przykład jeśli użytkownik jest przypisany do roli właściciela subskrypcji, a następnie mają dostęp do wszystkich zasobów w ramach subskrypcji, które obejmują wszystkie maszyny wirtualne, wszystkie sieci wirtualne i wszystkie laboratoria. W związku z tym właściciel subskrypcji automatycznie dziedziczy rolę właściciela laboratorium. Jednak nie jest odwrotnie. Właściciel laboratorium ma dostęp do laboratorium, który jest niższy zakres niż poziom subskrypcji. W związku z tym właściciel laboratorium nie będzie mógł zobaczyć maszyn wirtualnych lub sieci wirtualnych ani żadnych zasobów, które znajdują się poza laboratorium.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager
Wszystkie pojęcia omówione w tym artykule można skonfigurować przy użyciu szablonów usługi Azure Resource Manager, które umożliwiają definiowanie infrastruktury/konfiguracji rozwiązania platformy Azure i wielokrotne wdrażanie go w spójnym stanie.

[Zrozumienie struktury i składni szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) opisuje strukturę szablonu usługi Azure Resource Manager i właściwości, które są dostępne w różnych sekcjach szablonu.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
[Tworzenie laboratorium w DevTest Labs](devtest-lab-create-lab.md)
