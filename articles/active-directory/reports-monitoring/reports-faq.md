---
title: Raporty usługi Azure Active Directory – często zadawane pytania | Dokumenty firmy Microsoft
description: Często zadawane pytania dotyczące raportów usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266509"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące raportów usługi Azure Active Directory

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące raportowania usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**P: Obecnie używam `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego do ściągania inspekcji usługi Azure AD i zintegrowanych raportów użycia aplikacji do naszych systemów raportowania programowo. Na co należy się przełączyć?**

**Odp.:** Sprawdź [odwołanie do interfejsu API,](https://developer.microsoft.com/graph/) aby zobaczyć, jak można używać [interfejsów API do uzyskiwania dostępu do raportów aktywności](concept-reporting-api.md). Ten punkt końcowy ma dwa raporty **(Inspekcja** i **logowania),** które zawierają wszystkie dane, które zostały zebrane w starym punkcie końcowym interfejsu API. Ten nowy punkt końcowy ma również raport logowania z licencją usługi Azure AD Premium, której można użyć do uzyskania użycia aplikacji, użycia urządzenia i informacji logowania użytkownika.

---

**P: Obecnie używam `https://graph.windows.net/<tenant-name>/reports/` interfejsów API punktu końcowego do ściągania raportów zabezpieczeń usługi Azure AD (określonych typów wykrywania, takich jak wyciekające poświadczenia lub logowania z anonimowych adresów IP) do naszych systemów raportowania programowo. Na co należy się przełączyć?**

**Odp.:** Interfejs API [wykrywania ryzyka ochrony tożsamości](../identity-protection/graph-get-started.md)umożliwia dostęp do wykrywania zabezpieczeń za pośrednictwem programu Microsoft Graph. Ten nowy format zapewnia większą elastyczność w sposobie wykonywania zapytań o dane dzięki zaawansowanemu filtrowaniu, wyborowi pól i nie tylko, a także standaryzuje wykrywanie ryzyka w jeden typ, co ułatwia integrację z modułami SIEM i innymi narzędziami do zbierania danych. Ponieważ dane są w innym formacie, nie można zastąpić nowe zapytanie dla starych zapytań. Jednak [nowy interfejs API używa programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), który jest standardem firmy Microsoft dla takich interfejsów API, jak O365 lub Azure AD. Dlatego wymagana praca może rozszerzyć bieżące inwestycje programu Microsoft Graph lub ułatwić rozpoczęcie przejścia na nową standardową platformę.

---

**P: Jak uzyskać licencję premium?**

**Odp.:** Zobacz [Wprowadzenie do usługi Azure Active Directory Premium,](../fundamentals/active-directory-get-started-premium.md) aby uaktualnić wersję usługi Azure Active Directory.

---

**Pyt.: Jak szybko powinienem zobaczyć dane dotyczące działań po uzyskaniu licencji premium?**

**Odp.:** Jeśli masz już dane dotyczące działań jako bezpłatną licencję, możesz je natychmiast zobaczyć. Jeśli nie masz żadnych danych, to zajmie jeden lub dwa dni, aby dane pojawiły się w raportach.

---

**Pyt.: Czy mogę zobaczyć dane z ostatniego miesiąca po uzyskaniu licencji premium usługi Azure AD?**

**Odp.:** Jeśli niedawno przełączony do wersji Premium (w tym wersji próbnej), można zobaczyć dane do 7 dni początkowo. Gdy dane się gromadzą, można wyświetlić dane z ostatnich 30 dni.

---

**Pyt.: Czy muszę być administratorem globalnym, aby zobaczyć logowania aktywności do witryny Azure portal lub uzyskać dane za pośrednictwem interfejsu API?**

**Odp.:** Nie, można również uzyskać dostęp do danych raportowania za pośrednictwem portalu lub interfejsu API, jeśli jesteś **administratorem zabezpieczeń** lub **administratorem zabezpieczeń** dla dzierżawy. Oczywiście **administratorzy globalni** również będą mieli dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**Pyt.: Co to jest przechowywanie danych dla dzienników działań (inspekcji i logowania) w witrynie Azure portal?** 

**Odp.:** W poniższej tabeli wymieniono okres przechowywania danych dla dzienników działań. Aby uzyskać więcej informacji, zobacz [zasady przechowywania danych dla raportów usługi Azure AD](reference-reports-data-retention.md).

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | Nie dotyczy           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

---

**Pyt.: Jak długo trwa, aż zobaczę dane aktywności po wykonaniu zadania?**

**Odp.:** Dzienniki inspekcji mają opóźnienie od 15 minut do godziny. Logi aktywności logowania może trwać od 15 minut do maksymalnie 2 godzin dla niektórych rekordów.

---

**Pyt.: Czy mogę uzyskać informacje dziennika aktywności usługi Office 365 za pośrednictwem witryny Azure portal?**

**Odp.:** Mimo że dzienniki aktywności usługi Office 365 i usługi Azure AD współużytkują wiele zasobów katalogu, jeśli chcesz uzyskać pełny widok dzienników aktywności usługi Office 365, należy przejść do [centrum administracyjnego usługi Microsoft 365,](https://admin.microsoft.com) aby uzyskać informacje dziennika aktywności usługi Office 365.

---

**Pyt.: Których interfejsów API używam do uzyskania informacji o dziennikach aktywności usługi Office 365?**

**Odp.:** Użyj [interfejsów API zarządzania usługą Office 365,](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) aby uzyskać dostęp do dzienników aktywności usługi Office 365 za pośrednictwem interfejsu API.

---

**Pyt.: Ile rekordów można pobrać z witryny Azure portal?**

**Odp.:** Możesz pobrać maksymalnie 5000 rekordów z witryny Azure portal. Rekordy są sortowane według *najnowszych* i domyślnie otrzymujesz najnowsze rekordy 5000.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**Pyt.: Istnieje wykrywanie ryzyka w ochronie tożsamości, ale nie widzę odpowiedniego logowania w raporcie logowania. Czy to się spodziewa?**

**Odp.:** Tak, ochrona tożsamości ocenia ryzyko dla wszystkich przepływów uwierzytelniania, zarówno interaktywnych, jak i nieinterakcyjnych. Jednak wszystkie logowania tylko raport pokazuje tylko interaktywne logowania.

---

**Pyt.: Skąd mam wiedzieć, dlaczego logowanie lub użytkownik został oflagowany jako ryzykowny w witrynie Azure portal?**

**Odp.:** Jeśli masz subskrypcję **usługi Azure AD Premium,** możesz dowiedzieć się więcej o podstawowych wykrywania ryzyka, wybierając użytkownika w **użytkownicy oflagowane dla ryzyka** lub wybierając rekord w raporcie ryzykowne **logowania.** Jeśli masz subskrypcję **bezpłatną** lub **podstawową,** możesz wyświetlić raporty o ryzyku i ryzykownych logowaniach, ale nie widać podstawowych informacji o wykrywaniu ryzyka.

---

**Pyt.: Jak są obliczane adresy IP w raporcie logowania i ryzykownych logowaniach?**

**Odp.:** Adresy IP są wydawane w taki sposób, że nie ma ostatecznego połączenia między adresem IP a miejscem, w którym fizycznie znajduje się komputer z tym adresem. Mapowanie adresów IP dodatkowo komplikują takie czynniki, jak dostawcy usług komórkowych i sieci VPN emitujące adresy IP z centralnych pul, często bardzo daleko od miejsca, w którym urządzenie klienckie jest faktycznie używane. Obecnie w raportach usługi Azure AD konwertowanie adresu IP na lokalizację fizyczną jest najlepszym rozwiązaniem na podstawie śladów, danych rejestru, odwrotnego wyszukiwania i innych informacji. 

---

**Pyt.: Co oznacza wykrywanie ryzyka "Zaloguj się z wykrytym dodatkowym ryzykiem"?**

**Odp.:** Aby uzyskać wgląd w wszystkie ryzykowne logowania w środowisku, "Zaloguj się z dodatkowym ryzykiem wykryte" funkcje jako symbol zastępczy dla logowania do wykrywania, które są dostępne wyłącznie dla subskrybentów usługi Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**P: Co nowego w tej funkcji?**

**Odp.:** Klienci mogą teraz rozwiązywać problemy z zasadami dostępu warunkowego za pośrednictwem wszystkich raportów logowania. Klienci mogą przejrzeć stan dostępu warunkowego i zapoznać się ze szczegółami zasad zastosowanych do logowania i wynik dla każdej zasady.

**P: Jak zacząć?**

**Odp.:** Aby rozpocząć:

* Przejdź do raportu logowania w witrynie [Azure portal](https://portal.azure.com).
* Kliknij logowanie, które chcesz rozwiązać.
* Przejdź do karty **Dostęp warunkowy.** W tym miejscu można wyświetlić wszystkie zasady, które miały wpływ na logowanie i wynik dla każdej zasady. 
    
**Pyt.: Jakie są wszystkie możliwe wartości dla stanu dostęp warunkowy?**

**Odp.:** Stan dostępu warunkowego może mieć następujące wartości:

* **Nie zastosowano:** Oznacza to, że nie było żadnych zasad urzędu certyfikacji z użytkownikiem i aplikacją w zakresie. 
* **Powodzenie:** Oznacza to, że zasady urzędu certyfikacji z użytkownikiem i aplikacją w zakresie i zasady urzędu certyfikacji zostały pomyślnie spełnione. 
* **Niepowodzenie:** Oznacza to, że zasady urzędu certyfikacji z użytkownikiem i aplikacją w zakresie i zasady urzędu certyfikacji nie zostały spełnione. 
    
**Pyt.: Jakie są wszystkie możliwe wartości dla wyniku zasad dostępu warunkowego?**

**Odp.:** Zasady dostępu warunkowego mogą mieć następujące wyniki:

* **Sukces**: Polityka została pomyślnie spełniona.
* **Niepowodzenie:** Zasady nie zostały spełnione.
* **Nieostosowano:** Może to być spowodowane tym, że warunki zasad nie zostały spełnine.
* **Nie włączone:** wynika to z zasad w stanie wyłączonym. 
    
**P: Nazwa zasad we wszystkich raportach logowania nie jest zgodna z nazwą zasad w ucho. Dlaczego?**

**Odp.:** Nazwa zasad we wszystkich raportach logowania jest oparta na nazwie zasad urzędu certyfikacji w momencie logowania. Może to być niezgodne z nazwą zasad w ucho, jeśli nazwa zasad została zaktualizowana później, czyli po zalogowaniu.

**P: Moje logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport aktywności logowania pokazuje, że logowanie zakończyło się pomyślnie. Dlaczego?**

**Odp.:** Obecnie raport logowania może nie wyświetlać dokładnych wyników dla scenariuszy programu Exchange ActiveSync po zastosowaniu dostępu warunkowego. Mogą wystąpić przypadki, gdy wynik logowania w raporcie pokazuje pomyślne zalogowanie, ale logowanie faktycznie nie powiodło się z powodu zasad dostępu warunkowego. 
