---
title: Tworzenie kopii zapasowych i odzyskiwanie po awarii w programie Azure Australia
description: Tworzenie kopii zapasowych i odzyskiwanie po awarii w Microsoft Azure dla instytucji rządowych w Australii, które odnoszą się do programu ASD Essentials 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571525"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii w programie Azure Australia

Tworzenie kopii zapasowych i planów odzyskiwania po awarii z infrastrukturą pomocniczą w miejscu ma kluczowe znaczenie dla wszystkich organizacji. Znaczenie posiadania rozwiązania do tworzenia kopii zapasowych zostało wyróżnione przez uwzględnienie w [Security Center cybernetycznymiie australijskiego 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure udostępnia dwie usługi, które zapewniają odporność: Azure Backup i Azure Site Recovery. Te usługi umożliwiają ochronę danych, zarówno lokalnych, jak i w chmurze, w różnych scenariuszach projektowania. Azure Backup i Azure Site Recovery korzystają ze wspólnego magazynu i zasobów zarządzania: Magazyn Recovery Services platformy Azure. Ten magazyn służy do zarządzania, monitorowania i segregowania Azure Backup i Azure Site Recovery danych.

Ten artykuł zawiera szczegółowe informacje na temat najważniejszych elementów projektowych dotyczących wdrażania Azure Backup i Azure Site Recovery zgodnie z informacjami o systemie [kontroli zabezpieczeń (ASD](https://acsc.gov.au/infosec/ism/index.htm)) w firmie The australiaal.

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup przypomina tradycyjne lokalne rozwiązanie do tworzenia kopii zapasowych i zapewnia możliwość tworzenia kopii zapasowych zarówno lokalnych, jak i hostowanych danych platformy Azure. Azure Backup można używać do tworzenia kopii zapasowych następujących typów danych na platformie Azure:

* Pliki i foldery
* Obsługiwane systemy operacyjne Windows i Linux hostowane w systemie:
  * Funkcje hypervisor funkcji Hyper-V i programu VMWare
  * Sprzęt fizyczny
* Obsługiwane aplikacje firmy Microsoft

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replikuje obciążenia składające się z jednej maszyny wirtualnej lub aplikacji wielowarstwowych. Replikacja jest obsługiwana w środowisku lokalnym na platformie Azure, między regionami platformy Azure lub między lokalizacjami lokalnymi zorganizowanymi przez Azure Site Recovery. Lokalne maszyny wirtualne można replikować na platformę Azure lub do obsługiwanej lokalnej funkcji hypervisor. Po skonfigurowaniu Azure Site Recovery organizuje replikację, tryb failover i powrót po awarii.

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

W przypadku implementowania rozwiązania do tworzenia kopii zapasowej lub odzyskiwania po awarii należy wziąć pod uwagę proponowane rozwiązanie:

* Zakres i ilość danych do przechwycenia
* Jak długo dane zostaną zachowane
* Jak bezpieczne przechowywanie danych i zarządzanie nimi
* Lokalizacje geograficzne, w których są przechowywane dane
* Rutynowe procedury testowania systemu

ISM zawiera wskazówki dotyczące zagadnień związanych z zabezpieczeniami, które należy wykonać podczas projektowania rozwiązania. Microsoft Azure zapewnia, że należy rozwiązać te zagadnienia dotyczące zabezpieczeń.

### <a name="data-sovereignty"></a>Niezależność danych

Organizacje muszą zapewnić utrzymanie suwerenności danych podczas korzystania z lokalizacji magazynu w chmurze. Azure Policy zapewnia sposób ograniczenia dozwolonych lokalizacji, w których można utworzyć zasób platformy Azure. Wbudowane Azure Policy "dozwolone lokalizacje" są używane w celu zapewnienia, że wszystkie zasoby platformy Azure utworzone w ramach zakresu przypisanego Azure Policy mogą być tworzone tylko w określonych lokalizacjach geograficznych.

Azure Policy elementami ograniczenia geograficznego dla zasobów platformy Azure są:

* allowedLocations
* allowedSingleLocation

Te zasady umożliwiają administratorom platformy Azure ograniczenie tworzenia do listy wyznaczonych lokalizacji lub nawet jako pojedynczej lokalizacji geograficznej.

### <a name="redundant-and-geographically-dispersed-storage"></a>Magazyn nadmiarowy i rozproszony geograficznie

Dane przechowywane w magazynie usługi Azure Recovery są zawsze przechowywane w magazynie nadmiarowym. Domyślnie magazyn usługi Recovery Service używa magazynu geograficznie nadmiarowego platformy Azure (GRS). Dane przechowywane za pomocą GRS są replikowane do innych centrów danych platformy Azure w pomocniczym [regionie](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)magazynu usługi Recovery Service. Te zreplikowane dane są przechowywane jako tylko do odczytu i można je tylko zapisywać w przypadku wystąpienia zdarzenia trybu failover platformy Azure. W centrum danych platformy Azure dane są replikowane między oddzielnymi domenami błędów i domenami uaktualnienia, aby zminimalizować ryzyko awarii sprzętu lub konserwacji. GRS zapewnia co najmniej 99.99999999999999% dostępności rocznie.

Magazyn Recovery Services platformy Azure można skonfigurować do korzystania z magazynu lokalnie nadmiarowego (LRS). LRS to tańsza opcja magazynu z bezpłatną dostępnością. Ten model nadmiarowości wykorzystuje replikację między oddzielnymi domenami błędów i domenami uaktualnienia, ale nie jest replikowany między regionami geograficznymi. Dane znajdujące się w magazynie LRS, ale nie jako odporne jako GRS, nadal zapewniają co najmniej 99,999999999% dostępności rocznie.

W przeciwieństwie do tradycyjnych technologii magazynu poza siedzibą firmy, takich jak nośniki taśmowe, dodatkowe kopie danych są tworzone automatycznie i nie wymagają dodatkowych kosztów administracyjnych.

### <a name="restricted-access-and-activity-monitoring"></a>Ograniczony dostęp i monitorowanie działań

Dane kopii zapasowej muszą być chronione przed uszkodzeniem, modyfikacją i niezatwierdzonym usuwaniem. Zarówno Azure Backup, jak i Azure Site Recovery używają wspólnej sieci szkieletowej zarządzania Azure. Ta sieć szkieletowa zawiera szczegółowe informacje o inspekcji, rejestrowaniu i Access Control opartych na rolach (RBAC) do zasobów znajdujących się na platformie Azure. Dostęp do danych kopii zapasowej można ograniczyć do wyboru pracowników administracyjnych, a wszystkie akcje związane z danymi kopii zapasowych mogą być rejestrowane i poddawane inspekcji.

Zarówno Azure Backup, jak i Azure Site Recovery mają wbudowane funkcje rejestrowania i raportowania. Wszelkie problemy występujące podczas tworzenia kopii zapasowej lub replikacji są raportowane administratorom przy użyciu sieci szkieletowej zarządzania Azure.

Magazyn usługi Azure Recovery Services ma również następujące dodatkowe środki bezpieczeństwa danych:

* Dane kopii zapasowej są przechowywane przez 14 dni po wystąpieniu operacji usuwania
* Alerty i powiadomienia dla operacji krytycznych, takich jak "Zatrzymaj tworzenie kopii zapasowej z usuwaniem danych"
* Wymagania dotyczące zabezpieczeń numerów PIN dla operacji krytycznych
* Minimalne kontrole zakresu przechowywania są stosowane

Te minimalne wymagania dotyczące zakresu przechowywania obejmują:

* Codzienne przechowywanie danych wynosi co najmniej siedem dni przechowywania
* W przypadku przechowywania tygodniowego co najmniej cztery tygodnie przechowywania
* W przypadku przechowywania miesięcznie co najmniej trzy miesiące przechowywania
* W przypadku przechowywania corocznego, co najmniej jeden rok przechowywania

Wszystkie dane kopii zapasowej przechowywane na platformie Azure są szyfrowane w stanie spoczynku przy użyciu szyfrowanie usługi Storage platformy Azure (SSE). Ta funkcja jest domyślnie włączona dla wszystkich nowych i istniejących kont magazynu i nie można jej wyłączyć. Zaszyfrowane dane są automatycznie odszyfrowywane podczas pobierania. Domyślnie dane szyfrowane przy użyciu funkcji SSE są szyfrowane przy użyciu klucza dostarczonego przez firmę Microsoft i zarządzane przez nią. Organizacje mogą zdecydować się na udostępnienie własnego klucza szyfrowania i zarządzanie nim do użytku z użyciem instrukcji SSE. Zapewnia to opcjonalną dodatkową warstwę zabezpieczeń zaszyfrowanych danych. Ten klucz może być przechowywany lokalnie lub bezpiecznie w magazynie kluczy platformy Azure.

### <a name="secure-data-transport"></a>Zabezpieczanie transportu danych

Azure Backup dane zaszyfrowane podczas przesyłania przy użyciu algorytmu AES 256. Te dane są zabezpieczane przy użyciu hasła utworzonego przez personel administracyjny podczas pierwszej konfiguracji tworzenia kopii zapasowej. Firma Microsoft nie ma dostępu do tego hasła, co oznacza, że klient musi upewnić się, że to hasło jest bezpiecznie przechowywane. Następnie transfer danych odbywa się między środowiskiem lokalnym i magazynem Recovery Services platformy Azure za pośrednictwem bezpiecznego połączenia HTTPS.  Dane znajdujące się w magazynie Recovery Services są następnie szyfrowane przy użyciu funkcji SSE platformy Azure.

Dane Azure Site Recovery są również zawsze szyfrowane podczas przesyłania. Wszystkie zreplikowane dane są bezpiecznie przenoszone na platformę Azure przy użyciu protokołów HTTPS i TLS. Gdy klient platformy Azure nawiązuje połączenie z platformą Azure przy użyciu połączenia ExpressRoute, Azure Site Recovery dane są wysyłane za pośrednictwem tego połączenia prywatnego.  Gdy klient platformy Azure nawiązuje połączenie z platformą Azure przy użyciu połączenia sieci VPN, dane są replikowane między lokalnym i magazynem Recovery Services bezpiecznie przez Internet.

Ten bezpieczny transfer danych w sieci eliminuje zagrożenia bezpieczeństwa i wymagania dotyczące ograniczenia związane z zarządzaniem tradycyjnymi magazynami kopii zapasowych poza siedzibą firmy, takimi jak nośniki taśmowe.

### <a name="data-retention-periods"></a>Okresy przechowywania danych

Zalecany jest minimalny okres przechowywania kopii zapasowych z trzech miesięcy, jednak zazwyczaj wymagane są dłuższe okresy przechowywania. Azure Backup może udostępnić do 9999 kopii zapasowych. Jeśli pojedyncze Azure Backup chronionego wystąpienia było podejmowane codziennie, będzie to miało na celu przechowywanie 27-letnie codziennych kopii zapasowych. Indywidualne comiesięczne kopie zapasowe chronionego wystąpienia zezwalają na 833 lat przechowywania. Ponieważ dane kopii zapasowej są przestarzałe, a mniej szczegółowe kopie zapasowe są zachowywane w czasie, łączne okno przechowywania danych kopii zapasowej rośnie.  Platforma Azure nie ogranicza czasu, przez jaki dane mogą pozostawać w magazynie usługi Azure Recovery Services, tylko w przypadku całkowitej liczby kopii zapasowych na wystąpienie. Nie ma żadnej różnicy wydajności między przywracaniem ze starych lub nowych kopii zapasowych, a każdy z nich ma ten sam czas.

Magazyn usługi Azure Recovery Services ma wiele domyślnych zasad tworzenia kopii zapasowych i przechowywania danych.  Pracownicy administracyjni mogą również tworzyć niestandardowe zasady tworzenia kopii zapasowych i przechowywania.

![Zasady Azure Backup](media/create-policy.png)

Podczas konfigurowania zasad Azure Backup i przechowywania należy znaleźć równowagę między częstotliwością tworzenia kopii zapasowych i długoterminowymi wymaganiami dotyczącymi przechowywania.

### <a name="backup-and-restore-testing"></a>Testowanie kopii zapasowych i przywracania

ISM zaleca testowanie danych kopii zapasowej w celu upewnienia się, że chronione dane są prawidłowe, gdy wymagane jest przywracanie lub przejście w tryb failover. Azure Backup i Azure Site Recovery również zapewniają możliwość testowania chronionych danych po utworzeniu kopii zapasowej lub replikacji. Dane zarządzane przez Azure Backup można przywrócić do nominowanej lokalizacji, a następnie sprawdzić spójność danych.

Azure Site Recovery ma wbudowaną funkcję wykonywania testów trybu failover. Obciążenia replikowane do magazynu Recovery Services można przywrócić do wyznaczonego środowiska platformy Azure. Docelowe środowisko przywracania może być całkowicie odizolowane od dowolnego środowiska produkcyjnego, aby upewnić się, że nie ma to wpływu na systemy produkcyjne podczas przeprowadzania testów. Po zakończeniu testu środowisko testowe i wszystkie zasoby można natychmiast usunąć, aby zmniejszyć koszty operacyjne.

Testowanie i walidacja trybu failover można zautomatyzować za pomocą usługi Azure Automation wbudowanej w platformę Azure. Pozwala to na automatyczne zaplanowanie testowania trybu failover w celu zapewnienia, że dane zostaną pomyślnie zreplikowane na platformę Azure.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem na temat [zapewniania bezpieczeństwa Azure Policy](azure-policy.md).
