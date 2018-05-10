---
title: Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory? | Microsoft Docs
description: Opis usługi Azure Active Directory na podstawie grupy licencji, jak działa i najlepsze rozwiązania
services: active-directory
keywords: Licencjonowanie usługi Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/29/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 81d2916709f4d9e88faebc198940ad905705125a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Na podstawie grupy podstawy licencjonowania w usłudze Azure Active Directory

Za pomocą płatnej usługi w chmurze, takich jak usługi Office 365, Enterprise Mobility + zabezpieczeń usługi Dynamics CRM i inne podobne produkty firmy Microsoft wymaga licencji. Te licencje są przypisane do poszczególnych użytkowników, którzy potrzebują dostępu do tych usług. Zarządzanie licencjami, administratorów, użyj jednej z portali zarządzania (Office lub Azure) i poleceń cmdlet programu PowerShell. Azure Active Directory (Azure AD) jest podstawowej infrastruktury, która obsługuje zarządzanie tożsamościami dla wszystkich usług chmurowych firmy Microsoft. Usługi Azure AD są przechowywane informacje o stanach przypisania licencji dla użytkowników.

Do tej pory licencje może zostać przypisana tylko na poziomie indywidualnego użytkownika, co może utrudnić zarządzania na dużą skalę. Na przykład aby dodać lub usunąć licencje użytkownika na podstawie organizacyjnego zmian, takich jak użytkownicy przyłączenie się lub opuszczenie organizacji lub działu, administrator często musi być zapisana złożonych skrypt programu PowerShell. Ten skrypt wykonuje poszczególnych wywołań do usługi w chmurze.

Aby rozwiązać te problemy, teraz usługi Azure AD zawiera oparte na grupach licencji. Do grupy można przypisać jedną lub więcej licencji produktu. Usługi Azure AD zapewnia przypisanie licencji dla wszystkich członków grupy. Nowe elementy członkowskie, które dołączenie do grupy przypisano odpowiednie licencje. Gdy opuszczą grupy te licencje zostaną usunięte. Dzięki temu do automatyzacji zarządzania licencji za pomocą programu PowerShell celu odzwierciedlenia zmian w organizacji i struktury działów w poszczególnych użytkowników.

>[!NOTE]
>Ta funkcja jest obecnie w wersji zapoznawczej. Przygotuj się do przywrócenia lub Usuń wszystkie zmiany. Funkcja jest dostępna w żadnych subskrypcji usługi Azure Active Directory (Azure AD) w publicznej wersji zapoznawczej. Gdy funkcja stanie się ogólnie dostępna, niektórych aspektów funkcji mogą jednak wymagać jedną lub więcej licencji usługi Azure Active Directory — wersja Premium.

## <a name="features"></a>Funkcje

Oto główne funkcje na podstawie grupy licencji:

- Licencje można przypisywać do żadnej grupy zabezpieczeń w usłudze Azure AD. Grupy zabezpieczeń mogą być zsynchronizowanej lokalnej, za pomocą usługi Azure AD Connect. Bezpośrednio w usłudze Azure AD (nazywanych również grup tylko w chmurze), lub automatycznie za pomocą usługi Azure AD funkcji grup dynamicznych, mogą także tworzyć grupy zabezpieczeń.

- Po przypisaniu licencji produktu do grupy, administrator może wyłączyć jeden lub więcej planów usług w ramach produktu. Zazwyczaj jest to realizowane przy organizacji nie jest jeszcze gotowy do uruchomienia przy użyciu usługi zawarta w produkcie. Na przykład administrator może przypisać usługi Office 365 do działu, ale tymczasowe wyłączenie usługi Yammer.

- Obsługiwane są wszystkie usług chmurowych firmy Microsoft, które wymagają licencji na poziomie użytkownika. Obejmuje to wszystkie produkty, pakietu Enterprise Mobility + Security i Dynamics CRM usługi Office 365.

- Na podstawie grupy licencji jest obecnie dostępny tylko za pośrednictwem [portalu Azure](https://portal.azure.com). Przede wszystkim użyj innych portali zarządzania dla użytkowników i grupy zarządzania, takich jak portalu usługi Office 365, możesz to zrobić. Jednak należy używać portalu Azure do zarządzania licencjami na poziomie grupy.

- Usługi Azure AD automatycznie zarządza licencji modyfikacje w wyniku zmiany członkostwa w grupie. Zazwyczaj modyfikacje licencji są efektywne w ciągu minut zmiany członkostwa.

- Użytkownik może należeć do zasad licencji określono wiele grup. Użytkownik ma także niektórych przypisane bezpośrednio, poza żadnych grup licencji. Wynikowy stan użytkownika jest kombinacją wszystkich przydzielonych produktu i licencji usługi.

- W niektórych przypadkach licencji nie może być przypisana do użytkownika. Na przykład może nie być wystarczającej liczby dostępnych licencji w dzierżawie programu lub usługi powodujące konflikt może przypisane w tym samym czasie. Administratorzy mają dostęp do informacji o użytkownikach, dla których usługi Azure AD nie można całkowicie przetworzyć grupy licencji. Następnie potencjalnie działań korygujących, na podstawie tych informacji.

- W publicznej wersji zapoznawczej płatną lub wersji próbnej subskrypcji dla wersji Azure AD podstawowa lub Premium jest wymagany w dzierżawie do użycia licencji na podstawie grupy zarządzania.

## <a name="your-feedback-is-welcome"></a>Twoja opinia jest Zapraszamy!

Jeśli masz żądania opinii lub funkcji, można udostępniać je z nami za pomocą [tym forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o innych scenariuszy zarządzania licencji za pomocą licencjonowania na podstawie grupy, zobacz:

* [Rozpoczynanie pracy z licencjami w usłudze Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identyfikowanie i rozwiązywanie problemów z licencji dla grupy w usłudze Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Jak przeprowadzić migrację poszczególnych licencjonowanych użytkowników do licencjonowania oparte na grupach w usłudze Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Usługa Azure Active Directory na podstawie grupy licencjonowania dodatkowe scenariusze](active-directory-licensing-group-advanced.md)
