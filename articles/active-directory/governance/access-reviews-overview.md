---
title: Co to są przeglądy dostępu? — Azure Active Directory | Microsoft Docs
description: Korzystając z przeglądów dostępu usługi Azure Active Directory, można kontrolować członkostwo w grupach i dostęp do aplikacji, aby sprostać inicjatywom nadzoru, zarządzania ryzykiem i zgodności w organizacji.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262089"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co to są przeglądy dostępu w usłudze Azure AD?

Przeglądy dostępu usługi Azure Active Directory (Azure AD) umożliwiają organizacjom efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Dostęp użytkownika można regularnie przeglądać, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.

Oto film, który zawiera szybki przegląd opinii o dostępie:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Dlaczego opinie o dostępie są ważne?

Usługa Azure AD umożliwia wewnętrzna współpracę w organizacji i z użytkownikami z organizacji zewnętrznych, takich jak partnerzy. Użytkownicy mogą dołączać do grup, zapraszać gości, łączyć się z aplikacjami w chmurze i pracować zdalnie z urządzeń służbowych lub osobistych. Wygoda wykorzystania możliwości samoobsługi doprowadziła do potrzeby lepszego zarządzania dostępem.

- W jaki sposób, w miarę jak dołączają nowi pracownicy, mają odpowiedni dostęp do produktywnych?
- Jak ludzie przenieść zespoły lub opuścić firmę, w jaki sposób można zapewnić ich stary dostęp jest usuwany, zwłaszcza gdy dotyczy gości?
- Nadmierne prawa dostępu mogą prowadzić do ustaleń inspekcji i naruszyć, ponieważ wskazują one na brak kontroli nad dostępem.
- Musisz aktywnie współpracować z właścicielami zasobów, aby upewnić się, że regularnie sprawdzają, kto ma dostęp do swoich zasobów.

## <a name="when-to-use-access-reviews"></a>Kiedy korzystać z recenzji dostępu?

- **Zbyt wielu użytkowników w rolach uprzywilejowanych:** Warto sprawdzić, ilu użytkowników ma dostęp administracyjny, ilu z nich jest administratorami globalnymi i czy są zaproszeni goście lub partnerzy, którzy nie zostali usunięci po przydzieleniu do wykonania zadania administracyjnego. Można ponownie certyfikować użytkowników przypisywania ról w [rolach usługi Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) takich jak administratorzy globalni lub [role zasobów platformy Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) takie jak Administrator dostępu użytkownika w środowiska [zarządzania uprzywilejowanymi tożsamościami usługi Azure AD (PIM).](../privileged-identity-management/pim-configure.md)
- **Gdy automatyzacja jest nieupozywalna:** Można utworzyć reguły dynamicznego członkostwa w grupach zabezpieczeń lub grupach usługi Office 365, ale co zrobić, jeśli dane pakietu DANYCH nie są w usłudze Azure AD lub jeśli użytkownicy nadal potrzebują dostępu po opuszczeniu grupy, aby wyszkolić ich zastąpienie? Następnie można utworzyć przegląd w tej grupie, aby upewnić się, że osoby, które nadal potrzebują dostępu, powinny mieć stały dostęp.
- **Gdy grupa jest używana do nowego celu:** Jeśli masz grupę, która ma być zsynchronizowana z usługą Azure AD lub jeśli planujesz włączyć aplikację Salesforce dla wszystkich w grupie zespołu sprzedaży, warto poprosić właściciela grupy o sprawdzenie członkostwa w grupie przed użyciem grupy w innej zawartości ryzyka.
- **Dostęp do danych o krytycznym znaczeniu** dla działalności biznesowej: w przypadku niektórych zasobów może być konieczne zwrócenie się do osób spoza działu IT o regularne wylogowywanie się i uzasadnienie powodów, dla których potrzebują dostępu do celów inspekcji.
- **Aby zachować listę wyjątków zasad:** W idealnym świecie wszyscy użytkownicy będą przestrzegać zasad dostępu, aby zabezpieczyć dostęp do zasobów organizacji. Czasami jednak zdarzają się przypadki biznesowe, które wymagają wyjątków. Jako administrator IT możesz zarządzać tym zadaniem, unikać nadzoru nad wyjątkami zasad i dostarczać audytorom dowód, że te wyjątki są regularnie sprawdzane.
- **Poproś właścicieli grup, aby potwierdzili, że nadal potrzebują gości w swoich grupach:** Dostęp pracowników może być zautomatyzowany z niektórymi lokalnymi rozwiązaniami IAM, ale nie zaproszonymi gośćmi. Jeśli grupa daje gościom dostęp do treści wrażliwych w firmie, to właściciel grupy jest odpowiedzialny za potwierdzenie, że goście nadal mają uzasadnioną potrzebę dostępu biznesową.
- **Czy opinie powtarzają się okresowo:** Można skonfigurować cykliczne przeglądy dostępu użytkowników na ustawionych częstotliwościach, takich jak tygodniowe, miesięczne, kwartalne lub roczne, a recenzenci będą powiadamiani na początku każdej recenzji. Recenzenci mogą zatwierdzać lub odmawiać dostępu za pomocą przyjaznego interfejsu i za pomocą inteligentnych zaleceń.

## <a name="where-do-you-create-reviews"></a>Gdzie tworzysz recenzje?

W zależności od tego, co chcesz przejrzeć, utworzysz przegląd dostępu w przeglądach dostępu usługi Azure AD, aplikacjach korporacyjnych usługi Azure AD (w wersji zapoznawczej) lub usłudze Azure AD PIM.

| Prawa dostępu użytkowników | Recenzenci mogą być | Recenzja utworzona w | Doświadczenie recenzenta |
| --- | --- | --- | --- |
| Członkowie grupy zabezpieczeń</br>Członkowie grupy pakietu Office | Określony recenzenci</br>Właściciele grup</br>Recenzja własna | Przeglądy dostępu w usłudze Azure AD</br>Grupy usługi Azure AD | Panel dostępu |
| Przypisany do połączonej aplikacji | Określony recenzenci</br>Recenzja własna | Przeglądy dostępu w usłudze Azure AD</br>Aplikacje dla przedsiębiorstw usługi Azure AD (w wersji zapoznawczej) | Panel dostępu |
| Rola usługi Azure AD | Określony recenzenci</br>Recenzja własna | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal Azure |
| Rola zasobu platformy Azure | Określony recenzenci</br>Recenzja własna | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal Azure |


## <a name="create-access-reviews"></a>Tworzenie recenzji dostępu

Aby utworzyć przeglądy dostępu, wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać przeglądami dostępu i zalogować się jako administrator globalny lub administrator użytkownika.

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.

      ![Wyszukiwanie usługi Azure portal dla usługi Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Wybierz **pozycję Zarządzanie tożsamościami**.

1. Na stronie Wprowadzenie kliknij przycisk **Utwórz recenzję dostępu.**

   ![Strona początkowa przeglądów programu Access](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Dowiedz się więcej o recenzjach dostępu

Aby dowiedzieć się więcej o tworzeniu i wykonywaniu recenzji dostępu, obejrzyj to krótkie demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Jeśli jesteś gotowy do wdrożenia przeglądów dostępu w organizacji, wykonaj następujące kroki w klipie wideo, aby włączyć, wyszkolić administratorów i utworzyć pierwszą recenzję dostępu!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Wymagania licencyjne

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Ile masz licencji?

Upewnij się, że katalog ma co najmniej tyle licencji usługi Azure AD Premium P2, jak masz pracowników, którzy będą wykonywać następujące zadania:

- Użytkownicy członkowi i goście, którzy są przypisani jako recenzenci
- Użytkownicy będący członkami i gośćmi, którzy przeprowadzają samodzielną weryfikację
- Właściciele grup, którzy przeprowadzają przegląd dostępu
- Właściciele aplikacji, którzy przeprowadzają przegląd dostępu

Licencje usługi Azure AD Premium P2 **nie** są wymagane do następujących zadań:

- Żadne licencje nie są wymagane dla użytkowników z rolami administratora globalnego lub administratora użytkownika, które konfigurują przeglądy dostępu, konfigurują ustawienia lub stosują decyzje z recenzji.

Dla każdej płatnej licencji usługi Azure AD Premium P2 przypisanej do jednego z użytkowników własnej organizacji można użyć usługi Azure AD business (B2B), aby zaprosić maksymalnie pięciu użytkowników-gości w ramach dodatku dla użytkowników zewnętrznych. Ci użytkownicy-goście mogą również korzystać z funkcji usługi Azure AD Premium P2. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące licencjonowania współpracy usługi Azure AD B2B](../b2b/licensing-guidance.md).

Aby uzyskać więcej informacji o licencjach, zobacz [Przypisywanie lub usuwanie licencji za pomocą portalu usługi Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Przykładowe scenariusze licencji

Oto kilka przykładowych scenariuszy licencji ułatwiające określenie liczby licencji, które muszą mieć.

| Scenariusz | Obliczenia | Liczba licencji |
| --- | --- | --- |
| Administrator tworzy przegląd dostępu grupy A z 75 użytkownikami i właścicielem 1 grupy i przypisuje właściciela grupy jako recenzenta. | 1 licencja dla właściciela grupy jako recenzenta | 1 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkownikami i 3 właścicielami grup i przypisuje 3 właścicieli grup jako recenzentów. | 3 licencje dla każdego właściciela grupy jako recenzentów | 3 |
| Administrator tworzy przegląd dostępu grupy B z 500 użytkownikami. Sprawia, że jest to samoocenę. | 500 licencji dla każdego użytkownika jako recenzentów | 500 |
| Administrator tworzy przegląd dostępu grupy C z 50 użytkownikami członkowskimi i 25 użytkownikami-gośćmi. Sprawia, że jest to samoocenę. | 50 licencji dla każdego użytkownika jako recenzentów.<br/>(użytkownicy-goście są objęci wymaganym stosunkiem 1:5) | 50 |
| Administrator tworzy przegląd dostępu grupy D z 6 użytkowników członkowskich i 108 użytkowników-gości. Sprawia, że jest to samoocenę. | 6 licencji dla każdego użytkownika jako recenzentów samodzielnie + 16 dodatkowych licencji obejmujących wszystkich 108 użytkowników-gości w wymaganym stosunku 1:5. 6 licencji, które obejmują\*6 5 = 30 użytkowników gości. Dla pozostałych użytkowników (108-6\*5)=78 użytkowników-gości wymagane jest 78/5=16 dodatkowych licencji. W ten sposób w sumie wymagane są 6+16=22 licencje. | 22 |

## <a name="next-steps"></a>Następne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Ukończ przegląd dostępu grup lub aplikacji](complete-access-review.md)
