---
title: Usługa Azure Active Directory Reporting — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące raportowania usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f1683321e23eff82e73dc9bb44941fc390633b8c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056221"
---
# <a name="azure-active-directory-reporting-faq"></a>Usługa Azure Active Directory reporting — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD), raportowanie. Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**P: używam https://graph.windows.net/&lt; nazwa dzierżawy&gt;punktu końcowego /reports/ raporty do naszych systemów raportowania programowo interfejsy API w celu ściągania usługi Azure AD, inspekcji i użycia zintegrowanej aplikacji. Co powinni przełączyć się na?**

**A:** wyszukać [dokumentacji interfejsu API](https://developer.microsoft.com/graph/) aby zobaczyć, jak można użyć nowych interfejsów API dostępu do [raporty aktywności](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Ten punkt końcowy ma dwa raporty (inspekcji i logowania), które zawierają wszystkie dane, które masz w starym punkt końcowy interfejsu API. Ten nowy punkt końcowy ma również raportu logowania przy użyciu licencji usługi Azure AD Premium, która służy do użycia aplikacji, użycie urządzenia i informacje logowania użytkownika.

--- 

**P: używam https://graph.windows.net/&lt; nazwa dzierżawy&gt;/reports/ punktu końcowego interfejsów API, aby ściągnąć raporty dotyczące zabezpieczeń usługi Azure AD (określonych typów wykrywania zagrożeń, takich jak ujawnione poświadczenia lub logowania z anonimowych adresów IP) do naszych systemów raportowania programowo. Co powinni przełączyć się na?**

**Odp.:** można użyć [zdarzeń o podwyższonym ryzyku Identity Protection interfejsu API](../identity-protection/graph-get-started.md) do wykrywania zabezpieczeń dostępu za pomocą programu Microsoft Graph. Ten nowy format zapewnia większą elastyczność w sposób można tworzyć zapytania danych przy użyciu zaawansowanego filtrowania, pole wyboru i innych i standaryzuje zdarzeń o podwyższonym ryzyku w jeden typ ułatwia integrację rozwiązań Siem i inne narzędzia do zbierania danych. Ponieważ dane są w innym formacie, nie może zastąpić nowe zapytanie stare zapytań. Jednak [nowy interfejs API korzysta z programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), czyli standard firmy Microsoft dla interfejsów API, takich jak usługi Office 365 lub Azure AD. Dzięki pracy wymagane albo rozszerzyć dotychczasowe inwestycje w rozwiązania MS Graph Pomoc systemu lub możesz rozpocząć przejście do ta nowa platforma standardowych.

--- 

**P: jak uzyskać licencję premium?**

**Odp.:** zobacz [wprowadzenie do usługi Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md) odpowiedzi na to pytanie.

---

**P: jak wkrótce powinien zostać wyświetlony danych działania po otrzymaniu licencji premium?**

**Odp.:** Jeśli masz już dane działań w postaci bezpłatnej licencji, a następnie wyświetlenia tych samych danych. Jeśli nie masz żadnych danych, następnie potrwa jeden lub dwa dni.

---

**P: widzę danych w ciągu ostatniego miesiąca po otrzymaniu licencję usługi Azure AD w wersji premium?**

**Odp.:** Jeśli ostatnio nastąpiło przełączenie do wersji Premium (w tym wersja próbna), możesz zobaczyć dane się do 7 dni początkowo. Gdy dane są gromadzone, zobaczysz maksymalnie 30 dni.

---

**Pyt.: czy muszę być administratorem globalnym, aby zobaczyć działanie logowania do witryny Azure portal lub można pobrać danych za pośrednictwem interfejsu API?**

**Odpowiedź:** Nie. Musi być **Czytelnik zabezpieczeń**, **Administrator zabezpieczeń**, lub **administratora globalnego** uzyskać raportowania danych w witrynie Azure portal lub za pośrednictwem interfejsu API.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**P: co to jest okres przechowywania danych o Dzienniki aktywności (inspekcji i logowania) w witrynie Azure portal?** 

**Odp.:** zobacz [ile zebranych danych jest przechowywanych?](reference-reports-data-retention.md#q-for-how-long-is-the-collected-data-stored) odpowiedzi na to pytanie.

--- 

**Pytanie: jak długo trwa aż mogę zobaczyć dane o aktywności, po zakończeniu czy Moje zadania?**

**Odp.:** dzienników aktywności inspekcji mają opóźnienie w zakresie od 15 minut do godziny. Dzienniki aktywności logowania może potrwać od 15 minut do maksymalnie 2 godziny na niektórych rekordów.

---


**P: czy można uzyskać informacji z dziennika aktywności usługi Office 365 za pośrednictwem witryny Azure portal?**

**Odp.:** mimo że aktywności usługi Office 365 i usługi Azure AD działania dzienniki współdzielą wiele zasobów katalogu, jeśli potrzebujesz pełnego widoku dzienników aktywności usługi Office 365, przejdź do Centrum administracyjnego usługi Office 365 można pobrać informacji o dzienniku aktywności usługi Office 365.

---


**P: które interfejsów API należy używać, aby uzyskać informacje na temat dzienników aktywności usługi Office 365?**

**Odp.:** umożliwia dostęp do interfejsów API zarządzania usługi Office 365 [dzienników aktywności usługi Office 365, przy użyciu interfejsu API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: jak dużo rekordów można pobrać z witryny Azure portal?**

**Odp.:** maksymalnie 5000 rekordów można pobrać z witryny Azure portal. Rekordy są sortowane według *najnowszych* i domyślnie otrzymujesz najnowszej 5000 rekordów.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**P: czy istnieje zdarzenie o podwyższonym ryzyku w ochronie tożsamości, ale nie widzę odpowiedniej logowania w wszystkich logowań. Jest to oczekiwane?**

**Odp.:** tak, Identity Protection ocenia ryzyko dla wszystkich przepływów uwierzytelniania, czy interakcyjnego lub jako nieinteraktywną. Jednak wszystkie logowania tylko przedstawia tylko interakcyjne sesje logowania.

---

**P: jak można pobrać raportu "Użytkownicy oflagowani w związku z ryzykiem" w witrynie Azure portal?**

**Odp.:** możliwość pobierania *użytkownicy oflagowani w związku z ryzykiem* raportu zostaną dodane wkrótce.

---

**P: jak sprawdzić, dlaczego Zaloguj się lub użytkownik została oflagowana ryzykowne w witrynie Azure portal?**

**Odp.:** klienci wersji Premium można dowiedzieć się więcej o podstawowych zdarzeń o podwyższonym ryzyku, klikając użytkownika w ramach "Użytkownicy oflagowani w związku z ryzykiem" lub klikając na "ryzykowne logowania". Klienci wersji bezpłatna i podstawowa uzyskać zagrożonych użytkowników i logowania bez podstawowych informacji zdarzeń o podwyższonym ryzyku.

---

**P: jak adresy IP są obliczane w logowania i raport dotyczący ryzykownych logowań**

**Odp.:** adresy IP są przydzielane w taki sposób, że nie ma ostateczne połączenia między adresem IP i gdzie fizycznie znajduje się komputer przy użyciu tego adresu. Jest to skomplikowane od takich czynników, takich jak dostawców mobilnych i sieci VPN często bardzo wydawania adresów IP z centralnego pul dalekie od wartości, w którym będzie faktycznie używana na urządzeniu klienckim. W związku z powyższym konwertowanie adresu IP do lokalizacji fizycznej jest najlepszy nakład pracy na podstawie danych śledzenia, dane rejestru, odwrotnej wyszukiwań i inne informacje. 

---

**P: co to jest zdarzenie o podwyższonym ryzyku "Logowanie z dodatkowym ryzykiem wykryto" oznaczającego?**

**Odp.:** umożliwiają szczegółowych informacji o wszystkich ryzykownych logowań w danym środowisku, "Logowanie z dodatkowym ryzykiem wykryto" działa jako symbolu zastępczego dla operacji logowania do wykrywania, które są na wyłączność dla subskrybentów usługi Azure AD Identity Protection.

---

**P: co to jest zdarzenie o podwyższonym ryzyku "Logowanie z dodatkowym ryzykiem wykryto" oznaczającego?**

**Odp.:** umożliwiają szczegółowych informacji o wszystkich ryzykownych logowań w danym środowisku, "Logowanie z dodatkowym ryzykiem wykryto" działa jako symbolu zastępczego dla operacji logowania do wykrywania, które są na wyłączność dla subskrybentów usługi Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Dostęp warunkowy

**Pyt.: co nowego, za pomocą tej funkcji?**

**Odp.:** klientów teraz można rozwiązywać problemy z zasadami dostępu warunkowego za pomocą wszystkich raporcie logowań. Klientów można przeglądać stanu dostępu warunkowego i dokładniej omówić szczegółowe informacje o zasadach które są stosowane do logowania i wyników dla każdej zasady.

**P: jak zacząć?**

**Odp.:** rozpocząć pracę:
    * Przejdź do raportów logowania w [witryny Azure portal](https://portal.azure.com). 
    * Polecenie logowania, które użytkownik chce Rozwiązywanie problemów.
    * Przejdź do **dostępu warunkowego** kartę. W tym miejscu możesz wyświetlić wszystkie zasady, które wpływ na logowanie i wyników dla każdej zasady. 
    
**Pytanie: jakie są wszystkie możliwe wartości dla stanu dostępu warunkowego?**

**Odp.:** stanu dostępu warunkowego może mieć następujące wartości:
    * **Niestosowane**: oznacza to, że nie ma żadnych zasad dostępu Warunkowego za pomocą użytkownika i aplikacji w zakresie. 
    * **Powodzenie**: oznacza to, że wystąpił zasad dostępu Warunkowego, użytkownika i aplikacji w zakresie i zasady dostępu Warunkowego zostały pomyślnie spełnione. 
    * **Błąd**: oznacza to, że wystąpił zasad dostępu Warunkowego, użytkownika i aplikacji w zakresie i zasady dostępu Warunkowego nie zostały spełnione. 
    
**Pytanie: jakie są wszystkie możliwe wartości dla wyników zasad dostępu warunkowego?**

**Odp.:** zasady dostępu warunkowego może mieć następujące wyniki:
    * **Powodzenie**: zasady pomyślnie był spełniony.
    * **Błąd**: zasady nie był spełniony.
    * **Niestosowane**: może to być spowodowane nie spełniał warunki zasady.
    * **Nie włączono**: jest to spowodowane zasad w stanie wyłączenia. 
    
**Pyt.: Nazwa zasad w raporcie wszystkie logowania jest niezgodny z nazwa zasad w urzędzie certyfikacji. Dlaczego?**

**Odp.:** nazwę zasady w raporcie wszystkie logowania opiera się na nazwę zasad urzędu certyfikacji podczas logowania. Może to być niezgodne z nazwa zasad w urzędzie certyfikacji, jeśli zaktualizowano nazwę zasady później, oznacza to, że po zalogowaniu.