---
title: Azure Service Bus kontrolą dostępu przy użyciu sygnatury dostępu współdzielonego | Dokumentacja firmy Microsoft
description: Omówienie usługi Service Bus kontroli dostępu przy użyciu sygnatury dostępu współdzielonego Przegląd, szczegółowe informacje o autoryzacji SAS z usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm;clemensv
ms.openlocfilehash: 21e9e0a20842e365e40b71ac96888e7cd2056e52
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Kontrola dostępu usługi Service Bus przy użyciu sygnatury dostępu współdzielonego

*Sygnatury dostępu współdzielonego* (SAS) są mechanizmu podstawowego zabezpieczeń komunikatów usługi Service Bus. W tym artykule omówiono SAS, jak działają i sposobu ich używania w sposób niezależny od platformy.

Sygnatury dostępu Współdzielonego chroni dostęp do usługi Service Bus na podstawie reguł autoryzacji. Te są konfigurowane w przestrzeni nazw lub jednostki obsługi komunikatów (przekazywania, kolejka lub temat). Reguły autoryzacji ma nazwę, jest skojarzona z określonych praw i prowadzi pary kluczy kryptograficznych. Użyj nazwy i klucza przy użyciu usługi Service Bus SDK lub w swoim własnym kodem reguły jest do generowania tokenu sygnatury dostępu Współdzielonego. Klienta można następnie przekazać token do usługi Service Bus w celu potwierdzenia autoryzacji dla żądanej operacji.

## <a name="overview-of-sas"></a>Omówienie SAS

Udostępniony sygnatur dostępu są mechanizmu autoryzacji na podstawie oświadczeń tokeny proste. Przy użyciu sygnatury dostępu Współdzielonego, klucze nigdy nie są przekazywane w sieci. Klucze są używane do podpisywania kryptograficznie informacje, które można później sprawdzić przez usługę. SAS może służyć za podobne do nazwy użytkownika i hasła systemu, gdzie klient będzie w posiadaniu natychmiastowego Nazwa reguły autoryzacji i dopasowany klucz. Sygnatury dostępu Współdzielonego można również podobny do modelu zabezpieczeń, gdy klient odbiera token dostępu czas i podpisany z usługi tokenu zabezpieczającego bez kiedykolwiek wchodzących w posiadaniu klucza podpisywania.

Skojarzenia zabezpieczeń uwierzytelniania w usłudze Service Bus jest skonfigurowany z o nazwie [reguły autoryzacji dostępu do udostępnionych](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) o skojarzone prawa dostępu i parę kluczy kryptograficznych podstawowego i pomocniczego. Klucze są wartości 256-bitowe w reprezentacji Base64. Reguły można skonfigurować na poziomie przestrzeni nazw usługi Service Bus [przekazuje](service-bus-fundamentals-hybrid-solutions.md#relays), [kolejek](service-bus-fundamentals-hybrid-solutions.md#queues), i [tematy](service-bus-fundamentals-hybrid-solutions.md#topics).

[Sygnatura dostępu współdzielonego](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token zawiera nazwę reguły autoryzacji wybrany, identyfikator URI zasobu, który jest dostępna, błyskawiczne, wygaśnięcia i podpis kryptograficzny HMAC SHA256 obliczona dla tych pól przy użyciu podstawowy lub pomocniczy klucz kryptograficzny reguły autoryzacji wybrany.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego

Każdej przestrzeni nazw usługi Service Bus i każdej jednostki magistrali usług ma zasady autoryzacji dostępu do udostępnionych składają się z reguł. Zasady na poziomie przestrzeni nazw ma zastosowanie do wszystkich jednostek w obszarze nazw, niezależnie od ich konfiguracji poszczególnych zasad.

Dla każdej reguły zasad autoryzacji zdecydujesz się na trzy informacje: **nazwa**, **zakres**, i **prawa**. **Nazwa** jest po prostu; unikatową nazwę w ramach tego zakresu. Zakres jest dość proste: jest identyfikator URI zasobu. W przypadku przestrzeni nazw usługi Service Bus zakres jest w pełni kwalifikowaną nazwę (FQDN), takich jak `https://<yournamespace>.servicebus.windows.net/`.

Praw przyznanych przez regułę zasad może być kombinacją:

* "Send" - uprawnia do wysyłania komunikatów do jednostki
* "Nasłuchiwania" - uprawnia do nasłuchiwania (przekaźnik) lub odebrać (kolejki, subskrypcje) i wszystkich powiązanych komunikat — Obsługa
* "Zarządzaj" — uprawnia do zarządzania topologią obszaru nazw, w tym tworzenie i usuwanie jednostek

Uprawnienie "Zarządzaj" obejmuje prawa "Send" i "Receive".

Zasady przestrzeni nazw lub jednostka może zawierać maksymalnie 12 reguł autoryzacji dostępu do udostępnionych, udostępnianie miejsca dla trzech zestawów reguł, każdego obejmujące podstawowych praw i kombinacja wysyłania i nasłuchiwania. Podkreśleń ten limit, które magazynu zasad SAS nie mają być użytkownik lub magazyn kont usługi. Jeśli aplikacja wymaga w celu udzielenia dostępu do usługi Service Bus na podstawie użytkownika lub tożsamości usługi, powinien implementować usługi tokenu zabezpieczającego, który wystawia tokeny sygnatury dostępu Współdzielonego po kontroli dostępu i uwierzytelniania.

Reguły autoryzacji jest przypisany *klucza podstawowego* i *klucza pomocniczego*. Są to silną kryptograficznie kluczy. Nie utracone lub ich wyciek — zawsze będzie dostępna w [portalu Azure][Azure portal]. Możesz użyć dowolnej wygenerowane klucze i można je odtworzyć w dowolnym momencie. Jeśli ponownie wygenerować lub zmienić wartość klucza w zasadach, wszystkie wcześniej wystawione tokeny na podstawie tego klucza, stają się natychmiast nieprawidłowy. Jednak bieżących połączeń tworzone w oparciu takie tokeny będą w dalszym ciągu działać do momentu wygaśnięcia tokenu.

Po utworzeniu przestrzeni nazw usługi Service Bus reguły o nazwie **RootManageSharedAccessKey** jest tworzona automatycznie dla przestrzeni nazw. Ta zasada ma uprawnienia Zarządzaj dla całej przestrzeni nazw. Zalecane jest, aby traktować tej reguły, takie jak administracyjne **głównego** konta, a nie jest używany w aplikacji. Można utworzyć dodatkowe reguły w **Konfiguruj** kartę dla przestrzeni nazw w portalu, za pomocą programu Powershell lub interfejsu wiersza polecenia platformy Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguracja uwierzytelniania sygnatura dostępu współdzielonego

Można skonfigurować [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) reguły w przestrzeni nazw usługi Service Bus, kolejki i tematy. Konfigurowanie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) usługi Service Bus subskrypcji nie jest obecnie obsługiwane, ale zasady skonfigurowane w przestrzeni nazw lub temat umożliwia bezpieczny dostęp do subskrypcji. Dla przykładu pracy, która ilustruje tę procedurę, zobacz [uwierzytelniania przy użyciu dostępu sygnatury dostępu Współdzielonego z subskrypcjami magistrali usługi](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) próbki.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tym rysunku *manageRuleNS*, *sendRuleNS*, i *listenRuleNS* reguł autoryzacji dotyczą zarówno kolejka K1 i tematu T1, podczas gdy *listenRuleQ*  i *sendRuleQ* mają zastosowanie tylko do kolejki K1 i *sendRuleT* ma zastosowanie tylko do tematu T1.

## <a name="generate-a-shared-access-signature-token"></a>Wygeneruj token sygnatura dostępu współdzielonego

Każdy klient, który ma dostęp do nazwę reguły autoryzacji i jeden z jego klucze podpisywania można wygenerować tokenu sygnatury dostępu Współdzielonego. Token jest generowany przez obsługuje tworzenie ciąg w następującym formacie:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Tokenu błyskawicznych wygaśnięcia. Liczba całkowita, liczba sekund od epoka odzwierciedlające `00:00:00 UTC` na 1 stycznia 1970 (UNIX epoki) wygaśnięcia tokenu.
* **`skn`** -Nazwa reguły autoryzacji.
* **`sr`** — Identyfikator URI zasobu, do której uzyskuje dostęp.
* **`sig`** -Podpis.

`signature-string` Jest wyznaczania wartości skrótu SHA-256 jest obliczana przez identyfikator URI zasobu (**zakres** zgodnie z opisem w poprzedniej sekcji) reprezentację ciągu wygaśnięcia tokenu błyskawiczne, rozdzielając je CRLF.

Obliczania skrótu wygląda podobnie do poniższego kodu pseudo i zwraca wartość skrótu 256-bitowego/32-bajtowego.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości-mieszane, dzięki czemu odbiorca może ponownie obliczyć skrótu z takimi samymi parametrami, weryfikowanie, czy wystawca znajduje się w posiadaniu prawidłowego klucza podpisywania. 

Identyfikator URI zasobu jest pełny identyfikator URI zasobu usługi Service Bus, do którego dostęp jest określona. Na przykład `http://<namespace>.servicebus.windows.net/<entityPath>` lub `sb://<namespace>.servicebus.windows.net/<entityPath>`, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. Identyfikator URI musi być [procent, kodowane](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Reguły autoryzacji dostępu współdzielonego, używany do podpisywania musi być skonfigurowany w jednostce określona przez ten identyfikator URI lub za pomocą jednej z jej nadrzędnych hierarchicznej. Na przykład `http://contoso.servicebus.windows.net/contosoTopics/T1` lub `http://contoso.servicebus.windows.net` w poprzednim przykładzie.

Token sygnatury dostępu Współdzielonego jest prawidłowa dla wszystkich zasobów i jest poprzedzony prefiksem `<resourceURI>` używane w `signature-string`.

## <a name="regenerating-keys"></a>Trwa ponowne generowanie kluczy

Zalecane jest okresowe ponowne generowanie kluczy używanych w [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu. Gniazda klucza podstawowego i pomocniczego istnieje, dzięki czemu można obracać stopniowo kluczy. Aplikacja ogólnie używa klucza podstawowego, można skopiować klucza podstawowego do dodatkowej gniazda klucza i następnie ponowne wygenerowanie klucza podstawowego. Następnie można skonfigurować nowe wartości klucza podstawowego w aplikacji klienta, które mają stały dostęp w miejscu dodatkowej przy użyciu starego klucza podstawowego. Gdy wszyscy klienci zostaną zaktualizowane, można ponownie wygenerować klucza pomocniczego finally wycofywania starego klucza podstawowego.

Jeśli znasz lub podejrzenie, że klucz zostanie naruszony, a także Odwołaj klucze, należy ponownie wygenerować zarówno [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) i [klucz pomocniczy](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) z [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), zastępując je nowych kluczy. Ta procedura powoduje unieważnienie wszystkie tokeny podpisane przy użyciu starego klawiszy.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Uwierzytelniania współużytkowanego podpisu dostępu z usługą Service Bus

Poniżej opisano scenariusze obejmują Konfiguracja reguł autoryzacji, generowanie tokeny sygnatury dostępu Współdzielonego i uwierzytelnianiem klienta.

Dla pełnej pracy przykładowej aplikacji usługi Service Bus, która ilustruje konfiguracji i używa autoryzacji sygnatury dostępu Współdzielonego, zobacz [sygnatura dostępu współdzielonego uwierzytelniania za pomocą usługi Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Powiązane próbki, która ilustruje korzystanie z reguł autoryzacji sygnatury dostępu Współdzielonego skonfigurowany dla przestrzeni nazw lub tematy, aby zabezpieczyć subskrypcje usługi Service Bus jest dostępnych tutaj: [uwierzytelniania przy użyciu dostępu sygnatury dostępu Współdzielonego z subskrypcjami magistrali usługi](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Reguły dostępu do udostępnionych autoryzacji dostępu na jednostkę

Z biblioteki usługi magistrali .NET Framework, można uzyskać dostęp do [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) skonfigurowany dla kolejki usługi Service Bus lub temat za pośrednictwem obiektu [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) kolekcji w odpowiednich [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

Aplikacje z bibliotekami .NET magistrali usług przy użyciu zestawu .NET SDK usługi Azure mogą używać autoryzacji sygnatury dostępu Współdzielonego za pośrednictwem [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) klasy. Poniższy kod przedstawia użycie dostawcy tokenów do wysyłania wiadomości do kolejki usługi Service Bus. Alternatywa dla użycia w tym miejscu pokazano wcześniej wystawionego tokenu można również przekazać do metody fabryki dostawcy tokenu.

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

Umożliwia także dostawcę tokenów bezpośrednio wydawania tokenów do przekazania do innych klientów. 

Parametry połączenia mogą obejmować nazwę reguły (*SharedAccessKeyName*) i klucz reguły (*SharedAccessKey*) lub wcześniej wystawionego tokenu (*SharedAccessSignature*). Gdy te są obecne w parametrach połączenia przekazany do dowolnego konstruktora lub metody fabryki przyjmuje parametry połączenia, Dostawca tokenu sygnatury dostępu Współdzielonego jest automatycznie tworzone i wypełniane.

Należy pamiętać, że do autoryzacji sygnatury dostępu Współdzielonego za pomocą przekaźników usługi Service Bus, można użyć klawiszy SAS skonfigurowane w przestrzeni nazw usługi Service Bus. Jeśli utworzysz jawnie przekaźnik w przestrzeni nazw ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) z [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) obiektu, można ustawić tylko przekazywania tej zasady sygnatury dostępu Współdzielonego. Aby używać autoryzacji sygnatury dostępu Współdzielonego z subskrypcji usługi Service Bus, służy skonfigurowane na przestrzeni nazw usługi Service Bus lub na temat kluczy SAS.

## <a name="use-the-shared-access-signature-at-http-level"></a>Użyj sygnatura dostępu współdzielonego (na poziomie protokołu HTTP)

Teraz, gdy wiesz, jak utworzyć sygnatury dostępu współdzielonego dla dowolnego jednostek usługi Service Bus, możesz przystąpić do wykonania HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Należy pamiętać, że wszystko działa. Można utworzyć sygnatury dostępu Współdzielonego dla kolejki, tematu lub subskrypcji.

Jeśli nadawcy lub klienta tokenu sygnatury dostępu Współdzielonego, nie ma klucza bezpośrednio, a ich nie można wycofać wyznaczania wartości skrótu, aby go uzyskać. W efekcie masz kontroli nad co mogą uzyskiwać dostęp do, jak długo. Ważne jest, aby pamiętać jest zmiana klucza podstawowego w zasadach, żadnych sygnatur dostępu współdzielonego z niej utworzyć są unieważnione.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Użyj sygnatura dostępu współdzielonego (na poziomie protokołu AMQP)

W poprzedniej sekcji pokazano, jak używać tokenu sygnatury dostępu Współdzielonego z żądaniem HTTP POST do wysyłania danych do usługi Service Bus. Wiesz, można uzyskać dostępu do usługi Service Bus przy użyciu zaawansowane komunikatów usługi kolejkowania protokołu (AMQP), który jest preferowanym protokołem do użycia ze względu na wydajność w wielu scenariuszach. Sposób użycia tokenu sygnatury dostępu Współdzielonego z protokołu AMQP jest opisany w dokumencie [AMQP Claim-Based zabezpieczeń wersja 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) będący w pracy projekt od 2013, ale dobrze obsługiwane przez platformę Azure dzisiaj.

Przed rozpoczęciem z wysyłania danych do usługi Service Bus, wydawcy musi wysłać tokenu sygnatury dostępu Współdzielonego wewnątrz komunikat protokołu AMQP dobrze zdefiniowany AMQP węzła o nazwie **$cbs** (można to sprawdzić jako kolejka "specjalne" używane przez usługę nabywanie i zweryfikować wszystkie skojarzenia zabezpieczeń tokeny). Należy określić wydawcę **ReplyTo** pola w komunikacie protokołu AMQP; jest to węzeł, w którym usługa odpowiedzi do wydawcy o rezultat weryfikacji tokenu (żądania/odpowiedzi prostego wzorca między wydawcy i usługi ). Ten węzeł odpowiedzi jest tworzona "na bieżąco," mówiąc o "dynamiczne tworzenie zdalnego węzła", zgodnie z opisem w specyfikacji protokołu AMQP 1.0. Po sprawdzeniu, czy token sygnatury dostępu Współdzielonego jest prawidłowa, wydawcy Przejdź dalej i uruchom, aby wysłać dane do usługi.

W poniższej procedurze pokazano sposób wysyłania tokenu sygnatury dostępu Współdzielonego przy użyciu protokołu AMQP [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) biblioteki. Jest to przydatne, jeśli nie możesz użyć oficjalnego zestawu SDK magistrali usług (na przykład na WinRT, .net Compact Framework, .net Micro Framework i Mono) opracowywanie w języku C\#. Oczywiście Ta biblioteka jest przydatne ułatwi zrozumienie sposobu opartego na oświadczeniach zabezpieczeń działa na poziomie protokołu AMQP, jak przedstawiono, jak to działa na poziomie protokołu HTTP (z żądania HTTP POST i tokenu sygnatury dostępu Współdzielonego wysyłany w nagłówku "Autoryzacji"). Jeśli nie ma potrzeby takich głębokie wiedzą na temat protokołu AMQP, możesz użyć oficjalnego zestawu SDK magistrali usług z platformą .net Framework aplikacji, co zrobić to za Ciebie.

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

`PutCbsToken()` Metoda *połączenia* (wystąpienie klasy połączenia protokołu AMQP zgodnie z [AMQP .NET Lite biblioteki](https://github.com/Azure/amqpnetlite)) reprezentujący połączenie TCP do usługi i *sasToken* parametr, który jest sygnatury dostępu Współdzielonego token do wysłania. 

> [!NOTE]
> Należy pamiętać, że połączenie jest utworzone za pomocą **mechanizmu uwierzytelniania SASL ustawić anonimowy** (i nie zwykły domyślna nazwa użytkownika i hasło używane, gdy nie jest konieczne do wysyłania tokenu sygnatury dostępu Współdzielonego).
> 
> 

Następnie wydawcy tworzy dwa łącza AMQP wysyłania tokenu sygnatury dostępu Współdzielonego i odbierania odpowiedzi (wynik weryfikacji tokenu) z usługi.

Komunikat protokołu AMQP zawiera zbiór właściwości oraz dodatkowe informacje od prostego komunikatu. SAS token jest treści wiadomości (przy użyciu jego konstruktora). **"ReplyTo"** właściwość jest ustawiona na nazwę węzła odbieranie wynik weryfikacji łącze odbiornika (można zmienić jego nazwę, a zostanie utworzony dynamicznie przez usługę). Ostatnie trzy właściwości aplikacji/niestandardowe są używane przez usługę do wskazuje, jaki rodzaj operacji musi wykonać. Zgodnie z opisem w specyfikacji wersji roboczej CBS, musi być **nazwy operacji** ("put-token"), **typu tokenu** (w tym przypadku `servicebus.windows.net:sastoken`) i **"Nazwa" odbiorców** token dotyczy (jednostka całego).

Po wysłaniu tokenu sygnatury dostępu Współdzielonego łącze nadawcy, wydawcy musi odczytu odpowiedzi łącze odbiornika. Odpowiedź jest prosty komunikat protokołu AMQP z właściwością aplikacji o nazwie **"Kod stanu"** zawierających takie same wartości jak kod stanu HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Uprawnienia wymagane dla operacji usługi Service Bus

W poniższej tabeli przedstawiono prawa dostępu wymagane przez różne operacje na zasobów usługi Service Bus.

| Operacja | Oświadczenia wymagane | Zakres oświadczeń |
| --- | --- | --- |
| **Namespace** | | |
| Konfiguruj reguły autoryzacji w przestrzeni nazw |Zarządzanie |Każdy adres przestrzeni nazw |
| **Rejestr usługi** | | |
| Wyliczanie zasad prywatnych |Zarządzanie |Każdy adres przestrzeni nazw |
| Rozpoczęcie nasłuchiwania na przestrzeni nazw |Nasłuchuj |Każdy adres przestrzeni nazw |
| Wysyłanie komunikatów do odbiornika w przestrzeni nazw |Send |Każdy adres przestrzeni nazw |
| **Queue** | | |
| Tworzenie kolejki |Zarządzanie |Każdy adres przestrzeni nazw |
| Usuwanie kolejki |Zarządzanie |Każdy adres prawidłową kolejką |
| Wyliczanie kolejek |Zarządzanie |Katalogu zasobów/kolejek |
| Pobierz opis kolejki |Zarządzanie |Każdy adres prawidłową kolejką |
| Konfigurowanie reguł autoryzacji dla kolejki |Zarządzanie |Każdy adres prawidłową kolejką |
| Wysyłanie do do kolejki |Send |Każdy adres prawidłową kolejką |
| Odbieranie komunikatów z kolejki |Nasłuchuj |Każdy adres prawidłową kolejką |
| Porzucenia lub pełne wiadomości po otrzymaniu komunikatu w trybie blokady podglądu |Nasłuchuj |Każdy adres prawidłową kolejką |
| Odroczenie komunikat dla nowszej pobierania |Nasłuchuj |Każdy adres prawidłową kolejką |
| Wiadomości utraconych wiadomości |Nasłuchuj |Każdy adres prawidłową kolejką |
| Pobierz stan skojarzony z sesją kolejki komunikatów |Nasłuchuj |Każdy adres prawidłową kolejką |
| Ustaw stan skojarzony z sesją kolejki komunikatów |Nasłuchuj |Każdy adres prawidłową kolejką |
| Harmonogram wiadomości w celu późniejszego dostarczenia; na przykład [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Nasłuchuj | Każdy adres prawidłową kolejką
| **Temat** | | |
| Tworzenie tematu |Zarządzanie |Każdy adres przestrzeni nazw |
| Usunięcie tematu |Zarządzanie |Każdy adres nieprawidłowy temat |
| Wyliczanie — tematy |Zarządzanie |Katalogu zasobów/tematy |
| Opis tematu pobierania |Zarządzanie |Każdy adres nieprawidłowy temat |
| Konfigurowanie reguł autoryzacji dla tematu |Zarządzanie |Każdy adres nieprawidłowy temat |
| Wyślij do tematu |Send |Każdy adres nieprawidłowy temat |
| **Subskrypcja** | | |
| Tworzenie subskrypcji |Zarządzanie |Każdy adres przestrzeni nazw |
| Usuwanie subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie subskrypcji |Zarządzanie |.. / myTopic/subskrypcji |
| Pobrać opisu subskrypcji |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Porzucenia lub pełne wiadomości po otrzymaniu komunikatu w trybie blokady podglądu |Nasłuchuj |.. /myTopic/Subscriptions/mySubscription |
| Odroczenie komunikat dla nowszej pobierania |Nasłuchuj |.. /myTopic/Subscriptions/mySubscription |
| Wiadomości utraconych wiadomości |Nasłuchuj |.. /myTopic/Subscriptions/mySubscription |
| Pobierz stan skojarzony z sesją tematu |Nasłuchuj |.. /myTopic/Subscriptions/mySubscription |
| Ustaw stan skojarzony z sesją tematu |Nasłuchuj |.. /myTopic/Subscriptions/mySubscription |
| **Reguły** | | |
| Utwórz regułę |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Usuwanie reguły |Zarządzanie |.. /myTopic/Subscriptions/mySubscription |
| Wyliczanie zasad |Zarządzanie lub nasłuchiwania |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com