---
title: Usługa Azure Active Directory raporty — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane quesitons wokół raporty usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
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
ms.openlocfilehash: 6fc90d62f889bfd9f439a7e8955f049c6c979746
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437043"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące usługi Azure Active Directory raporty

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD), raportowanie. Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**Pyt.: Obecnie używana `https://graph.windows.net/<tenant-name>/reports/` punktu końcowego raporty do naszych systemów raportowania programowo interfejsy API w celu ściągania usługi Azure AD, inspekcji i użycia zintegrowanej aplikacji. Co powinni przełączyć się na?**

**Odp.:** Wyszukaj [dokumentacja interfejsu API](https://developer.microsoft.com/graph/) można zobaczyć, jak [dostępu do raportów aktywności przy użyciu interfejsów API](concept-reporting-api.md). Ten punkt końcowy ma dwa raporty (**inspekcji** i **logowania**) które zawierają wszystkie dane uzyskany w starym punkt końcowy interfejsu API. Ten nowy punkt końcowy ma również raportu logowania przy użyciu licencji usługi Azure AD Premium, która służy do użycia aplikacji, użycie urządzenia i informacje logowania użytkownika.

---

**Pyt.: Obecnie używana `https://graph.windows.net/<tenant-name>/reports/` punktu końcowego interfejsów API, aby ściągnąć raporty dotyczące zabezpieczeń usługi Azure AD (określonych typów wykrywania zagrożeń, takich jak ujawnione poświadczenia lub logowania z anonimowych adresów IP) do naszych systemów raportowania programowo. Co powinni przełączyć się na?**

**Odp.:** Możesz użyć [zdarzeń o podwyższonym ryzyku Identity Protection API](../identity-protection/graph-get-started.md) do wykrywania zabezpieczeń dostępu za pomocą programu Microsoft Graph. Ten nowy format zapewnia większą elastyczność w sposób można tworzyć zapytania danych przy użyciu zaawansowanego filtrowania, pole wyboru i innych i standaryzuje zdarzeń o podwyższonym ryzyku w jeden typ ułatwia integrację rozwiązań Siem i inne narzędzia do zbierania danych. Ponieważ dane są w innym formacie, nie może zastąpić nowe zapytanie stare zapytań. Jednak [nowy interfejs API korzysta z programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), czyli standard firmy Microsoft dla interfejsów API, takich jak usługi Office 365 lub Azure AD. Dzięki pracy wymagane albo rozszerzyć dotychczasowe inwestycje w rozwiązania MS Graph Pomoc systemu lub możesz rozpocząć przejście do ta nowa platforma standardowych.

---

**Pyt.: Jak uzyskać licencję premium?**

**Odp.:** Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).

---

**Pyt.: Jak szybko powinien zostać wyświetlony danych działania po otrzymaniu licencji premium?**

**Odp.:** Jeśli masz już dane działań postaci bezpłatnej licencji, a następnie można go było wyświetlić natychmiast. Jeśli nie masz żadnych danych, następnie potrwa jeden lub dwa dni, dane wyświetlane w raportach.

---

**Pyt.: Po otrzymaniu licencja premium usługi Azure AD może wyświetlać dane w ciągu ostatniego miesiąca?**

**Odp.:** Jeśli ostatnio nastąpiło przełączenie do wersji Premium (w tym wersja próbna), widać danych się do 7 dni początkowo. Gdy dane są gromadzone, dane można wyświetlić w ciągu ostatnich 30 dni.

---

**Pyt.: Czy muszę być administratorem globalnym, aby zobaczyć działanie logowania do witryny Azure portal lub można pobrać danych za pośrednictwem interfejsu API?**

**Odp.:** Nie, można również przejść danych raportowania za pośrednictwem portalu lub za pośrednictwem interfejsu API przypadku **Czytelnik zabezpieczeń** lub **Administrator zabezpieczeń** dla dzierżawy. Oczywiście **Administratorzy globalni** mają również dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**Pyt.: Co to jest okres przechowywania danych o Dzienniki aktywności (inspekcji i logowania) w witrynie Azure portal?** 

**Odp.:** W poniższej tabeli wymieniono okres przechowywania danych dla dzienników aktywności. Aby uzyskać więcej informacji, zobacz [zasadami przechowywania danych dla raportów usługi Azure AD](reference-reports-data-retention.md).

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | ND           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

---

**Pyt.: Jak długo trwa aż mogę zobaczyć dane o aktywności, po zakończeniu czy Moje zadania?**

**Odp.:** Dzienniki inspekcji mają opóźnienie w zakresie od 15 minut do godziny. Dzienniki aktywności logowania może potrwać od 15 minut do maksymalnie 2 godziny na niektórych rekordów.

---

**Pyt.: Czy można uzyskać informacji z dziennika aktywności usługi Office 365 za pośrednictwem witryny Azure portal?**

**Odp.:** Mimo że aktywności usługi Office 365 i usługi Azure AD działania dzienniki współdzielą wiele zasobów katalogu, jeśli potrzebujesz pełnego widoku dzienników aktywności usługi Office 365, należy przejść do [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com) można pobrać dziennika aktywności usługi Office 365 informacje.

---

**Pyt.: Które interfejsów API należy używać, aby uzyskać informacje na temat dzienników aktywności usługi Office 365?**

**Odp.:** Użyj [interfejsów API zarządzania usługi Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) dostęp do dzienników aktywności usługi Office 365 za pośrednictwem interfejsu API.

---

**Pyt.: Liczba rekordów można pobrać z witryny Azure portal?**

**Odp.:** Z witryny Azure Portal można pobrać maksymalnie 5000 rekordów. Rekordy są sortowane według *najnowszych* i domyślnie otrzymujesz najnowszej 5000 rekordów.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**Pyt.: Występuje zdarzenie o podwyższonym ryzyku w ochronie tożsamości, ale nie widzę odpowiedniego Zaloguj się w raporcie logowań. Jest to oczekiwane?**

**Odp.:** Tak, Identity Protection ocenia ryzyko dla wszystkich przepływów uwierzytelniania, czy uwierzytelnianie interakcyjne nieinterakcyjnych. Jednak wszystkie logowania tylko przedstawia tylko interakcyjne sesje logowania.

---

**Pyt.: Jak sprawdzić, dlaczego Zaloguj się lub użytkownik została oflagowana ryzykowne w witrynie Azure portal?**

**Odp.:** Jeśli masz **usługi Azure AD Premium** subskrypcji, możesz dodatkowe informacje na temat zdarzeń związanych z ryzykiem, wybierając użytkownika w **użytkownicy oflagowani w związku z ryzykiem** lub wybierając rekord w **ryzykowne operacje logowania** raportu. Jeśli masz **bezpłatna** lub **podstawowe** subskrypcji, możesz wyświetlić użytkowników o podwyższonym ryzyku i raportów ryzykownych logowań, ale nie widać podstawowych informacji o zdarzeniu o podwyższonym ryzyku.

---

**Pyt.: Jak obliczane są adresy IP w logowania i raport dotyczący ryzykownych logowań?**

**Odp.:** Adresy IP są przydzielane w taki sposób, że nie ma ostateczne połączenia między adresem IP i gdzie fizycznie znajduje się komputer przy użyciu tego adresu. Czynników, takich jak dostawców mobilnych i sieci VPN często bardzo wydawania adresów IP z centralnego pul dalekie od wartości, w którym będzie faktycznie używana na urządzeniu klienckim dalsze skomplikowany mapowania adresów IP. Obecnie w raportach usługi Azure AD, konwertowania adresu IP do lokalizacji fizycznej jest najlepszy nakład pracy na podstawie danych śledzenia, dane rejestru, odwrotnej wyszukiwań i inne informacje. 

---

**Pyt.: Co to jest zdarzenie o podwyższonym ryzyku "Logowanie z dodatkowym ryzykiem wykryto" oznaczającego?**

**Odp.:** Daje wgląd dotyczący ryzykownych logowań w danym środowisku, funkcje "Logowanie z dodatkowym ryzykiem wykryto" jako symbol zastępczy dla operacji logowania do wykrywania, które są na wyłączność dla subskrybentów usługi Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**Pyt.: What's new za pomocą tej funkcji?**

**Odp.:** Klienci, teraz można rozwiązywać problemy z zasad dostępu warunkowego za pomocą wszystkich raporcie logowań. Klientów można przeglądać stanu dostępu warunkowego i dokładniej omówić szczegółowe informacje o zasadach które są stosowane do logowania i wyników dla każdej zasady.

**Pyt.: Jak rozpocząć pracę?**

**Odp.:** Aby rozpocząć pracę:

* Przejdź do raportów logowania w [witryny Azure portal](https://portal.azure.com).
* Polecenie logowania, które użytkownik chce Rozwiązywanie problemów.
* Przejdź do **dostępu warunkowego** kartę. W tym miejscu możesz wyświetlić wszystkie zasady, które wpływ na logowanie i wyników dla każdej zasady. 
    
**Pyt.: Co to są wszystkie możliwe wartości dla stanu dostępu warunkowego?**

**Odp.:** Stan dostępu warunkowego może mieć następujące wartości:

* **Niestosowane**: Oznacza to, że nie ma żadnych zasad dostępu Warunkowego za pomocą użytkownika i aplikacji w zakresie. 
* **Powodzenie**: Oznacza to, że wystąpił zasad dostępu Warunkowego, użytkownika i aplikacji w zakresie i zasady dostępu Warunkowego zostały pomyślnie spełnione. 
* **Błąd**: Oznacza to, że wystąpił zasad dostępu Warunkowego, użytkownika i aplikacji w zakresie i zasady dostępu Warunkowego nie zostały spełnione. 
    
**Pyt.: Co to są wszystkie możliwe wartości dla wyników zasad dostępu warunkowego?**

**Odp.:** Zasady dostępu warunkowego może mieć następujące wyniki:

* **Powodzenie**: Zasady pomyślnie był spełniony.
* **Błąd**: Zasady nie był spełniony.
* **Niestosowane**: Może to być, ponieważ nie spełniały warunki zasady.
* **Nie włączono**: Jest to spowodowane zasad w stanie wyłączenia. 
    
**Pyt.: Nazwa zasad w raporcie wszystkie logowania jest niezgodny z nazwa zasad w urzędzie certyfikacji. Dlaczego?**

**Odp.:** Nazwa zasad w raporcie wszystkie logowania opiera się na nazwę zasad urzędu certyfikacji podczas logowania. Może to być niezgodne z nazwa zasad w urzędzie certyfikacji, jeśli zaktualizowano nazwę zasady później, oznacza to, że po zalogowaniu.

**Pyt.: Moje Logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport aktywności logowania pokazuje, czy logowanie zakończyło się pomyślnie. Dlaczego?**

**Odp.:** Obecnie w raporcie logowania nie mogą być wyświetlane dokładne wyniki dla scenariuszy programu Exchange ActiveSync, stosowania dostępu warunkowego. Może istnieć przypadków, gdy wynik Zaloguj się w raporcie pokazuje pomyślne logowanie, ale logowania w rzeczywistości nie powiodło się z powodu zasad dostępu warunkowego. 
