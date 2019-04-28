---
title: Migrowanie z usługi Azure Active Directory Access Control Service do autoryzacji sygnatura dostępu współdzielonego | Dokumentacja firmy Microsoft
description: Migrowanie aplikacji z usługi kontroli dostępu do sygnatury dostępu Współdzielonego
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 746b19062c3014caa37c6668e6c41df054a47e25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60868166"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrowanie z usługi Azure Active Directory Access Control Service do autoryzacji sygnatura dostępu współdzielonego

Aplikacje usługi Service Bus wcześniej mieli do wyboru przy użyciu dwóch różnych autoryzacji modeli: [sygnatury dostępu współdzielonego (SAS)](service-bus-sas.md) tokenu modelu dostępna bezpośrednio w usłudze Service Bus i modelem federacyjnego gdzie zarządzania reguły autoryzacji zarządza wewnątrz [usługi Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) i tokeny uzyskanymi z usługi ACS, są przekazywane do usługi Service Bus w celu Autoryzowanie dostępu do żądanych funkcji.

Modelu autoryzacji ACS długo została zastąpiona [autoryzacji sygnatury dostępu Współdzielonego](service-bus-authentication-and-authorization.md) jako model preferowany i całą dokumentację, wskazówki i przykłady używają wyłącznie SAS już dziś. Ponadto nie jest już jest możliwe utworzyć nowe przestrzenie nazw usługi Service Bus, które są skojarzone z usługą ACS.

Sygnatury dostępu Współdzielonego ma tę zaletę, ponieważ nie jest od razu zależna od innej usługi, ale można używać bezpośrednio na klienta bez żadnych pośredników, dając dostęp klienta do klucza sygnatury dostępu Współdzielonego reguły nazwy i reguły. Sygnatury dostępu Współdzielonego również można łatwo zintegrować z podejścia, w którym klient musi najpierw przejść autoryzacji, skontaktuj się z inną usługę i następnie wystawiono tokenu. Drugie podejście jest podobny do wzorca użycia usług ACS, ale umożliwia wystawiającego tokenów dostępu na podstawie warunków specyficzne dla aplikacji, które są trudne do express w usłudze ACS.

Dla wszystkich istniejących aplikacji, które są zależne od usług ACS firma Microsoft zachęca klientów do migrację swoich aplikacji, zamiast polegać na sygnatury dostępu Współdzielonego.

## <a name="migration-scenarios"></a>Scenariusze migracji

Usługi ACS i usługi Service Bus są zintegrowane za pomocą udostępnionej wiedzy o *klucza podpisywania*. Klucz podpisywania jest używany przez obszar nazw usługi ACS do podpisywania tokenów autoryzacji i jest używany przez usługę Service Bus, aby sprawdzić, czy token został wystawiony przez sparowanej przestrzeni nazw usługi ACS. Przestrzeń nazw usługi ACS przechowuje tożsamości usługi i reguł autoryzacji. Reguły autoryzacji definiowania tożsamość usługi lub których token wystawiony przez zewnętrznego dostawcę tożsamości pobiera typy dostępu do części przestrzeni nazw usługi Service Bus, wykres w formie dopasowanie najdłuższego prefiksu.

Na przykład reguła ACS może udzielić **wysyłania** oświadczenia na prefiksie ścieżki `/` tożsamości usługi, co oznacza, że token wystawiony przez usługi ACS na podstawie tej reguły przyznawane klienta uprawnienia do wysyłania do wszystkich jednostek w przestrzeni nazw. Jeśli jest prefiks ścieżki `/abc`, tożsamość jest ograniczone do wysyłania do jednostki o nazwie `abc` lub zorganizowane poniżej tego prefiksu. Zakłada się, czy czytelnicy wskazówek dotyczących tej migracji znają już tych pojęć.

Scenariusze migracji można podzielić na trzy kategorie:

1.  **Bez zmian wartości domyślne**. Niektórzy klienci użyj [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) obiektu, przekazując automatycznie generowanych **właściciela** usługi tożsamości i jego klucz tajny dla przestrzeni nazw usługi ACS, skojarzone z obszarem nazw usługi Service Bus i nie należy dodawać nowe zasady.

2.  **Tożsamości usługi niestandardowych za pomocą prostych reguł**. Niektórzy klienci Dodawanie nowej tożsamości usługi i przyznawanie każdej nowej tożsamości usługi **wysyłania**, **nasłuchiwania**, i **Zarządzaj** uprawnienia dla jednej, określonej jednostki.

3.  **Tożsamości usługi niestandardowych za pomocą reguł złożonych**. Klienci bardzo mało mają reguł złożonych zestawów w której zewnętrznie wystawione tokeny są mapowane na prawa przekazywania lub których przypisano tożsamość pojedynczą usługę zróżnicowanych praw w kilku ścieżkach przestrzeni nazw za pomocą wielu reguł.

Aby uzyskać pomoc dotyczącą migracji zestawy reguł złożonych, możesz skontaktować się ze [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/). Inne scenariusze umożliwiają proste migracji.

### <a name="unchanged-defaults"></a>Bez zmian wartości domyślne

Jeśli aplikacja nie zmienił ustawienia domyślne usług ACS, można zastąpić wszystkie [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) użycia za pomocą [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) obiektu, a następnie użyć wstępnie skonfigurowanego obszaru nazw  **RootManageSharedAccessKey** zamiast ACS **właściciela** konta. Należy pamiętać, że nawet w przypadku ACS **właściciela** konto, ta konfiguracja była (i nadal) nie jest zazwyczaj jest to zalecane, ponieważ konto/reguły udostępnia pełną zarządzającej w porównaniu z przestrzeni nazw, w tym uprawnienia do usuwania wszystkich jednostki.

### <a name="simple-rules"></a>Proste zasady

Jeśli aplikacja korzysta z tożsamości niestandardowej usługi za pomocą prostych reguł, migracja jest bardzo proste, w przypadku której został utworzony tożsamości usługi ACS w celu zapewnienia kontroli dostępu w odpowiedniej kolejce. Ten scenariusz jest często w przypadku rozwiązania SaaS stylu gdzie każda kolejka jest używany jako Most witryna dzierżawcy lub oddziału i tożsamość usługi jest tworzona dla danej witryny. W tym przypadku tożsamość usługi odpowiednich można poddać migracji do reguły sygnatura dostępu współdzielonego, bezpośrednio w kolejce. Nazwa tożsamości usługi może stać się nazwa reguły sygnatury dostępu Współdzielonego i klucz tożsamości usług mogą stać się klucza reguły sygnatury dostępu Współdzielonego. Prawa reguły sygnatury dostępu Współdzielonego są równoważne skonfigurowany odpowiednio ACS reguły dla jednostki.

Ułatwia to nowych i dodatkowych konfiguracji sygnatury dostępu Współdzielonego w miejscu istniejącej przestrzeni nazw, które są Sfederowane z usługą ACS i migracji poza ACS następnie odbywa się za pomocą [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zamiast [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Przestrzeń nazw nie musi być odłączone od usługi ACS.

### <a name="complex-rules"></a>Złożone reguły

Zasady sygnatury dostępu Współdzielonego są nie należy traktować jako konta, ale są nazywane klucze podpisywania, skojarzone z uprawnieniami. Jako takie scenariusze, w których aplikacja tworzy wiele tożsamości usługi i przyznaje im prawa dostępu do jednostki lub całą przestrzeń nazw nadal wymaga pośrednika wystawiania tokenu. Możesz uzyskać wskazówki dotyczące pośredniczącego przez [kontaktując się z pomocą techniczną](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu usługi Service Bus, zobacz następujące tematy:

* [Usługa Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md)
* [Uwierzytelnianie usługi Service Bus przy użyciu sygnatury dostępu współdzielonego](service-bus-sas.md)

