---
title: Azure Relay — migracja do autoryzacji podpisu dostępu współdzielonego
description: W tym artykule opisano sposób migracji aplikacji usługi Azure Relay z korzystania z usługi Azure Active Directory access control service do autoryzacji podpisu dostępu współdzielonego.
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 59b9e734526c56016e2ddf59c2afb5b8f7b4ad09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514905"
---
# <a name="azure-relay---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Azure Relay — migrowanie z usługi Azure Active Directory Access Control Service do autoryzacji podpisu dostępu współdzielonego

Aplikacje usługi Azure Relay w przeszłości miały do wyboru dwa różne modele autoryzacji: model [tokenu sygnatury dostępu współdzielonego (SAS)](../service-bus-messaging/service-bus-sas.md) dostarczany bezpośrednio przez usługę przekazywania oraz model federowany, w którym zarządzanie regułami autoryzacji jest zarządzane wewnątrz usługi [Azure Active Directory](/azure/active-directory/) Access Control Service (ACS), a tokeny uzyskane z usługi ACS są przekazywane do programu Relay w celu autoryzowania dostępu do żądanych funkcji.

Model autoryzacji ACS od dawna został zastąpiony przez [autoryzację SYGNA](../service-bus-messaging/service-bus-authentication-and-authorization.md) jako preferowany model, a cała dokumentacja, wskazówki i próbki używają obecnie wyłącznie sygnatury dostępu Współdzielonego. Ponadto nie jest już możliwe tworzenie nowych przekaźnikowych obszarów nazw sparowanych z usługią ACS.

Sygnatura dostępu Współdzielonego ma tę zaletę, że nie jest natychmiast zależna od innej usługi, ale może być używana bezpośrednio od klienta bez żadnych pośredników, dając klientowi dostęp do nazwy reguły sygnatury dostępu współdzielonego i klucza reguły. Sygnatury dostępu Współdzielonego można również łatwo zintegrować z podejściem, w którym klient musi najpierw przejść kontrolę autoryzacji z inną usługą, a następnie zostanie wystawiony token. To ostatnie podejście jest podobne do wzorca użycia usługi ACS, ale umożliwia wystawianie tokenów dostępu na podstawie warunków specyficznych dla aplikacji, które są trudne do wyrażenia w programie ACS.

W przypadku wszystkich istniejących aplikacji, które są zależne od systemu ACS, zachęcamy klientów do migracji ich aplikacji, aby zamiast tego polegać na sygnatury dostępu Współdzielonego.

## <a name="migration-scenarios"></a> Scenariusze migracji

ACS i Relay są zintegrowane poprzez wspólną wiedzę o *kluczu podpisywania.* Klucz podpisywania jest używany przez obszar nazw usługi ACS do podpisywania tokenów autoryzacji i jest używany przez usługę Azure Relay w celu sprawdzenia, czy token został wystawiony przez sparowany obszar nazw USŁUGI ACS. Obszar nazw usługi ACS przechowuje tożsamości usług i reguły autoryzacji. Reguły autoryzacji określają, która tożsamość usługi lub token wystawiony przez zewnętrznego dostawcę tożsamości pobiera jaki typ dostępu do części wykresu przekaźnika obszaru nazw w postaci dopasowania najdłuższego prefiksu.

Na przykład reguła ACS może udzielić **wyślij** `/` oświadczenie w prefiksie ścieżki do tożsamości usługi, co oznacza, że token wystawiony przez usługę ACS na podstawie tej reguły udziela praw klienta do wysyłania do wszystkich jednostek w obszarze nazw. Jeśli prefiks `/abc`ścieżki jest, tożsamość jest ograniczona `abc` do wysyłania do jednostek o nazwie lub zorganizowane poniżej tego prefiksu. Zakłada się, że czytelnicy tych wskazówek dotyczących migracji są już zaznajomieni z tymi pojęciami.

Scenariusze migracji dzielą się na trzy szerokie kategorie:

1.  **Niezmienione wartości domyślne**. Niektórzy klienci używają obiektu [SharedSecretTokenProvider,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) przekazując automatycznie wygenerowaną tożsamość usługi **właściciela** i jego klucz tajny dla obszaru nazw ACS, sparowane z przestrzenią nazw przekazywania i nie dodają nowych reguł.

2.  **Niestandardowe tożsamości usługi z prostymi regułami**. Niektórzy klienci dodają nowe tożsamości usługi i przyznają każdej nowej tożsamości usługi **Wyślij,** **Słuchaj**i **Zarządzaj uprawnieniami** dla jednej określonej encji.

3.  **Niestandardowe tożsamości usługi ze złożonymi regułami**. Bardzo niewielu klientów ma złożone zestawy reguł, w których tokeny wystawione zewnętrznie są mapowane na prawa w programie Relay lub gdy jednej tożsamości usługi są przypisywane zróżnicowane prawa w kilku ścieżkach obszaru nazw za pomocą wielu reguł.

Aby uzyskać pomoc dotyczącą migracji złożonych zestawów reguł, możesz skontaktować się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). Pozostałe dwa scenariusze umożliwiają prostą migrację.

### <a name="unchanged-defaults"></a>Niezmienione wartości domyślne

Jeśli aplikacja nie zmieniła ustawień domyślnych usługi ACS, można zastąpić wszystkie użycie [sharedsecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) obiektem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) i użyć wstępnie skonfigurowanego obiektu **RootManageSharedAccessKey** w obszarze nazw zamiast konta **właściciela** usługi ACS. Należy zauważyć, że nawet w przypadku konta **właściciela** usługi ACS ta konfiguracja nie była (i nadal jest) ogólnie nie zalecana, ponieważ to konto/reguła zapewnia pełny urząd zarządzania nad obszarem nazw, w tym uprawnienia do usuwania wszystkich jednostek.

### <a name="simple-rules"></a>Proste zasady

Jeśli aplikacja używa tożsamości usługi niestandardowej z prostymi regułami, migracja jest prosta w przypadku, gdy tożsamość usługi ACS została utworzona w celu zapewnienia kontroli dostępu dla określonego przekaźnika. W tym scenariuszu jest często w przypadku rozwiązań w stylu SaaS, gdzie każdy przekaźnik jest używany jako most do lokacji dzierżawy lub oddziału firmy, a tożsamość usługi jest tworzony dla tej konkretnej lokacji. W takim przypadku tożsamość odpowiedniej usługi może zostać zmigrowana do reguły podpisu dostępu udostępnionego bezpośrednio w przekaźniku. Nazwa tożsamości usługi może stać się nazwą reguły sygnatury dostępu Współdzielonego, a klucz tożsamości usługi może stać się kluczem reguły sygnatury dostępu Współdzielonego. Prawa reguły sygnatury dostępu Współdzielonego są następnie konfigurowane jako równoważne odpowiednio obowiązującej regule ACS dla jednostki.

Tę nową i dodatkową konfigurację sygnatury dostępu współdzielonego można wprowadzić w miejscu na dowolnym istniejącym obszarze nazw, który jest sfederowany z usługą ACS, a migracja z usługi ACS jest następnie wykonywana przy użyciu [sharedaccesssignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zamiast [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). Obszar nazw nie musi być odłączony od systemu ACS.

### <a name="complex-rules"></a>Złożone zasady

Reguły sygnatury dostępu Współdzielonego nie mają być kontami, ale są nazywane kluczami podpisywania skojarzonymi z prawami. W związku ze scenariuszami, w których aplikacja tworzy wiele tożsamości usługi i udziela im praw dostępu dla kilku jednostek lub całego obszaru nazw, nadal wymagają pośrednika wystawiającego token. Możesz uzyskać wskazówki dla takiego [pośrednika, kontaktując się z pomocą techniczną](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure Relay, zobacz następujące tematy:

* [Uwierzytelnianie i autoryzacja usługi Azure Relay](relay-authentication-and-authorization.md)
* [Uwierzytelnianie usługi Service Bus za pomocą podpisów dostępu współdzielonego](../service-bus-messaging/service-bus-sas.md)


