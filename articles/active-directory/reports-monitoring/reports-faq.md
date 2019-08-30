---
title: Raporty Azure Active Directory często zadawane pytania | Microsoft Docs
description: Często zadawane quesitons wokół raportów Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c3138b82c7dc4a7217e8cb67448a5d824398ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127023"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące Azure Active Directory raportów

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące raportowania Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**Pyt.: Obecnie używam `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego do programistycznego ściągania raportów dotyczących użycia usługi Azure AD i zintegrowanych aplikacji w naszych systemach raportowania. Z czym mam się przełączyć?**

**Odp.:** Zapoznaj się z dokumentacją [interfejsu API](https://developer.microsoft.com/graph/) , aby zobaczyć, jak można [używać interfejsów API do uzyskiwania dostępu do raportów aktywności](concept-reporting-api.md). Ten punkt końcowy ma dwa raporty (inspekcja i **logowania**), które zawierają wszystkie dane, które zostały uzyskane w starym punkcie końcowym interfejsu API. Ten nowy punkt końcowy zawiera również raport dotyczący logowania z licencją Azure AD — wersja Premium, za pomocą której można uzyskać informacje dotyczące użycia aplikacji, użycia urządzenia i logowania użytkownika.

---

**Pyt.: Obecnie używam `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego do ściągania raportów zabezpieczeń usługi Azure AD (określonych typów wykryć, takich jak nieujawnione poświadczenia lub logowania z anonimowych adresów IP) w naszych systemach raportowania. Z czym mam się przełączyć?**

**Odp.:** Korzystając z  [interfejsu API wykrywania ryzyka ochrony tożsamości](../identity-protection/graph-get-started.md), można uzyskać dostęp do wykrywania zabezpieczeń za pomocą Microsoft Graph. Ten nowy format zapewnia większą elastyczność w zakresie wykonywania zapytań dotyczących danych, z zaawansowanymi filtrowaniem, zaznaczaniem pól i wieloma innymi, a także umożliwia ujednolicenie wykrywania ryzyka w jednym typie w celu łatwiejszej integracji z rozwiązań Siem i innymi narzędziami zbierania danych. Ponieważ dane są w innym formacie, nie można zastąpić nowego zapytania dla starych zapytań. Jednak [Nowy interfejs API używa Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), który jest standardem firmy Microsoft dla takich interfejsów API jak O365 lub Azure AD. W związku z tym wymagana służba może rozciągnąć bieżące inwestycje programu MS Graph lub ułatwić rozpoczęcie przejścia do nowej platformy standardowej.

---

**Pyt.: Jak mogę uzyskać licencję Premium?**

**Odp.:** Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).

---

**Pyt.: Jak wkrótce należy zobaczyć dane dotyczące działań po uzyskaniu licencji Premium?**

**Odp.:** Jeśli masz już dane dotyczące działań jako bezpłatną licencję, możesz ją natychmiast zobaczyć. Jeśli nie masz żadnych danych, dane będą wyświetlane w raportach dopiero po upływie jednego lub dwóch dni.

---

**Pyt.: Czy po uzyskaniu licencji usługi Azure AD Premium mogę zobaczyć dane z ostatniego miesiąca?**

**Odp.:** Jeśli ostatnio przełączono się do wersji Premium (w tym wersji próbnej), na początku można zobaczyć dane do 7 dni. Po zebraniu danych można zobaczyć dane z ostatnich 30 dni.

---

**Pyt.: Czy muszę być administratorem globalnym, aby wyświetlić logowania do Azure Portal lub pobrać dane za pomocą interfejsu API?**

**Odp.:** Nie. Możesz również uzyskać dostęp do danych raportowania za pomocą portalu lub za pomocą interfejsu API, jeśli jesteś **czytelnikiem zabezpieczeń** lub **administratorem zabezpieczeń** dla dzierżawy. Oczywiście **administratorzy globalni** będą również mieli dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**Pyt.: Co to jest przechowywanie danych dla dzienników aktywności (inspekcja i logowania) w Azure Portal?** 

**Odp.:** W poniższej tabeli przedstawiono okres przechowywania danych dzienników aktywności. Aby uzyskać więcej informacji, zobacz [zasady przechowywania danych dla raportów usługi Azure AD](reference-reports-data-retention.md).

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | ND           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

---

**Pyt.: Jak długo trwa czas, aż zobaczysz dane dotyczące aktywności po zakończeniu zadania?**

**Odp.:** Dzienniki inspekcji mają opóźnienie z przedziału od 15 minut do godziny. Dzienniki aktywności logowania mogą trwać od 15 minut do 2 godzin w przypadku niektórych rekordów.

---

**Pyt.: Czy mogę uzyskać informacje o dzienniku aktywności pakietu Office 365 za pomocą Azure Portal?**

**Odp.:** Mimo że działania pakietu Office 365 i dzienniki aktywności usługi Azure AD współdzielą wiele zasobów katalogu, aby uzyskać pełny widok dzienników aktywności pakietu Office 365, należy przejść do [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com) , aby uzyskać informacje o dzienniku aktywności pakietu Office 365.

---

**Pyt.: Które interfejsy API są używane do uzyskiwania informacji o dziennikach aktywności pakietu Office 365?**

**Odp.:** Użyj [interfejsów API zarządzania pakietu office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) , aby uzyskać dostęp do dzienników aktywności pakietu Office 365 za pomocą interfejsu API.

---

**Pyt.: Ile rekordów można pobrać z Azure Portal?**

**Odp.:** Z witryny Azure Portal można pobrać maksymalnie 5000 rekordów. Rekordy są sortowane według najnowszych ustawień i domyślnie otrzymują najnowsze rekordy 5000.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**Pyt.: Istnieje możliwość wykrywania zagrożeń w usłudze Identity Protection, ale nie widzę odpowiedniego logowania w raporcie logowania. Czy jest to oczekiwane?**

**Odp.:** Tak, program Identity Protection szacuje ryzyko dla wszystkich przepływów uwierzytelniania niezależnie od tego, czy interaktywny, czy nieinteraktywny. Jednak wszystkie raporty dotyczące tylko logowania są wyświetlane tylko w przypadku logowania interakcyjnego.

---

**Pyt.: Jak mogę wiedzieć, dlaczego zalogowanie lub użytkownik został oflagowany ryzykiem w Azure Portal?**

**Odp.:** Jeśli masz subskrypcję usługi **Azure AD — wersja Premium** , możesz dowiedzieć się więcej na temat podstawowych wykryć ryzyka, wybierając użytkownika w **przypadku użytkowników oflagowanych w ramach ryzyka** lub wybierając rekord w raporcie ryzykowne **logowania** . Jeśli masz subskrypcję **bezpłatną** lub **podstawową** , możesz przeglądać użytkowników na ryzyko i ryzykowne raporty dotyczące logowania, ale nie widzisz podstawowych informacji o wykrywaniu ryzyka.

---

**Pyt.: Jak są obliczane adresy IP w raporcie logowania i ryzykowne logowania?**

**Odp.:** Adresy IP są wystawiane w taki sposób, że nie istnieje ostateczne połączenie między adresem IP i lokalizacją, w której komputer z tym adresem jest fizycznie zlokalizowany. Mapowanie adresów IP jest bardziej skomplikowane, ponieważ takie czynniki jak dostawcy urządzeń przenośnych i sieci VPN wystawiające adresy IP z pul centralnych często są bardzo daleko od miejsca, w którym urządzenie klienckie jest rzeczywiście używane. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym nakładem pracy na podstawie śladów, danych rejestru, wyszukiwania wstecznego i innych informacji. 

---

**Pyt.: Co oznacza wykrycie ryzyka "Logowanie z dodatkowym ryzykiem" oznacza?**

**Odp.:** Aby uzyskać wgląd we wszystkie ryzykowne logowania w środowisku, "Zaloguj się z dodatkowym ryzykiem wykrytym" funkcji jako symbol zastępczy dla logowań do wykrycia, które są wyłączne dla subskrybentów Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**Pyt.: Co nowego w tej funkcji?**

**Odp.:** Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za całym raportem logowania. Klienci mogą zapoznać się ze stanem dostępu warunkowego i szczegółowe w celu uzyskania szczegółowych informacji dotyczących zasad, które zastosowały się do logowania, oraz wyników dla każdej zasady.

**Pyt.: Jak mogę rozpocząć?**

**Odp.:** Aby rozpocząć pracę:

* Przejdź do raportu logowania w [Azure Portal](https://portal.azure.com).
* Kliknij Logowanie, które chcesz rozwiązać.
* Przejdź do karty **dostęp warunkowy** . W tym miejscu można wyświetlić wszystkie zasady, które mają wpływ na logowanie i wyniki dla każdej zasady. 
    
**Pyt.: Jakie są wszystkie możliwe wartości stanu dostępu warunkowego?**

**Odp.:** Stan dostępu warunkowego może mieć następujące wartości:

* **Nie zastosowano**: Oznacza to, że nie ma żadnych zasad urzędu certyfikacji z określonym zakresem użytkownika i aplikacji. 
* **Powodzenie**: Oznacza to, że wystąpiły zasady urzędu certyfikacji z użytkownikiem i aplikacją w zakresie, a zasady urzędu certyfikacji zostały pomyślnie spełnione. 
* **Błąd**: Oznacza to, że wystąpiły zasady urzędu certyfikacji z użytkownikiem i aplikacją w zakresie, a zasady urzędu certyfikacji nie zostały spełnione. 
    
**Pyt.: Jakie są wszystkie możliwe wartości wyniku zasady dostępu warunkowego?**

**Odp.:** Zasady dostępu warunkowego mogą mieć następujące wyniki:

* **Powodzenie**: Zasady zostały pomyślnie spełnione.
* **Błąd**: Zasady nie zostały spełnione.
* **Nie zastosowano**: Może to być spowodowane tym, że warunki zasad nie zostały spełnione.
* **Nie włączono**: Wynika to z zasad w stanie wyłączenia. 
    
**Pyt.: Nazwa zasad we wszystkich raportach logowania nie jest zgodna z nazwą zasad w urzędzie certyfikacji. Dlaczego?**

**Odp.:** Nazwa zasad we wszystkich raportach logowania jest oparta na nazwie zasad urzędu certyfikacji w momencie logowania. Ta wartość może być niespójna z nazwą zasad w urzędzie certyfikacji w przypadku późniejszej aktualizacji nazwy zasad, czyli po zalogowaniu się.

**Pyt.: Moje logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport działań związanych z logowaniem pokazuje, że logowanie powiodło się. Dlaczego?**

**Odp.:** Obecnie raport logowania może nie wyświetlać dokładnych wyników scenariuszy programu Exchange ActiveSync, gdy jest stosowany dostęp warunkowy. Mogą wystąpić sytuacje, w których wynik logowania w raporcie przedstawia Pomyślne logowanie, ale logowanie nie powiodło się z powodu zasad dostępu warunkowego. 
