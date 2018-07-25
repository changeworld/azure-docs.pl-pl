---
title: Co to jest licencjonowanie oparte na grupach w usłudze Azure AD? | Microsoft Docs
description: Opis licencjonowania opartego na grupach w usłudze Azure Active Directory, sposobu jego działania i najlepszych rozwiązań
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.workload: identity
ms.date: 06/13/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 5f5aab6a8514636e8be09deb773748080871b9d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005235"
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Podstawy licencjonowania opartego na grupach w usłudze Azure Active Directory

Płatne usługi w chmurze firmy Microsoft, takie jak usługa Office 365, pakiet Enterprise Mobility + Security, usługa Dynamics 365 i inne podobne produkty firmy Microsoft, wymagają licencji. Te licencje są przypisywane do każdego użytkownika, który potrzebuje dostępu do tych usług. Aby zarządzać licencjami, administratorzy korzystają z jednego z portali zarządzania (Office lub Azure) i poleceń cmdlet programu PowerShell. Azure Active Directory (Azure AD) to podstawowa infrastruktura, która obsługuje zarządzanie tożsamościami dla wszystkich usług w chmurze firmy Microsoft. Usługa Azure AD przechowuje informacje o stanach przypisania licencji dla użytkowników.

Do tej pory licencje mogły być przypisywane tylko na poziomie pojedynczego użytkownika, co może utrudniać zarządzanie na dużą skalę. Na przykład aby dodać lub usunąć licencje użytkowników na podstawie zmian w organizacji, takich jak dołączenie użytkownika do organizacji lub działu albo opuszczenie organizacji lub działu przez użytkownika, administrator często musi napisać złożony skrypt programu PowerShell. Ten skrypt wykonuje poszczególne wywołania usługi w chmurze.

Aby rozwiązać te problemy, obecnie usługa Azure AD udostępnia licencjonowanie oparte na grupach. Do grupy można przypisać jedną lub więcej licencji produktu. Usługa Azure AD gwarantuje, że licencje są przypisywane do wszystkich członków grupy. Nowym członkom, którzy dołączają do grupy, są przypisywane odpowiednie licencje. Podczas opuszczania grupy te licencje są usuwane. Eliminuje to potrzebę automatyzowania zarządzania licencjami za pomocą programu PowerShell w celu odzwierciedlenia zmian w strukturze organizacji i działów według poszczególnych użytkowników.

>[!Note]
>Licencjonowanie oparte na grupach to dostępna w publicznej wersji zapoznawczej funkcja usługi Azure Active Directory (Azure AD), oferowana w ramach dowolnego płatnego planu licencjonowania usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features"></a>Funkcje

Poniżej przedstawiono główne funkcje licencjonowania opartego na grupach:

- Licencje można przypisać do dowolnej grupy zabezpieczeń w usłudze Azure AD. Grupy zabezpieczeń można zsynchronizować lokalnie za pomocą usługi Azure AD Connect. Można również utworzyć grupy zabezpieczeń bezpośrednio w usłudze Azure AD (tak zwane grupy tylko w chmurze) lub automatycznie, za pomocą funkcji grupy dynamicznej usługi Azure AD.

- Po przypisaniu licencji produktu do grupy administrator może wyłączyć poszczególne plany usług w ramach produktu. Zwykle jest to wykonywane, gdy organizacja nie jest jeszcze gotowa na rozpoczęcie korzystania z usługi zawartej w produkcie. Na przykład administrator może przypisać usługę Office 365 do działu, ale tymczasowo wyłączyć usługę Yammer.

- Obsługiwane są wszystkie usługi w chmurze firmy Microsoft, które wymagają licencjonowania na poziomie użytkownika. Obejmuje to wszystkie produkty usługi Office 365, pakiet Enterprise Mobility + Security i usługę Dynamics 365.

- Licencjonowanie oparte na grupach jest obecnie dostępne wyłącznie za pośrednictwem [witryny Azure Portal](https://portal.azure.com). Jeśli do zarządzania użytkownikami i grupami używasz głównie innych portali zarządzania, takich jak portal usługi Office 365, możesz to robić nadal. Jednak do zarządzania licencjami na poziomie grupy należy używać witryny Azure Portal.

- Usługa Azure AD automatycznie zarządza modyfikacjami licencji wynikającymi ze zmian członkostwa w grupie. Zazwyczaj modyfikacje licencji zaczynają obowiązywać w ciągu minut od zmiany członkostwa.

- Użytkownik może należeć do wielu grup z określonymi zasadami licencji. Użytkownik może również mieć niektóre licencje przypisane bezpośrednio, poza jakimikolwiek grupami. Wynikowy stan użytkownika jest połączeniem wszystkich przypisanych licencji produktów i usług.

- W niektórych przypadkach nie można przypisać licencji do użytkownika. Na przykład w dzierżawie może być za mało dostępnych licencji lub usługi powodujące konflikt mogły zostać przypisane w tym samym czasie. Administratorzy mają dostęp do informacji o użytkownikach, dla których usługa Azure AD nie mogła w pełni przetworzyć licencji grup. Na podstawie tych informacji mogą następnie wykonać akcję naprawczą.

- W publicznej wersji zapoznawczej płatna lub próbna wersja subskrypcji usługi Azure AD w wersji Podstawowa lub Premium wymaga, aby dzierżawa używała zarządzania licencjami opartego na grupach.

## <a name="your-feedback-is-welcome"></a>Chętnie poznamy Twoją opinię!

Jeśli masz propozycję nowej funkcji lub chcesz podzielić się opinią, odwiedź [forum administratorów usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencjami w ramach programu licencjonowania opartego na grupach, zobacz:

* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
