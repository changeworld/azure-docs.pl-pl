---
title: Usługa Azure Active Directory raporty — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane quesitons wokół raporty usługi Azure Active Directory.
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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 98a1dd3fb3fd733cc17ac9c6ccf9d0dfc77737e1
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868025"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Często zadawane pytania dotyczące usługi Azure Active Directory raporty

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Active Directory (Azure AD), raportowanie. Aby uzyskać więcej informacji, zobacz [Raporty w usłudze Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Wprowadzenie 

**P: czy mogę używać obecnie https://graph.windows.net/&lt; nazwa dzierżawy&gt;punktu końcowego /reports/ raporty do naszych systemów raportowania programowo interfejsy API w celu ściągania usługi Azure AD, inspekcji i użycia zintegrowanej aplikacji. Co powinni przełączyć się na?**

**Odp.:** wyszukiwania [dokumentacja interfejsu API](https://developer.microsoft.com/graph/) można zobaczyć, jak [dostępu do raportów aktywności przy użyciu interfejsów API](concept-reporting-api.md). Ten punkt końcowy ma dwa raporty (**inspekcji** i **logowania**) które zawierają wszystkie dane uzyskany w starym punkt końcowy interfejsu API. Ten nowy punkt końcowy ma również raportu logowania przy użyciu licencji usługi Azure AD Premium, która służy do użycia aplikacji, użycie urządzenia i informacje logowania użytkownika.

--- 

**P: czy mogę używać obecnie https://graph.windows.net/&lt; nazwa dzierżawy&gt;/reports/ punktu końcowego interfejsów API, aby ściągnąć raporty dotyczące zabezpieczeń usługi Azure AD (określonych typów wykrywania zagrożeń, takich jak ujawnione poświadczenia lub logowania z anonimowych adresów IP) do naszych systemów raportowania programowo. Co powinni przełączyć się na?**

**Odp.:** można użyć [zdarzeń o podwyższonym ryzyku Identity Protection interfejsu API](../identity-protection/graph-get-started.md) do wykrywania zabezpieczeń dostępu za pomocą programu Microsoft Graph. Ten nowy format zapewnia większą elastyczność w sposób można tworzyć zapytania danych przy użyciu zaawansowanego filtrowania, pole wyboru i innych i standaryzuje zdarzeń o podwyższonym ryzyku w jeden typ ułatwia integrację rozwiązań Siem i inne narzędzia do zbierania danych. Ponieważ dane są w innym formacie, nie może zastąpić nowe zapytanie stare zapytań. Jednak [nowy interfejs API korzysta z programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), czyli standard firmy Microsoft dla interfejsów API, takich jak usługi Office 365 lub Azure AD. Dzięki pracy wymagane albo rozszerzyć dotychczasowe inwestycje w rozwiązania MS Graph Pomoc systemu lub możesz rozpocząć przejście do ta nowa platforma standardowych.

--- 

**P: jak uzyskać licencję premium?**

**Odp.:** zobacz [wprowadzenie do usługi Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md) uaktualnić swoją wersję usługi Azure Active Directory.

---

**P: jak wkrótce powinien zostać wyświetlony danych działania po otrzymaniu licencji premium?**

**Odp.:** Jeśli masz już dane działań w postaci bezpłatnej licencji, a następnie można go było wyświetlić natychmiast. Jeśli nie masz żadnych danych, następnie potrwa jeden lub dwa dni, dane wyświetlane w raportach.

---

**P: czy można widzę danych w ciągu ostatniego miesiąca po otrzymaniu licencję usługi Azure AD w wersji premium?**

**Odp.:** Jeśli ostatnio nastąpiło przełączenie do wersji Premium (w tym wersja próbna), możesz zobaczyć dane się do 7 dni początkowo. Gdy dane są gromadzone, dane można wyświetlić w ciągu ostatnich 30 dni.

---

**Pyt.: czy muszę być administratorem globalnym, aby zobaczyć działanie logowania do witryny Azure portal lub można pobrać danych za pośrednictwem interfejsu API?**

**A:** nie, można również przejść danych raportowania za pośrednictwem portalu lub za pośrednictwem interfejsu API przypadku **Czytelnik zabezpieczeń** lub **Administrator zabezpieczeń** dla dzierżawy. Oczywiście **Administratorzy globalni** mają również dostęp do tych danych.

---


## <a name="activity-logs"></a>Dzienniki aktywności


**P: co to jest okres przechowywania danych o Dzienniki aktywności (inspekcji i logowania) w witrynie Azure portal?** 

**Odp.:** w poniższej tabeli wymieniono okres przechowywania danych dla dzienników aktywności. Aby uzyskać więcej informacji, zobacz [zasadami przechowywania danych dla raportów usługi Azure AD](reference-reports-data-retention.md).

| Raport                 | Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Dzienniki inspekcji             | 7 dni        | 30 dni             | 30 dni             |
| Logowania               | ND           | 30 dni             | 30 dni             |
| Użycie usługi Azure MFA        | 30 dni       | 30 dni             | 30 dni             |

--- 

**Pytanie: jak długo trwa aż mogę zobaczyć dane o aktywności, po zakończeniu czy Moje zadania?**

**Odp.:** dzienników inspekcji mają opóźnienie w zakresie od 15 minut do godziny. Dzienniki aktywności logowania może potrwać od 15 minut do maksymalnie 2 godziny na niektórych rekordów.

---

**P: czy można uzyskać informacji z dziennika aktywności usługi Office 365 za pośrednictwem witryny Azure portal?**

**Odp.:** mimo że aktywności usługi Office 365 i usługi Azure AD działania dzienniki współdzielą wiele zasobów katalogu, jeśli potrzebujesz pełnego widoku dzienników aktywności usługi Office 365, przejdź do Centrum administracyjnego usługi Office 365 można pobrać informacji o dzienniku aktywności usługi Office 365.

---

**P: które interfejsów API należy używać, aby uzyskać informacje na temat dzienników aktywności usługi Office 365?**

**Odp.:** użyj [interfejsów API zarządzania usługi Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) dostęp do dzienników aktywności usługi Office 365 za pośrednictwem interfejsu API.

---

**P: jak dużo rekordów można pobrać z witryny Azure portal?**

**Odp.:** maksymalnie 5000 rekordów można pobrać z witryny Azure portal. Rekordy są sortowane według *najnowszych* i domyślnie otrzymujesz najnowszej 5000 rekordów.

---

## <a name="risky-sign-ins"></a>Ryzykowne logowania

**P: występuje zdarzenie o podwyższonym ryzyku w ochronie tożsamości, ale nie widzę odpowiedniego Zaloguj się w raporcie logowań. Jest to oczekiwane?**

**Odp.:** tak, Identity Protection ocenia ryzyko dla wszystkich przepływów uwierzytelniania, czy interakcyjnego lub jako nieinteraktywną. Jednak wszystkie logowania tylko przedstawia tylko interakcyjne sesje logowania.

---

**P: jak sprawdzić, dlaczego Zaloguj się lub użytkownik została oflagowana ryzykowne w witrynie Azure portal?**

**Odp.:** w przypadku **usługi Azure AD Premium** subskrypcji, możesz dodatkowe informacje na temat zdarzeń związanych z ryzykiem, wybierając użytkownika w **użytkownicy oflagowani w związku z ryzykiem** lub wybierając rekord w  **Ryzykowne logowania** raportu. Jeśli masz **bezpłatna** lub **podstawowe** subskrypcji, możesz wyświetlić użytkowników o podwyższonym ryzyku i raportów ryzykownych logowań, ale nie widać podstawowych informacji o zdarzeniu o podwyższonym ryzyku.

---

**P: jak adresy IP są obliczane w logowania i raport dotyczący ryzykownych logowań**

**Odp.:** adresy IP są przydzielane w taki sposób, że nie ma ostateczne połączenia między adresem IP i gdzie fizycznie znajduje się komputer przy użyciu tego adresu. Czynników, takich jak dostawców mobilnych i sieci VPN często bardzo wydawania adresów IP z centralnego pul dalekie od wartości, w którym będzie faktycznie używana na urządzeniu klienckim dalsze skomplikowany mapowania adresów IP. Obecnie w raportach usługi Azure AD, konwertowania adresu IP do lokalizacji fizycznej jest najlepszy nakład pracy na podstawie danych śledzenia, dane rejestru, odwrotnej wyszukiwań i inne informacje. 

---

**P: co to jest zdarzenie o podwyższonym ryzyku "Logowanie z dodatkowym ryzykiem wykryto" oznaczającego?**

**Odp.:** umożliwiają wgląd w wszystkich ryzykownych logowań w danym środowisku, "Logowanie z dodatkowym ryzykiem wykryto" działa jako symbolu zastępczego dla operacji logowania do wykrywania, które są na wyłączność dla subskrybentów usługi Azure AD Identity Protection.

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

**Pytanie: Moja Logowanie zostało zablokowane z powodu zasad dostępu warunkowego, ale raport aktywności logowania pokazuje, czy logowanie zakończyło się pomyślnie. Dlaczego?**

**Odp.:** obecnie raport logowania może nie zawierać dokładne wyniki dla scenariuszy programu Exchange ActiveSync, stosowania dostępu warunkowego. Może istnieć przypadków, gdy wynik Zaloguj się w raporcie pokazuje pomyślne logowanie, ale logowania w rzeczywistości nie powiodło się z powodu zasad dostępu warunkowego. 
