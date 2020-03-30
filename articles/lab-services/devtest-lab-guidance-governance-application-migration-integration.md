---
title: Migracja i integracja aplikacji w laboratoriach DevTest w usłudze Azure
description: Ten artykuł zawiera wskazówki dotyczące nadzoru infrastruktury laboratoriów Azure DevTest Labs w kontekście migracji i integracji aplikacji.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644890"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Zarządzanie infrastrukturą laboratoriów testów dewiacyjnych platformy Azure — migracja i integracja aplikacji
Po utworzeniu środowiska laboratorium deweloperskiego/testowego należy zastanowić się nad następującymi pytaniami:

- Jak wykorzystać środowisko w swoim zespole projektowym?
- Jak zapewnić, że należy przestrzegać wszelkich wymaganych zasad organizacyjnych i zachować elastyczność, aby dodać wartość do aplikacji?

## <a name="azure-marketplace-images-vs-custom-images"></a>Obrazy portalu Azure Marketplace a obrazy niestandardowe

### <a name="question"></a>Pytanie
Kiedy należy używać obrazu portalu Azure Marketplace w porównaniu z własnym niestandardowym obrazem organizacyjnym?

### <a name="answer"></a>Odpowiedź
Usługa Azure Marketplace powinna być używana domyślnie, chyba że masz określone problemy lub wymagania organizacyjne. Niektóre typowe przykłady obejmują;

- Złożona konfiguracja oprogramowania, która wymaga, aby aplikacja została uwzględniona jako część obrazu podstawowego.
- Instalacja i konfiguracja aplikacji może potrwać wiele godzin, które nie są efektywne wykorzystanie czasu obliczeniowego, które mają być dodawane na obrazie portalu Azure Marketplace.
- Deweloperzy i testerzy wymagają szybkiego dostępu do maszyny wirtualnej i chcą zminimalizować czas instalacji nowej maszyny wirtualnej.
- Zgodność lub warunki regulacyjne (na przykład zasady zabezpieczeń), które muszą być obowiązują dla wszystkich komputerów.

Korzystanie z obrazów niestandardowych nie należy lekceważyć. Wprowadzają one dodatkową złożoność, ponieważ teraz musisz zarządzać plikami VHD dla tych podstawowych obrazów podstawowych. Należy również rutynowo załatać te obrazy podstawowe za pomocą aktualizacji oprogramowania. Te aktualizacje obejmują nowe aktualizacje systemu operacyjnego (OS) oraz wszelkie aktualizacje lub zmiany konfiguracji potrzebne dla samego pakietu oprogramowania.

## <a name="formula-vs-custom-image"></a>Formuła a obraz niestandardowy

### <a name="question"></a>Pytanie
Kiedy należy używać formuły a obrazu niestandardowego?

### <a name="answer"></a>Odpowiedź
Zazwyczaj czynnikiem decydującym w tym scenariuszu jest koszt i ponowne użycie.

Jeśli masz scenariusz, w którym wielu użytkowników /laboratoriów wymaga obrazu z dużą ilością oprogramowania na górze obrazu podstawowego, można zmniejszyć koszty, tworząc obraz niestandardowy. Oznacza to, że obraz jest tworzony raz. Skraca czas instalacji maszyny wirtualnej i koszty poniesione z powodu maszyny wirtualnej uruchomionej po zakończeniu instalacji.

Dodatkowym czynnikiem, na który należy zwrócić uwagę, jest częstotliwość zmian w pakiecie oprogramowania. Jeśli uruchamiasz codzienne kompilacje i wymagasz, aby oprogramowanie było na maszynach wirtualnych użytkowników, rozważ użycie formuły zamiast niestandardowego obrazu.

## <a name="use-custom-organizational-images"></a>Używanie niestandardowych obrazów organizacji

### <a name="question"></a>Pytanie
Jak skonfigurować łatwy do powtórzenia proces, aby przenieść niestandardowe obrazy organizacyjne do środowiska DevTest Labs?

### <a name="answer"></a>Odpowiedź
Zobacz [ten film na wzór fabryki obrazu](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Ten scenariusz jest scenariusz zaawansowany, a skrypty dostarczone są tylko przykładowe skrypty. Jeśli wymagane są jakiekolwiek zmiany, należy zarządzać i obsługiwać skrypty używane w danym środowisku.

Korzystanie z laboratoriów DevTest Labs do tworzenia niestandardowego potoku obrazów w potokach platformy Azure:

- [Wprowadzenie: Przygotowanie maszyn wirtualnych w ciągu kilku minut przez skonfigurowanie fabryki obrazów w laboratorium usługi Azure DevTest](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Fabryka obrazów – część 2! Konfigurowanie potoków platformy Azure i laboratorium fabrycznego w celu tworzenia maszyn wirtualnych](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Fabryka obrazów – część 3: Zapisywanie obrazów niestandardowych i dystrybucja w wielu laboratoriach](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Klip wideo: niestandardowa fabryka obrazów w laboratoriach devtest azure](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Wzorce konfigurowania konfiguracji sieci

### <a name="question"></a>Pytanie
Jak upewnić się, że maszyny programistyczne i testowe wirtualne nie mogą dotrzeć do publicznego Internetu? Czy istnieją jakieś zalecane wzorce do skonfigurowania konfiguracji sieci?

### <a name="answer"></a>Odpowiedź
Tak. Istnieją dwa aspekty do rozważenia - ruch przychodzący i wychodzący.

**Ruch przychodzący** — jeśli maszyna wirtualna nie ma publicznego adresu IP, nie można do niego uzyskać dostępu przez Internet. Typowym podejściem jest zapewnienie, że zasady na poziomie subskrypcji jest ustawiona, tak, że żaden użytkownik nie jest w stanie utworzyć publiczny adres IP.

**Ruch wychodzący** — jeśli chcesz zapobiec maszynom wirtualnym przechodzącym bezpośrednio do publicznego Internetu i wymusić ruch przez firmową zaporę, możesz kierować ruch lokalny za pośrednictwem trasy ekspresowej lub sieci VPN przy użyciu wymuszonego routingu.

> [!NOTE]
> Jeśli masz serwer proxy, który blokuje ruch bez ustawień serwera proxy, nie zapomnij dodać wyjątków do konta magazynu artefaktów laboratorium, .

Można również użyć sieciowych grup zabezpieczeń dla maszyn wirtualnych lub podsieci. Ten krok dodaje dodatkową warstwę ochrony, aby umożliwić / zablokować ruch.

## <a name="new-vs-existing-virtual-network"></a>Nowa a istniejąca sieć wirtualna

### <a name="question"></a>Pytanie
Kiedy należy utworzyć nową sieć wirtualną dla mojego środowiska DevTest Labs w porównaniu z istniejącą siecią wirtualną?

### <a name="answer"></a>Odpowiedź
Jeśli maszyny wirtualne muszą wchodzić w interakcje z istniejącą infrastrukturą, należy rozważyć użycie istniejącej sieci wirtualnej w środowisku DevTest Labs. Ponadto jeśli używasz usługi ExpressRoute, można zminimalizować ilość sieci wirtualnych / podsieci, tak aby nie fragmentować przestrzeni adresowej IP, która zostanie przypisana do użycia w subskrypcjach. Należy również rozważyć użycie wzorca komunikacji równorzędnej sieci wirtualnej w tym miejscu (model szprychy koncentratora). Takie podejście umożliwia komunikację vnet/podsieć między subskrypcjami w danym regionie, chociaż komunikacja równorzędna między regionami jest nadchodzącą funkcją w sieci platformy Azure.

W przeciwnym razie każde środowisko DevTest Labs może mieć własną sieć wirtualną. Należy jednak pamiętać, że istnieją [ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) dotyczące liczby sieci wirtualnych na subskrypcję. Domyślna kwota wynosi 50, chociaż ten limit można podnieść do 100.

## <a name="shared-public-or-private-ip"></a>Współdzielony, publiczny lub prywatny adres IP

### <a name="question"></a>Pytanie
Kiedy należy używać współdzielonego adresu IP a publicznego adresu IP a prywatnego adresu IP?

### <a name="answer"></a>Odpowiedź
Jeśli korzystasz z sieci VPN typu lokacja-lokacja lub trasy ekspresowej, rozważ użycie prywatnych adresów IP, aby twoje maszyny były dostępne za pośrednictwem sieci wewnętrznej i niedostępne za pośrednictwem publicznego Internetu.

> [!NOTE]
> Właściciele laboratoriów mogą zmienić te zasady podsieci, aby upewnić się, że nikt przypadkowo nie tworzy publicznych adresów IP dla swoich maszyn wirtualnych. Właściciel subskrypcji należy utworzyć zasady subskrypcji uniemożliwiające publiczne wiadomości IP z tworzone.

Podczas korzystania z udostępnionych publicznych adresów IP maszyny wirtualne w laboratorium współużytkuje publiczny adres IP. Takie podejście może być przydatne, gdy trzeba uniknąć naruszenia limitów publicznych adresów IP dla danej subskrypcji.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limity liczby maszyn wirtualnych na użytkownika lub laboratorium

### <a name="question"></a>Pytanie
Czy istnieje reguła dotycząca liczby maszyn wirtualnych, które należy ustawić na użytkownika lub na laboratorium?

### <a name="answer"></a>Odpowiedź
Biorąc pod uwagę liczbę maszyn wirtualnych na użytkownika lub laboratorium, istnieją trzy główne problemy:

- **Całkowity koszt,** który zespół może wydać na zasoby w laboratorium. Łatwo jest rozkręcić wiele maszyn. Aby kontrolować koszty, jednym z mechanizmów jest ograniczenie liczby maszyn wirtualnych na użytkownika i/lub na laboratorium
- Na całkowitą liczbę maszyn wirtualnych w laboratorium mają wpływ dostępne [przydziały na poziomie subskrypcji.](../azure-resource-manager/management/azure-subscription-service-limits.md) Jednym z górnych limitów jest 800 grup zasobów na subskrypcję. DevTest Labs tworzy obecnie nową grupę zasobów dla każdej maszyny Wirtualnej (chyba że są używane udostępnione publiczne adresy IP). Jeśli istnieje 10 laboratoriów w ramach subskrypcji, laboratoria mogą zmieścić około 79 maszyn wirtualnych w każdym laboratorium (800 górny limit — 10 grup zasobów dla 10 laboratoriów) = 79 maszyn wirtualnych na laboratorium.
- Jeśli laboratorium jest połączone z lokalnymi za pośrednictwem trasy ekspresowej (na przykład), istnieją **zdefiniowane przestrzenie adresów IP dostępne** dla sieci wirtualnej/podsieci. Aby upewnić się, że maszyny wirtualne w laboratorium nie nie mogą zostać utworzone (błąd: nie można uzyskać adresu IP), właściciele laboratorium mogą określić maksymalną liczbę maszyn wirtualnych na laboratorium wyrównaną z dostępną przestrzenią adresową IP.

## <a name="use-resource-manager-templates"></a>Używanie szablonów usługi Resource Manager

### <a name="question"></a>Pytanie
Jak używać szablonów Menedżera zasobów w moim środowisku DevTest Labs?

### <a name="answer"></a>Odpowiedź
Wdrażanie szablonów Menedżera zasobów w środowisku Laboratoriów DevTest przy użyciu kroków wymienionych w [funkcji środowiska w laboratoriach DevTest](devtest-lab-test-env.md) artykułu. Zasadniczo można sprawdzić szablony Menedżera zasobów w repozytorium Git (repozytorium platformy Azure lub GitHub) i dodać [prywatne repozytorium szablonów](devtest-lab-test-env.md) do laboratorium.

Ten scenariusz może nie być przydatne, jeśli używasz DevTest Labs do obsługi maszyn deweloperskich, ale może być przydatne, jeśli budujesz środowisko przejściowe, który jest reprezentatywny dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub na użytkownika opcja ogranicza tylko liczbę maszyn natywnie utworzonych w samym laboratorium, a nie przez żadne środowiska (szablony Menedżera zasobów).

## <a name="next-steps"></a>Następne kroki
Zobacz [Używanie środowisk w devtest labs](devtest-lab-test-env.md).