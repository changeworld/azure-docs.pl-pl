---
title: Azure API rozliczeń | Dokumentacja firmy Microsoft
description: Więcej informacji na temat użycia rozliczeń Azure i RateCard interfejsów API, które są używane do wgląd w użycie zasobów platformy Azure i trendów.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: mobandyo
ms.openlocfilehash: e26d8ad1f08eb711dc22b45d48f8dc326d8c17c7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Programowe uzyskiwanie wglądu w wykorzystanie sieci Azure przy użyciu interfejsów API rozliczeń w Azure
Za pomocą interfejsów API usługi Azure rozliczeń pobierania danych użycia i zasobów do narzędziami analizy danych preferowany. API RateCard i użycia zasobów Azure może pomóc dokładnie przewidzieć i zarządzanie nimi kosztów. Interfejsy API są zaimplementowane jako dostawca zasobów i częścią rodziny interfejsach API udostępnianych przez Menedżera zasobów Azure.  

## <a name="azure-invoice-download-api-preview"></a>Interfejs API pobierania faktury Azure (wersja zapoznawcza)
Raz [opcjonalnych zostało ukończone](billing-manage-access.md#opt-in), faktury pobierania za pomocą wersji zapoznawczej [API faktury](/rest/api/billing). Funkcje obejmują:

* **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) Aby określić użytkowników lub aplikacji, które mogą korzystać z dane użycia dla tej subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.
* **Data filtrowanie** -użyj `$filter` parametr, aby uzyskać wszystkie faktury w odwrotnej kolejności chronologicznej Data zakończenia okresu faktury. 

> [!NOTE]
> Ta funkcja jest w pierwszej wersji zapoznawczej i mogą być regulowane zmiany niezgodne z wcześniejszymi wersjami. Obecnie nie jest dostępne dla niektórych subskrypcji oferty (EA, dostawcy usług Kryptograficznych, AIO nieobsługiwane) i platformy Azure w Niemczech.

## <a name="azure-resource-usage-api-preview"></a>Użycie zasobów platformy Azure, interfejsu API (wersja zapoznawcza)
Użyj platformy Azure [API użycia zasobów](https://msdn.microsoft.com/library/azure/mt219003) Twoje dane szacowany wykorzystania platformy Azure. Interfejs API zawiera:

* **Azure kontroli dostępu opartej na rolach** -skonfigurować zasady dostępu na [portalu Azure](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) Aby określić użytkowników lub aplikacji, które mogą korzystać z dane użycia dla tej subskrypcji. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do albo czytnika rozliczeń, czytnika, właściciela lub współautora roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.
* **Co godzinę lub agregacje codzienne** — wywołań można określić, czy chcą ich danych użycia usługi Azure co godzinę pakiety w ramach Agreement lub pakiety codziennie w ramach Agreement. Wartość domyślna to codziennie.
* **Wystąpienie metadanych (w tym tagi zasobów)** — Pobierz szczegóły na poziomie wystąpienia takich jak identyfikator uri zasobu w pełni kwalifikowana (/subscriptions/ {identyfikator subskrypcji} /..), informacje o grupie zasobów i tagi zasobów. Te metadane pomaga w sposób niejednoznaczny i programowo przydzielić użycia według znaczników, dla przypadków użycia, takich jak między ładowania.
* **Metadane zasobu** — szczegóły zasobów, takie jak nazwa miernika, kategorii licznika, podkategorii miernika, jednostki i region zapewniają wywołującego lepiej zrozumieć co został wykorzystany. Również pracujemy, aby były wyrównane terminologii metadanych zasobów w portalu Azure, Azure użycia woluminów CSV, EA rozliczeń CSV i inne środowiska publicznych pozwalają korelowania danych środowiska.
* **Użycie typów inną ofertę** — danych użycia jest dostępna dla typów oferty, takie jak płatność za rzeczywiste użycie, MSDN, zobowiązania pieniężnego, środki pieniężne i atrybutów Rozszerzonych, z wyjątkiem [dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>RateCard zasobów platformy Azure, interfejsu API (wersja zapoznawcza)
Użyj [interfejsu API usługi Azure Resource RateCard](https://msdn.microsoft.com/library/azure/mt219005) Aby uzyskać listę dostępnych zasobów platformy Azure i szacowany informacje o cenach dla każdego. Interfejs API zawiera:

* **Azure kontroli dostępu opartej na rolach** — Konfigurowanie zasad dostępu na [portalu Azure](https://portal.azure.com) lub za pomocą [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview) Aby określić użytkowników lub aplikacji, które mogą korzystać z RateCard danych. Obiekty wywołujące należy użyć standardowego tokeny usługi Azure Active Directory do uwierzytelniania. Dodaj element wywołujący do czytnika, właścicielem lub współautorem roli do uzyskania dostępu do danych użycia dla określonej subskrypcji platformy Azure.
* **Obsługa płatność za rzeczywiste użycie, MSDN, zobowiązań i udostępnia środki pieniężne (EA i [dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) nieobsługiwane)** — ten interfejs API zawiera informacje o szybkości poziomu oferty Azure.  Wywołujący ten interfejs API należy przekazać informacje oferty, aby uzyskać szczegóły dotyczące zasobów i szybkości. Obecnie możemy udostępnić EA stawki, dlatego EA oferty zostały dostosowane stawki dla rejestracji. 

## <a name="scenarios"></a>Scenariusze
Oto niektóre scenariusze, które są możliwe z kombinacją użycia i interfejsów API RateCard:

* **Spędzają na platformie Azure w miesiącu** — za pomocą odpowiedniej kombinacji użycia i RateCard interfejsów API, aby uzyskać lepszą wgląd w chmurze spędzają w miesiącu. Można analizować zasobników co godzinę i dziennego użycia i opłat prognozy.
* **Konfigurowanie alertów** — umożliwia użycie i interfejsów API RateCard zużycie szacowany chmury i opłat i skonfigurować alerty oparte na zasobach lub pieniężne na podstawie.
* **Przewidywanie rachunku** — Get szacowane zużycie i w chmurze wydatków i zastosować algorytmów uczenia maszynowego na potrzeby prognozowania BOM będzie końca cyklu rozliczeniowego.
* **Wstępne zużycia koszt analizy** — za pomocą interfejsu API RateCard do prognozowania, ile rachunku byłby przez użycie oczekiwanego przenoszenia obciążeń w systemie Azure. Jeśli masz istniejące obciążenia w innych chmur lub chmur prywatnych, użycie z platformy Azure można również mapować spędzają szybkości, aby uzyskać lepszą oszacowanie Azure. Ta Szacowana daje możliwość przestawnego na oferty i porównaj i kontrast między typami inną ofertę poza płatność za rzeczywiste użycie, takich jak zobowiązań i środki pieniężne. Interfejs API również daje możliwość Zobacz różnice koszt według regionu i można wykonać analizy warunkowej kosztów podejmowanie decyzji dotyczących wdrożenia.
* **Analizy warunkowej** -
  
  * Można określić, czy jest bardziej ekonomiczne rozwiązanie do uruchamiania obciążeń w innym regionie lub w innej konfiguracji zasobów platformy Azure. Koszty zasobów platformy Azure mogą się różnić w zależności na region platformy Azure, którego używasz.
  * Można również określić, czy innego typu oferty Azure zapewnia większą szybkość na zasobów platformy Azure.
  
## <a name="partner-solutions"></a>Rozwiązania partnerskie
[Cruiser i rozliczeń integracji interfejsu API programu Microsoft Azure w chmurze](billing-usage-rate-card-partner-solution-cloudcruiser.md) w tym artykule opisano sposób [Express Cruiser chmury Azure pakietu](http://www.cloudcruiser.com/partners/microsoft/) współpracuje bezpośrednio z portalu Windows Azure Pack (map). Operacyjne i finansowe aspekty Microsoft Azure prywatne lub hostowanej chmurze publicznej bezproblemowo można zarządzać za pomocą interfejsu użytkownika.   

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z próbek kodu w witrynie GitHub:
  * [Przykładowy kod faktury interfejsu API](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Przykładowy kod użycia interfejsu API](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Przykładowy kod RateCard interfejsu API](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Aby dowiedzieć się więcej na temat usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 



