---
title: Dzienników usługi Azure Stack i obsługi danych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat sposobu zbierania informacji w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2019
ms.locfileid: "56318927"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack dziennika i klienta obsługi do danych 
*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*  

W zakresie firma Microsoft procesor lub subprocessor danych osobowych użytkownika usługi Azure Stack, firma Microsoft udziela dla wszystkich klientów, obowiązywać 25 maja 2018 r. zobowiązania w (a) w czasie "przetwarzania danych osobowych. Aprowizacja RODO"sekcji"Warunki ochrony danych" [Online Services — warunki](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) i (b) w Unii Europejskiej ogólnego warunki ochrony danych w rozporządzeniu w 4 załącznika [Online Services — warunki](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Zgodnie z usługi Azure Stack znajduje się w centrach danych klienta, firma Microsoft jest kontrolerem danych wyłącznie dane, które zostaną udostępnione firmie Microsoft za pośrednictwem [diagnostyki](azure-stack-diagnostics.md), [Telemetrii](azure-stack-telemetry.md), i [rozliczeń](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Kontroli dostępu do danych 
Pracownicy firmy Microsoft, przypisani do zbadania sprawy pomocy technicznej właściwe, również uzyska dostęp do zaszyfrowanych danych tylko do odczytu. Pracownicy firmy Microsoft również mieć dostęp do narzędzi, których można użyć do usuwania danych, jeśli to konieczne. Dostęp do danych klienta jest poddawane inspekcji i rejestrowane.  

Kontroli dostępu do danych:
1.  Dane tylko są zachowywane przez maksymalnie 90 dni od zamknięcia przypadek.
2.  Klient zawsze ma możliwość znajdują się dane, usunąć w dowolnej chwili, w tym z 90-dniowego okresu.
3.  Pracownicy firmy Microsoft mających dostęp do danych na podstawie każdym przypadku i tylko zgodnie z potrzebami, aby pomóc rozwiązać problemu wymagającego pomocy technicznej. 
4.  W przypadku której firma Microsoft musi udostępniać dane klienta partnerom OEM, zgody jest obowiązkowe.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Jakie żądania podmiotu danych (DSR) formanty czy klienci mają?
Jak wspomniano wcześniej, firma Microsoft obsługuje usunięcia danych z wybieraniem numeru na żądanie klienta. Klienci mogą żądać, że nasze ze specjalistą pomocy technicznej usunięcia ich dzienników dla danego przypadku w dowolnym momencie wyboru klienta, zanim dane są trwale usuwane.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Powoduje Microsoft powiadomienia klientów, gdy dane zostaną usunięte?
Dla akcji usuwania danych (90 dni, po Zamknij przypadek) firma Microsoft nie jest aktywnie docieranie do klientów i powiadamiać użytkowników o usunięcie. 

Dla akcji usuwania danych na żądanie inżynier pomocy technicznej firmy Microsoft ma dostęp do narzędzia, których one mogą zainicjować usuwanie danych na żądanie i zapewniają potwierdzenie na telefonie z klientem po jej zakończeniu.

## <a name="diagnostic-data"></a>Dane diagnostyczne
Jako część procesu pomocy technicznej, Azure Stack operatorzy mogą [Udostępnij dzienniki diagnostyczne](azure-stack-diagnostics.md) za pomocą usługi Azure Stack zespoły pomocy technicznej i inżynierii w celu ułatwienia rozwiązywania problemów.

Firma Microsoft udostępnia narzędzia i skryptu dla klientów zbierać i przekazywać żądane pliki dziennika diagnostycznego. Zebrane pliki dziennika są przesyłane za pośrednictwem przy użyciu protokołu HTTPS chronionych szyfrowanego połączenia do firmy Microsoft. Ponieważ protokół HTTPS oferuje szyfrowanie przewodowo, nie istnieje żadne hasło służące do szyfrowania podczas przesyłania. Po otrzymaniu ich dzienniki są szyfrowane i przechowywane, dopóki nie zostaną automatycznie usunięte 90 dni, po zamknięciu zgłoszenia do pomocy technicznej.

## <a name="telemetry-data"></a>Dane telemetryczne
[Usługa Azure Stack telemetrii](azure-stack-telemetry.md) automatyczne przekazywanie danych systemowych do firmy Microsoft za pośrednictwem środowiska użytkownika połączenia. Operatorzy usługi Azure Stack zostały kontrolki umożliwiające dostosowanie funkcji telemetrii i ustawienia prywatności w dowolnym momencie.

Microsoft nie zamierzasz zbierać dane poufne, takie jak numery kart kredytowych, nazwy użytkowników i hasła, adresy e-mail lub podobne informacje poufne. Jeśli określamy przypadkowo otrzymane informacje poufne, zostaną usunięte. 

## <a name="billing-data"></a>Dane dotyczące rozliczeń
[Rozliczenia platformy Azure Stack](azure-stack-usage-reporting.md) wykorzystuje global Azure rozliczenia i użycie potoków i dlatego zgodnie z wytycznymi dotyczącymi zgodności firmy Microsoft.

Operatorzy usługi Azure Stack można skonfigurować usługi Azure Stack do przekazywania informacji o użyciu do platformy Azure do rozliczeń. Jest to wymagane dla klientów z wieloma węzłami usługi Azure Stack, którzy wybrać model rozliczeń płatność za użycie. Funkcje raportowania użycia jest kontrolowane niezależnie od danych telemetrycznych i nie jest wymagany w przypadku klientów z wieloma węzłami, którzy zdecydowali modelu wydajności oraz dla użytkowników usługi Azure Stack Development Kit. Dla tych scenariuszy raportowania użycia można wyłączyć za pomocą [skrypt rejestracji](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Kolejne kroki 
[Dowiedz się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-security-foundations.md) 
