---
title: Raport danych użycia usługi Azure stosu na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dane użycia raportowania w programie Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 9153efb73e84d7b84a732217b92a9c66617e90ae
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Raport danych użycia usługi Azure stosu na platformie Azure 

Dane użycia, nazywany również dane dotyczące zużycia, reprezentuje ilość zasobów używanych. 

Azure systemów wielowęzłowego stosu, które używają modelu rozliczeń na podstawie zużycia powinni zgłaszać dane użycia do platformy Azure na potrzeby rozliczeń.  Operatory stosu Azure należy skonfigurować ich wystąpienia stosu Azure raportować dane użycia na platformie Azure.

> [!NOTE]
> Raportowanie danych użycia jest wymagana dla użytkowników wielowęzłowego stosu Azure licencji, zgodnie z modelem płatności jako — użytkownik użycia. Opcjonalne dla klientów, którzy licencji, zgodnie z modelem pojemności (zobacz [jak kupić strony](https://azure.microsoft.com/en-us/overview/azure-stack/how-to-buy/). Dla użytkowników usługi Azure stosu Development Kit operatory stosu Azure raport danych użycia i przetestować tę funkcję. Jednak użytkownicy nie zostanie obciążona dla bez użycia, które wiążą się. 


![Przepływ rozliczeń](media/azure-stack-usage-reporting/billing-flow.png)

Dane użycia są wysyłane z stosu Azure na platformie Azure przez mostek Azure. Na platformie Azure commerce system przetwarza dane użycia i generuje BOM. Po wygenerowaniu BOM, właściciela subskrypcji platformy Azure można wyświetlić i pobierz go z [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions). Aby dowiedzieć się, jak jest licencjonowana stosu Azure, zobacz [stosu Azure pakowania i cenach dokumentu](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Konfigurowanie raportowania danych użycia

Aby skonfigurować raportowanie danych użycia, należy najpierw [zarejestruj wystąpienie stosu Azure Azure](azure-stack-register.md). W ramach procesu rejestracji składnik Azure Mostek stosu Azure, która łączy stosu Azure na platformie Azure i wysyła dane użycia, jest skonfigurowany. Następujące dane użycia są wysyłane z stosu Azure do platformy Azure:

- **Pomiarowe identyfikator** — Unikatowy identyfikator zasobu, który został wykorzystany.
- **Ilość** — ilość użycia zasobów.
- **Lokalizacja** — lokalizacja wdrożonym bieżącego zasobu stosu Azure.
- **Identyfikator URI zasobu** — identyfikator URI zasobu, dla którego obciążenie jest raportowany w pełni kwalifikowana.
- **Identyfikator subskrypcji** — identyfikator subskrypcji Azure stosu. To jest lokalne subskrypcji (Azure stosu).
- **Czas** — godzina rozpoczęcia i zakończenia danych użycia. Brak pewne opóźnienie między czasem, podczas tych zasobów są używane w stosie Azure i danych użycia jest zgłaszany do obsługi handlu. Azure stosu agreguje dane dotyczące wykorzystania co 24 godziny i raportowania danych użycia do potoku handlu na platformie Azure ma inny kilka godzin. Tak użycie, która występuje tuż przed północy może wyświetlane na platformie Azure następnego dnia.

## <a name="generate-usage-data-reporting"></a>Generowanie raportowania danych użycia

1. Aby przetestować dane użycia raportowania, należy utworzyć kilka zasobów w stosie Azure. Na przykład można utworzyć [konta magazynu](azure-stack-provision-storage-account.md), [maszyny Wirtualnej systemu Windows Server](azure-stack-provision-vm.md) i maszyny Wirtualnej systemu Linux z podstawowe i standardowe jednostki SKU, aby zobaczyć sposób użycia rdzeni został zgłoszony. Dane użycia dla różnych typów zasobów są zgłaszane w różnych liczników.

2. Pozostaw zasobów uruchomione przez kilka godzin. Informacje dotyczące użycia są zbierane mniej więcej co godzinę. Po zebraniu, dane te są przesyłane do usługi Azure i przetwarzane w systemie Azure commerce. Ten proces może potrwać kilka godzin.

## <a name="view-usage---csp-subscriptions"></a>Wyświetl informacje o użyciu - subskrypcje dostawcy usług Kryptograficznych

Jeśli zarejestrowano stosu Azure przy użyciu subskrypcji dostawcy usług Kryptograficznych, można wyświetlić z użycia i opłat w taki sam sposób, w którym możesz wyświetlić wykorzystania platformy Azure. Wykorzystanie stosu Azure zostanie uwzględniony, faktury i w pliku uzgadniania, dostępne za pośrednictwem [Centrum partnerskiego](https://partnercenter.microsoft.com/partner/home). Plik uzgadniania jest aktualizowany co miesiąc. Jeśli musisz uzyskiwać dostęp najnowsze informacje o użyciu stosu Azure, można użyć interfejsów API Centrum partnerskiego.

   ![Centrum partnerskiego](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Wyświetl informacje o użyciu — Umowa Enterprise Agreement subskrypcji

Jeśli zarejestrowano stosu Azure przy użyciu subskrypcji Enterprise Agreement, można wyświetlić z użycia i opłat w [EA Portal](https://ea.azure.com/). Wykorzystanie stosu Azure zostaną uwzględnione w zaawansowanych pliki do pobrania wraz z użycia platformy Azure w sekcji raportów w portalu EA. 

## <a name="view-usage--other-subscriptions"></a>Wyświetl informacje o użyciu — inne subskrypcje

Jeśli zarejestrowane stosu Azure przy użyciu żadnego innego typu subskrypcji, na przykład w przypadku subskrypcji płatności zgodnie z rzeczywistym użyciem, można wyświetlić użycia i opłat w Centrum konta platformy Azure. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako platformy Azure konta administratora, a następnie wybierz subskrypcję platformy Azure, które używane do rejestrowania stosu Azure. Można wyświetlić dane użycia stosu Azure, ilości pobierane dla każdego z używanych zasobów, jak pokazano na poniższej ilustracji:

   ![Przepływ rozliczeń](media/azure-stack-usage-reporting/pricing-details.png)

Dla usługi Azure stosu Development Kit zasobów Azure stosu nie są naliczane, ceny jest wyświetlany jako 0,00 USD. Po wielowęzłowego stosu Azure staje się ogólnie dostępna, można wyświetlić rzeczywisty koszt dla każdego z tych zasobów.

## <a name="which-azure-stack-deployments-are-charged"></a>Które wdrożenia stosu Azure są naliczane?

Użycie zasobów jest bezpłatna dla zestawu SDK usługi Azure stosu. Natomiast systemy wielowęzłowego stosu Azure są naliczane obciążenia maszyn wirtualnych, usług magazynu i usług aplikacji.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Użytkownicy są naliczane za infrastrukturę maszyn wirtualnych?

Nie. Dane użycia dla niektórych stosu Azure dostawcy zasobów maszyn wirtualnych jest zgłaszany na platformie Azure, ale nie ma żadnych opłat dla tych maszyn wirtualnych, ani dla maszyn wirtualnych utworzonych podczas wdrażania umożliwia infrastruktury Azure stosu.  

Użytkownicy są naliczane tylko dla maszyn wirtualnych uruchamianych w ramach subskrypcji dzierżawy. W obszarze subskrypcje dzierżawy w celu zapewnienia przestrzegania postanowień licencyjnych dotyczących stosu Azure należy wdrożyć wszystkich obciążeń.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Użytkownik ma licencję systemu Windows Server, który ma być użyty na stosie Azure, jak to zrobić?

Przy użyciu istniejących licencji pozwala uniknąć generowania liczników użycia. Istniejących licencji systemu Windows Server mogą być używane w stosie Azure, zgodnie z opisem w sekcji "Przy użyciu istniejącego oprogramowania z usługi Azure stosu" [Przewodnik licencjonowania stosu Azure](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Klienci muszą wdrażać maszynami wirtualnymi systemu Windows Server, zgodnie z opisem w [korzyści hybrydowego licencji systemu Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) tematu, aby można było używać ich istniejących licencji.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Subskrypcji, której dotyczy kosztów używanych zasobów?
Subskrypcję, która jest dostępne, gdy [rejestrowania stosu Azure za pomocą usługi Azure](azure-stack-register.md) rozliczany.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jakie rodzaje subskrypcje są obsługiwane dla raportowania danych użycia?

Multinode stosu Azure Enterprise Agreement (EA) i dostawcy usług Kryptograficznych subskrypcje są obsługiwane. Azure stosu Development Kit subskrypcji Enterprise Agreement (EA), płatność za rzeczywiste użycie dostawcy usług Kryptograficznych i MSDN obsługiwać raportowanie danych użycia.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Czy dane użycia raportowania pracy w chmurach suwerennych?

W zestawie Azure stosu Development Kit raportowania danych użycia wymaga subskrypcji, które są tworzone w globalnym systemie Azure. Subskrypcjami w jednym z suwerennych chmury (chmury Azure dla instytucji rządowych, platformy Azure w Niemczech i Chińskiej wersji platformy Azure) nie można zarejestrować przy użyciu platformy Azure, więc nie obsługują użycia danych raportowania.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Jak użytkownicy zidentyfikować dane użycia stosu Azure w portalu Azure rozliczeń

Użytkownicy mogą zobaczyć stos Azure dane użycia w pliku szczegóły użycia. Aby wiedzieć, jak uzyskać pliku szczegóły użycia, zapoznaj się [Pobierz plik użycia z artykułu Centrum konta platformy Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Szczegóły użycia zawiera liczniki stosu Azure identyfikujące Azure stosu magazynu i maszyn wirtualnych. Wszystkie zasoby używane w stosie Azure są zgłaszane w regionie o nazwie "Azure stosu."

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Dlaczego użycia raportowane w stosie Azure nie odpowiada raportów wygenerowanych z Centrum konta platformy Azure?

Zawsze jest delaybetween danych użycia zgłoszonych przez wykorzystanie stosu Azure interfejsów API i danych użycia zgłoszonych przez Centrum konta platformy Azure. Opóźnienie to czas potrzebny do przekazywania danych użycia ze stosu Azure do handlu Azure. Ze względu na to opóźnienie użycia, która występuje tuż przed północy może wyświetlane w usłudze Azure następnego dnia. Jeśli używasz [interfejsów API usługi Azure stosu użycia](azure-stack-provider-resource-api.md)i porównywania wyników do użycia w portalu Azure rozliczeń, zobaczysz różnicy.

## <a name="next-steps"></a>Kolejne kroki

* [Interfejs API użycia dostawcy](azure-stack-provider-resource-api.md)  
* [Interfejs API użycia dzierżawy](azure-stack-tenant-resource-usage-api.md)
* [Często zadawane pytania na temat użycia](azure-stack-usage-related-faq.md)
* [Zarządzanie użycia i rozliczeń jako dostawcy usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md)
