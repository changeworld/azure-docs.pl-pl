---
title: Migrowanie z usługi kontroli dostępu Active Directory platformy Azure do autoryzacji sygnatura dostępu współdzielonego | Dokumentacja firmy Microsoft
description: Aplikacje można migrować z usługi kontroli dostępu do sygnatury dostępu Współdzielonego
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 7a2a55a6ad6a721a39c9f064aad817f841dd3235
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
ms.locfileid: "27160268"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrowanie z usługi kontroli dostępu Active Directory platformy Azure do autoryzacji sygnatura dostępu współdzielonego

Aplikacje usługi Service Bus wcześniej miały do wyboru przy użyciu dwóch różnych autoryzacji modeli: [dostępu sygnatury dostępu Współdzielonego](service-bus-sas.md) token modelu udostępniane bezpośrednio przez magistralę usług i federacyjnych modelu gdzie zarządzania reguły autoryzacji zarządza wewnątrz [usługi Azure Active Directory](/azure/active-directory/) usługi kontroli dostępu (ACS) i tokenów uzyskane z usługi kontroli dostępu są przekazywane do usługi Service Bus w celu autoryzowania dostępu do żądanych funkcji.

Modelu autoryzacji ACS długo została zastąpiona [autoryzacji sygnatury dostępu Współdzielonego](service-bus-authentication-and-authorization.md) jako model preferowany i całą dokumentację, wskazówki i przykłady używają wyłącznie SAS dzisiaj. Ponadto go nie jest już możliwe utworzenie nowego przestrzenie nazw usługi Service Bus, które są skojarzone z usług ACS.

Sygnatury dostępu Współdzielonego ma tę zaletę, że nie jest od razu zależna od innej usługi, ale mogą być używane bezpośrednio z klienta bez żadnych pośredników przez zapewniające dostęp klientów do klucza nazwy i reguły reguły sygnatury dostępu Współdzielonego. Sygnatury dostępu Współdzielonego można również łatwo integrować z podejścia, w którym klient musi najpierw przekazać wyboru autoryzacji z inną usługą i następnie wystawionego tokenu. Drugie podejście jest podobny do wzorca użycia ACS, ale umożliwia wystawianie tokenów dostępu na podstawie warunków specyficzne dla aplikacji, które są trudne do express usługi ACS.

Dla wszystkich istniejących aplikacji, które są zależne od usług ACS możemy zachęcamy do migracji aplikacji i zależne od SAS zamiast tego.

## <a name="migration-scenarios"></a>Scenariusze migracji

Usługi ACS i usługi Service Bus są zintegrowane za pomocą udostępnionej wiedzy z *klucza podpisywania*. Klucz podpisujący jest używany przez ACS przestrzeni nazw do podpisywania tokenów autoryzacji i jest używany przez usługi Service Bus, aby sprawdzić, czy token został wystawiony przez pary nazw usług ACS. Przestrzeń nazw ACS przechowuje usługi tożsamości i reguł autoryzacji. Reguły autoryzacji definiują tożsamość usługi lub których token wystawiony przez zewnętrznego dostawcy tożsamości pobiera typ dostępu do części przestrzeni nazw usługi Service Bus wykresu, w formie dopasowanie prefiksu najdłuższy.

Na przykład reguła ACS może udzielić **wysyłania** oświadczenia na podstawie prefiksów ścieżki `/` do tożsamości usługi, co oznacza, że token wystawiony przez usługi ACS na podstawie tej reguły przyznaje klienta prawa do wysyłania do wszystkich jednostek w przestrzeni nazw. Jeśli ścieżka prefiks jest `/abc`, tożsamość jest ograniczony do wysyłania do jednostek o nazwie `abc` lub zorganizowane poniżej tego prefiksu. Zakłada się, czytników tej wskazówki dotyczące migracji są już znasz te pojęcia.

Scenariusze migracji można podzielić na trzy kategorie:

1.  **Takie same jak wartości domyślne**. Niektórzy klienci użyj [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) przekazania automatycznie wygenerowanego obiektu **właściciela** usługi tożsamości i jego klucz tajny dla przestrzeni nazw usługi ACS, łączyć się z obszarem nazw usługi Service Bus i nie należy dodawać nowych zasad.

2.  **Tożsamości usługi niestandardowej przy użyciu prostych reguł**. Niektórzy klienci Dodawanie nowej tożsamości usługi i udzielanie każdej nowej tożsamości usługi **wysyłania**, **nasłuchiwania**, i **Zarządzaj** uprawnienia dla jednego określonego obiektu.

3.  **Tożsamości usługi niestandardowych przy użyciu reguł złożonych**. Bardzo mało klienci mają złożonych reguł zestawów, w których zewnętrznie wystawione tokeny są mapowane na prawa przekazywania lub których przypisano tożsamości usługi pojedynczego zróżnicowane prawa w kilku ścieżki przestrzeni nazw za pomocą wielu reguł.

Aby uzyskać pomoc dotyczącą migracji zestawów reguł złożonych, możesz skontaktować się [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/). Inne scenariusze Włącz bezpośrednie migracji.

### <a name="unchanged-defaults"></a>Niezmienione wartości domyślne

Jeśli aplikacja nie uległ zmianie ustawienia domyślne usług ACS, musisz zastąpić wszystkie [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) użycia za pomocą [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) obiektu i korzystać z przestrzeni nazw wstępnie  **RootManageSharedAccessKey** zamiast ACS **właściciela** konta. Należy pamiętać, że nawet w przypadku ACS **właściciela** konto, ta konfiguracja została (i nadal jest) nie zwykle jest to zalecane, ponieważ ta reguła/konta zapewnia pełne zarządzająca za pośrednictwem przestrzeni nazw, w tym uprawnienia do usuwania wszystkich jednostki.

### <a name="simple-rules"></a>Proste zasady

Jeśli aplikacja korzysta z tożsamości niestandardowej usługi przy użyciu prostych reguł, migracja jest prosta w przypadku, gdy tożsamość usługi ACS został utworzony w celu zapewnienia kontroli dostępu w odpowiedniej kolejce. Ten scenariusz jest często w przypadku rozwiązania SaaS stylu gdzie każdej kolejki jest używany jako mostka witryna dzierżawcy lub oddziału i tożsamość usługi jest tworzony dla danej witryny. W takim przypadku tożsamość usługi odpowiednich można poddać migracji do reguły sygnatura dostępu współdzielonego, bezpośrednio w kolejce. Nazwa tożsamości usługi może stać się nazwą SAS reguły i klucz tożsamości usługi może stać się klucza sygnatury dostępu Współdzielonego reguły. Prawa reguły SAS są skonfigurowane równoważne odpowiednio ACS reguł dla jednostki.

Można wprowadzić tej nowych i dodatkowych konfiguracji SAS w miejscu na istniejącą przestrzeń nazw, który jest Sfederowane przy użyciu usług ACS i migracji od ACS następnie odbywa się przy użyciu [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zamiast [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Przestrzeń nazw nie trzeba można odłączyć od ACS.

### <a name="complex-rules"></a>Złożone zasady

Zasady sygnatury dostępu Współdzielonego mają nie być kont, ale są nazywane klucze podpisywania związane z prawami. Tak scenariusze, w których aplikacja tworzy wiele tożsamości usługi i przyznał im prawa dostępu do kilku jednostek lub pośrednik wystawiania tokenu ciągle wymagane w całej przestrzeni nazw. Możesz uzyskać wskazówki dotyczące pośredniej przez [kontaktu z pomocą techniczną](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat uwierzytelniania usługi Service Bus, zobacz następujące tematy:

* [Magistrala usług uwierzytelniania i autoryzacji](service-bus-authentication-and-authorization.md)
* [Uwierzytelnianie usługi Service Bus za pomocą sygnatury dostępu współdzielonego](service-bus-sas.md)
* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)

