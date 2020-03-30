---
title: Co to jest licencjonowanie oparte na grupach — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się więcej o licencjonowaniu opartym na grupach usługi Azure Active Directory, w tym o tym, jak działa i jakie są najlepsze rozwiązania.
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eec896e6cccaf58c83820161c54c4f10cfadadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561565"
---
# <a name="what-is-group-based-licensing-in-azure-active-directory"></a>Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?

Płatne usługi w chmurze firmy Microsoft, takie jak usługa Office 365, pakiet Enterprise Mobility + Security, usługa Dynamics 365 i inne podobne produkty firmy Microsoft, wymagają licencji. Te licencje są przypisywane do każdego użytkownika, który potrzebuje dostępu do tych usług. Aby zarządzać licencjami, administratorzy korzystają z jednego z portali zarządzania (Office lub Azure) i poleceń cmdlet programu PowerShell. Azure Active Directory (Azure AD) to podstawowa infrastruktura, która obsługuje zarządzanie tożsamościami dla wszystkich usług w chmurze firmy Microsoft. Usługa Azure AD przechowuje informacje o stanach przypisania licencji dla użytkowników.

Do tej pory licencje mogły być przypisywane tylko na poziomie pojedynczego użytkownika, co może utrudniać zarządzanie na dużą skalę. Na przykład aby dodać lub usunąć licencje użytkowników na podstawie zmian w organizacji, takich jak dołączenie użytkownika do organizacji lub działu albo opuszczenie organizacji lub działu przez użytkownika, administrator często musi napisać złożony skrypt programu PowerShell. Ten skrypt wykonuje poszczególne wywołania usługi w chmurze.

Aby rozwiązać te problemy, obecnie usługa Azure AD udostępnia licencjonowanie oparte na grupach. Do grupy można przypisać jedną lub więcej licencji produktu. Usługa Azure AD gwarantuje, że licencje są przypisywane do wszystkich członków grupy. Nowym członkom, którzy dołączają do grupy, są przypisywane odpowiednie licencje. Podczas opuszczania grupy te licencje są usuwane. To zarządzanie licencjonowaniem eliminuje potrzebę automatyzacji zarządzania licencjami za pośrednictwem programu PowerShell w celu odzwierciedlenia zmian w organizacji i strukturze działów na podstawie dla użytkownika.

## <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania
Aby korzystać z licencjonowania grupowego, musisz mieć jedną z następujących licencji:

- Płatna lub próbna subskrypcja usługi Azure AD Premium P1 i wyższej

- Płatna lub próbna wersja usługi Office 365 Enterprise E3 lub Office 365 A3 lub Office 365 GCC G3 lub Office 365 E3 dla GCCH lub Office 365 E3 dla DOD i powyżej

### <a name="required-number-of-licenses"></a>Wymagana liczba licencji
W przypadku wszystkich grup, do których przypisano licencję, musisz mieć licencję dla każdego unikatowego członka. Chociaż nie trzeba przypisywać każdemu członkowi grupy licencji, musisz mieć co najmniej wystarczającą liczbę licencji, aby uwzględnić wszystkich członków. Jeśli na przykład masz 1000 unikatowych członków, którzy są częścią licencjonowanych grup w dzierżawie, musisz mieć co najmniej 1000 licencji, aby spełnić umowę licencyjną.

## <a name="features"></a>Funkcje

Poniżej przedstawiono główne funkcje licencjonowania opartego na grupach:

- Licencje można przypisać do dowolnej grupy zabezpieczeń w usłudze Azure AD. Grupy zabezpieczeń można synchronizować z lokalnego, za pomocą usługi Azure AD Connect. Można również utworzyć grupy zabezpieczeń bezpośrednio w usłudze Azure AD (tak zwane grupy tylko w chmurze) lub automatycznie, za pomocą funkcji grupy dynamicznej usługi Azure AD.

- Po przypisaniu licencji produktu do grupy administrator może wyłączyć poszczególne plany usług w ramach produktu. Zazwyczaj to przypisanie jest wykonywane, gdy organizacja nie jest jeszcze gotowa do rozpoczęcia korzystania z usługi zawartej w produkcie. Na przykład administrator może przypisać usługę Office 365 do działu, ale tymczasowo wyłączyć usługę Yammer.

- Obsługiwane są wszystkie usługi w chmurze firmy Microsoft, które wymagają licencjonowania na poziomie użytkownika. Ta pomoc techniczna obejmuje wszystkie produkty usługi Office 365, usługi Enterprise Mobility + Security i Dynamics 365.

- Licencjonowanie oparte na grupach jest obecnie dostępne tylko za pośrednictwem [witryny Azure portal](https://portal.azure.com). Jeśli używasz głównie innych portali zarządzania do zarządzania użytkownikami i grupami, takich jak [centrum administracyjne usługi Microsoft 365,](https://admin.microsoft.com)możesz nadal to robić. Jednak do zarządzania licencjami na poziomie grupy należy używać witryny Azure Portal.

- Usługa Azure AD automatycznie zarządza modyfikacjami licencji wynikającymi ze zmian członkostwa w grupie. Zazwyczaj modyfikacje licencji zaczynają obowiązywać w ciągu minut od zmiany członkostwa.

- Użytkownik może należeć do wielu grup z określonymi zasadami licencji. Użytkownik może również mieć niektóre licencje przypisane bezpośrednio, poza jakimikolwiek grupami. Wynikowy stan użytkownika jest połączeniem wszystkich przypisanych licencji produktów i usług. Jeśli użytkownikowi przypisano tę samą licencję z wielu źródeł, licencja zostanie wykorzystana tylko raz.

- W niektórych przypadkach nie można przypisać licencji do użytkownika. Na przykład w dzierżawie może być za mało dostępnych licencji lub usługi powodujące konflikt mogły zostać przypisane w tym samym czasie. Administratorzy mają dostęp do informacji o użytkownikach, dla których usługa Azure AD nie mogła w pełni przetworzyć licencji grup. Na podstawie tych informacji mogą następnie wykonać akcję naprawczą.

## <a name="your-feedback-is-welcome"></a>Chętnie poznamy Twoją opinię!

Jeśli masz opinie lub żądania funkcji, podziel się nimi z nami za pomocą [forum administracyjnego usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Jak migrować użytkowników między licencjami produktów przy użyciu licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
* [Przykłady programu PowerShell dotyczące licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
