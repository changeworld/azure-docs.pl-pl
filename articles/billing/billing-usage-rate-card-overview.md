---
title: Interfejsy API rozliczeń Azure | Dokumentacja firmy Microsoft
description: Informacje na temat użycia rozliczeń platformy Azure i interfejsów API usługi RateCard, które są używane do zapewniają wgląd w wykorzystanie zasobów platformy Azure i trendów.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: erikre
ms.openlocfilehash: 7e504b2fb01bc8b92c529b68f818df5ed1b889a1
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276452"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Użyj interfejsów API rozliczeń platformy Azure, aby programowo uzyskać wgląd w użycie platformy Azure
Użyj interfejsów API rozliczeń platformy Azure do pobierania danych użycia i zasobów do narzędzia do analizy danych preferowany. Interfejsy API usługi RateCard i użycia zasobów platformy Azure mogą ułatwić dokładne przewidywanie kosztów i zarządzanie nimi. Interfejsy API są zaimplementowane jako dostawcy zasobów i częścią rodziny interfejsach API udostępnianych przez usługę Azure Resource Manager.  

> [!div class="nextstepaction"]
> [Pomoc w ulepszaniu dokumentów rozliczeniowych platformy Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="azure-invoice-download-api-preview"></a>Interfejs API pobierania faktur platformy Azure (wersja zapoznawcza)
Raz [zgody zostało ukończone](billing-manage-access.md#opt-in), pobierania faktur za pomocą wersji zapoznawczej [API faktury](/rest/api/billing). Funkcje obejmują:

* **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) określić użytkowników lub aplikacji, które mogą uzyskać dostęp do dane użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.
* **Filtrowanie dat** — użyj `$filter` parametru, aby pobrać wszystkie faktury w odwrotnej kolejności chronologicznej według daty zakończenia okresu faktury. 

> [!NOTE]
> Ta funkcja jest w pierwszej wersji zapoznawczej i mogą paść ofiarą zmiany niekompatybilne z poprzednimi wersjami. Obecnie nie jest dostępna w przypadku niektórych ofert subskrypcji (umowy EA, dostawcy usług Kryptograficznych, AIO nieobsługiwane) i Azure (Niemcy).

## <a name="azure-resource-usage-api-preview"></a>Interfejs API (wersja zapoznawcza) użycia zasobów platformy Azure
Skorzystaj z Kalkulatora [interfejs API użycia zasobów](https://msdn.microsoft.com/library/azure/mt219003) można pobrać danych Szacowane użycie platformy Azure. Ten interfejs API obejmuje:

* **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) określić użytkowników lub aplikacji, które mogą uzyskać dostęp do dane użycia dla subskrypcji. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik rozliczenia, Czytelnik, właściciel albo współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.
* **Co godzinę lub agregacje codzienne** — obiekty wywołujące można określić, czy mają swoje dane użycia platformy Azure godzinowo przedziałów lub codziennie przedziałów. Wartość domyślna to dzienny.
* **Wystąpienie metadanych (w tym tagi zasobów)** — Pobierz szczegóły na poziomie wystąpienia takich jak identyfikator uri zasobu w pełni kwalifikowana (/subscriptions/ {identyfikator subskrypcji} /...), informacje o grupie zasobów i tagi zasobów. Te metadane pomaga także jednoznacznie i programowo przydzielić użycia według tagów, przypadki użycia, takich jak ładowania wielu.
* **Metadane zasobu** — szczegóły zasobów, takie jak nazwa miernika, kategoria licznika, podkategoria licznika, jednostki i region zapewniają obiekt wywołujący lepiej co został wykorzystany. Pracujemy także wyrównać terminologii metadanych zasobów w witrynie Azure portal, Azure użycia woluminów CSV, EA rozliczeń CSV i inne środowiska publicznego umożliwiają korelowanie danych środowiska.
* **Użycia dla typów inną ofertę** — dane użycia są dostępne dla typów ofert, takie jak płatność za rzeczywiste użycie, MSDN, w ramach zobowiązań pieniężnych, środki pieniężne i umowy EA, z wyjątkiem [dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>RateCard zasobów platformy Azure, interfejsu API (wersja zapoznawcza)
Użyj [interfejsu API RateCard zasobów Azure](https://msdn.microsoft.com/library/azure/mt219005) można pobrać listy dostępnych zasobów platformy Azure oraz informacje o cenach Szacowana dla każdego. Ten interfejs API obejmuje:

* **Azure Role-based Access Control** — Konfigurowanie zasad dostępu na [witryny Azure portal](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) określić użytkowników lub aplikacji, które mogą uzyskać dostęp do Dane usługi RateCard. Obiekty wywołujące musi używać standardowych tokenów usługi Azure Active Directory do uwierzytelniania. Dodaj obiekt wywołujący do roli Czytelnik, właściciela lub współautora do uzyskania dostępu do danych użycia dla określonej subskrypcji systemu Azure.
* **Obsługa płatność za rzeczywiste użycie, MSDN, w ramach zobowiązań pieniężnych i ofert kredytowo (umowy EA i [dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nieobsługiwane)** — ten interfejs API zawiera informacje za platformę Azure poziomu oferty.  Obiekt wywołujący ten interfejs API musi przekazać informacje oferty, aby uzyskać szczegółowe informacje o zasobie i kursów. Obecnie nie możemy zapewniają stawki umowy EA, ponieważ EA ofert dostosowanych stawki dla rejestracji. 

## <a name="scenarios"></a>Scenariusze
Poniżej przedstawiono scenariusze, które są możliwe przy użyciu połączenia API RateCard i użycie:

* **Wydatki na platformę Azure w miesiącu** — użyj kombinacji użycia i RateCard interfejsów API, aby uzyskać lepszy wgląd w chmurze wydatki w miesiącu. Możesz analizować przedziały co godzinę i codzienne szacuje użycie i opłat.
* **Konfigurowanie alertów** — umożliwia API RateCard i użycie chmury Szacowane użycie i opłat i skonfigurować alerty oparte na zasobach lub pieniężnych na podstawie.
* **Przewidywanie rachunku** — pobieranie Szacowane użycie i w chmurze wydatków i zastosować algorytmów uczenia maszynowego w celu przewidywania, rachunek będzie na końcu okresu rozliczeniowego.
* **Użycie wstępnego analiza kosztów** — przewidywanie, ile na rachunku będzie oczekiwanego użycia podczas przenoszenia obciążeń do platformy Azure za pomocą interfejsu API RateCard. Jeśli masz istniejące obciążenia w innych chmurach lub chmur prywatnych, można również mapować użycia z platformą Azure poświęcić kursów, które można pobrać lepiej oszacować platformy Azure. Te dane szacunkowe daje możliwość przestawnego na oferty i porównywanie i zestawianie między typami inną ofertę poza płatności, np. w ramach zobowiązań pieniężnych i środków pieniężnych. Interfejs API także zapewnia możliwość wyświetlania różnic kosztów według regionu i pozwoli to wykonywać analizy warunkowej kosztów, ułatwiające podjęcie decyzji dotyczących wdrożenia.
* **Analiza warunkowa** -
  
  * Można określić, czy jest to bardziej ekonomiczne do uruchamiania obciążeń w innym regionie lub w innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure może się różnić w zależności na region platformy Azure, z którego korzystasz.
  * Można również określić, jeśli inny typ oferty platformy Azure zapewnia lepszą szybkość w obrębie zasobu platformy Azure.
  
## <a name="partner-solutions"></a>Rozwiązania partnerskie
W artykule [Cloud Cruiser and Microsoft Azure Billing API Integration](billing-usage-rate-card-partner-solution-cloudcruiser.md) (Integracja Cloud Cruiser z interfejsem API rozliczeń platformy Microsoft Azure) opisano, jak pakiet [Cloud Cruiser's Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) działa bezpośrednio z poziomu portalu Windows Azure Pack (WAP). Możesz bezproblemowo zarządzać zarówno operacyjnymi, jak i finansowymi aspektami hostowanej chmury publicznej lub prywatnej platformy Microsoft Azure z jednego interfejsu użytkownika.   

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z przykładów kodu w serwisie GitHub:
  * [Przykład kodu dla interfejsu API faktur](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Przykład kodu dla interfejsu API użycia](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Przykład kodu dla interfejsu API usługi RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 



