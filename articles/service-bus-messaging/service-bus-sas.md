---
title: Kontrola dostępu usługi Azure Service Bus z podpisami dostępu współdzielonego
description: Omówienie kontroli dostępu usługi Service Bus przy użyciu sygnatur dostępu współdzielonego— szczegółowe informacje o autoryzacji sygnatury dostępu współdzielonego w usłudze Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259476"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Kontrola dostępu usługi Service Bus z podpisami dostępu współdzielonego

*Sygnatury dostępu współdzielonego* (SAS) są podstawowym mechanizmem zabezpieczeń dla obsługi wiadomości usługi Service Bus. W tym artykule omówiono sygnatury dostępu Współdzielonego, jak działają i jak z nich korzystać w sposób niezależny od platformy.

SAS zapewnia dostęp do usługi Service Bus na podstawie reguł autoryzacji. Są skonfigurowane w obszarze nazw lub w jednostce obsługi wiadomości (przekaźnik, kolejka lub temat). Reguła autoryzacji ma nazwę, jest skojarzona z określonymi prawami i zawiera parę kluczy kryptograficznych. Nazwa i klucz reguły służy za pośrednictwem SDK usługi Service Bus lub we własnym kodzie do generowania tokenu sygnatury dostępu Współdzielonego. Klient może następnie przekazać token do usługi Service Bus, aby udowodnić autoryzację żądanej operacji.

> [!NOTE]
> Usługa Azure Service Bus obsługuje autoryzowanie dostępu do obszaru nazw usługi Service Bus i jej jednostek przy użyciu usługi Azure Active Directory (Azure AD). Autoryzowanie użytkowników lub aplikacji przy użyciu tokenu OAuth 2.0 zwracanego przez usługę Azure AD zapewnia doskonałe bezpieczeństwo i łatwość użycia za pośrednictwem sygnatur dostępu współdzielonego (SAS). Dzięki usłudze Azure AD nie ma potrzeby przechowywania tokenów w kodzie i potencjalnych luk w zabezpieczeniach.
>
> Firma Microsoft zaleca korzystanie z usługi Azure AD z aplikacjami usługi Azure Service Bus, jeśli to możliwe. Aby uzyskać więcej informacji zobacz następujące artykuły:
> - [Uwierzytelnij i autoryzuj aplikację za pomocą usługi Azure Active Directory w celu uzyskania dostępu do encji usługi Azure Service Bus](authenticate-application.md).
> - [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Omówienie sygnatury dostępu Współdzielon

Podpisy dostępu współdzielonego to mechanizm autoryzacji oparty na oświadczeniach przy użyciu prostych tokenów. Za pomocą sygnatury dostępu Współdzielonego klucze nigdy nie są przekazywane na przewodach. Klucze są używane do kryptograficznie podpisywania informacji, które mogą być później zweryfikowane przez usługę. Sygnatury dostępu Współdzielonego można używać podobnie do schematu nazwy użytkownika i hasła, w którym klient jest w bezpośrednim posiadaniu nazwy reguły autoryzacji i pasującego klucza. Sygnatury dostępu Współdzielonego można również używać podobnie do modelu zabezpieczeń federacyjnego, w którym klient otrzymuje ograniczony czasowo i podpisany token dostępu z usługi tokenu zabezpieczającego bez konieczności posiadania klucza podpisywania.

Uwierzytelnianie SAS w usłudze Service Bus jest skonfigurowane przy użyciu [nazwanych reguł autoryzacji dostępu współdzielonego](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) z skojarzonymi prawami dostępu oraz pary podstawowych i pomocniczych kluczy kryptograficznych. Klucze są wartościami 256-bitowymi w reprezentacji Base64. Reguły można konfigurować na poziomie obszaru nazw, w [przekaźnikach,](../service-bus-relay/relay-what-is-it.md) [kolejkach](service-bus-messaging-overview.md#queues)i [tematach](service-bus-messaging-overview.md#topics)usługi Service Bus.

Token [podpisu dostępu współdzielonego](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) zawiera nazwę wybranej reguły autoryzacji, identyfikator URI zasobu, do który ma być dostępny, natychmiastowy czas wygaśnięcia oraz podpis kryptograficzny HMAC-SHA256 obliczony za pomocą podstawowego lub pomocniczego klucza kryptograficznego wybranej reguły autoryzacji.

## <a name="shared-access-authorization-policies"></a>Zasady autoryzacji dostępu współdzielonego

Każdy obszar nazw usługi Service Bus i każda encja usługi Service Bus ma zasady autoryzacji dostępu współdzielonego składające się z reguł. Zasady na poziomie obszaru nazw mają zastosowanie do wszystkich jednostek wewnątrz obszaru nazw, niezależnie od ich konfiguracji poszczególnych zasad.

Dla każdej reguły zasad autoryzacji decydujesz o trzech informacjach: **nazwa,** **zakres**i **prawa.** **Nazwa** jest właśnie taka; unikatową nazwę w tym zakresie. Zakres jest dość łatwy: jest to identyfikator URI danego zasobu. W przypadku obszaru nazw usługi Service Bus zakresem jest w pełni `https://<yournamespace>.servicebus.windows.net/`kwalifikowana nazwa domeny (FQDN), na przykład .

Prawa przyznane przez regułę zasad mogą być kombinacją:

* "Wyślij" - Przyznaje prawo do wysyłania wiadomości do podmiotu
* "Słuchaj" - przyznaje prawo do słuchania (przekazywania) lub odbierania (kolejka, subskrypcje) i wszystkie powiązane obsługi wiadomości
* "Zarządzanie" — przyznaje prawo do zarządzania topologią obszaru nazw, w tym do tworzenia i usuwania encji

Prawo "Zarządzaj" obejmuje prawa "Wyślij" i "Odbierz".

Zasady obszaru nazw lub encji mogą zawierać maksymalnie 12 reguł autoryzacji dostępu współdzielonego, zapewniając miejsce dla trzech zestawów reguł, z których każdy obejmuje podstawowe prawa i kombinację funkcji Wyślij i nasłuchuj. Ten limit podkreśla, że magazyn zasad sygnatury dostępu Współdzielonego nie jest przeznaczony do magazynu kont użytkowników lub usług. Jeśli aplikacja musi udzielić dostępu do usługi Service Bus na podstawie tożsamości użytkowników lub usług, należy zaimplementować usługę tokenu zabezpieczającego, która wystawia tokeny sygnatury dostępu współdzielonego po sprawdzeniu uwierzytelniania i dostępu.

Regułie autoryzacji jest przypisywany *klucz podstawowy* i *klucz pomocniczy*. Są to kryptograficznie silne klucze. Nie trać ich ani nie przeciekaj - zawsze będą dostępne w [witrynie Azure portal.][Azure portal] Można użyć jednego z wygenerowanych kluczy i można je ponownie wygenerować w dowolnym momencie. Jeśli ponownie wygenerujesz lub zmienisz klucz w zasadach, wszystkie wcześniej wystawione tokeny oparte na tym kluczu staną się natychmiast nieprawidłowe. Jednak bieżące połączenia utworzone na podstawie takich tokenów będą nadal działać do momentu wygaśnięcia tokenu.

Podczas tworzenia obszaru nazw usługi Service Bus dla obszaru nazw jest automatycznie tworzona reguła zasad o nazwie **RootManageSharedAccessKey.** Ta zasada ma uprawnienia Do zarządzania dla całej przestrzeni nazw. Zaleca się traktowanie tej reguły jak administracyjnego konta **głównego** i nieużyj jej w aplikacji. Dodatkowe reguły zasad można utworzyć na karcie **Konfigurowanie** obszaru nazw w portalu za pośrednictwem programu Powershell lub interfejsu wiersza polecenia platformy Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfiguracja uwierzytelniania podpisu dostępu współdzielonego

Regułę [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) można skonfigurować w obszarze nazw usługi Service Bus, kolejkach lub tematach. Konfigurowanie [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) w subskrypcji usługi Service Bus nie jest obecnie obsługiwane, ale można użyć reguł skonfigurowanych w obszarze nazw lub w temacie, aby zabezpieczyć dostęp do subskrypcji. W przykładzie roboczym, który ilustruje tę procedurę, zobacz [using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) sample.

![Sas](./media/service-bus-sas/service-bus-namespace.png)

Na tej ilustracji *manageRuleNS*, *sendRuleNS*i *listenRuleNS* reguły autoryzacji stosuje się zarówno do kolejki Q1 i temat T1, podczas gdy *listenRuleQ* i *sendRuleQ* stosuje się tylko do kolejki Q1 i *sendRuleT* dotyczy tylko tematu T1.

## <a name="generate-a-shared-access-signature-token"></a>Generowanie tokenu podpisu dostępu współdzielonego

Każdy klient, który ma dostęp do nazwy nazwy reguły autoryzacji i jeden z jego kluczy podpisywania może wygenerować token sygnatury dostępu Współdzielonego. Token jest generowany przez wykonanie ciągu w następującym formacie:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Natychmiastowe wygaśnięcie tokenu. Liczba całkowita odzwierciedlająca sekundy `00:00:00 UTC` od epoki 1 stycznia 1970 r. (epoka UNIX) po wygaśnięciu tokenu.
* **`skn`**- Nazwa reguły autoryzacji.
* **`sr`**- Identyfikator URI zasobu, do który uzyskuje się dostęp.
* **`sig`**- Podpis.

Jest `signature-string` sha-256 skrót obliczanych przez identyfikator URI zasobu **(zakres** opisany w poprzedniej sekcji) i reprezentacji ciągu natychmiastowego wygaśnięcia tokenu, oddzielone LF.

Obliczenia skrótu wygląda podobnie do następującego pseudo kodu i zwraca wartość mieszania 256-bit/32-bajtowy.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token zawiera wartości nie skrótowe, dzięki czemu odbiorca może ponownie obliczyć wartość skrótu z tymi samymi parametrami, sprawdzając, czy wystawca jest w posiadaniu prawidłowego klucza podpisywania.

Identyfikator URI zasobu jest pełnym identyfikatorem URI zasobu usługi Service Bus, do którego jest żądany dostęp. Na przykład `http://<namespace>.servicebus.windows.net/<entityPath>` `sb://<namespace>.servicebus.windows.net/<entityPath>`lub ; oznacza to, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`że . 

**Identyfikator URI musi być [zakodowany w procentach.](https://msdn.microsoft.com/library/4fkewx0t.aspx)**

Reguła autoryzacji dostępu współdzielonego używana do podpisywania musi być skonfigurowana w jednostce określonej przez ten identyfikator URI lub przez jeden z jej hierarchicznych łzawych. Na przykład `http://contoso.servicebus.windows.net/contosoTopics/T1` `http://contoso.servicebus.windows.net` lub w poprzednim przykładzie.

Token sygnatury dostępu Współdzielonego jest prawidłowy dla wszystkich zasobów `<resourceURI>` poprzedzonych użyciem w pliku `signature-string`.

## <a name="regenerating-keys"></a>Klucze regenerujące

Zaleca się okresowe ponowne generowanie kluczy używanych w [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) obiektu. Gniazda kluczy podstawowych i pomocniczych istnieją, dzięki czemu można obracać klawisze stopniowo. Jeśli aplikacja zazwyczaj używa klucza podstawowego, można skopiować klucz podstawowy do gniazda klucza pomocniczego, a dopiero następnie ponownie wygenerować klucz podstawowy. Nową wartość klucza podstawowego można następnie skonfigurować w aplikacjach klienckich, które mają stały dostęp przy użyciu starego klucza podstawowego w autodle. Po zaktualizowaniu wszystkich klientów można ponownie wygenerować klucz pomocniczy, aby ostatecznie wycofać stary klucz podstawowy.

Jeśli wiesz lub podejrzewasz, że klucz jest zagrożona i trzeba odwołać klucze, można ponownie wygenerować zarówno [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) i [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), zastępując je nowymi kluczami. Ta procedura unieważnia wszystkie tokeny podpisane przy za pomocą starych kluczy.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Uwierzytelnianie podpisu dostępu współdzielonego za pomocą usługi Service Bus

Scenariusze opisane w następujący sposób obejmują konfigurację reguł autoryzacji, generowanie tokenów sygnatury dostępu Współdzielonego i autoryzację klienta.

Aby uzyskać pełną próbkę roboczą aplikacji usługi Service Bus, która ilustruje konfigurację i używa autoryzacji [sygnatury dostępu współdzielonego, zobacz uwierzytelnianie podpisu dostępu współdzielonego za pomocą usługi Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Pokrewny przykład, który ilustruje użycie reguł autoryzacji sygnatury dostępu Współdzielonego skonfigurowanych w obszarach nazw lub tematach do bezpiecznych subskrypcji usługi Service Bus, jest dostępny tutaj: [Korzystanie z uwierzytelniania sygnatury dostępu współdzielonego (SAS) za pomocą subskrypcji magistrali usług.](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Uzyskiwanie dostępu do reguł autoryzacji dostępu współdzielonego w encji

Za pomocą bibliotek programu Service Bus .NET Framework można uzyskać dostęp do obiektu [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) skonfigurowanego w kolejce lub temacie magistrali usługowej za pośrednictwem kolekcji [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) w odpowiedniej [kolejceDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) lub [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

Poniższy kod pokazuje, jak dodać reguły autoryzacji dla kolejki.

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

## <a name="use-shared-access-signature-authorization"></a>Korzystanie z autoryzacji podpisu dostępu współdzielonego

Aplikacje korzystające z narzędzia Azure .NET SDK z bibliotekami .NET usługi Service Bus mogą używać autoryzacji sygnatury dostępu współdzielonego za pośrednictwem klasy [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) Poniższy kod ilustruje użycie dostawcy tokenu do wysyłania wiadomości do kolejki usługi Service Bus. Alternatywa dla użycia pokazano w tym miejscu, można również przekazać wcześniej wystawiony token do metody fabrycznej dostawcy tokenu.

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

Można również użyć dostawcy tokenu bezpośrednio do wystawiania tokenów do przekazania do innych klientów.

Parametry połączenia mogą zawierać nazwę reguły (*SharedAccessKeyName*) i klucz reguły (*SharedAccessKey*) lub wcześniej wystawiony token (*SharedAccessSignature*). Gdy te są obecne w ciągu połączenia przekazywane do dowolnego konstruktora lub metody fabrycznej akceptując ciąg połączenia, dostawca tokenu sygnatury dostępu Współdzielonego jest automatycznie tworzony i wypełniany.

Należy zauważyć, że aby użyć autoryzacji sygnatury dostępu Współdzielonego z przekaźnikami usługi Service Bus, można użyć kluczy Sygnatury dostępu Współdzielonego skonfigurowanych w obszarze nazw usługi Service Bus. Jeśli jawnie utworzyć przekaźnik na obszarze nazw[(NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) z [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) obiektu, można ustawić reguły sygnatury dostępu Współdzielonego tylko dla tego przekaźnika. Aby użyć autoryzacji sygnatury dostępu Współdzielonego z subskrypcjami usługi Service Bus, można użyć kluczy Sygnatury dostępu Współdzielonego skonfigurowanych w obszarze nazw usługi Service Bus lub w temacie.

## <a name="use-the-shared-access-signature-at-http-level"></a>Używanie podpisu dostępu współdzielonego (na poziomie HTTP)

Teraz, gdy wiesz, jak utworzyć podpisy dostępu współdzielonego dla dowolnych jednostek w usłudze Service Bus, możesz przystąpić do wykonania protokołu HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Pamiętaj, że to działa na wszystko. Można utworzyć sygnaturę dostępu Współdzielonego dla kolejki, tematu lub subskrypcji.

Jeśli dasz nadawcy lub klienta token sygnatury dostępu Współdzielonego, nie mają klucza bezpośrednio i nie można odwrócić skrótu, aby go uzyskać. W związku z tym masz kontrolę nad tym, co mogą uzyskać dostęp i jak długo. Ważną rzeczą do zapamiętania jest to, że jeśli zmienisz klucz podstawowy w zasadach, wszystkie utworzone z niego podpisy dostępu współdzielonego zostaną unieważnione.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Korzystanie z podpisu dostępu współdzielonego (na poziomie usługi AMQP)

W poprzedniej sekcji opisano, jak używać tokenu sygnatury dostępu Współdzielonego z żądaniem HTTP POST do wysyłania danych do usługi Service Bus. Jak wiadomo, można uzyskać dostęp do usługi Service Bus przy użyciu protokołu AMQP (Advanced Message UOKiK), który jest preferowanym protokołem używanym ze względu na wydajność, w wielu scenariuszach. Użycie tokenu sygnatury dostępu Współdzielonego z usługą AMQP jest opisane w dokumencie [W wersji zabezpieczeń opartych na oświadczeniach AMQP w wersji 1.0,](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) który jest w wersji roboczej działającej od 2013 r., ale jest obecnie dobrze obsługiwany przez platformę Azure.

Przed rozpoczęciem wysyłania danych do usługi Service Bus wydawca musi wysłać token sygnatury dostępu Współdzielonego wewnątrz komunikatu USŁUGI AMQP do dobrze zdefiniowanego węzła AMQP o nazwie **$cbs** (można go zobaczyć jako "specjalną" kolejkę używaną przez usługę do uzyskiwania i sprawdzania poprawności wszystkich tokenów sygnatury dostępu Współdzielonego dostępu Współdzielonego. Wydawca musi określić **replyTo** pole wewnątrz komunikatu AMQP; jest to węzeł, w którym usługa odpowiada wydawcy w wyniku sprawdzania poprawności tokenu (prosty wzorzec żądania/odpowiedzi między wydawcą a usługą). Ten węzeł odpowiedzi jest tworzony "w locie", mówiąc o "dynamicznym tworzeniu zdalnego węzła", zgodnie z opisem w specyfikacji AMQP 1.0. Po sprawdzeniu, czy token sygnatury dostępu Współdzielonego jest prawidłowy, wydawca może przejść do przodu i rozpocząć wysyłanie danych do usługi.

Poniższe kroki pokazują, jak wysłać token sygnatury dostępu Współdzielonego z protokołem AMQP przy użyciu [biblioteki AMQP.NET Lite.](https://github.com/Azure/amqpnetlite) Jest to przydatne, jeśli nie można użyć oficjalnego SDK usługi Service Bus (na przykład w winRT,\#.NET Compact Framework, .NET Micro Framework i Mono) rozwijającym się w języku C . Oczywiście ta biblioteka jest przydatna, aby pomóc zrozumieć, jak zabezpieczenia oparte na oświadczenia działa na poziomie PROTOKOŁU AMQP, jak to działa na poziomie HTTP (z żądaniem HTTP POST i tokenem Sygnatury dostępu Współdzielonego wysłane wewnątrz nagłówka "Autoryzacja"). Jeśli nie potrzebujesz tak głębokiej wiedzy na temat usługi AMQP, możesz użyć oficjalnego zestawie SDK usługi Service Bus z aplikacjami .NET Framework, które zrobią to za Ciebie.

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

Metoda `PutCbsToken()` odbiera *połączenie* (wystąpienie klasy połączenia AMQP dostarczone przez [bibliotekę AMQP .NET Lite),](https://github.com/Azure/amqpnetlite)które reprezentuje połączenie TCP z usługą i parametr *sasToken,* który jest tokenem sygnatury dostępu Współdzielonego do wysłania.

> [!NOTE]
> Ważne jest, aby połączenie zostało utworzone za pomocą **mechanizmu uwierzytelniania SASL ustawionego na ANONIMOWY** (a nie domyślnego ZWYKŁEGO z nazwą użytkownika i hasłem używanym, gdy nie trzeba wysyłać tokenu Sygnatury dostępu Współdzielonego).
>
>

Następnie wydawca tworzy dwa łącza AMQP do wysyłania tokenu sygnatury dostępu Współdzielonego i odbierania odpowiedzi (wynik sprawdzania poprawności tokenu) z usługi.

Komunikat USŁUGI AMQP zawiera zestaw właściwości i więcej informacji niż prosty komunikat. Sygnatura dostępu Współdzielonego jest treścią wiadomości (przy użyciu jego konstruktora). **Właściwość "ReplyTo"** jest ustawiona na nazwę węzła do odbierania wyniku weryfikacji na łączu odbiorcy (można zmienić jego nazwę, jeśli chcesz, i zostanie utworzony dynamicznie przez usługę). Ostatnie trzy właściwości aplikacji/niestandardowe są używane przez usługę, aby wskazać, jakiego rodzaju operacji ma do wykonania. Zgodnie z opisem specyfikacji wersji roboczej CBS muszą to być **nazwa operacji** ("put-token"), **typ tokenu** (w tym przypadku a `servicebus.windows.net:sastoken`) i **"nazwa" odbiorców,** do których stosuje się token (cała jednostka).

Po wysłaniu tokenu Sygnatury dostępu Współdzielonego w łączu nadawcy wydawca musi odczytać odpowiedź na łącze odbiorcy. Odpowiedź jest prosty komunikat AMQP z właściwości aplikacji o nazwie **"kod stanu",** który może zawierać takie same wartości jak kod stanu HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Prawa wymagane do operacji usługi Service Bus

W poniższej tabeli przedstawiono prawa dostępu wymagane dla różnych operacji w zasobach usługi Service Bus.

| Operacja | Wymagane roszczenie | Zakres oświadczeń |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurowanie reguły autoryzacji w obszarze nazw |Zarządzaj |Dowolny adres obszaru nazw |
| **Rejestr usług** | | |
| Wyliczaj zasady prywatne |Zarządzaj |Dowolny adres obszaru nazw |
| Rozpoczynanie nasłuchiwania w obszarze nazw |Nasłuchiwanie |Dowolny adres obszaru nazw |
| Wysyłanie wiadomości do odbiornika w obszarze nazw |Wysyłanie |Dowolny adres obszaru nazw |
| **Kolejka** | | |
| Tworzenie kolejki |Zarządzaj |Dowolny adres obszaru nazw |
| Usuwanie kolejki |Zarządzaj |Dowolny prawidłowy adres kolejki |
| Wyliczaj kolejki |Zarządzaj |/$Resources/Kolejki |
| Pobierz opis kolejki |Zarządzaj |Dowolny prawidłowy adres kolejki |
| Konfigurowanie reguły autoryzacji dla kolejki |Zarządzaj |Dowolny prawidłowy adres kolejki |
| Wyślij do kolejki |Wysyłanie |Dowolny prawidłowy adres kolejki |
| Odbieranie wiadomości z kolejki |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Porzucenie lub zakończenie wiadomości po otrzymaniu wiadomości w trybie blokady wglądu |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Odroczenie wiadomości w celu późniejszego pobrania |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Deadletter wiadomość |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Pobierz stan skojarzony z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Ustawianie stanu skojarzonego z sesją kolejki komunikatów |Nasłuchiwanie |Dowolny prawidłowy adres kolejki |
| Zaplanuj wiadomość do późniejszego dostarczenia; na przykład [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Nasłuchiwanie | Dowolny prawidłowy adres kolejki
| **Temat** | | |
| Tworzenie tematu |Zarządzaj |Dowolny adres obszaru nazw |
| Usuwanie tematu |Zarządzaj |Dowolny prawidłowy adres tematu |
| Wyliczaj tematy |Zarządzaj |/$Resources/Tematy |
| Pobierz opis tematu |Zarządzaj |Dowolny prawidłowy adres tematu |
| Konfigurowanie reguły autoryzacji dla tematu |Zarządzaj |Dowolny prawidłowy adres tematu |
| Wyślij do tematu |Wysyłanie |Dowolny prawidłowy adres tematu |
| **Subskrypcja** | | |
| Tworzenie subskrypcji |Zarządzaj |Dowolny adres obszaru nazw |
| Usuwanie subskrypcji |Zarządzaj |.. /myTopic/Subskrypcje/mySubscription |
| Wyliczaj subskrypcje |Zarządzaj |.. /myTopic/Subskrypcje |
| Pobierz opis subskrypcji |Zarządzaj |.. /myTopic/Subskrypcje/mySubscription |
| Porzucenie lub zakończenie wiadomości po otrzymaniu wiadomości w trybie blokady wglądu |Nasłuchiwanie |.. /myTopic/Subskrypcje/mySubscription |
| Odroczenie wiadomości w celu późniejszego pobrania |Nasłuchiwanie |.. /myTopic/Subskrypcje/mySubscription |
| Deadletter wiadomość |Nasłuchiwanie |.. /myTopic/Subskrypcje/mySubscription |
| Pobierz stan skojarzony z sesją tematu |Nasłuchiwanie |.. /myTopic/Subskrypcje/mySubscription |
| Ustawianie stanu skojarzonego z sesją tematu |Nasłuchiwanie |.. /myTopic/Subskrypcje/mySubscription |
| **Zasady** | | |
| Tworzenie reguły |Zarządzaj |.. /myTopic/Subskrypcje/mySubscription |
| Usuwanie reguły |Zarządzaj |.. /myTopic/Subskrypcje/mySubscription |
| Wyliczanie zasad |Zarządzanie zarządzaniem lub słuchanie |.. /myTopic/Subskrypcje/mySubscription/Rules

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy.

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak używać kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
