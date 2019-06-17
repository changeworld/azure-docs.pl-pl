---
title: Funkcja Azure access control Service Bus za pomocą sygnatur dostępu współdzielonego | Dokumentacja firmy Microsoft
description: Omówienie kontroli dostępu usługi Service Bus przy użyciu sygnatury dostępu współdzielonego Przegląd, szczegółowe informacje o autoryzacji sygnatury dostępu Współdzielonego z usługą Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: a14e03c21de0b5388040943fbe5e9434271b567f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258813"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Kontrola dostępu usługi Service Bus przy użyciu sygnatury dostępu współdzielonego

*Sygnatury dostępu współdzielonego* (SAS) są komunikatów usługi Service Bus przy użyciu mechanizmu głównych zabezpieczeń. W tym artykule omówiono sygnatury dostępu Współdzielonego, jak działają i jak z nich korzystać w sposób niezależny od platformy.

Sygnatury dostępu Współdzielonego chroni dostęp do usługi Service Bus na podstawie reguł autoryzacji. Te są konfigurowane w przestrzeni nazw lub jednostki obsługi komunikatów (przekazywania, kolejki lub tematu). Reguła autoryzacji o nazwie, jest skojarzona z określonych praw i niesie ze sobą parę kluczy kryptograficznych. Używasz nazwy i klucza za pomocą zestawu SDK usługi Service Bus lub we własnym kodzie reguły do generowania tokenu sygnatury dostępu Współdzielonego. Klient następnie przekazać token do usługi Service Bus, aby potwierdzić autoryzacji dla żądanej operacji.

## <a name="overview-of-sas"></a>Omówienie sygnatury dostępu Współdzielonego

Udostępnione sygnatur dostępu to mechanizm autoryzacji opartej na oświadczeniach przy użyciu prostego tokenów. Przy użyciu sygnatury dostępu Współdzielonego, klucze nigdy nie są przekazywane w sieci. Klucze są używane do podpisania kryptograficznie informacji, który później może zostać zweryfikowany przez usługę. Sygnatury dostępu Współdzielonego może służyć za podobne do schematu nazwy użytkownika i hasła, gdzie klient będzie w posiadaniu natychmiastowego Nazwa reguły autoryzacji i dopasowany klucz. Sygnatury dostępu Współdzielonego można również podobny do modelu zabezpieczeń, gdzie klient odbiera token dostępu ograniczonej czasowo i podpisany z usługi tokenu zabezpieczającego bez kiedykolwiek wchodzących w posiadaniu klucza podpisywania.

Skonfigurowano uwierzytelnianie sygnatury dostępu Współdzielonego w usłudze Service Bus z o nazwie [udostępnione reguł autoryzacji dostępu](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) mających skojarzone prawa dostępu i pary kluczy kryptograficznych podstawowego i pomocniczego. Klucze są wartości 256-bitowego w reprezentacji Base64. Możesz skonfigurować reguły na poziomie przestrzeni nazw, w usłudze Service Bus [przekazuje](../service-bus-relay/relay-what-is-it.md), [kolejek](service-bus-messaging-overview.md#queues), i [tematy](service-bus-messaging-overview.md#topics).

[Sygnatura dostępu współdzielonego](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token zawiera nazwę reguły autoryzacji wybrany, identyfikator URI zasobu, która jest dostępna, natychmiastowe, wygaśnięcia i podpisu kryptograficznego HMAC SHA256 obliczona dla tych pól przy użyciu podstawowy lub pomocniczy klucz kryptograficzny regułę autoryzacji wybrany.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego

Każda przestrzeń nazw usługi Service Bus i każda jednostka usługi Service Bus ma zasady autoryzacji dostępu do udostępnionych składają się z zasadami. Zasady na poziomie przestrzeni nazw ma zastosowanie do wszystkich jednostek w przestrzeni nazw, niezależnie od ich konfiguracji poszczególnych zasad.

Dla każdej reguły zasad autoryzacji zdecydujesz się na trzy informacje: **nazwa**, **zakres**, i **praw**. **Nazwa** jest po prostu; unikatową nazwę, w tym zakresie. Zakres jest dość proste: jest to identyfikator URI zasobu w danym. W przypadku przestrzeni nazw usługi Service Bus zakres jest w pełni kwalifikowana nazwa domeny (FQDN), takich jak `https://<yournamespace>.servicebus.windows.net/`.

Praw przyznanych przez reguły może być kombinacją:

* "Send" - uprawnia do wysyłania komunikatów do jednostki
* "Nasłuchiwania" - uprawnia do nasłuchiwania (przekaźnik) lub odebrać (kolejki, subskrypcji) i wszystkie powiązane Obsługa komunikatów
* "Zarządzanie" — nadaje prawo do zarządzania topologii przestrzeni nazw: tworzenie i usuwanie jednostek

Po prawej stronie "Zarządzanie" obejmuje prawa "Send" i "Odbierz".

Zasady przestrzeni nazw lub jednostki może zawierać maksymalnie 12 reguł autoryzacji dostępu do udostępnionych, udostępnianie miejsca dla trzech zestawów reguł, każdego podstawowych praw i kombinacji wysyłania i nasłuchiwania. Ten limit podkreślenia, przechowujących zasad sygnatury dostępu Współdzielonego nie jest przeznaczona do użytkownika lub konta usług w sklepie. Jeśli aplikacja wymaga udzielić dostępu do usługi Service Bus, w oparciu o użytkownika lub tożsamości usługi, powinien implementować usługi tokenu zabezpieczającego, która wystawia tokeny sygnatur dostępu Współdzielonego po wyboru uwierzytelnianiem i dostępem.

Reguły autoryzacji jest przypisany *klucza podstawowego* i *klucz pomocniczy*. Są to klucze silną kryptograficznie. Nie utracić je lub ich przecieku — będą zawsze dostępne w [witryny Azure portal][Azure portal]. Można użyć dowolnego z wygenerowanych kluczy i można go odtworzyć w dowolnym momencie. Jeśli ponownie wygenerować lub zmienić wartość klucza w zasadach, wszystkie wcześniej wydane tokeny oparte na stanie się natychmiast nieprawidłowy klucz. Jednak bieżących połączeń oparta na tych tokenów będą w dalszym ciągu działać do momentu wygaśnięcia ważności tokenu.

Podczas tworzenia przestrzeni nazw usługi Service Bus reguły o nazwie **RootManageSharedAccessKey** jest tworzona automatycznie dla przestrzeni nazw. Ta zasada ma Zarządzaj uprawnieniami dla całej przestrzeni nazw. Zalecane jest, aby traktować tę regułę, takich jak administracyjne **głównego** konta, a nie jest używany w aplikacji. Możesz utworzyć dodatkowe zasady w **Konfiguruj** kartę dla przestrzeni nazw w portalu przy użyciu programu Powershell lub wiersza polecenia platformy Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguracja uwierzytelniania sygnatura dostępu współdzielonego

Można skonfigurować [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) reguły w przestrzeni nazw usługi Service Bus, kolejki lub tematy. Konfigurowanie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w usłudze Service Bus subskrypcji nie jest obecnie obsługiwane, ale zasady skonfigurowane w przestrzeni nazw lub temat umożliwia bezpieczny dostęp do subskrypcji. Dla przykładu pracy, który ilustruje tę procedurę, zobacz [uwierzytelniania przy użyciu sygnatury dostępu współdzielonego (SAS) za pomocą subskrypcji magistrali usług](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) próbki.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tym rysunku *manageRuleNS*, *sendRuleNS*, i *listenRuleNS* reguł autoryzacji dotyczą zarówno kolejka K1, jak i tematu T1, podczas gdy *listenRuleQ*  i *sendRuleQ* mają zastosowanie tylko do kolejki Q1 i *sendRuleT* ma zastosowanie tylko do tematu T1.

## <a name="generate-a-shared-access-signature-token"></a>Wygeneruj token sygnatura dostępu współdzielonego

Każdy klient, który ma dostęp do nazwy nazwa reguły autoryzacji i jeden z jego klucze podpisywania, można wygenerować tokenu sygnatury dostępu Współdzielonego. Token jest generowany przez umożliwiają utworzenie dobrze dopasowanego ciągu w następującym formacie:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Błyskawiczne wygaśnięcia tokenu. Liczba całkowita odzwierciedlający sekund od początku epoki `00:00:00 UTC` na 1 stycznia 1970 r. (UNIX epoki) po wygaśnięciu ważności tokenu.
* **`skn`** — Nazwa reguły autoryzacji.
* **`sr`** — Identyfikator URI zasobu, do którego uzyskiwany jest dostęp.
* **`sig`** -Podpis.

`signature-string` Identyfikator URI zasobu jest obliczane wyznaczania wartości skrótu SHA-256 (**zakres** zgodnie z opisem w poprzedniej sekcji), a ciąg reprezentujący wygaśnięcia tokenu błyskawiczne, oddzielone CRLF.

Obliczenia skrótu wygląda podobnie do poniższego kodu pseudotłumaczeń i zwraca wartość skrótu 256-bitowy/32 bajtów.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości-mieszane, tak aby odbiorca może ponownie obliczyć skrótu z tymi samymi parametrami, weryfikowanie, czy wystawca ma prawidłowy klucz podpisywania.

Identyfikator URI zasobu jest pełny identyfikator URI zasobu usługi Service Bus, do której dostęp jest zgłoszone. Na przykład `http://<namespace>.servicebus.windows.net/<entityPath>` lub `sb://<namespace>.servicebus.windows.net/<entityPath>`, czyli `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. 

**Identyfikator URI musi być [zakodowane w formacie procent](https://msdn.microsoft.com/library/4fkewx0t.aspx).**

Reguły autoryzacji dostępu współdzielonego użytego do podpisania musi być skonfigurowany w jednostce określona przez ten identyfikator URI lub za pomocą jednej z grup nadrzędnych hierarchicznych. Na przykład `http://contoso.servicebus.windows.net/contosoTopics/T1` lub `http://contoso.servicebus.windows.net` w poprzednim przykładzie.

Token sygnatury dostępu Współdzielonego jest prawidłowa dla wszystkich zasobów z prefiksem `<resourceURI>` używane w `signature-string`.

## <a name="regenerating-keys"></a>Ponowne generowanie kluczy

Zalecane jest, okresowo generować ponownie klucze używane w [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu. Gniazda klucza podstawowego i pomocniczego istnieje, dzięki czemu można stopniowo wymiany kluczy. Jeśli Twoja aplikacja ogólnie używa klucza podstawowego, skopiuj klucz podstawowy do pomocniczego klucza gniazda, a dopiero wtedy ponownie wygenerować klucz podstawowy. Następnie można skonfigurować nową wartość klucza podstawowego w aplikacji klienta, które mają stały dostęp przy użyciu starego klucza podstawowego w gnieździe dodatkowej. Gdy wszyscy klienci zostaną zaktualizowane, można ponownie wygenerować klucza pomocniczego, aby na koniec wycofać stary klucz podstawowy.

Jeśli jest Ci znana lub podejrzeń, że klucz zostanie naruszony, i że masz odwołać kluczy, można ponownie wygenerować zarówno [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) i [klucz pomocniczy](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), zastępując je za pomocą nowych kluczy. Ta procedura powoduje unieważnienie wszystkie tokeny podpisane przy użyciu starych kluczy.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Udostępnione uwierzytelniania sygnatury dostępu za pomocą usługi Service Bus

Konfiguracja reguł autoryzacji, generowanie tokenów sygnatur dostępu Współdzielonego i uwierzytelnianiem klienta są następujące scenariusze, w opisany następująco.

Aby uzyskać pełne pracy przykładowej aplikacji usługi Service Bus, który ilustruje konfiguracji i używa autoryzacji sygnatury dostępu Współdzielonego, zobacz [uwierzytelniania sygnatura dostępu współdzielonego z usługą Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Powiązane próbki, który ilustruje sposób używania reguł autoryzacji sygnatury dostępu Współdzielonego skonfigurowanej w przestrzeni nazw lub tematy, aby zabezpieczyć subskrypcje usługi Service Bus jest dostępna tutaj: [Korzystanie z uwierzytelniania sygnatury dostępu współdzielonego (SAS) z subskrypcji magistrali usług](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Reguły autoryzacji dostępu do udostępnionych dostępu w jednostce

Biblioteki usługi Service Bus .NET Framework umożliwia dostęp do [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) skonfigurowany dla kolejki usługi Service Bus lub temat za pomocą obiektu [regułautoryzacji](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) kolekcji w odpowiednich [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Poniższy kod przedstawia sposób dodawania reguł autoryzacji dla kolejki.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Użyj autoryzacji sygnatura dostępu współdzielonego

Aplikacje z bibliotekami .NET magistrali usług przy użyciu zestawu Azure .NET SDK, można użyć autoryzacji sygnatury dostępu Współdzielonego za pośrednictwem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) klasy. Poniższy kod ilustruje sposób używania dostawcy tokenu, aby wysyłać komunikaty do kolejki usługi Service Bus. Alternatywa dla użycia tutaj pokazany, poprzednio wystawiony token można również przekazać do metody fabryki dostawcy tokenu.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Można również użyć dostawcy tokenu, który bezpośrednio do wystawiania tokenów do przekazania do innych klientów.

Parametry połączenia mogą zawierać nazwę reguły (*SharedAccessKeyName*) i klucz reguły (*SharedAccessKey*) lub wcześniej wystawiony token (*SharedAccessSignature*). Jeśli są obecne w ciągu połączenia, przekazywane do dowolnego konstruktora lub metody fabryki, które przyjmuje parametry połączenia, dostawcy tokenu sygnatury dostępu Współdzielonego jest automatycznie tworzone i wypełniane.

Należy pamiętać, że w celu autoryzacji sygnatury dostępu Współdzielonego za pomocą usługi Service Bus Relay, można użyć kluczy sygnatury dostępu Współdzielonego skonfigurowane w przestrzeni nazw usługi Service Bus. Jeśli jawnie utworzyć wystąpienie usługi przekazywania w przestrzeni nazw ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) z [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) obiektu, można ustawić tylko odpowiadającym zasady sygnatury dostępu Współdzielonego. W celu autoryzacji sygnatury dostępu Współdzielonego za pomocą subskrypcji usługi Service Bus, można użyć kluczy sygnatury dostępu Współdzielonego skonfigurowane w przestrzeni nazw usługi Service Bus lub temat.

## <a name="use-the-shared-access-signature-at-http-level"></a>Użyj Shared Access Signature (na poziomie protokołu HTTP)

Teraz gdy wiesz, jak utworzyć sygnatury dostępu współdzielonego dla dowolnej jednostki w usłudze Service Bus, można przystąpić do wykonania żądania HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Należy pamiętać, że działa to wszystko. Można utworzyć sygnatury dostępu Współdzielonego dla kolejki, tematu lub subskrypcji.

Jeśli nadasz nadawcy lub klienta tokenu sygnatury dostępu Współdzielonego, nie muszą oni klucz bezpośrednio, a ich nie można wycofać wyznaczania wartości skrótu, aby go uzyskać. W efekcie masz kontrolę nad co mogą oni uzyskać dostęp oraz jak długo. Ważne jest, aby pamiętać to, że w przypadku zmiany klucza podstawowego w zasadach żadnych sygnatur dostępu współdzielonego utworzonych na jej podstawie są unieważniane.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Użyj Shared Access Signature (na poziomie protokołu AMQP)

W poprzedniej sekcji pokazano, jak za pomocą tokenu sygnatury dostępu Współdzielonego żądania HTTP POST do wysyłania danych do usługi Service Bus. Jak już wiadomo, można uzyskać dostęp do usługi Service Bus przy użyciu zaawansowanych wiadomości Queuing Protocol (AMQP), jest preferowanym protokołem do użycia ze względu na wydajność w wielu scenariuszach. Użycie tokenu sygnatury dostępu Współdzielonego z obsługą protokołu AMQP jest opisany w dokumencie [AMQP Claim-Based zabezpieczeń wersja 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) to znaczy w pracy narzędzia draft od 2013, ale dobrze obsługiwane przez platformę Azure już dziś.

Przed rozpoczęciem do przesyłania danych do usługi Service Bus, wydawca musi wysłać tokenu sygnatury dostępu Współdzielonego w wiadomości protokołu AMQP do dobrze zdefiniowanych AMQP węzła o nazwie **$cbs** (można go było wyświetlić jako "specjalne" kolejka używane przez usługę, aby pobrać i zweryfikować wszystkie sygnatury dostępu Współdzielonego tokeny). Należy określić wydawcę **ReplyTo** pola wewnątrz komunikatu protokołu AMQP; jest to węzeł, w którym usługa odpowiada z wydawcą zostało nawiązane z wynikami weryfikacji tokenu (proste żądanie/nietypizowana odpowiedź wzorca od wydawcy i usługi ). Ten węzeł odpowiedzi jest tworzony "w locie," mówić o "dynamiczne tworzenie węzeł zdalny", jak opisano w specyfikacji protokołu AMQP 1.0. Po sprawdzeniu, czy token sygnatury dostępu Współdzielonego jest prawidłowa, wydawcy można przejść do przodu i wysyłać dane do usługi.

Poniższe kroki pokazują sposób wysyłania tokenu sygnatury dostępu Współdzielonego przy użyciu protokołu AMQP [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) biblioteki. Jest to przydatne, jeśli nie możesz użyć usługi Service Bus z zestawu SDK (na przykład na WinRT, platformy .NET Compact Framework, .NET Micro Framework i Mono) oficjalne Programowanie w języku C\#. Oczywiście, ta biblioteka jest przydatne lepiej zrozumieć, jak opartego na oświadczeniach zabezpieczeń działa na poziomie protokołu AMQP, co pokazano, jak to działa na poziomie protokołu HTTP (z żądania HTTP POST i tokenu sygnatury dostępu Współdzielonego, wysyłany w nagłówku "Autoryzacja"). Jeśli nie potrzebujesz takiego głębokiego wiedzę na temat protokołu AMQP umożliwia oficjalne zestawu SDK magistrali usług w aplikacjach .NET Framework, które zrobić to za Ciebie.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()` Metoda otrzymuje *połączenia* (wystąpienie klasy połączenia AMQP zgodnie z informacjami od [AMQP .NET Lite biblioteki](https://github.com/Azure/amqpnetlite)) reprezentującą połączenie TCP do usługi i *sasToken* parametr, który jest sygnatura dostępu Współdzielonego token do wysłania.

> [!NOTE]
> Jest ważne, że połączenie jest tworzony z **mechanizm uwierzytelniania SASL ustawiono anonimowe** (i nie zwykły domyślne przy użyciu nazwy użytkownika i hasło użyte podczas nie trzeba będzie wysłać tokenu sygnatury dostępu Współdzielonego).
>
>

Następnie wydawca tworzy dwa połączenia AMQP do wysyłania tokenu sygnatury dostępu Współdzielonego i odbierania odpowiedzi (wynik weryfikacji tokenu) z usługi.

Komunikat protokołu AMQP zawiera zbiór właściwości oraz więcej informacji, niż prosty komunikat. Token sygnatury dostępu Współdzielonego jest treść wiadomości (przy użyciu jej konstruktora). **"ReplyTo"** właściwość jest ustawiona na nazwę węzła do odbierania wynik weryfikacji Link odbiorcy (możesz zmienić jego nazwę, jeśli chcesz, a zostanie ona utworzona w dynamicznie przez usługę). Ostatnie trzy właściwości aplikacji/niestandardowe są używane przez usługę do wskazania, jaki rodzaj operacji ma do wykonania. Zgodnie z opisem w specyfikacji wersji roboczej CBS, musi być **nazwy operacji** ("put-token"), **typ tokenu** (w tym przypadku `servicebus.windows.net:sastoken`), a **"Nazwa" odbiorców** token dotyczy (jednostka całego).

Po wysłaniu tokenu sygnatury dostępu Współdzielonego Link nadawcy, wydawca musi odczytać odpowiedzi przez łącze odbiorcy. Odpowiedź jest prosty komunikat protokołu AMQP z właściwością aplikacji o nazwie **"Kod stanu"** zawierających te same wartości Kod stanu HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Uprawnienia wymagane do operacji usługi Service Bus

W poniższej tabeli przedstawiono prawa dostępu wymagane dla różnych operacji na zasobach usługi Service Bus.

| Operacja | Wymagane oświadczenia | Oświadczenie zakresu |
| --- | --- | --- |
| **Namespace** | | |
| Skonfiguruj reguły autoryzacji w przestrzeni nazw |Zarządzanie |Dowolny adres przestrzeni nazw |
| **Service Registry** | | |
| Wyliczać zasady prywatne |Zarządzanie |Dowolny adres przestrzeni nazw |
| Rozpocząć nasłuchiwania w przestrzeni nazw |Nasłuchiwanie |Dowolny adres przestrzeni nazw |
| Wysyłanie komunikatów do odbiornika w przestrzeni nazw |Wysyłanie |Dowolny adres przestrzeni nazw |
| **kolejki** | | |
| Tworzenie kolejki |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie kolejki |Zarządzanie |Dowolny adres prawidłową kolejką |
| Wyliczanie kolejek |Zarządzanie |Katalogu zasobów/kolejki |
| Pobierz opis kolejki |Zarządzanie |Dowolny adres prawidłową kolejką |
| Konfigurowanie reguł autoryzacji dla kolejki |Zarządzanie |Dowolny adres prawidłową kolejką |
| Wysyłać do kolejki |Wysyłanie |Dowolny adres prawidłową kolejką |
| Odbieranie komunikatów z kolejki |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Porzucenie lub ukończenie wiadomości po odebraniu wiadomości w trybie blokady podglądu |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Odrocz wiadomość do nowszych pobierania |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Utraconych wiadomości |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Pobierz stan skojarzony z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Ustaw stan skojarzony z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny adres prawidłową kolejką |
| Harmonogram wiadomości w celu późniejszego dostarczenia; na przykład [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Nasłuchiwanie | Dowolny adres prawidłową kolejką
| **Temat** | | |
| Tworzenie tematu |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie tematu |Zarządzanie |Dowolny adres nieprawidłowy temat |
| Wyliczanie — tematy |Zarządzanie |Katalogu zasobów/tematów |
| Opis tematu pobierania |Zarządzanie |Dowolny adres nieprawidłowy temat |
| Konfigurowanie reguł autoryzacji dla tematu |Zarządzanie |Dowolny adres nieprawidłowy temat |
| Wysyłanie do tematu |Wysyłanie |Dowolny adres nieprawidłowy temat |
| **Subskrypcja** | | |
| Tworzenie subskrypcji |Zarządzanie |Dowolny adres przestrzeni nazw |
| Usuwanie subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie subskrypcji |Zarządzanie |.. / myTopic/subskrypcji |
| Pobierz opis subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Porzucenie lub ukończenie wiadomości po odebraniu wiadomości w trybie blokady podglądu |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Odrocz wiadomość do nowszych pobierania |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Utraconych wiadomości |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Pobierz stan skojarzony z sesją tematu |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| Ustaw stan skojarzony z sesją tematu |Nasłuchiwanie |.. /myTopic/Subscriptions/mySubscription |
| **reguły** | | |
| Tworzenie reguły |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Usuwanie reguły |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie zasad |Zarządzania lub nasłuchiwania |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
