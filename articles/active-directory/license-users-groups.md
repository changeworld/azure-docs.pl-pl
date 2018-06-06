---
title: Licencji użytkowników w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Informacje o licencji użytkownika, jak i użytkowników w usłudze Azure Active Directory.
services: active-directory
documentationcenter: ''
author: jeffgilb
manager: mtillman
editor: ''
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: 7dc71b33f542cf49d8a0abdb16e6e2f21e5dc2a0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713749"
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Szybki Start: Licencji użytkowników w usłudze Azure Active Directory
Na podstawie licencji usługi Azure AD usług pracy aktywować subskrypcję usługi Azure Active Directory (Azure AD) w dzierżawie platformy Azure. Po subskrypcja jest aktywna, możliwości usługi są zarządzane przez administratorów usługi Azure AD i używane przez licencjonowanych użytkowników. Po zakupie pakietu Enterprise Mobility + Security, Azure AD Premium lub Azure AD podstawowa dzierżawy jest aktualizowana subskrypcji, takie jak jego okresu ważności i przedpłaty licencji. Informacje o Twojej subskrypcji, wraz z liczbą przydzielonych lub dostępnych licencji, jest dostępna za pośrednictwem portalu Azure w obszarze **usługi Azure Active Directory** otwierając **licencji** kafelka. **Licencji** bloku jest również najlepszym miejscem, aby zarządzać przypisaniami licencji.

Uzyskiwanie subskrypcji ma wszystkie informacje wymagane do konfigurowania płatnej możliwości, ale nadal musi przypisywać licencje użytkowników dla płatnej usługi Azure AD płatnej funkcji. Każdy użytkownik, którzy powinni mieć dostęp do lub który odbywa się za pośrednictwem usługi Azure AD płatnej funkcji należy przypisać im licencję. Przypisanie licencji jest mapowanie między użytkownikiem i zakupionych usług, takich jak Azure AD Premium, Basic lub pakietu Enterprise Mobility + Security.

Można użyć [przypisanie licencji na podstawie grupy](active-directory-licensing-whatis-azure-portal.md) do konfigurowania reguł, takich jak następujące:
* Wszyscy użytkownicy w katalogu automatycznie otrzymuje licencję
* Każdy z tytułem odpowiednie zadanie pobiera licencji
* Możesz delegować decyzja o innych menedżerów w organizacji (przy użyciu [grup samoobsługi](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Szczegółowe omówienie przypisania licencji do grup, zobacz zaawansowanych scenariuszy i usługi Office 365 licencjonowania scenariuszy, w tym [przypisać licencje do użytkowników na podstawie członkostwa w grupie w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Przypisywanie licencji do użytkowników i grup
Przy użyciu aktywnej subskrypcji, należy najpierw przypisać licencję do siebie i Odśwież przeglądarkę, aby zapewnić, że widoczny wszystkie oczekiwane funkcje uwzględnionych w subskrypcji. Następnym krokiem jest, aby przypisać licencje do użytkowników, którzy potrzebują dostępu do płatnej funkcje usługi Azure AD. Prosty sposób na przypisywanie licencji jest przypisywanie licencji do grup użytkowników, a nie do osób. Po przypisaniu licencji do grupy Wszyscy członkowie grupy przypisanych licencji. Jeśli użytkownicy są dodawane lub usuwane z grupy, odpowiednią licencję jest automatycznie przypisać lub usunąć. 

> [!NOTE]
> Pewnych usług firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Zanim można przypisać licencję do użytkownika, administrator musi określić **lokalizacji użytkowania** właściwości dla użytkownika. Można ustawić tę właściwość w obszarze **użytkownika** &gt; **profilu** &gt; **ustawienia** w portalu Azure. Używając przypisanie do grupy licencji, każdy użytkownik nie określono lokalizacji użytkowania, której dziedziczy lokalizację katalogu.

Aby przypisać licencję, w obszarze **usługi Azure Active Directory** &gt; **licencji** &gt; **wszystkie produkty**, wybierz jeden lub więcej produktów, a następnie wybierz **przypisać** na pasku poleceń.

![Wybierz licencji do przypisania](media/license-users-groups/select-license-to-assign.png)

Można użyć **użytkowników i grup** bloku, aby wybrać wielu użytkowników lub grup lub wyłącz planów usług w ramach produktu. Użyj pola wyszukiwania w górnej części do wyszukania nazwy użytkowników i grup.

![Wybierz użytkownika lub grupy do przypisania licencji](media/license-users-groups/select-user-for-license-assignment.png)

Po przypisaniu licencji do grupy może trwać pewien czas, zanim wszyscy użytkownicy dziedziczą licencji, w zależności od wielkości grupy. Można sprawdzić stan przetwarzania na **grupy** bloku, w obszarze **licencji** kafelka.

![Stan przypisania licencji](media/license-users-groups/license-assignment-status.png)

Przypisanie błędy mogą wystąpić podczas przypisanie licencji usługi Azure AD, ale są stosunkowo rzadko podczas zarządzania usługi Azure AD i Enterprise Mobility + Security produktów. Potencjalne błędy przydziału są ograniczone do:
- Konflikt przypisania: gdy użytkownik został już wcześniej przypisany licencji, która jest niezgodna z bieżącej licencji. W takim przypadku Przypisywanie nowej licencji wymaga usunięcia bieżącego.
- Przekroczono dostępnych licencji: gdy liczbę użytkowników w grupach przypisanej przekracza dostępne licencje, stan przypisania użytkownika odzwierciedla awarii można przypisać ze względu na Brak licencji.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Azure licencjonowania współpracy B2B usługi AD

Współpraca B2B umożliwia zaprosić użytkowników gościa do dzierżawy usługi Azure AD w celu zapewnienia dostępu do usługi Azure AD i dowolnych zasobów platformy Azure mają być dostępne.  

Jest bezpłatna dla zapraszanie B2B użytkowników i przypisywanie ich do aplikacji w usłudze Azure AD. Do 10 aplikacji według użytkownika gościa i 3 podstawowe raporty są także bezpłatna dla użytkowników współpracy B2B. Jeśli użytkownika gościa zawiera wszystkie odpowiednie licencje przypisane w dzierżawie usługi Azure AD partnera, będą one licencjonowane w należy do Ciebie również.

Nie jest to wymagane, ale jeśli chcesz zapewnić dostęp do płatnej funkcje usługi Azure AD, muszą mieć licencję tych gości B2B z odpowiednimi licencjami usługi Azure AD. Zapraszanie dzierżawcy z usługą Azure AD, płatną licencję współpracy B2B prawa użytkownika można przypisać dodatkowym użytkownikom pięć gościa zaproszenie do dzierżawy. Scenariusze i informacje, zobacz [współpracy B2B licencjonowania wskazówki](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Wyświetl przypisane licencje

Widok podsumowania przypisane i dostępnych licencji zostanie wyświetlona w obszarze **usługi Azure Active Directory** &gt; **licencji** &gt; **wszystkie produkty**.

![Licencja wyświetlania podsumowania](media/license-users-groups/view-license-summary.png)

Szczegółową listę przypisanych użytkowników i grup jest dostępna po wybraniu określonego produktu. **Licencjonowanych użytkowników** lista zawiera wszystkich użytkowników, w obecnie korzystających z licencji i czy została przypisana licencja bezpośrednio do użytkownika lub jeśli zostało ono odziedziczone po grupie.

![Wyświetl szczegóły licencji](media/license-users-groups/view-license-detail.png)

Podobnie **grup licencji** lista zawiera wszystkich grup, do których zostały przypisane licencje. Wybierz użytkownika lub grupę, aby otworzyć **licencji** bloku, który zawiera wszystkie licencje przypisane do tego obiektu.

## <a name="remove-a-license"></a>Usunąć licencję

Aby usunąć licencję, przejdź do użytkownika lub grupy, a następnie otwórz **licencji** kafelka. Wybierz licencji, a następnie kliknij przycisk **Usuń**.

![Usunąć licencję](media/license-users-groups/remove-license.png)

Nie można bezpośrednio usunąć dziedziczone przez użytkownika z grupy licencji. Zamiast tego Usuń użytkownika z grupy, z którego są dziedziczone licencji.


## <a name="next-steps"></a>Kolejne kroki
W tym szybkiego startu kiedy znasz już jak przypisać licencje do użytkowników i grup w katalogu usługi Azure AD. 

Poniższe łącze służy do konfigurowania przypisania licencji subskrypcji w usłudze Azure AD z portalu Azure.

> [!div class="nextstepaction"]
> [Przypisywanie licencji usługi Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 