---
title: Zarządzanie infrastrukturą usługi Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące ładu infrastruktury usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 75ce5d6a88b5398bd010cc363b4241bc90068f55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193003"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Zarządzanie infrastrukturą usługi Azure DevTest Labs — migrację aplikacji i integracji
Po ustanowieniu środowiska laboratoryjnego projektowania i testowania, należy wziąć pod uwagę na następujące pytania:

- Jak korzystać środowiska w ramach zespołu projektu
- Jak zapewnić wykonaj wszelkie wymagane zasady organizacyjne i zachować elastyczność, aby dodać wartość do aplikacji?

## <a name="azure-marketplace-images-vs-custom-images"></a>Obrazy platformy Azure Marketplace i niestandardowych obrazów

### <a name="question"></a>Pytanie
Kiedy używać obrazu witryny Azure Marketplace a własnego niestandardowego obrazu organizacji?

### <a name="answer"></a>Odpowiedź
Portal Azure Marketplace należy używać domyślnie, chyba że masz określone problemy lub wymagań organizacyjnych. Typowe przykłady obejmują;

- Ustawienia skomplikowanego oprogramowania, wymagającego aplikację do uwzględnienia jako część obrazu podstawowego.
- Instalacja i Konfiguracja aplikacji może potrwać wiele godzin, które nie są efektywne wykorzystanie czasu obliczeń, które ma zostać dodany w obrazie w portalu Azure Marketplace.
- Deweloperom i testerom szybko wymagają dostępu do maszyny wirtualnej i chcesz zminimalizować czas instalacji nowej maszyny wirtualnej.
- Zgodność lub regulacji prawnych (na przykład zasady zabezpieczeń), które muszą być spełnione dla wszystkich maszyn.

Przy użyciu niestandardowych obrazów nie powinny być uwzględniane niewielkim stopniu. Z ich używaniem wiążą dodatkowej złożoności, ponieważ trzeba zarządzać plikami wirtualnego dysku twardego te obrazy podstawowe. Należy również regularnie patch te obrazy podstawowe z aktualizacjami oprogramowania. Te aktualizacje obejmują nowe aktualizacje systemu operacyjnego (OS) i wszelkie aktualizacje lub zmiany konfiguracji potrzebne do pakietu oprogramowania, sam.

## <a name="formula-vs-custom-image"></a>Formuły, a obraz niestandardowy

### <a name="question"></a>Pytanie
Kiedy używać formuły, a obrazu niestandardowego?

### <a name="answer"></a>Odpowiedź
Zazwyczaj decydującym czynnikiem w tym scenariuszu jest kosztowne i ponowne użycie.

W przypadku scenariusza, gdy wielu użytkowników/labs wymagają obrazu z dużą ilością oprogramowania dodaną do obrazu podstawowego można zmniejszyć koszt, tworząc obraz niestandardowy. Oznacza to, że po utworzeniu obrazu. Zmniejsza to czas instalacji maszyny wirtualnej oraz koszt ze względu na maszynie wirtualnej podczas przeprowadzana jest konfiguracja.

Dodatkowy czynnik należy pamiętać, jest jednak częstotliwość zmian do pakietu oprogramowania. Po uruchomieniu codziennie kompilacje i wymagają, że oprogramowanie do na maszynach wirtualnych użytkowników, należy wziąć pod uwagę przy użyciu formuły zamiast obrazu niestandardowego.

## <a name="use-custom-organizational-images"></a>Korzystanie z niestandardowych obrazów organizacji

### <a name="question"></a>Pytanie
Jak mogę skonfigurować łatwe powtarzalnego procesu do wprowadzenia Moje niestandardowe obrazy organizacji w środowisku usługi DevTest Labs?

### <a name="answer"></a>Odpowiedź
Zobacz [tego wideo na wzorcu fabrycznie obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). W tym scenariuszu jest to zaawansowany scenariusz, a podane skrypty są przykładowe skrypty. Jeśli są wymagane zmiany, musisz zarządzać i obsługiwać skrypty używane w danym środowisku.

Tworzenie potoku obrazu niestandardowego w potokach platformy Azure przy użyciu usługi DevTest Labs:

- [Wprowadzenie: Przygotowywanie maszyn wirtualnych w ciągu kilku minut, konfigurując fabrykę obrazu w usłudze Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Fabryka obrazu — część 2! Konfigurowanie potoków usługi Azure i fabryki laboratorium do tworzenia maszyn wirtualnych](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Fabryka obrazu — część 3: Zapisywanie niestandardowych obrazów i dystrybucji do wielu Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Wideo: Fabryka obrazu niestandardowego za pomocą usługi Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Wzorce do ustawiania konfiguracji sieci

### <a name="question"></a>Pytanie
Jak upewnij się, tworzenia i testowania maszyny wirtualne są nie można nawiązać połączenia z publicznego Internetu? Czy istnieją wszystkie zalecane wzorców do ustawiania konfiguracji sieci?

### <a name="answer"></a>Odpowiedź
Tak. Istnieją dwie kwestie należy wziąć pod uwagę — ruch przychodzący i wychodzący.

**Ruch przychodzący** — Jeśli maszyna wirtualna ma publiczny adres IP, wówczas nie można nawiązać połączenia przez internet. Typowym podejściem jest zapewnienie, że ustawiono zasady na poziomie subskrypcji, taki sposób, że żaden użytkownik nie jest w stanie utworzyć publiczny adres IP.

**Ruch wychodzący** — Jeśli chcesz uniemożliwić maszyn wirtualnych, przechodząc bezpośrednio do publicznej sieci internet i wymuszają ruch za pośrednictwem zapory firmowej, a następnie może kierować ruch w środowisku lokalnym za pośrednictwem expressroute lub sieci VPN, za pomocą wymuszają routingu.

> [!NOTE]
> Jeśli masz serwer proxy, która blokuje ruch bez ustawienia serwera proxy, nie zapomnij dodać wyjątki na konto magazynu artefaktów laboratorium.

Można również użyć grup zabezpieczeń sieci dla maszyn wirtualnych lub podsieci. Ten krok powoduje dodanie dodatkową warstwę ochrony, aby umożliwić / blokować ruch.

## <a name="new-vs-existing-virtual-network"></a>Nowość w porównaniu do istniejącej sieci wirtualnej

### <a name="question"></a>Pytanie
Kiedy utworzyć nową sieć wirtualną dla środowiska Moje DevTest Labs, a za pomocą istniejącej sieci wirtualnej?

### <a name="answer"></a>Odpowiedź
Jeśli Twoje maszyny wirtualne muszą wchodzić w interakcje z istniejącą infrastrukturą, następnie należy rozważyć przy użyciu istniejącej sieci wirtualnej w środowisku laboratorium. Ponadto, jeśli korzystasz z usługi ExpressRoute, możesz chcieć minimalizację liczby sieci wirtualnych / podsieci, aby nie fragmentu przestrzenią adresów IP, pobiera przypisaną do użycia w ramach subskrypcji. Należy również rozważyć użycie sieci wirtualnej komunikacji równorzędnej wzorzec tutaj (piasty i szprych model). Takie podejście umożliwia komunikację sieci wirtualnej/podsieci w subskrypcjach w danym regionie, mimo że komunikacji równorzędnej w różnych regionach jest funkcją wchodzenia w górę w sieci platformy Azure.

W przeciwnym razie każde środowisko usługi DevTest Labs może mieć własną sieć wirtualną. Jednak należy pamiętać, że istnieją [limity](../azure-subscription-service-limits.md) liczby sieci wirtualnych na subskrypcję. Wartość domyślna wynosi 50, chociaż ten limit może wzrosnąć do 100.

## <a name="shared-public-or-private-ip"></a>Udostępnionych, publicznych lub prywatnych adresów IP

### <a name="question"></a>Pytanie
Kiedy używać udostępnionego IP a publicznym adresem IP a prywatny adres IP?

### <a name="answer"></a>Odpowiedź
Jeśli używasz sieci VPN lokacja lokacja lub Expressroute, należy wziąć pod uwagę przy użyciu prywatnych adresów IP, maszynach są dostępne za pośrednictwem sieci wewnętrznej i niedostępne za pośrednictwem publicznej sieci internet.

> [!NOTE]
> Laboratorium właścicieli można zmienić te zasady podsieci, upewnij się, nikt przypadkowo tworzy publiczne adresy IP dla maszyn wirtualnych. Właściciel subskrypcji należy utworzyć zasady subskrypcji, uniemożliwiając tworzona publicznych adresów IP.

Korzystając z udostępnionych, publicznych adresów IP, maszyny wirtualne w laboratorium udostępniać publicznego adresu IP. Takie podejście może być przydatne, gdy potrzebujesz w celu uniknięcia przekroczenia limitów dla publicznych adresów IP w ramach danej subskrypcji.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limity liczby maszyn wirtualnych dla poszczególnych użytkowników lub laboratorium

### <a name="question"></a>Pytanie
Czy istnieje reguła pod względem liczby maszyn wirtualnych, należy ustawić dla poszczególnych użytkowników lub na laboratorium?

### <a name="answer"></a>Odpowiedź
Podczas wybierania liczby maszyn wirtualnych, dla określonego użytkownika lub na laboratorium, istnieją trzy główne kwestie:

- **Całkowity koszt** , zespół może przeznaczyć na zasoby w środowisku laboratoryjnym. To proste nad ułatwieniem wdrażania wiele maszyn. Aby kontrolować koszty, jeden mechanizm jest ograniczenie liczby maszyn wirtualnych na użytkownika i/lub na laboratorium
- Całkowita liczba maszyn wirtualnych w laboratorium jest wpływ [przydziały poziomu subskrypcji](../azure-subscription-service-limits.md) dostępne. Jednym z górne limity jest 800 grup zasobów na subskrypcję. DevTest Labs obecnie tworzy nową grupę zasobów dla każdej maszyny Wirtualnej (chyba że udostępnionego publiczne adresy IP są używane). W przypadku 10 laboratoriów w ramach subskrypcji, labs można dopasować około 79 maszyn wirtualnych w każdym laboratorium (800 górny limit — 10 grup zasobów do 10 laboratoriów, samodzielnie) = 79 maszyn wirtualnych na laboratorium.
- Jeśli laboratorium jest podłączony do sieci lokalnej za pośrednictwem Express Route (na przykład), istnieją **zdefiniowane przestrzeni adresów IP** dla sieci wirtualnej/podsieci. Aby upewnić się, że nie zakończy się niepowodzeniem maszynami wirtualnymi w laboratorium ma zostać utworzony (błąd: nie można pobrać adresu IP), właściciele laboratorium można określić maksymalny maszyn wirtualnych na laboratorium, dostosowane do obszaru adresów IP dostępne.

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager

### <a name="question"></a>Pytanie
Jak użyć szablonów usługi Resource Manager w mojej usługi DevTest Labs środowiska?

### <a name="answer"></a>Odpowiedź
Wdrażanie szablonów usługi Resource Manager w środowisku laboratorium, wykonując kroki opisane w [funkcji środowisk w usłudze DevTest labs](devtest-lab-test-env.md) artykułu. Po prostu zaznacz szablonów usługi Resource Manager do repozytorium Git (repozytoriów platformy Azure lub GitHub) i Dodaj [prywatnym repozytorium szablonów](devtest-lab-test-env.md) do laboratorium.

Ten scenariusz może okazać się przydatne, jeśli używasz usługi DevTest Labs do komputerów-hostów rozwoju, ale mogą być przydatne, jeśli tworzysz środowisko przejściowe, który jest reprezentatywny dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub względem poszczególnych opcji użytkownika tylko ogranicza liczbę maszyn natywnie utworzonych w laboratorium, a nie przez dowolnego środowiska (szablony usługi Resource Manager).

## <a name="next-steps"></a>Kolejne kroki
Zobacz [korzystanie ze środowisk w usłudze DevTest Labs](devtest-lab-test-env.md).