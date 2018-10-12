---
title: Raport danych użycia usługi Azure Stack na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować dane użycia raportowania w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: cf6604730ab3bd9ee04fac36e703022f63dcb949
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090369"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Raport danych użycia usługi Azure Stack na platformie Azure 

Dane użycia, nazywany również dane dotyczące zużycia, reprezentuje ilość używanych zasobów. 

Stos wielowęzłowego systemy usługi Azure, które używają modelu rozliczeń na podstawie użycia należy zgłaszać dane użycia na platformie Azure w celu rozliczeń.  Operatorzy usługi Azure Stack, należy skonfigurować swoje wystąpienie usługi Azure Stack raportować dane użycia na platformie Azure.

> [!NOTE]
> Raportowanie danych użycia jest wymagana dla użytkowników wielowęzłowego usługi Azure Stack, którzy zakupili zgodnie z modelem płatności jako — rzeczywiste użycie. Jest to opcjonalne dla klientów, którzy licencji w ramach modelu pojemności (zobacz [jak strony zakupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). Dla użytkowników usługi Azure Stack Development Kit operatorom usługi Azure Stack można zgłaszać dane użycia i przetestować funkcję. Jednak użytkownicy nie będą naliczane za dotychczasowe użycie, które wiążą się. 


![Przepływ rozliczeń](media/azure-stack-usage-reporting/billing-flow.png)

Dane użycia są wysyłane z usługi Azure Stack na platformie Azure za pośrednictwem mostka platformy Azure. Na platformie Azure commerce system przetwarza dane użycia i generuje rachunku. Po rachunek jest generowany, właściciel subskrypcji platformy Azure można wyświetlić i pobrać z [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions). Aby dowiedzieć się, jak ma licencję usługi Azure Stack, zapoznaj się [usługi Azure Stack, pakowania i ceny dokumentu](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Skonfiguruj raportowanie danych użycia

Aby skonfigurować raportowanie danych użycia, należy najpierw [rejestrowania Twojego wystąpienia usługi Azure Stack na platformie Azure](azure-stack-register.md). W ramach procesu rejestracji składnik Mostek platformy Azure, usługi Azure Stack, który łączy z usługi Azure Stack na platformie Azure i wysyła dane użycia, jest skonfigurowany. Następujące dane użycia są wysyłane z usługi Azure Stack na platformie Azure:

- **Identyfikator licznika** — Unikatowy identyfikator zasobu, który został wykorzystany.
- **Ilość** — stopień użycia zasobów.
- **Lokalizacja** — lokalizacji, w których jest wdrażany bieżącego zasobu usługi Azure Stack.
- **Identyfikator URI zasobu** — identyfikator URI zasobu, dla których użycie jest zgłaszany w pełni kwalifikowana.
- **Identyfikator subskrypcji** — identyfikator subskrypcji użytkownika usługi Azure Stack, czyli lokalnego subskrypcji (usługa Azure Stack).
- **Czas** — godzina rozpoczęcia i zakończenia danych użycia. Ma pewne opóźnienie między czas podczas tych zasobów są używane w usłudze Azure Stack i danych użycia jest zgłaszany do commerce. Azure Stack agreguje dane dotyczące wykorzystania co 24 godziny i raportowania danych użycia do potoku commerce na platformie Azure ma inną kilka godzin. Tak użycie występujący wkrótce wcześniejszą niż północ może być ujęte w systemie Azure następnego dnia.

## <a name="generate-usage-data-reporting"></a>Generowanie raportowanie danych użycia

1. Aby przetestować raportowanie danych użycia, należy utworzyć kilka zasobów w usłudze Azure Stack. Na przykład można utworzyć [konta magazynu](azure-stack-provision-storage-account.md), [maszyny Wirtualnej z systemem Windows Server](azure-stack-provision-vm.md) i Maszynę wirtualną systemu Linux przy użyciu Basic i standardowej jednostki SKU, aby zobaczyć, jak raportowania użycia rdzeni. Dane użycia dla różnych typów zasobów są zgłaszane w ramach różnych liczników.

2. Zostaw swoje zasoby, uruchomione przez kilka godzin. Informacje dotyczące użycia są zbierane mniej więcej co godzinę. Po zebraniu, te dane są przesyłane do platformy Azure i przetwarzane w systemie handlu platformy Azure. Ten proces może potrwać kilka godzin.

## <a name="view-usage---csp-subscriptions"></a>Wyświetl informacje o użyciu - subskrypcji dostawcy CSP

Jeśli zarejestrowano usługi Azure Stack przy użyciu subskrypcji programu CSP możesz wyświetlić użycia i opłat w taki sam sposób, w którym można wyświetlić użycie platformy Azure. Użycie platformy Azure Stack będą uwzględnione na fakturze i w pliku uzgodnień, dostępne za pośrednictwem [Centrum partnerskiego](https://partnercenter.microsoft.com/partner/home). W pliku uzgodnień jest aktualizowany co miesiąc. Jeśli musisz uzyskiwać dostęp do najnowszych informacji użycia usługi Azure Stack, używając interfejsów API Centrum partnerskiego.

   ![Centrum partnerskie](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Wyświetl informacje o użyciu — subskrypcji Enterprise Agreement

Jeśli zarejestrowano usługi Azure Stack przy użyciu subskrypcji Enterprise Agreement, możesz wyświetlić użycia i kosztów w [portalu EA Portal](https://ea.azure.com/). Użycie platformy Azure Stack zostaną uwzględnione w zaawansowanych pliki do pobrania wraz z użycia platformy Azure w witrynie EA portal, w sekcji raporty. 

## <a name="view-usage--other-subscriptions"></a>Wyświetl informacje o użyciu — inne subskrypcje

Jeśli zarejestrowano usługi Azure Stack przy użyciu dowolny inny typ subskrypcji, na przykład w przypadku subskrypcji płatności zgodnie z rzeczywistym użyciem, użycia i opłat można wyświetlić w Centrum konta platformy Azure. Zaloguj się do [Centrum konta platformy Azure](https://account.windowsazure.com/Subscriptions) jako platformy Azure, konto administratora i wybierz subskrypcję platformy Azure, używany do rejestrowania usługi Azure Stack. Możesz wyświetlić dane użycia usługi Azure Stack, kwota naliczana dla każdego z używanych zasobów, jak pokazano na poniższej ilustracji:

   ![Przepływ rozliczeń](media/azure-stack-usage-reporting/pricing-details.png)

Zasoby usługi Azure Stack dla usługi Azure Stack Development Kit nie są naliczane, więc cena jest przedstawiana w postaci 0,00 USD.

## <a name="which-azure-stack-deployments-are-charged"></a>Wdrożenia usługi Azure Stack, które są rozliczane?

Użycie zasobów jest bezpłatne w przypadku usługi Azure Stack Development Kit. Natomiast w przypadku systemów z wieloma węzłami usługi Azure Stack, obciążenie maszyn wirtualnych, usług magazynu i usług aplikacji jest naliczana.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Czy użytkownicy są naliczane za infrastrukturą maszyn wirtualnych?

Nie. Dane użycia dla dostawcy zasobów usługi Azure Stack niektórych maszyn wirtualnych jest zgłaszany do usługi Azure, ale nie będą naliczane opłaty dla tych maszyn wirtualnych ani utworzony podczas wdrażania maszyn wirtualnych umożliwiające infrastruktury Azure Stack.  

Użytkownicy są naliczane tylko dla maszyn wirtualnych działających w ramach subskrypcji dzierżawy. Wszystkie obciążenia musi zostać wdrożony w ramach subskrypcji dzierżawy do przestrzegania postanowień licencyjnych w usłudze Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Mam licencję systemu Windows Server, które w usłudze Azure Stack, jak to zrobić?

Przy użyciu istniejących licencji pozwala uniknąć generowania mierników użycia. Istniejące licencje systemu Windows Server mogą być używane w usłudze Azure Stack, zgodnie z opisem w sekcji "Za pomocą istniejącego oprogramowania za pomocą usługi Azure Stack" [wskazówki dotyczące licencjonowania usługi Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Klienci muszą wdrożyć maszyn wirtualnych systemu Windows Server, zgodnie z opisem w [korzyść użycia hybrydowego dla licencji systemu Windows Server](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) artykuł, aby można było używać swoich istniejących licencji.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Opłaty za subskrypcję, która są naliczane za zasoby używane?
Subskrypcję, która znajduje się kiedy [rejestrowania usługi Azure Stack z platformą Azure](azure-stack-register.md) jest naliczana.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jakie rodzaje subskrypcje są obsługiwane dla raportowanie danych użycia?

W przypadku multinode usługi Azure Stack subskrypcji Enterprise Agreement (EA) i dostawcy usług Kryptograficznych są obsługiwane. Azure Stack Development Kit subskrypcji Enterprise Agreement (EA), rozliczana według bieżącego użycia i dostawcy usług Kryptograficznych oraz MSDN obsługuje raportowanie danych użycia.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Odbywa się praca w chmurach suwerennych raportowanie danych użycia?

W usłudze Azure Stack Development Kit raportowanie danych użycia wymaga subskrypcji, które są tworzone w globalnego systemu Azure. Nie można zarejestrować subskrypcji utworzonych w jednym z chmur suwerennych (chmury Azure Government, Azure (Niemcy) i Azure — Chiny) na platformie Azure, dzięki czemu nie obsługują raportowanie danych użycia.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Dlaczego użycia zgłoszonych w usłudze Azure Stack nie odpowiada Raport wygenerowany na podstawie Centrum konta platformy Azure?

Zawsze jest opóźnienie między danych użycia zgłoszonych przez użycie usługi Azure Stack interfejsów API i danych użycia zgłoszonych przez Centrum konta platformy Azure. Opóźnienie to czas potrzebny na przekazywanie danych użycia z usługi Azure Stack do handlu platformy Azure. Ze względu na to opóźnienie użycie występujący wkrótce wcześniejszą niż północ może być ujęte w systemie Azure następnego dnia. Jeśli używasz [interfejsów API usługi Azure Stack użycia](azure-stack-provider-resource-api.md)i porównać wyniki do użycia w portalu rozliczeń systemu Azure, możesz zobaczyć różnicę.

## <a name="next-steps"></a>Kolejne kroki

* [Interfejs API użycia dostawcy](azure-stack-provider-resource-api.md)  
* [Interfejs API użycia dzierżawy](azure-stack-tenant-resource-usage-api.md)
* [Często zadawane pytania na temat użycia](azure-stack-usage-related-faq.md)
* [Zarządzanie użycia i rozliczeń co dostawcy usług w chmurze](azure-stack-add-manage-billing-as-a-csp.md)
