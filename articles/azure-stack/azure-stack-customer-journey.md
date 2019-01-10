---
title: Centrum danych Azure Stack — przewodnik integracji | Dokumentacja firmy Microsoft
description: Dowiedz się, czego można oczekiwać na pomyślne wdrożenie lokalne usługi Azure Stack w centrum danych.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: asganesh
ms.openlocfilehash: 554fd9c32c9831d45a40c62a871e3a8d5f8d7cb9
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191755"
---
# <a name="azure-stack-datacenter-integration"></a>Integracja z usługą Azure datacenter stosu

W tym artykule opisano obsługę klientów usługi Azure Stack end-to-end z zakupu za pośrednictwem zintegrowane rozwiązanie do pomyślnego wdrożenia na miejscu przez dostawcę rozwiązania. Dzięki tym informacjom do jej obsługi ułatwiają realizację swoją podróż i pomóc w ustaleniu oczekiwań, na jakie, jako klient usługi Azure Stack, należy się spodziewać podczas integrowania usługi Azure Stack w centrum danych.

Jako klient korzystający z usługi Azure Stack powinna przewidywać następujące fazy integrację centrum danych:

|     |Faza planowania|Kolejność przetwarzania|Przed wdrożeniem|Fabryka procesu|Dostarczanie sprzętu|Wdrożenia u klienta|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Zapewnienie przedsprzedażna pomoc skontaktuj się z partnerem.|Przygotuj licencjonowania oprogramowania i kontrakty, zgodnie z potrzebami.|Zapewnia narzędzia wymagane do zbierania wymagania dotyczące integracji w centrum danych i dokumentacji do klienta.|Dostarczanie najnowszych kompilacji według planu bazowego i narzędzie łańcucha aktualizacji w erze miesięcznych.|ND|Inżynierowie pomocy technicznej firmy Microsoft, która pomaga w żadnych problemów z wdrażaniem.|
|**Partner**|Zaleca się opcji rozwiązania, w zależności od wymagań klientów.<br><br>Zaproponuj weryfikacji koncepcji (POC), jeśli to konieczne.<br><br>Ustanawiania relacji biznesowych.<br><br>Decyzje dotyczące poziomu pomocy technicznej.|Przygotuj stosowne umowy z klientem.<br><br>Utwórz zamówienie zakupu klienta.<br><br>Zdecyduj się na osi czasu dostarczania.<br><br>Połącz z klientów z firmą Microsoft, jeśli to konieczne.|Udostępnij niezbędne szkolenie w celu zapewnienia zrozumienie wszystkie wymagania wstępne dotyczące wdrażania i centrum danych opcji integracji klienta.<br><br>Pomocy klientowi z weryfikacją zebranych danych w celu zapewnienia kompletności i dokładności.|Zastosuj Ostatnia kompilacja zweryfikowanych linii bazowej.<br><br>Zastosuj wymagany zestawu narzędzi firmy Microsoft do wdrażania.|Dostarczaj sprzętu do lokacji klienta.|Wdrożenie obsługiwane przez inżyniera ds.<br><br>Stojakiem i podstawą stosu.<br><br>Wdrażanie hosta (HLH) cyklu życia sprzętu.<br><br>Wdrożenie usługi Azure Stack.<br><br>Dostarcz do klienta.|
|**Odbiorcy**|Opisz zamierzonych zastosowań i określenie wymagań w zakresie.|Określ model rozliczeń za usługę do używania, przejrzyj i Zatwierdź zamówienia.|Wypełnij arkusz wdrażania i upewnij się, że wszystkie wymagania wstępne dotyczące wdrażania niespełnienia i gotowe do wdrożenia.|ND|Przygotowanie centrum danych, zapewniając wszystkie wymagane usługi power i chłodzenie, łącznością obramowania i inne wymagania integracji wymagane centrum danych na miejscu.|Będą dostępne podczas wdrażania, aby podać poświadczenia subskrypcji i pomocy technicznej w przypadku pytań na podstawie udostępnionych danych.|
| | | | | | | |


## <a name="planning-phase"></a>Faza planowania
Faza planowania jest, gdy firmy Microsoft lub partnerem rozwiązania usługi Azure Stack, pomoże ocenić i zrozumienie potrzeb w celu ustalenia, czy odpowiednie rozwiązanie dla Ciebie w usłudze Azure Stack:

Są pomocne w podjęciu decyzji od następujących czynników:

-   Jest usługa Azure Stack właściwe rozwiązanie dla Twojej organizacji?

-   Jakie rozwiązania rozmiar będą potrzebne?

-   Jakiego typu modelu rozliczeń i licencjonowania będzie działać dla Twojej organizacji?

-   Jakie są zasilania i chłodzenia wymagania?

Aby upewnić się, jak rozwiązanie sprzętowe najlepiej będzie zaspokajać potrzeby, [Planisty wydajności usługi Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) może służyć do pomocy w przypadku zakupu z góry planujesz określić odpowiednią pojemność i konfiguracji sprzętu usługi Azure Stack rozwiązanie.

Arkusz kalkulacyjny jest *nie* przeznaczony do użycia w zastępstwie własne badania i analizowania rozwiązania sprzętowe, najlepiej swoich potrzeb. Podczas planowania wdrożenia usługi Azure Stack, należy także przejrzeć [zagadnienia dotyczące integracji ogólne datacenter](azure-stack-datacenter-integration.md) dla usługi Azure Stack zintegrowane systemy.

## <a name="order-process-phase"></a>Kolejność przetwarzania fazy
Na tym etapie wiele swoje pytania w odniesieniu do możliwości będzie mieć odpowiedzi. Teraz, że wszystko jest gotowe do zatwierdzenia z zakupem usługi Azure Stack, a po zarejestrowaniu się wszystkie wymagane kontrakty i zamówień zakupu, pojawi się prośba do dostarczania danych wymagania dotyczące integracji do dostawcy rozwiązań.

## <a name="pre-deployment-phase"></a>Faza wstępnego wdrożenia
W tej fazie należy zdecydować, jak zintegrować usługę Azure Stack w centrum danych. Aby ułatwić ten proces, Microsoft, we współpracy z dostawcami rozwiązań Podsumowując szablonu wymagania do zbierania potrzebnych do zaplanowania wdrożenia zintegrowanego systemu w danym środowisku.

[Zagadnienia dotyczące integracji ogólne datacenter](azure-stack-datacenter-integration.md) artykuł zawiera informacje ułatwiające ukończyć szablon, znane jako arkusz wdrażania. 

> [!IMPORTANT]
> Na tym etapie należy badają i decyzję przed porządkowanie rozwiązania wszystkie wstępnie wymagane informacje. Należy pamiętać, że ten krok jest czasochłonne i wymaga koordynacji i zbieranie danych z wielu dyscyplin w Twojej organizacji. 

W fazie przed wdrożeniem należy zdecydować, od następujących czynników:

- **Dostawca usługi Azure Stack połączenia modelu i tożsamości**. Możesz wdrożyć usługę Azure Stack, albo [połączonych z Internetem (i na platformie Azure) lub odłączona](azure-stack-connection-models.md). Aby uzyskać największe korzyści z usługi Azure Stack, hybrydowe scenariusze, w tym należy wdrożyć połączony z platformą Azure. Wybór usługi Active Directory Federation Services (AD FS) lub Azure Active Directory (Azure AD) jest jednorazowe decyzji, które należy wykonać w czasie wdrażania. **Nie można zmienić to później bez konieczności ponownego wdrażania całego systemu**.

- **Model licencjonowania**. Opcje modelu licencjonowania, można wybrać z zależą od typu wdrożenia, które będą mieć. Wybranego dostawcy tożsamości nie ma żadnego wpływu na maszyny wirtualne dzierżawcy lub system obsługi tożsamości i kont, których używają.
    - Klienci, którzy znajdują się w [odłączony wdrożenia](azure-stack-disconnected-deployment.md) mieć tylko jedną z opcji: rozliczanie na podstawie pojemności.

    - Klienci, którzy znajdują się w [połączone wdrożenia](azure-stack-connected-deployment.md) można wybrać na podstawie pojemności rozliczeń i płatności jako — rzeczywiste użycie. Rozliczanie na podstawie pojemności wymaga subskrypcji platformy Azure Enterprise Agreement (EA) dla rejestracji. Jest to niezbędne do rejestracji, co zapewnia dostępność elementów w witrynie Marketplace za pośrednictwem subskrypcji platformy Azure.

- **Integracja z siecią**. [Integracja z siecią](azure-stack-network.md) ma kluczowe znaczenie podczas wdrażania, działania i zarządzania systemy usługi Azure Stack. Istnieje kilka kwestii, które są przekazywane do zapewnienia rozwiązania usługi Azure Stack będą odporne i ma wysoką dostępność infrastruktury fizycznej do obsługi swoich operacji.

- **Zapory integracji**. Zalecane jest, [za pomocą zapory](azure-stack-firewall.md) ułatwia bezpieczne usługi Azure Stack. Zapory może zapobiec ataków DDOS, Wykrywanie nieautoryzowanego dostępu i inspekcji zawartości. Jednak należy zauważyć, że może stać się wąskim gardłem przepływności dla usług Azure storage.


- **Wymagania dotyczące certyfikatów**. Bardzo ważne jest, wszystkie [wymaganych certyfikatów](azure-stack-pki-certs.md) dostępnych *wcześniejsze* na inżyniera ds otrzymywanych z centrum danych do wdrożenia.

Gdy wszystkie wstępnie wymagane informacje są zbierane przez arkusz wdrażania, dostawcy rozwiązania będzie uruchamiał proces fabryki, na podstawie danych zebranych w celu zapewnienia pomyślnej integracji usługi Azure Stack w centrum danych.

## <a name="hardware-delivery-phase"></a>Faza dostarczania sprzętu
Dostawcy rozwiązań będą pracy na planowanie, gdy rozwiązanie zostanie dotrze do Twojej funkcji. Gdy odbierane i umieścić w miejscu, należy zaplanować czas przy użyciu dostawcy rozwiązania, aby mieć inżyniera pochodzą u klienta, aby wykonać wdrożenie usługi Azure Stack.

Jest **kluczowym** że wszystkie wstępnie wymagane dane, jest zablokowany i *przed u klienta inżynier dociera do wdrożenia rozwiązania*.

-   Wszystkie certyfikaty należy zakupić i gotowe.

-   Nazwa domeny, należy podejmować.

-   Wszystkie parametry integracji sieci sfinalizowaniu i jest zgodna z zostały udostępnione przez z dostawcą rozwiązania.

> [!TIP]
> Jeśli jakiekolwiek informacje uległ zmianie, upewnij się, do komunikowania się zmiany przy użyciu dostawcy rozwiązania, zanim zaplanowane rzeczywiste wdrożenie.

## <a name="onsite-deployment-phase"></a>Faza wdrożenia u klienta
Aby wdrożyć usługę Azure Stack, inżynier ds przez dostawcę rozwiązania sprzętowe będą musiały mieć obecna, aby uruchamiał wdrożenia. W celu zapewnienia pomyślnego wdrożenia, upewnij się, że wszystkie informacje podane przez arkusz wdrażania nie została zmieniona. 

Oto co Spodziewaj od inżyniera ds podczas wdrażania:

- Sprawdź wszystkie obramowania i okablowania łączność upewnij się, rozwiązanie jest prawidłowo Podsumowując i spełnia Twoje wymagania
- Konfigurowanie rozwiązania HLH (Host cyklu życia sprzętu)
- Sprawdź, upewnij się, że ustawienia kontrolera BMC, systemu BIOS i sieci są poprawne.
- Upewnij się, że oprogramowanie układowe wszystkich składników jest w najnowszej wersji zatwierdzonych przez rozwiązanie
- Rozpocząć wdrażanie

> [!NOTE]
> Procedury wdrażania, zadając je inżynierom DS na miejscu może zająć około jeden tydzień roboczy.

## <a name="post-integration-phase"></a>Faza po integracji
Kilka kroków muszą być wykonywane przez partnera, zanim rozwiązanie jest przekazywane do klienta w fazie po integracji. W tej fazie sprawdzania poprawności jest ważne, aby upewnić się, że system jest wdrażana i działają prawidłowo. 

Dostępne są następujące akcje, które powinny zostać podjęte przez partnera OEM:

-   [Uruchom test azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

-   [Rejestracja za pomocą platformy Azure](azure-stack-registration.md)

-   [Syndykacja witryny Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

-   Pliki konfiguracji przełącznika kopii zapasowej

-   Usuń Menedżer DVM

-   Przygotowywanie podsumowania klienta do wdrożenia

-   [Sprawdź dostępność aktualizacji, aby upewnić się, że oprogramowanie rozwiązanie jest aktualizowane do najnowszej wersji](azure-stack-updates.md)

Istnieje kilka kroków, które są wymagane lub opcjonalne w zależności od typu instalacji.

-   Jeśli wdrożenie zostało zakończone, za pomocą [usług AD FS](azure-stack-integrate-identity.md), następnie sygnatury będą musieli można zintegrować z klientem usługi Azure Stack w własnych usług AD FS.

  > [!NOTE]
  > Ten krok jest odpowiedzialny za klienta, mimo że partner może opcjonalnie oferty usług w tym celu.

-   Integracja z istniejącego systemu monitorowania z odpowiednim partnerem.

    -   [Integracji programu System Center Operations Manager](azure-stack-integrate-monitor.md) obsługuje również zarządzanie flotą możliwości.

    -   [Integracja Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Ogólną oś czasu

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Pomoc techniczna
Usługa Azure Stack umożliwia spójne z platformą Azure, środowisko zintegrowanej pomocy technicznej, który obejmuje pełny cykl życia systemu. Do zapewnienia pełnej obsługi usługi Azure Stack, zintegrowanych systemów, klienci muszą dwóch umów pomocy technicznej. jeden z firmą Microsoft (lub ich dostawcy rozwiązań w chmurze) do obsługi usług platformy Azure i jednego dostawcy sprzętu do obsługi systemów. Środowisko zintegrowanej pomocy technicznej udostępnia skoordynowanego eskalację i rozwiązanie problemu, tak, aby klienci uzyskują techniczną niezależnie od tego, w której będą wywoływać najpierw. Dla klientów, którzy już mają Premier Azure — Standard / techniczna ProDirect albo jako partnera firmy Microsoft, pomoc techniczna dla oprogramowania Azure Stack jest dostępna.

Środowisko zintegrowanej pomocy technicznej sprawia, że korzystanie z programu Exchange w przypadku mechanizm transferu dwukierunkowej przypadki pomocy technicznej i wielkość aktualizacji między firmami Microsoft i partnerów sprzętowych. Microsoft Azure Stack będą się odbywać [zasad nowoczesnego cyklu życia](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [zagadnienia dotyczące integracji ogólne datacenter](azure-stack-datacenter-integration.md).
